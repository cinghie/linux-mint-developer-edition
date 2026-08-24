# Linux Mint Cinnamon Installation and USB Guide

Safe installation guide for **Linux Mint 22.3 Cinnamon Edition, 64 bit**.

> **Warning:** creating a bootable USB erases the selected USB device. Installing Linux Mint with the **Erase disk** option erases the selected internal disk. Back up important data first and verify device names carefully before writing anything.

## Official download links

Use official project/vendor pages rather than third-party download portals:

- **Linux Mint official download:** https://www.linuxmint.com/download.php
- **Linux Mint official installation guide:** https://linuxmint-installation-guide.readthedocs.io/en/latest/
- **Linux Mint official ISO verification guide:** https://linuxmint-installation-guide.readthedocs.io/en/latest/verify.html
- **Linux Mint official bootable-media guide:** https://linuxmint-installation-guide.readthedocs.io/en/latest/burn.html
- **balenaEtcher official download (Windows, macOS and Linux):** https://etcher.balena.io/

For this project choose the current **Cinnamon 64-bit** image from the Linux Mint download page. Avoid hard-coding a mirror ISO URL or checksum in this repository because release filenames, mirrors and hashes change over time.

## 1. Download Linux Mint

Open the official Linux Mint download page:

https://www.linuxmint.com/download.php

Choose **Cinnamon 64-bit** and download the ISO from one of the mirrors listed by Linux Mint.

Do not download installation images from random file-hosting sites, forums, software-download aggregators or unofficial mirrors.

## 2. Verify the ISO before writing the USB

Verifying the ISO protects against both corrupted downloads and modified images.

Official instructions:

https://linuxmint-installation-guide.readthedocs.io/en/latest/verify.html

### On Linux Mint

Right-click the ISO and choose **Verify**, or run:

```bash
mint-iso-verify linuxmint.iso
```

Replace `linuxmint.iso` with the actual ISO filename.

### SHA-256 integrity check

Official Linux Mint mirrors publish `sha256sum.txt` and `sha256sum.txt.gpg` alongside the ISO.

On Linux:

```bash
sha256sum -b linuxmint.iso
```

On macOS:

```bash
shasum -a 256 linuxmint.iso
```

Compare the result with the corresponding entry in the official `sha256sum.txt` file.

If the hashes do not match, **do not use the ISO**. Delete it and download it again from an official source.

For a stronger authenticity check, follow Linux Mint's official instructions to verify the GPG signature of `sha256sum.txt`. Do not hard-code copied checksums into this repository because release files and hashes change over time.

## 3. Choose a USB stick

Use a USB stick with enough capacity for the Linux Mint ISO. A dedicated **8 GB or larger** USB drive is a practical choice.

Everything currently stored on that USB stick will be erased.

Before writing the image:

- disconnect unnecessary external disks;
- copy any files you need from the USB stick;
- identify the USB by both capacity and device name;
- never assume that `/dev/sdb`, `/dev/sdc`, etc. always refers to the USB drive.

## 4. Create the bootable USB

Linux Mint's official bootable-media instructions are available here:

https://linuxmint-installation-guide.readthedocs.io/en/latest/burn.html

### From Linux Mint

Linux Mint includes **USB Image Writer**, so no additional download is required.

1. Right-click the downloaded ISO.
2. Choose **Make Bootable USB Stick**, or open **Menu -> Accessories -> USB Image Writer**.
3. Select the ISO.
4. Select the USB device.
5. Verify the target one final time.
6. Click **Write**.
7. Wait until writing has completed before unplugging the drive.

This graphical method is preferred over raw `dd` commands in a public beginner-friendly guide because selecting the wrong block device with `dd` can destroy unrelated data.

### From Windows

Linux Mint's installation documentation recommends **balenaEtcher**.

Official download:

https://etcher.balena.io/

1. Download Etcher only from the official balena website above.
2. Start Etcher.
3. Select the verified Linux Mint ISO.
4. Select the USB stick.
5. Confirm the target capacity/device carefully.
6. Start the flash operation.
7. Wait for writing and verification to complete.

If Windows offers to format partitions on the USB after flashing, cancel that request. Windows may not recognize all Linux/ISO filesystem structures and this does not mean the USB is broken.

### From macOS

Use Etcher as documented by Linux Mint.

Official download:

https://etcher.balena.io/

1. Install Etcher from the official balena website above.
2. Select the verified ISO.
3. Select the USB stick.
4. Check the target carefully.
5. Flash the image and let verification finish.

macOS may report that the resulting disk is unreadable after imaging because it does not understand every filesystem on the Linux installer. Choose **Eject**, not **Initialize**.

### From another Linux distribution

Etcher is also available for Linux from its official site:

https://etcher.balena.io/

