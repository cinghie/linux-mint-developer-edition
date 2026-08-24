# Linux Mint Cinnamon Personal Useful Software

Useful desktop software for **Linux Mint 22.3 Cinnamon Edition**.

The goal is not to reproduce every macOS application exactly, but to prefer native, maintained and lightweight Linux solutions.

> **Public repository rule:** never commit personal account identifiers, passwords, browser profiles, cookies, VPN profiles, recovery codes, password databases, cloud credentials, exported mail data, private hostnames or other sensitive information.

## Update system

```bash
sudo apt update
sudo apt upgrade
```

Review proposed changes before accepting major removals or replacements.

## Browsers

### Firefox

Linux Mint includes Firefox by default. Keep it as the main browser unless there is a specific reason to replace it.

### Google Chrome

Install Google's official `.deb` package from Google's official site. Once installed, Chrome can receive updates through its vendor repository.

Avoid copying a downloaded package command into automation unless the package source and signature/repository flow are clearly understood.

### Tor Browser

Install Tor Browser only when needed and use the official Tor Project distribution or installation guidance. Do not place Tor profiles, bookmarks or configuration containing private information in this repository.

### Opera

Optional. Installing many Chromium-based browsers increases disk usage and background processes. For a 16 GB developer workstation, Firefox + Chrome normally cover daily use and web-development testing.

## Thunderbird

```bash
sudo apt install thunderbird
```

Do not version-control Thunderbird profiles, exported mailboxes or account credentials in this public repository.

## VLC

```bash
sudo apt install vlc
```

## LibreOffice

```bash
sudo apt install libreoffice libreoffice-l10n-it
```

If another language is preferred, replace the language package accordingly rather than hard-coding personal locale assumptions into future automation.

## PDF reader

Linux Mint already includes a document viewer. Install Okular only if its extra features are useful:

```bash
sudo apt install okular
```

Avoid installing duplicate applications without a concrete need; this keeps the system smaller and easier to maintain.

## Archive utilities

```bash
sudo apt install p7zip-full unzip zip
```

Nemo can use these tools directly, so a separate equivalent of The Unarchiver is usually unnecessary.

## KeePassXC

Recommended replacement for MacPass:

```bash
sudo apt install keepassxc
```

Password databases (`*.kdbx`), exported credentials, recovery codes and key files must never be committed to this public repository.

## Spotify

Spotify can be used through its Linux client or browser. On a machine with 16 GB RAM, prefer the browser/PWA approach if many Electron or Chromium-based applications are already running.

Install desktop clients only from trusted vendor or distribution sources.

## WhatsApp

Prefer WhatsApp Web or a browser-installed PWA rather than an unofficial desktop wrapper.

Do not add browser profile directories, session data or exported chats to this repository.

## Slack

Install the official Linux client if it is used continuously. Otherwise the browser/PWA version avoids another permanently running Electron application.

Never publish workspace names, internal URLs, authentication tokens or exported conversations in this public setup project.

## FileZilla

```bash
sudo apt install filezilla
```

For development and deployment workflows, prefer SSH, SFTP, rsync and rclone where appropriate.

Do not commit FileZilla Site Manager exports or saved credentials.

## Remote desktop

Install **either** AnyDesk or TeamViewer only if required. Avoid keeping multiple remote-control services installed and running automatically when they serve the same purpose.

After installation, review whether unattended access and automatic startup are actually needed. Do not publish remote-access IDs, passwords, configuration exports or allowlists.

## VPN

If using a commercial VPN service, install its official Linux client according to current vendor documentation.

Linux also supports standard VPN technologies such as WireGuard and OpenVPN. Install them only if required by your workflow.

For example:

```bash
sudo apt install wireguard
```

Never commit WireGuard private keys, `.ovpn` files containing credentials, provider tokens, private endpoints or company VPN configuration.

## Google Drive / cloud storage

Google does not provide the same first-party Drive desktop synchronization client for Linux as on macOS/Windows. Depending on the workflow, consider:

- browser access;
- desktop online-account integration;
- `rclone`;
- a maintained third-party synchronization client.

