# Azure Linux 4.0 — Troubleshooting Guide

Common issues and how to fix them, using `dnf5` and Azure Linux's default toolset.

---

## Network Issues

### No internet connection

```bash
nmcli device status
nmcli device connect eth0
```

For a static IP if DHCP fails:

```bash
nmcli connection modify eth0 ipv4.method manual ipv4.addresses 192.168.1.100/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8
nmcli connection up eth0
```

### DNS resolution fails

Edit `/etc/resolv.conf` and add:

```
nameserver 8.8.8.8
```

---

## SELinux Issues

### Application blocked by SELinux

```bash
sudo ausearch -m avc -ts recent
sudo setenforce 0   # temporary, testing only
```

If it works in permissive mode, generate a policy module instead of leaving SELinux disabled:

```bash
sudo grep "denied" /var/log/audit/audit.log | audit2allow -M mypolicy
sudo semodule -i mypolicy.pp
sudo setenforce 1
```

### Check status

```bash
sudo sestatus
```

---

## Package Management (dnf5) Issues

### Missing or broken dependencies

```bash
sudo dnf5 clean all
sudo dnf5 makecache
```

### Package not found

Azure Linux ships two default repos: `azurelinux-base` and `azurelinux-microsoft`. Confirm both are enabled:

```bash
dnf5 repolist
```

If a package genuinely isn't packaged for Azure Linux yet (it's a young, minimal distro), check whether it's available as a Fedora COPR or build it from source — there's no EPEL-equivalent repo to fall back on here.

---

## Boot Issues

### System boots to a black screen

```bash
journalctl -b -p 3
systemctl status network
```

Switch TTY with `Ctrl+Alt+F2` if the console appears stuck.

### Root password forgotten

1. Reboot, press `e` at the GRUB menu.
2. Append `init=/bin/bash` to the kernel line.
3. `Ctrl+X` to boot.
4. `mount -o remount,rw /`
5. `passwd`
6. Reboot.

### Secure Boot failure

Expected on this preview build — the ISO isn't Secure Boot signed yet. Disable Secure Boot in firmware/VM settings.

---

## SSH Issues

### Cannot SSH into the server

```bash
sudo systemctl status sshd
sudo systemctl enable sshd
sudo systemctl start sshd
```

If a firewall is configured:

```bash
sudo firewall-cmd --list-all
sudo firewall-cmd --add-service=ssh --permanent
sudo firewall-cmd --reload
```
