# Linux Mint Cinnamon Development Useful Software

Development workstation guide for **Linux Mint 22.3 Cinnamon Edition**.

> **Public repository rule:** all commands and examples must use placeholders. Never commit personal email addresses, real server names, tokens, SSH private keys, `.env` files, credentials, database dumps, customer data or other secrets.

## Update Linux Mint

Start with normal updates:

```bash
sudo apt update
sudo apt upgrade
```

Use `full-upgrade` only after reviewing the proposed package changes:

```bash
sudo apt full-upgrade
```

Avoid automatic `autoremove --purge` in bootstrap scripts. If you use `autoremove`, review the package list before confirming.

## Essential development packages

```bash
sudo apt install \
  build-essential ca-certificates curl wget git git-lfs gnupg \
  unzip zip p7zip-full rsync tree jq htop btop ncdu tmux vim nano \
  ripgrep fd-find fzf shellcheck imagemagick ffmpeg openssh-client \
  python3 python3-pip python3-venv pipx direnv rclone
```

Initialize Git LFS for the current user:

```bash
git lfs install
```

> On Ubuntu/Mint, the `fd` executable provided by `fd-find` may be named `fdfind`. Do not create system-wide symlinks unless needed; aliases can be configured locally.

## Git configuration

Use local placeholder values and replace them only on your own machine:

```bash
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR_EMAIL@example.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input
```

Check configuration before publishing dotfiles:

```bash
git config --global --list
```

Do not copy a real `.gitconfig` into this public repository without sanitizing identity, signing keys, private URLs and credential helpers.

## SSH key for GitHub

Generate keys locally only:

```bash
ssh-keygen -t ed25519 -C "YOUR_EMAIL@example.com"
```

Display **only the public key** when you need to copy it:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add the public key to GitHub and test:

```bash
ssh -T git@github.com
```

Never commit files such as:

```text
~/.ssh/id_ed25519
~/.ssh/id_rsa
~/.ssh/config   # unless carefully sanitized
```

## GitHub CLI

Linux Mint's repository may provide GitHub CLI directly:

```bash
sudo apt install gh
```

If a newer release is needed, use GitHub's documented package source rather than an unverified install script.

Authenticate interactively on the local machine:

```bash
gh auth login
```

Useful commands:

```bash
gh repo clone OWNER/REPOSITORY
gh pr list
gh pr create
gh issue list
```

GitHub Desktop is not required: Git CLI, GitHub CLI and the Git integrations in PhpStorm, Cursor and VS Code cover most workflows.

## Docker Engine

On Linux, Docker Engine is usually lighter than Docker Desktop for this workstation. Do not install XAMPP for normal container-based development.

Docker's Ubuntu instructions generally work on Ubuntu derivatives, but Linux Mint itself is not an officially tested Docker target. Linux Mint 22.x is Ubuntu-based, so use the Ubuntu codename exposed by `/etc/os-release` rather than hard-coding one.

### Check for conflicting packages first

Do **not** blindly remove packages in a loop on a public guide. First inspect what is installed:

```bash
dpkg -l | grep -E 'docker|containerd|runc|podman'
```

If conflicting packages are present, compare them with Docker's current official documentation and remove only the packages that actually conflict with the chosen installation.

### Add Docker's official APT repository

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Create the source using the Ubuntu codename reported by Mint:

```bash
. /etc/os-release
UBUNTU_SUITE="${UBUNTU_CODENAME:-$VERSION_CODENAME}"
ARCH="$(dpkg --print-architecture)"

cat <<EOF | sudo tee /etc/apt/sources.list.d/docker.sources > /dev/null
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: ${UBUNTU_SUITE}
Components: stable
Architectures: ${ARCH}
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Review the generated source before installing:

```bash
cat /etc/apt/sources.list.d/docker.sources
```

Install Docker Engine:

```bash
sudo apt update
sudo apt install \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

Verify the service first:

```bash
sudo systemctl status docker --no-pager
```

Then test Docker with elevated privileges:

```bash
sudo docker run --rm hello-world
```

Verify Compose:

```bash
docker compose version
```

### Running Docker without sudo

Adding a user to the `docker` group effectively grants root-level control over the host through the Docker daemon. This is convenient on a single-user development workstation but is a security decision, not a harmless permission tweak.

If you explicitly accept that trade-off:

```bash
sudo usermod -aG docker "$USER"
```

Log out and back in, then test:

```bash
docker run --rm hello-world
```

For a stricter security model, review Docker's **rootless mode** instead of adding the user to the `docker` group.

Do not expose the Docker daemon over TCP without a specific, secured requirement.

## Docker networking and local-only services

Docker-published ports can bypass normal UFW expectations. Development services that should only be available on the workstation should bind to loopback explicitly:

```yaml
services:
  web:
    ports:
      - "127.0.0.1:8080:80"
```

Avoid this for local-only services:

```yaml
ports:
  - "8080:80"
```

because it normally publishes on all host interfaces.

Never commit real database passwords in `compose.yaml`. Prefer placeholders and environment files excluded by `.gitignore`:

```text
.env
.env.local
.env.*.local
```

Commit an `.env.example` containing non-secret placeholders instead.

## PHP and Composer

Prefer project-specific PHP versions inside Docker. A small host CLI installation can still be useful:

```bash
sudo apt install php-cli php-curl php-mbstring php-xml php-zip composer
```

Verify:

```bash
php --version
composer --version
```

Avoid mixing many third-party PHP repositories into the host unless a project genuinely requires host-side PHP versions. Containers reduce dependency conflicts and make upgrades safer.

