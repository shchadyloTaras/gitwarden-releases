# GitWarden

[![Release](https://img.shields.io/github/v/release/shchadyloTaras/gitwarden-releases?label=release)](https://github.com/shchadyloTaras/gitwarden-releases/releases/latest)

A cross-platform desktop Git GUI focused on **safe multi-account GitHub usage** — it prevents committing or pushing with the wrong profile, author name, email, SSH key, or repository.

Built around **profiles** (e.g. Personal / Work / Client). Each repository is assigned to exactly one profile, and GitWarden surfaces identity safety **before** every commit and push — so you never accidentally push with the wrong account or key.

This repository hosts **binary releases only** — no source code.

## Download

**Easiest:** the [download site](https://gitwarden.vercel.app) detects your OS and links the right installer in one click.

Prefer to choose yourself? Grab it from the [Releases page](https://github.com/shchadyloTaras/gitwarden-releases/releases/latest):

| Platform                  | File                        | Install                                                   |
| ------------------------- | --------------------------- | --------------------------------------------------------- |
| **macOS (Apple Silicon)** | `GitWarden-<ver>-arm64.dmg` | Open the `.dmg`, drag GitWarden to Applications           |
| **macOS (Intel)**         | `GitWarden-<ver>-x64.dmg`   | Open the `.dmg`, drag GitWarden to Applications           |
| **Windows**               | `GitWarden-Setup-<ver>.exe` | Run the installer; choose install dir, creates shortcuts  |
| **Linux (AppImage)**      | `GitWarden-<ver>.AppImage`  | `chmod +x GitWarden-*.AppImage && ./GitWarden-*.AppImage` |
| **Linux (deb)**           | `gitwarden_<ver>_amd64.deb` | `sudo apt install ./gitwarden_*.deb`                      |

### One-time OS warning (unsigned builds)

The current release is **unsigned** (no paid code-signing certificate yet). Your OS will show a one-time warning:

- **macOS Gatekeeper:** "GitWarden can't be opened because Apple cannot check it for malicious software." → Right-click the app → **Open** → **Open**.
- **Windows SmartScreen:** "Windows protected your PC — Unknown publisher." → **More info** → **Run anyway**.
- **Linux:** no warning.

## Why

Juggling multiple GitHub identities locally is error-prone: the right email but the wrong SSH key, a repo committed under your personal name at work, a push to the wrong account. GitWarden makes the active identity always visible and **blocks** unsafe actions.

## Support & security

- **Security policy:** [SECURITY.md](SECURITY.md)
- **License:** [LICENSE](LICENSE)
- Found a bug or have feedback? Open an issue on this repository.

## Stack

Electron + TypeScript + React. Runs on macOS, Linux, and Windows.
