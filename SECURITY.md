# SECURITY.md — GitWarden

This document describes GitWarden's threat model and the security rules its engineering
team holds itself to. It is published here, on the public binary-distribution repository,
for transparency — even though the application's source code is maintained in a private
repository.

## Threat model

Opening a repository can **execute code**. Git hooks (`.git/hooks/*`) and certain config
keys (e.g. `core.fsmonitor`, `core.pager`, alias expansions) run external programs as a
side effect of ordinary git commands. Therefore **every repo path and every repo's
contents are untrusted input**, even on the user's own machine.

## Enforceable rules

1. **`execFile` + args array, never a shell.**
   Git is run only via `child_process.execFile` with an **arguments array**. Never `exec`,
   never `sh -c`, never string interpolation or shell concatenation. Path arguments go
   after `--` to prevent option injection. A single internal component is the only caller
   of `execFile`.

2. **Canonicalize & validate every repo path.**
   The real path is resolved (symlinks followed), paths containing `..` traversal are
   rejected, and a genuine `.git` (directory or gitfile) is required before treating a path
   as a repo. Untrusted paths never reach git unvalidated.

3. **Controlled git environment.**
   Every git invocation runs with a fixed environment:
   - `GIT_CONFIG_NOSYSTEM=1` — ignore system-level git config.
   - `GIT_TERMINAL_PROMPT=0` — never block on an interactive credential prompt.
   - `GIT_OPTIONAL_LOCKS=0` — for read-only ops, avoid taking optional locks.
   - `LC_ALL=C` — stable, parseable, locale-independent output.
     Only the minimal environment git/ssh need is forwarded (`HOME`/`USERPROFILE`, `PATH`,
     `SSH_AUTH_SOCK`); nothing else from the host environment leaks through.

4. **Locked-down renderer.**
   The application UI runs with `contextIsolation: true`, `nodeIntegration: false`, and
   `sandbox: true`. The UI has **no Node access**; it reaches the backend only through a
   typed, validated bridge.

5. **Validated IPC.**
   Every message crossing the UI/backend bridge (both directions) is schema-validated at
   the boundary. Malformed or unexpected payloads are rejected, not coerced.

6. **No secret logging.**
   Tokens, secrets, and credential material are never logged. Logging redacts known-
   sensitive fields; when in doubt, the value is omitted. Secrets never appear in logs,
   error messages, or telemetry.

7. **Hooks risk acknowledged.**
   GitWarden does not sanitize or sandbox git hooks. This residual risk is accepted and
   documented. Mitigation: all **destructive and remote actions stay behind explicit
   confirmation**, and irreversible actions (e.g. `git clean`) get a distinct, stronger
   warning.

## Credential handling (GitHub, AI connections)

- GitHub authentication uses the OAuth **Device Authorization Flow**, which has no client
  secret. Access tokens are held only in the OS-native encrypted credential store, keyed
  per profile, and never written in plaintext or persisted to JSON. Tokens never cross into
  the UI layer.
- Any optional AI-assistant connection is **off by default**. No repository content leaves
  the machine until a user explicitly enables AI and selects a connection. AI output is
  strictly advisory — it never triggers a git action on its own. API keys for AI connections
  are held only in the same encrypted credential store and are never logged or returned to
  the UI after they are saved.

## Reporting a vulnerability

If you believe you've found a security issue in GitWarden, please open an issue on this
repository describing the problem. Avoid including exploit details or sensitive data in a
public issue; a maintainer will follow up to arrange a private disclosure channel if needed.
