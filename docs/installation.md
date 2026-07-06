# Azure Linux 4.0 — Installation Guide (Detailed)

Step-by-step guide to installing Azure Linux 4.0 in a local VM or on bare metal, using the Anaconda text installer.

> **Prerequisite**: you have downloaded, verified (checksum + GPG signature), and prepared the ISO (`AzureLinux-4.0-x86_64.iso`) as described in the main README.

---

## Firmware requirement

The installer creates an EFI partition. **Boot the media in UEFI mode.** Legacy BIOS boot is not supported. If using a VM, make sure OVMF/edk2 (or your hypervisor's UEFI firmware) is configured before attaching the ISO.

---

## Boot Screen

Boot menu options typically include:

- **Install Azure Linux 4.0**: fresh installation via Anaconda.
- **Troubleshooting**: recovery/rescue options.

Select **"Install Azure Linux 4.0"**.

> **Secure Boot**: disable it in firmware settings first — the preview ISO isn't Secure Boot signed yet.

---

## Language and Keyboard

Select your installation language, then your keyboard layout (default: English (US) if unsure).

---

## Installation Destination

Choose the target disk:

- **Automatic partitioning**: recommended for beginners — creates `/boot`, `/`, and swap automatically, typically on LVM.
- **Manual partitioning**: advanced users only.

Click **"Done"** once selected.

---

## Network Configuration (Optional)

DHCP is used by default. For a static IP, select the interface, choose **"Manual"**, and enter IP/netmask/gateway/DNS.

---

## Time Zone

Select your region and city (search by typing or use the map).

---

## Root Password

Set a strong root password. **Do not forget it** — recovery requires booting into rescue mode (see troubleshooting).

---

## User Creation (Recommended)

Create a non-root user with `sudo` privileges. Avoid using root for daily tasks.

---

## Begin Installation

Review the installation summary. Resolve any `[!]` warnings, then click **"Begin Installation"**. This typically takes a few minutes.

---

## Completion and First Boot

Once done, reboot and remove the installation media (or detach the ISO from your VM). You'll reach a login prompt:

```
azurelinux login: _
```

Log in with your non-root user account. Installation complete.
