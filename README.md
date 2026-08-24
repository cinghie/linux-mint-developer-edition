# Linux Mint Cinnamon - Developer Edition

Personal setup and installation guide for turning **Linux Mint 22.3 Cinnamon Edition** into a clean, fast and reproducible developer workstation.

This project follows the same idea as my macOS Homebrew useful-software project, but uses a Linux-native approach: APT and official vendor repositories for system tools, Docker Engine instead of Docker Desktop where appropriate, containers instead of XAMPP for project services, and version managers for development runtimes.

> **Public repository safety:** this project must never contain real usernames, email addresses, SSH private keys, API tokens, passwords, VPN configuration files, `.env` files, database dumps containing sensitive data, private hostnames/IP addresses, customer names, recovery keys, or other secrets. Examples must always use placeholders.

## Target system

- Linux Mint 22.3 Cinnamon Edition, 64 bit
- Developer workstation
- Git / GitHub
- Docker / Docker Compose
- PHP / Composer
- Node.js
- Java when required
- Cursor
- Visual Studio Code
- PhpStorm

## Guides

- [Installation and USB Guide](Installation%20and%20USB%20Guide.md)
- [Development Useful Software](Development%20Useful%20Software.md)
- [Personal Useful Software](Personal%20Useful%20Software.md)

## Software profiles

Software is divided into three levels so future automation does not install everything indiscriminately.

### Essential

Install on almost every developer workstation:

```text
System updates
Git
SSH client
Build tools
curl / wget
Archive tools
Basic CLI utilities
Browser
Timeshift configuration
```

### Recommended

Install for the normal development workflow described by this project:

```text
GitHub CLI
Docker Engine + Docker Compose
PhpStorm
Cursor
Visual Studio Code
Runtime/version manager
Composer when PHP CLI is useful
ImageMagick / FFmpeg when used by projects
KeePassXC
Thunderbird / LibreOffice as needed for daily work
```

### Optional

Install only when there is a concrete use case:

```text
Java / JDK
DBeaver / DataGrip
Postman / Bruno / HTTPie
FileZilla
Poedit
Tor Browser
WireGuard / commercial VPN client
AnyDesk / TeamViewer
Spotify desktop client
Additional browsers
ClamAV
```

The goal is to keep the host small, predictable and performant. A public bootstrap script should eventually expose these profiles rather than enabling all optional software by default.

## Recommended installation order

1. Download and verify the Linux Mint ISO.
2. Create a bootable USB stick safely.
3. Test the live session.
4. Back up existing data.
5. Install Linux Mint Cinnamon.
6. Install system updates.
7. Configure hardware drivers using Linux Mint Driver Manager.
8. Configure Timeshift before major customizations.
9. Install Essential software.
10. Install Recommended development tools actually used on the workstation.
11. Add Optional tools only when needed.
12. Configure Git, SSH and GitHub CLI using personal values locally only.
13. Configure backups and sanitized dotfiles.

See the complete [Installation and USB Guide](Installation%20and%20USB%20Guide.md) before modifying disks or replacing an existing operating system.

## First system update

Run normal system updates first:

```bash
sudo apt update
sudo apt upgrade
```

Use `full-upgrade` only when you understand the proposed package changes:

```bash
sudo apt full-upgrade
```

Before accepting removals, read APT's summary carefully. Avoid putting automatic package-removal commands into a public bootstrap script unless the affected packages are explicitly reviewed.

Reboot after kernel or driver updates when required:

```bash
sudo reboot
```

## Driver Manager

Use Linux Mint **Driver Manager** for proprietary hardware drivers instead of downloading and running vendor `.run` installers manually.

For NVIDIA laptops, install the driver recommended by Mint and verify it with:

```bash
nvidia-smi
```

For laptops with Intel + NVIDIA hybrid graphics, prefer integrated graphics for normal desktop work and NVIDIA on-demand when supported. This usually reduces heat, fan noise and power consumption.

Do not hard-code a specific NVIDIA driver version in this repository: the recommended version can change with the kernel, GPU generation and Linux Mint updates.

## Timeshift

Before making major system changes, configure **Timeshift** from the Linux Mint menu. Timeshift is useful for restoring the operating system after a problematic update or configuration change, but it is not a replacement for backups of source code and personal files.

Keep snapshots on storage with enough free space and review retention periodically.

## Philosophy

Keep the host operating system simple:

```text
Linux Mint
├── IDEs
├── Git / SSH
├── Docker Engine
├── CLI tools
├── Browser / desktop tools
└── Development projects
    ├── project-a
    │   └── compose.yaml
    ├── project-b
    │   └── compose.yaml
    └── project-c
        └── compose.yaml
```

Avoid installing project-specific databases, web servers and many runtime versions globally when containers or a runtime manager can keep them isolated.

Do not expose development services to the network unless they actually need to be reachable from another device.

## Development directory

```bash
mkdir -p ~/Development
```

Suggested structure:

```text
~/Development/
├── personal/
├── clients/
└── experiments/
```

Do not place local development projects under `/var/www` unless there is a specific reason to do so.

## Dotfiles and configuration

A public dotfiles repository must contain only sanitized configuration. Never commit:

```text
~/.ssh/id_*
~/.gnupg/
.env
.env.*
credentials files
authentication cookies
access tokens
private VPN profiles
cloud credentials
database dumps with real data
recovery keys
password-manager databases
```

Use `.example` files for configuration templates, for example `.env.example`, and inject secrets locally through environment variables, a password manager or another appropriate secret-management mechanism.

## Future improvements

This repository can later be extended with:

```text
scripts/
├── install.sh
├── system.sh
├── development.sh
├── desktop.sh
└── doctor.sh

config/
├── git/
├── shell/
├── vscode/
└── cinnamon/
```

Any future automation should be **idempotent**, show important changes before applying them, avoid destructive defaults, separate Essential/Recommended/Optional software, and never download and execute remote shell scripts without verification.

The final goal is a reproducible workstation that can be rebuilt safely from a clean Linux Mint installation.