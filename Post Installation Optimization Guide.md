# Linux Mint Cinnamon Post-Installation Optimization Guide

Safe post-installation configuration and optimization guide for **Linux Mint 22.3 Cinnamon Edition**.

The objective is a responsive and reliable developer workstation, not maximum benchmark scores. Prefer supported defaults, measure actual bottlenecks, and change one thing at a time.

> **Public repository safety:** commands and examples in this guide contain no personal usernames, hostnames, credentials, private network information, tokens or secrets. Keep machine-specific/private values outside this repository.

## 1. Establish a baseline before tuning

Before changing the system, record basic hardware and software information locally:

```bash
inxi -Fxxxz
free -h
df -h
lsblk -f
```

`inxi -Fxxxz` deliberately filters some sensitive fields, but still review output before publishing it anywhere.

Check the Linux Mint and kernel versions:

```bash
cat /etc/linuxmint/info
uname -r
```

Do not copy complete diagnostic dumps into this public repository without reviewing them first.

## 2. Update the system

Start with normal updates:

```bash
sudo apt update
sudo apt upgrade
```

Reboot when a kernel, graphics driver or other component requiring a reboot has changed:

```bash
sudo reboot
```

Do not automatically run destructive cleanup commands after every update. If using `autoremove`, inspect the proposed removals first:

```bash
sudo apt autoremove
```

## 3. Configure Timeshift before major changes

Open **Timeshift** and create a system snapshot before driver changes or significant tuning.

Timeshift protects system state; it is not a replacement for backups of source code, documents, credentials or local databases.

Keep snapshot retention modest on smaller SSDs and periodically check available disk space.

## 4. Hardware drivers

Open **Driver Manager** and use the driver recommended by Linux Mint for proprietary hardware.

Do not install NVIDIA `.run` packages downloaded manually from the vendor website unless there is a very specific, understood reason. Distribution-managed packages are easier to update and recover.

For NVIDIA systems, verify after reboot:

```bash
nvidia-smi
```

On hybrid Intel + NVIDIA laptops, use integrated graphics for ordinary desktop/development workloads and NVIDIA on-demand when supported. This can reduce heat, fan activity and power consumption.

Do not hard-code a particular NVIDIA driver version in this public guide.

## 5. Firmware updates

Check whether firmware updates are available through the system-supported firmware mechanism:

```bash
fwupdmgr get-devices
fwupdmgr get-updates
```

Apply firmware updates only after reading the vendor/device information and following the tool's instructions. Keep the laptop connected to reliable AC power during firmware updates.

Do not interrupt BIOS/UEFI or device firmware updates.

## 6. Power profile

Check whether power profiles are available:

```bash
powerprofilesctl list
powerprofilesctl get
```

For normal development, `balanced` is generally a sensible starting point when supported:

```bash
powerprofilesctl set balanced
```

Use a performance profile only when sustained CPU performance is actually required. A permanently aggressive profile can increase temperature, fan noise and power use without noticeably improving normal IDE work.

Do not stack several power-management/tuning frameworks without understanding their interaction. Prefer one supported mechanism and measure the result.

## 7. CPU temperature and thermal behavior

Install monitoring tools if needed:

```bash
sudo apt install lm-sensors
```

Detect supported sensors interactively:

```bash
sudo sensors-detect
```

Read temperatures:

```bash
sensors
```

For an older laptop, persistent high temperatures or thermal throttling may indicate a physical cooling issue rather than a Linux configuration problem. Cleaning vents/fans and servicing thermal interfaces should be considered separately from software tuning and performed only by someone comfortable servicing the hardware.

Do not disable thermal protection mechanisms to improve benchmark results.

## 8. Memory and swap

Inspect current memory and swap configuration before changing anything:

```bash
free -h
swapon --show
cat /proc/sys/vm/swappiness
```

Linux intentionally uses otherwise-free RAM for filesystem cache; high cached memory is not by itself a problem.

For a 16 GB developer workstation, first observe real workloads such as PhpStorm, Cursor/VS Code, browsers and Docker before changing swap behavior.

Useful monitoring:

```bash
free -h
vmstat 1
btop
```

### zram

Compressed RAM swap (zram) can help systems that occasionally experience memory pressure, but it should not be enabled blindly alongside unknown existing swap configuration.

First check whether zram already exists:

```bash
swapon --show
zramctl
```

If it is not configured and measurements show meaningful swap pressure, evaluate the distribution-supported zram package/configuration available for the installed Mint base before enabling it.

Do not copy arbitrary zram sizes or aggressive `vm.swappiness` values from tuning guides. Workload and available RAM matter.

If normal development consistently exhausts 16 GB, a physical upgrade to 32 GB (when supported by the machine) is preferable to increasingly aggressive memory tuning.

## 9. SSD / NVMe health and TRIM

Check storage devices:

