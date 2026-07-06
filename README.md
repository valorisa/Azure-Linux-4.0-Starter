<!-- Badges -->
![GitHub last commit](https://img.shields.io/github/last-commit/valorisa/azure-linux-4.0-starter)
![GitHub issues](https://img.shields.io/github/issues/valorisa/azure-linux-4.0-starter)
![GitHub stars](https://img.shields.io/github/stars/valorisa/azure-linux-4.0-starter)
![License](https://img.shields.io/badge/license-MIT-blue)
![Status](https://img.shields.io/badge/status-public%20preview-orange)

# Azure Linux 4.0 — Starter Guide for Beginners

Welcome to the **Azure Linux 4.0 Starter Guide**, a comprehensive, beginner-friendly tutorial to install and try out Microsoft's Fedora-derived Linux distribution on your own hardware or in a local VM.

> **Preview status**: Azure Linux 4.0 was announced at Microsoft Build (June 2026) and is currently in **public preview**. Microsoft supports it officially only on Azure; running the ISO locally is community-supported, evaluation-only, not for production.

## Table of Contents
- [Why Azure Linux 4.0?](#why-azure-linux-40)
- [What is Azure Linux?](#what-is-azure-linux)
- [System Requirements](#system-requirements)
- [Installation Steps](#installation-steps)
- [Post-Installation Setup](#post-installation-setup)
- [Troubleshooting](#troubleshooting)
- [Frequently Asked Questions](#frequently-asked-questions)
- [Contributing](#contributing)
- [License](#license)

## Why Azure Linux 4.0?

More than two-thirds of Azure's compute cores already run Linux. Azure Linux 4.0 is Microsoft's own distribution — now based on Fedora, unlike earlier versions built on VMware Photon OS — optimized for cloud and server workloads, and available for the first time as a downloadable ISO for local testing outside Azure.

For beginners, this is a great opportunity to explore an enterprise-grade Linux distribution while learning system administration basics: package management with the new `dnf5` tool, SELinux, and RPM packaging.

## What is Azure Linux?

Azure Linux (formerly CBL-Mariner) is an open-source Linux distribution developed by Microsoft. Version 4.0 is a significant shift: it's built directly on a Fedora 43 snapshot, uses TOML configuration files (replacing the old `.spec` files), and ships:

- **Kernel 6.18 LTS**, tuned for Hyper-V and Azure VM performance.
- **dnf5**, a full rewrite of the package manager (replacing the older `tdnf` tool).
- **SELinux enforcing by default.**
- **systemd 258.**
- **OpenSSL 3.5** with post-quantum cryptography (Kyber, Dilithium).

> **Important**: Azure Linux is NOT a desktop operating system. It has no graphical interface. You'll need to be comfortable with the command line.

## System Requirements

| Component | Requirement |
|-----------|-------------|
| **CPU** | x86_64 or ARM64, virtualization support recommended |
| **RAM** | 4 GB recommended for the installer (preview build footprint is ~1.1 GB installed) |
| **Storage** | 16 GB+ recommended |
| **Firmware** | **UEFI required** (the installer lays down an EFI partition; legacy BIOS is not supported) |
| **Network** | Internet connection for updates |

## Installation Steps

### Step 1: Download the ISO, checksum, and signature

Microsoft publishes the 4.0 image behind short `aka.ms` links rather than on the standard GitHub Releases page:

```bash
wget -O AzureLinux-4.0-x86_64.iso https://aka.ms/azurelinux-4.0-x86_64.iso
wget https://aka.ms/azurelinux-4.0-x86_64-iso-checksum
wget https://aka.ms/azurelinux-4.0-x86_64-iso-checksum-signature
```

> The `-O` flag matters: the checksum file expects the exact filename `AzureLinux-4.0-x86_64.iso`.

### Step 2: Verify the ISO (do not skip this)

```bash
wget https://raw.githubusercontent.com/microsoft/azurelinux/refs/heads/4.0/base/comps/azurelinux-repos/RPM-GPG-KEY-azurelinux-4.0-primary
gpg --import RPM-GPG-KEY-azurelinux-4.0-primary
gpg --verify AzureLinux-4.0-x86_64-iso-checksum-signature AzureLinux-4.0-x86_64-iso-checksum
tr -d '\r' < AzureLinux-4.0-x86_64-iso-checksum | sha256sum --check -
```

The signature check should report a good signature from the Azure Linux key, and the checksum check should print `OK`. If either fails, re-download — do not boot an unverified image.

### Step 3: Create a Bootable USB (Linux/macOS) or boot a VM directly

For bare metal:

```bash
sudo dd if=AzureLinux-4.0-x86_64.iso of=/dev/sdX bs=4M status=progress
```

**Replace `/dev/sdX`** with your actual USB device. **Be careful**: this erases all data on the device.

For a local VM, you can also boot the ISO directly with QEMU/KVM (UEFI/OVMF firmware required) or Hyper-V — see Microsoft's own [ISO installer guide](https://github.com/microsoft/azurelinux/blob/4.0/docs/iso-installer-in-local-vm.md) for exact QEMU flags.

> **Secure Boot**: Azure Linux 4.0 preview ISOs are not Secure Boot signed yet. Disable Secure Boot in your VM/firmware settings before booting.

### Step 4: Follow the Anaconda Installer

Azure Linux 4.0 uses the same Anaconda text installer as Fedora/RHEL:

1. **Select language and keyboard layout.**
2. **Disk partitioning**: automatic partitioning is recommended for beginners.
3. **Set root password**: choose a strong password. **Do not forget it.**
4. **Create a user account**: add a non-root user for daily use (recommended).
5. Resolve any `[!]` warnings shown in the installer summary before continuing.

### Step 5: Reboot

Once installation completes, reboot, remove the USB (or detach the ISO from your VM), and log in with your user account.

## Post-Installation Setup

### 1. Update the system (dnf5)

```bash
sudo dnf5 update
sudo dnf5 upgrade
```

### 2. Install basic tools

Azure Linux images are stripped down by design — even `less` is absent out of the box.

```bash
sudo dnf5 install -y vim git curl wget net-tools
```

### 3. Configure SSH

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

### 4. Check SELinux status

```bash
sudo sestatus
```

SELinux is enforcing by default. Treat any `avc: denied` messages as a signal to investigate, not to disable SELinux outright.

### 5. Install Podman (Optional, for containers)

Azure Linux 4.0 is Fedora-derived, so Podman — not Docker/moby — is the native container tool:

```bash
sudo dnf5 install -y podman
sudo systemctl enable --now podman.socket
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **Network not working** | Check `ip a` and `nmcli device status`. Ensure your interface is up and DHCP is configured. |
| **SELinux blocking apps** | Check logs with `sudo ausearch -m avc -ts recent`. Temporarily set permissive mode for testing only: `sudo setenforce 0`, then re-enable with `sudo setenforce 1`. |
| **No internet after install** | Check `/etc/resolv.conf`; add `nameserver 8.8.8.8` if missing. |
| **Package not found** | Azure Linux ships two repos by default: `azurelinux-base` and `azurelinux-microsoft`. Confirm both are enabled with `dnf5 repolist`, then `sudo dnf5 makecache` before retrying. |
| **Root password forgotten** | Boot into recovery (`e` at GRUB, append `init=/bin/bash`), remount `/` read-write (`mount -o remount,rw /`), then `passwd`. |
| **Secure Boot failure at boot** | Expected on this preview — disable Secure Boot in firmware settings; the ISO isn't signed for it yet. |

## Frequently Asked Questions

**Q: Is Azure Linux 4.0 production-ready?**
A: No — it's a public preview as of mid-2026. Microsoft recommends it for evaluation and testing, with formal support and SLAs only through the Azure Marketplace.

**Q: Can I install Azure Linux on a Raspberry Pi?**
A: Not via this ISO — it targets x86_64 and ARM64 server/VM hardware, not Raspberry Pi specifically.

**Q: Is Azure Linux free?**
A: Yes, it's open source (MIT-licensed) and free to use.

**Q: Does it have a desktop environment?**
A: No. It's a server-only distribution, intentionally shipped without a GUI.

**Q: How do I uninstall it?**
A: Wipe the disk with any OS installer, or overwrite the boot sector with `dd`.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

This project is licensed under the MIT License.

**Happy exploring!** 🚀