## Node.js

Do not depend on the distribution Node.js version for active development projects when multiple Node versions are needed. Use one version manager, not several at the same time.

Good choices include:

- `fnm` for a focused Node.js workflow
- `mise` for a single multi-runtime manager
- `nvm` when compatibility with an existing workflow matters

Prefer installation methods documented by the selected project. Avoid piping remote shell scripts directly into a shell without reviewing what they do.

After installation:

```bash
node --version
npm --version
```

For pnpm or Yarn projects, use the package manager/version expected by the project. Corepack availability depends on the installed Node.js release, so do not assume it is always present.

## Java

If several JDK versions are required, use one runtime manager such as SDKMAN or mise.

For a simple distro-managed JDK:

```bash
sudo apt install default-jdk
java --version
```

Keep the host setup minimal when Java is not a primary development requirement.

## Python

Python tooling is included in the essential package set:

```bash
python3 --version
python3 -m venv .venv
```

Activate the environment locally:

```bash
source .venv/bin/activate
```

Use virtual environments for project dependencies and `pipx` for standalone Python CLI applications. Avoid `sudo pip install` and avoid modifying the distribution-managed Python environment globally.

## Cursor

Download Cursor only from its official site. Prefer the official Linux `.deb` package when available.

After downloading the package locally, install the exact downloaded file rather than relying on a broad wildcard in directories containing unrelated packages:

```bash
sudo apt install ./cursor-package.deb
```

Replace `cursor-package.deb` with the actual downloaded filename.

## Visual Studio Code

Install VS Code using Microsoft's official Debian/Ubuntu package or repository. Avoid unofficial repackaged builds unless you intentionally want one.

Verify:

```bash
code --version
```

Useful extension categories:

- Docker / Dev Containers
- PHP
- GitHub
- EditorConfig
- ESLint
- Prettier

Export installed extension identifiers to a temporary/local file:

```bash
code --list-extensions > vscode-extensions.txt
```

Before committing that file publicly, review the list for proprietary/internal extensions or anything that reveals private project information.

Restore extensions with a safe read loop:

```bash
while IFS= read -r extension; do
  [ -n "$extension" ] && code --install-extension "$extension"
done < vscode-extensions.txt
```

## PhpStorm

Prefer **JetBrains Toolbox** or JetBrains' currently documented Linux installation method for installing and updating PhpStorm.

Typical tools:

- PhpStorm
- DataGrip (optional)
- IntelliJ IDEA (optional)

PhpStorm already includes strong Git, Docker and database integrations, so separate GUI applications are not always necessary. On a 16 GB workstation, avoiding duplicate always-running tools can save meaningful RAM.

## Database client

Instead of installing a different GUI for every database, consider one of:

- PhpStorm database tools
- DBeaver Community
- DataGrip

Typical databases should run inside Docker when practical:

- MariaDB / MySQL
- PostgreSQL
- Redis

Bind database ports to `127.0.0.1` unless LAN access is intentionally required.

## API clients

Use one primary API client instead of keeping several Electron applications running:

- Postman
- Bruno
- HTTPie

Bruno is useful when API collections should live as reviewable files alongside Git repositories. Never commit authorization tokens or real secrets in collections.

## File transfer and remote systems

Install FileZilla only if a GUI is genuinely useful:

```bash
sudo apt install filezilla
```

For automated workflows prefer native tools:

```bash
ssh USER@HOST
scp FILE USER@HOST:/REMOTE/PATH/
rsync -av ./ USER@HOST:/REMOTE/PATH/
rclone --help
```

All values above are placeholders. Never publish private hosts, usernames, keys or production paths in this repository.

## Poedit

Useful for gettext and WordPress localization:

```bash
sudo apt install poedit
```

## Image and video tools

Already installed in the essential package set:

```bash
magick -version 2>/dev/null || convert --version
ffmpeg -version
```

ImageMagick command naming can vary by packaged version, so the check above accepts both styles.

## Suggested Docker project layout

```text
~/Development/project/
├── compose.yaml
├── Dockerfile
├── .env.example
├── .gitignore
└── src/
```

Example services can include:

```text
nginx
php-fpm
mariadb/postgresql
redis
mail testing service
```

This replaces the traditional XAMPP workflow and allows each project to use its own runtime versions.

## Useful CLI tools added to the macOS setup

```text
gh          GitHub CLI
git-lfs     Git Large File Storage
ripgrep     fast text search
fd-find     fast file search package (command may be fdfind)
fzf         fuzzy finder
jq          JSON processor
btop        system monitor
ncdu        disk usage explorer
tmux        terminal multiplexer
rsync       file synchronization
rclone      cloud/remote synchronization
direnv      per-directory environment
shellcheck  shell script analyzer
pipx        isolated Python CLI applications
```

## Performance principles

For a 16 GB development workstation:

- avoid running multiple Electron apps when a browser/PWA or IDE integration is enough;
- run only the containers needed for the current project;
- avoid installing duplicate database servers on both host and Docker;
- prefer integrated Git/database/Docker tooling in PhpStorm or VS Code when it replaces another always-running GUI;
- investigate sustained memory pressure with `btop`, `free -h` and Docker statistics before applying random kernel tweaks.

Useful checks:

```bash
free -h
docker stats --no-stream
btop
```

## Update development tools

System packages:

```bash
sudo apt update
sudo apt upgrade
```

Docker packages installed from the official repository are updated through APT as well.

Runtime managers and IDEs should be updated through their own supported mechanisms.

Before major upgrades, keep source code pushed/backed up and consider a Timeshift snapshot for system recovery.