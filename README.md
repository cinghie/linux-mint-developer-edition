# Linux Mint Cinnamon - Developer Edition

Personal setup and installation guide for turning **Linux Mint 22.3 Cinnamon Edition** into a clean, fast and reproducible developer workstation.

This project follows the same idea as my macOS Homebrew useful-software project, but uses a Linux-native approach: APT and official vendor repositories for system tools, Docker Engine instead of Docker Desktop, containers instead of XAMPP, and version managers for development runtimes.

## Target system

- Linux Mint 22.3 Cinnamon Edition, 64 bit
- Developer workstation
- Git / GitHub
- Docker / Docker Compose
- PHP / Composer
- Node.js
- Java
- Cursor
- Visual Studio Code
- PhpStorm

## Guides

- [Development Useful Software](Development%20Useful%20Software.md)
- [Personal Useful Software](Personal%20Useful%20Software.md)

## Recommended installation order

1. Install Linux Mint Cinnamon.
2. Install all system updates.
3. Configure drivers, especially NVIDIA on hybrid Intel/NVIDIA laptops.
4. Configure Timeshift.
5. Install development command-line tools.
6. Configure Git, SSH and GitHub CLI.
7. Install Docker Engine and Docker Compose.
8. Install development runtimes and IDEs.
9. Install personal desktop software.
10. Configure backups and dotfiles.

## First system update

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove --purge -y
```

Reboot after kernel or driver updates:

```bash
sudo reboot
```

## Driver Manager

Use Linux Mint **Driver Manager** for proprietary hardware drivers instead of downloading `.run` installers manually.

For NVIDIA laptops, install the driver recommended by Mint and verify it with:

```bash
nvidia-smi
```

For a development laptop with Intel + NVIDIA hybrid graphics, prefer Intel graphics for normal desktop use and NVIDIA on-demand when possible. This reduces heat, fan noise and battery usage.

## Timeshift

Before making major system changes, configure **Timeshift** from the Linux Mint menu. Timeshift is useful for restoring the operating system after a problematic update or configuration change, but it is not a replacement for backups of source code and personal files.

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

Avoid installing project-specific databases, web servers and multiple runtime versions globally when Docker or a runtime manager can keep them isolated.

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

The final goal is a reproducible workstation that can be rebuilt from a clean Linux Mint installation.