For command-line/cloud workflows:

```bash
sudo apt install rclone
```

Configure it locally:

```bash
rclone config
```

`rclone.conf` can contain sensitive tokens and remote configuration. Do not commit it to this repository.

## Photopea

Photopea remains a browser-based image editor and requires no local installation.

## Antivirus

Do not automatically reproduce the AVG setup from macOS. For a Linux developer workstation, prioritize:

- trusted distribution/vendor repositories;
- regular security updates;
- browser updates;
- firewall configuration;
- strong authentication;
- backups;
- minimal installation of unnecessary privileged services.

ClamAV can be installed when there is a concrete need to scan files, particularly files exchanged with other systems:

```bash
sudo apt install clamav
```

Do not install security software merely to increase the number of background scanners; unnecessary resident services can add resource use and complexity without improving the workstation's actual threat model.

## Firewall

Linux Mint provides firewall configuration based on UFW.

Check status first:

```bash
sudo ufw status verbose
```

For a normal laptop/workstation that does not intentionally accept inbound connections, enabling UFW with its default deny-incoming policy is a reasonable baseline:

```bash
sudo ufw enable
```

If SSH server, development servers or other inbound services are later enabled, add only the minimum rules actually required and understand their network scope.

### Docker warning

Docker-published container ports can bypass normal UFW expectations. For development services that should only be accessible locally, bind explicitly to loopback:

```yaml
services:
  web:
    ports:
      - "127.0.0.1:8080:80"
```

Avoid publishing local-only services on all interfaces:

```yaml
ports:
  - "8080:80"
```

Apply the same rule to databases, Redis, mail-testing tools, admin dashboards and other development services.

## Timeshift

Use Timeshift for operating-system snapshots before major updates or configuration changes.

Timeshift is **not** a backup of personal data.

Keep only a modest number of snapshots and periodically verify available disk space. Do not configure an aggressive fixed retention policy in a generic public guide because available storage and workload differ between systems.

## Backups

Protect different data with appropriate mechanisms:

```text
Source code       -> Git + remote repository where appropriate
Dotfiles          -> sanitized Git repository
Personal files    -> separate backup destination
Important DB data -> explicit database exports/backups
System state      -> Timeshift
```

A Git remote is not automatically a complete backup: uncommitted work, ignored files, local databases and personal documents need separate protection.

Tools worth considering for encrypted automated backups include Restic or BorgBackup. `rclone` can be used as transport for supported remote storage.

Never put repository credentials or encryption passwords directly into public scripts.

## System monitoring

Useful tools:

```bash
sudo apt install btop ncdu
```

Monitor memory while running IDEs, browsers and Docker:

```bash
free -h
btop
```

Also inspect container memory when needed:

```bash
docker stats --no-stream
```

If normal development frequently consumes almost all 16 GB of RAM, upgrading to 32 GB will generally have a much larger impact than replacing Cinnamon with a lighter desktop environment.

## SSD TRIM

Check the systemd TRIM timer:

```bash
systemctl status fstrim.timer
```

Only enable it manually if it is not already enabled and the system configuration requires it:

```bash
sudo systemctl enable --now fstrim.timer
```

Avoid applying old SSD-tuning guides blindly. Do not disable filesystem journaling or apply obscure kernel/storage tweaks simply to chase benchmark numbers on a development workstation.

## Performance and startup services

Periodically review running services and autostart applications through Linux Mint's graphical tools or with:

```bash
systemctl --type=service --state=running
```

Do **not** disable unfamiliar system services just because they appear in a list. Remove or disable only software you understand and no longer need, especially remote desktop clients, sync applications and third-party background agents.

## Recommended minimal personal setup

```text
Firefox
Google Chrome (when Chromium testing is needed)
Thunderbird
VLC
LibreOffice
KeePassXC
FileZilla (when a GUI transfer client is needed)
Photopea
Spotify or Spotify Web
WhatsApp Web/PWA
Slack or Slack Web/PWA
```

Install additional software only when it has a real use. Fewer resident services and Electron/Chromium applications leave more RAM and CPU available for IDEs and Docker.