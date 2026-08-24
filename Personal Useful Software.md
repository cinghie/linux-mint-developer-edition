# Linux Mint Cinnamon Personal Useful Software

Useful desktop software for **Linux Mint 22.3 Cinnamon Edition**.

The goal is not to reproduce every macOS application exactly, but to prefer native, maintained and lightweight Linux solutions.

## Update system

```bash
sudo apt update
sudo apt upgrade
```

## Browsers

### Firefox

Linux Mint includes Firefox by default. Keep it as the main browser unless there is a specific reason to replace it.

### Google Chrome

Install Google's official `.deb` package. Once installed, Chrome's repository can provide future updates through APT.

### Tor Browser

Install Tor Browser only when needed. Prefer the official Tor Project distribution rather than unofficial third-party packages.

### Opera

Optional. Installing many Chromium-based browsers increases disk usage and background processes. For a 16 GB developer workstation, Firefox + Chrome normally cover both daily use and web-development testing.

## Thunderbird

```bash
sudo apt install -y thunderbird
```

## VLC

```bash
sudo apt install -y vlc
```

## LibreOffice

```bash
sudo apt install -y libreoffice libreoffice-l10n-it
```

## PDF reader

Linux Mint already includes a PDF/document viewer. For additional features, install Okular:

```bash
sudo apt install -y okular
```

There is normally no need to reproduce the Adobe Acrobat Reader setup used on macOS.

## Archive utilities

```bash
sudo apt install -y p7zip-full unzip zip
```

Nemo can use these tools directly, so a separate equivalent of The Unarchiver is usually unnecessary.

## KeePassXC

Recommended replacement for MacPass:

```bash
sudo apt install -y keepassxc
```

Never store password databases or credentials in a public Git repository.

## Spotify

Spotify can be used through its Linux client or as a browser/PWA application. On a machine with 16 GB RAM, the web/PWA option can be preferable if many Electron applications are already running.

## WhatsApp

Prefer WhatsApp Web or a browser-installed PWA rather than an unofficial desktop wrapper.

## Slack

Install the official Linux client if it is used continuously. Otherwise the browser/PWA version avoids another permanently running Electron application.

## FileZilla

```bash
sudo apt install -y filezilla
```

For development and deployment workflows, also prefer SSH, SFTP, rsync and rclone where appropriate.

## Remote desktop

Install **either** AnyDesk or TeamViewer if required. Avoid keeping multiple remote-control services running automatically when they serve the same purpose.

## VPN

If using NordVPN, install its official Linux client according to the vendor documentation.

Linux also supports standard VPN technologies such as WireGuard and OpenVPN.

Install WireGuard only if required:

```bash
sudo apt install -y wireguard
```

## Google Drive / cloud storage

Google does not provide the same official Drive desktop synchronization client for Linux as on macOS/Windows. Depending on the workflow, consider:

- browser access
- desktop online-account integration
- `rclone`
- a dedicated third-party synchronization client

For command-line/cloud workflows:

```bash
sudo apt install -y rclone
rclone config
```

## Photopea

Photopea remains a useful browser-based image editor and requires no local installation.

## Antivirus

Do not automatically reproduce the AVG setup from macOS. For a Linux developer workstation, prioritize:

- trusted APT/vendor repositories
- regular security updates
- browser updates
- firewall configuration
- strong passwords and SSH keys
- backups

ClamAV can be installed when there is a concrete need to scan files, especially files exchanged with Windows systems:

```bash
sudo apt install -y clamav
```

## Firewall

Linux Mint provides firewall configuration based on UFW.

Check status:

```bash
sudo ufw status verbose
```

Enable it if appropriate for the workstation:

```bash
sudo ufw enable
```

When using Docker, remember that published container ports require separate attention. For development services that should only be accessible locally, bind them explicitly to localhost, for example:

```yaml
ports:
  - "127.0.0.1:8080:80"
```

instead of exposing the port on all interfaces.

## Timeshift

Use Timeshift for operating-system snapshots before major updates or configuration changes.

Timeshift is **not** a backup of personal data.

A reasonable starting retention policy for a 512 GB SSD is a small number of daily and weekly snapshots; adjust it according to available disk space.

## Backups

Keep different data protected appropriately:

```text
Source code       -> Git / GitHub
Dotfiles          -> private/sanitized Git repository
Personal files    -> external/cloud backup
Important DB data -> database dumps/backups
System state      -> Timeshift
```

Tools worth considering for automated encrypted backups include Restic or BorgBackup. `rclone` can be used as a transport for supported remote storage.

## System monitoring

Useful tools:

```bash
sudo apt install -y btop ncdu
```

Monitor memory while running IDEs, browsers and Docker:

```bash
free -h
btop
```

If normal development frequently consumes almost all 16 GB of RAM, upgrading to 32 GB will generally have a much larger impact than replacing Cinnamon with a lighter desktop environment.

## SSD TRIM

Check the systemd TRIM timer:

```bash
systemctl status fstrim.timer
```

Enable it if required:

```bash
sudo systemctl enable --now fstrim.timer
```

Avoid applying old SSD-tuning guides blindly; modern Linux distributions already handle NVMe SSDs well.

## Recommended minimal personal setup

```text
Firefox
Google Chrome
Thunderbird
VLC
LibreOffice
KeePassXC
FileZilla
Photopea
Spotify or Spotify Web
WhatsApp Web/PWA
Slack or Slack Web/PWA
```

Install additional software only when it has a real use. Fewer resident services and Electron applications leave more RAM and CPU available for IDEs and Docker.