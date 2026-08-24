# Linux Mint Cinnamon Development Useful Software

Development workstation guide for **Linux Mint 22.3 Cinnamon Edition**.

## Update Linux Mint

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove --purge -y
```

## Essential development packages

```bash
sudo apt install -y \
  build-essential ca-certificates curl wget git git-lfs gnupg \
  unzip zip p7zip-full rsync tree jq htop btop ncdu tmux vim nano \
  ripgrep fd-find fzf shellcheck imagemagick ffmpeg openssh-client \
  python3 python3-pip python3-venv pipx direnv rclone
```

Initialize Git LFS:

```bash
git lfs install
```

## Git configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
git config --global core.autocrlf input
```

Check configuration:

```bash
git config --global --list
```

## SSH key for GitHub

```bash
ssh-keygen -t ed25519 -C "your@email.com"
cat ~/.ssh/id_ed25519.pub
```

Add the public key to GitHub and test:

```bash
ssh -T git@github.com
```

Never commit private SSH keys, tokens, `.env` files or credentials to this repository.

## GitHub CLI

```bash
sudo apt install -y gh
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

On Linux prefer **Docker Engine** over Docker Desktop. Do not install XAMPP for normal container-based development.

Remove conflicting packages if present:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do
  sudo apt-get remove -y "$pkg"
done
```

Add Docker's official APT repository:

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Linux Mint is Ubuntu-based. Use the Ubuntu codename exposed by Mint:

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

Install Docker:

```bash
sudo apt update
sudo apt install -y \
  docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

Test:

```bash
sudo docker run hello-world
docker compose version
```

Optional: allow the current user to run Docker without `sudo`:

```bash
sudo usermod -aG docker "$USER"
```

Log out and back in before testing:

```bash
docker run hello-world
```

> Membership in the `docker` group effectively grants root-level privileges. Only add trusted users.

## PHP and Composer

Prefer project-specific PHP versions inside Docker. A small host CLI installation can still be useful:

```bash
sudo apt install -y php-cli php-curl php-mbstring php-xml php-zip composer
```

Verify:

```bash
php --version
composer --version
```

For reproducible projects, keep PHP, web server, database and related services in `compose.yaml`.

## Node.js

Do not depend on the distribution Node.js version for development projects. Use a version manager such as **fnm**, **nvm**, or a multi-runtime manager such as **mise**.

After installing your preferred manager, install the current project/LTS Node version and verify:

```bash
node --version
npm --version
```

For projects using pnpm or Yarn, enable Corepack when supported by the installed Node version:

```bash
corepack enable
```

## Java

For projects that need multiple Java versions, use a version manager such as SDKMAN or mise rather than maintaining several system JDKs manually.

For a simple system JDK installation:

```bash
sudo apt install -y default-jdk
java --version
```

## Python

Python tooling is already included in the essential packages:

```bash
python3 --version
python3 -m venv .venv
```

Use virtual environments for project dependencies and `pipx` for standalone Python CLI applications. Avoid `sudo pip install`.

## Cursor

Download the official Linux `.deb` x64 package from Cursor and install it with APT:

```bash
sudo apt install ./cursor*.deb
```

Using APT to install a local `.deb` allows dependencies to be resolved automatically.

## Visual Studio Code

Install VS Code using Microsoft's official Debian/Ubuntu package or repository. After installation:

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

Export installed extensions for future workstation rebuilds:

```bash
code --list-extensions > vscode-extensions.txt
```

Restore them with:

```bash
xargs -n 1 code --install-extension < vscode-extensions.txt
```

## PhpStorm

Prefer **JetBrains Toolbox** for installing and updating PhpStorm and other JetBrains IDEs.

Typical tools:

- PhpStorm
- DataGrip (optional)
- IntelliJ IDEA (optional)

PhpStorm already includes excellent Git, Docker and database integrations, so separate GUI applications are not always necessary.

## Database client

Instead of installing a different GUI for each database, consider **DBeaver Community**, DataGrip, or PhpStorm's database tools.

Typical databases should run inside Docker:

- MariaDB / MySQL
- PostgreSQL
- Redis

## API clients

Use one of:

- Postman
- Bruno
- HTTPie

Bruno is useful when API collections should live as files alongside Git repositories.

## File transfer and remote systems

```bash
sudo apt install -y filezilla
```

For automated workflows prefer native tools:

```bash
ssh user@server
scp file user@server:/path/
rsync -av ./ user@server:/path/
rclone --help
```

## Poedit

Useful for gettext and WordPress localization:

```bash
sudo apt install -y poedit
```

## Image and video tools

Already installed in the essential package set:

```bash
convert --version
ffmpeg -version
```

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
mailpit
```

This replaces the traditional XAMPP workflow and allows each project to use its own runtime versions.

## Useful CLI tools added to the macOS setup

```text
gh          GitHub CLI
git-lfs     Git Large File Storage
ripgrep     fast text search
fd          fast file search
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

## Update development tools

System packages:

```bash
sudo apt update
sudo apt upgrade
```

Docker packages installed from the official repository are updated through APT as well.

Runtime managers and IDEs should be updated through their own supported mechanisms.