If the distribution already provides a trusted graphical ISO/USB writer, that can also be used. Prefer distribution-provided or official vendor software over third-party repackaging sites.

## 5. Boot the computer from USB

1. Shut down the computer.
2. Insert the Linux Mint USB stick.
3. Power on the computer.
4. Open the firmware boot menu.
5. Select the USB device, preferably its **UEFI** entry on modern systems.

Common boot-menu or firmware keys include `F2`, `F12`, `Esc`, `Delete`, `F10` and `F11`, but the correct key depends on the computer manufacturer.

### Dell systems

On many Dell computers:

- `F12` opens the one-time boot menu;
- `F2` opens BIOS/UEFI setup.

Firmware versions can differ, so use the message shown during startup or the computer's official documentation if these keys do not work.

## 6. Test the live session before installing

Start Linux Mint from the USB and spend a few minutes in the live session before changing the internal disk.

Check at least:

- keyboard and touchpad;
- Wi-Fi and Ethernet;
- audio;
- screen brightness;
- display resolution;
- USB ports;
- Bluetooth if used;
- suspend/resume where practical.

The live session runs from the USB and is slower than the installed system. Most changes made in the live session are temporary.

For systems with NVIDIA graphics, failure to obtain full acceleration in the live environment does not necessarily mean the installed system will fail. Driver Manager can install the appropriate proprietary driver after installation.

If normal boot fails because of graphics compatibility, Linux Mint provides **compatibility mode** and documented temporary boot options such as `nomodeset`. Use these only for troubleshooting, then install the recommended hardware driver after installation.

## 7. Back up Windows and important data first

Before replacing an existing Windows installation, save anything that matters to another physical device or a trusted backup destination.

Examples:

```text
Documents
Source code not already pushed
SSH keys
Password-manager database
Browser bookmarks if not synchronized
Email archives if locally stored
2FA recovery codes
Application licences
VPN configuration that is actually needed
```

Do **not** put these personal backups into this public repository.

If Windows device encryption or BitLocker is enabled, keep the recovery key somewhere safe before modifying partitions or firmware settings.

## 8. Start the Linux Mint installer

From the live desktop, open **Install Linux Mint**.

Recommended sequence:

1. choose language;
2. configure keyboard layout;
3. connect to the Internet when practical;
4. install multimedia codecs when desired;
5. choose the installation type;
6. verify the target disk carefully;
7. configure timezone;
8. create the local user account with a strong password;
9. install;
10. restart and remove the USB when requested.

## 9. Installation type

### Linux Mint only

If Linux Mint will be the only operating system and all existing data on the internal disk has already been backed up, the installer's **Erase disk and install Linux Mint** option is the simplest approach.

> Stop and re-check the selected disk before confirming. This operation destroys existing partitions and data on that disk.

### Dual boot

If Windows must remain installed, use the installer's supported **install alongside** option when it is offered and appropriate.

Do not copy partition commands from a generic Internet guide without understanding the machine's current partition table, EFI setup and encryption state.

### Manual partitioning

Use manual partitioning only when there is a concrete requirement and the partition layout is understood.

For a simple Linux-only developer workstation, automatic partitioning is usually safer and easier to maintain than an unnecessarily complex manual layout.

Linux Mint recommends `ext4` for the root filesystem.

## 10. UEFI and Secure Boot

Prefer UEFI boot mode on a machine already configured for UEFI.

Do not switch randomly between Legacy/CSM and UEFI during installation; changing firmware boot modes can make existing operating systems appear unbootable.

Linux Mint can work with Secure Boot in many configurations. If a Secure Boot-related problem occurs, follow the current Linux Mint installation documentation rather than disabling security features pre-emptively without a reason.

## 11. First boot after installation

After installation:

```bash
sudo apt update
sudo apt upgrade
```

Then use:

**Menu -> Administration -> Driver Manager**

to install recommended proprietary hardware drivers when required.

For NVIDIA systems, reboot after applying the driver and verify it with:

```bash
nvidia-smi
```

Then configure **Timeshift** before making large customizations.

## 12. Do not erase the installer immediately

Keep the bootable USB for a few days until the new system is confirmed stable. It is useful for:

- reinstalling Linux Mint;
- booting a live environment for diagnostics;
- copying files from an unbootable system;
- repairing some boot problems.

After that, the USB can be reformatted and reused normally.

## Safety principles

```text
Download Linux Mint only from the official site or listed official mirrors.
Download USB creation software only from its official vendor/project site.
Verify the ISO.
Back up important data.
Confirm the target USB before flashing.
Confirm the target internal disk before installing.
Prefer graphical imaging tools over raw disk commands for general users.
Do not publish recovery keys, credentials or personal backups.
Do not blindly copy partition or bootloader commands from another machine.
Use official Linux Mint documentation when firmware/EFI troubleshooting differs from the examples here.
```