```bash
lsblk -o NAME,MODEL,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

Check periodic TRIM:

```bash
systemctl status fstrim.timer
```

If it is already enabled, no action is required. If it is disabled and the system/storage configuration supports periodic TRIM:

```bash
sudo systemctl enable --now fstrim.timer
```

Do not run continuous/manual TRIM loops or disable filesystem journaling for performance. Modern Linux defaults are generally appropriate for an NVMe development workstation.

For SMART/NVMe diagnostics, install the relevant tools only when needed:

```bash
sudo apt install smartmontools nvme-cli
```

Inspect the correct device rather than copying a hard-coded device path from a public guide:

```bash
lsblk
```

Never run destructive NVMe format/sanitize commands as part of optimization.

## 10. Filesystem free space

Keep adequate free space on the system SSD. Check with:

```bash
df -h
```

Find large directories interactively:

```bash
ncdu ~
```

Docker can also consume significant disk space:

```bash
docker system df
```

Do not put `docker system prune -a` or volume deletion commands into automatic cleanup scripts. Images, build cache and especially volumes can contain valuable development data.

## 11. Startup applications

Use Linux Mint's **Startup Applications** tool to review third-party applications that launch automatically.

Typical candidates to disable from automatic startup when not continuously needed include:

- remote desktop clients;
- cloud synchronization clients;
- chat clients;
- vendor helper applications;
- optional development GUIs.

Do not disable system services simply because their names are unfamiliar.

Inspect running services when troubleshooting:

```bash
systemctl --type=service --state=running
```

Change a service only after understanding its purpose and dependencies.

## 12. Cinnamon visual effects

Cinnamon should run comfortably on a machine with 16 GB RAM and an SSD. Do not disable every visual feature preemptively.

If desktop animations feel sluggish, reduce only the effects that are actually noticeable through Cinnamon's graphical settings. Avoid replacing the compositor or applying unsupported desktop hacks solely for small benchmark gains.

A stable desktop is more valuable for development than saving a small amount of idle memory.

## 13. Browser efficiency

Browsers can use more memory than the desktop environment itself.

For a 16 GB workstation:

- keep one primary browser;
- keep Chrome/Chromium available when required for development/testing;
- avoid running several Chromium-based browsers continuously;
- review unnecessary extensions;
- use browser/PWA versions of optional Electron applications when that reduces duplicated background processes.

Measure memory use rather than assuming Cinnamon is the main bottleneck.

## 14. IDE optimization

PhpStorm, Cursor and VS Code do not all need to run simultaneously for every task.

For JetBrains IDEs:

- exclude generated/cache/vendor directories from indexing when appropriate and supported by the project;
- do not blindly increase JVM heap size;
- keep plugins limited to those actually used;
- avoid indexing huge directories that do not belong to the project.

For VS Code/Cursor:

- remove unused extensions;
- avoid opening unnecessarily large parent directories as workspaces;
- exclude generated/build directories from watchers/search where appropriate.

Do not commit machine-specific IDE caches or project metadata containing sensitive/local paths to this public repository.

## 15. Docker performance

Linux runs Docker Engine natively, so a separate Docker Desktop VM is normally unnecessary for this workstation.

Run only the services required by the active project:

```bash
docker compose up -d
```

Stop project services when they are no longer needed:

```bash
docker compose stop
```

Inspect resource use:

```bash
docker stats --no-stream
docker system df
```

Avoid running duplicate MySQL/PostgreSQL/Redis services both on the host and in containers unless there is a deliberate requirement.

For local-only development ports, bind to loopback:

```yaml
ports:
  - "127.0.0.1:8080:80"
```

Do not automatically prune Docker volumes. Volumes may contain databases or other irreplaceable local state.

## 16. Development runtimes

Keep the host predictable by avoiding many globally installed runtime versions.

Recommended model:

```text
Host
├── system packages
├── Git / SSH
├── IDEs
├── Docker
└── one runtime/version manager when needed

Projects
├── project-specific Node version
├── project-specific Java version
└── containerized PHP/database/services where practical
```

Use one runtime manager per purpose rather than installing several competing managers.

## 17. Firewall and listening services

Check UFW:

```bash
sudo ufw status verbose
```

Check listening sockets when auditing the workstation:

```bash
ss -lntup
```

Review the output locally before publishing it; it can reveal software and network configuration.

For a normal workstation, expose as few inbound services as possible. Do not enable SSH server, database listeners, Docker remote API or development servers on all interfaces unless remote access is intentionally required and secured.

Remember that Docker-published ports require separate attention and may not behave as expected from UFW rules alone.

## 18. Logs and failed services

Check failed systemd units:

```bash
systemctl --failed
```

Inspect serious messages from the current boot when troubleshooting:

```bash
journalctl -p 3 -b
```

Warnings/errors are not automatically performance problems. Investigate recurring messages before changing configuration.

System logs may contain usernames, device information, network details or application data. Sanitize them before posting publicly.

## 19. Disable only what is understood

Avoid generic optimization guides that recommend commands such as disabling random services, removing packages in bulk, changing kernel parameters, turning off security mitigations, disabling filesystem journaling or installing custom kernels without a demonstrated need.

Do not disable:

- CPU security mitigations;
- thermal protections;
- firewall/security controls merely for benchmark results;
- filesystem journaling;
- automatic security updates;
- services whose purpose has not been identified.

Performance tuning should solve a measured problem, not create a fragile system.

## 20. Suggested baseline for this Developer Edition

A good baseline is deliberately simple:

```text
Linux Mint Cinnamon
├── current supported updates
├── Mint-managed hardware drivers
├── Timeshift configured
├── balanced power behavior
├── periodic TRIM enabled when supported
├── UFW enabled/configured as required
├── minimal startup applications
├── Docker services stopped when unused
├── one primary browser
├── only useful IDE plugins/extensions
└── regular backups
```

## 21. Quick health check

Run these commands after setup and periodically when troubleshooting:

```bash
free -h
df -h
swapon --show
systemctl --failed
systemctl status fstrim.timer --no-pager
sudo ufw status verbose
docker stats --no-stream 2>/dev/null || true
docker system df 2>/dev/null || true
```

Optional hardware checks:

```bash
sensors
nvidia-smi
```

A healthy workstation does not need every number optimized. The target is predictable performance, low unnecessary background load, safe updates and a development environment that remains easy to reproduce.