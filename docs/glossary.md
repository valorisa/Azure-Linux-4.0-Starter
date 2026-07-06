# Azure Linux 4.0 — Glossary

Key terms used throughout this guide.

---

## A

**ACL (Azure Container Linux)**: an immutable, container-optimized variant of Azure Linux, based on Flatcar, aimed at AKS and stricter security/compliance environments.

**Anaconda**: the text-based installer used by Azure Linux 4.0, Fedora, and RHEL.

**AKS (Azure Kubernetes Service)**: Microsoft's managed Kubernetes service.

---

## B

**Bare-metal**: a server with no virtualization layer — the OS runs directly on hardware.

---

## C

**CBL-Mariner**: the internal/earlier name for Azure Linux, before the Fedora-based 4.0 rewrite.

**Cgroup v2**: kernel control groups used to isolate and limit process resource usage.

---

## D

**dnf5**: the rewritten package manager introduced with Azure Linux 4.0, replacing the earlier `tdnf` tool. Command syntax closely mirrors classic `dnf`.

---

## E

**eBPF**: a technology for running sandboxed programs inside the Linux kernel (networking, tracing, security).

---

## F

**Fedora**: the upstream distribution Azure Linux 4.0 is built from, starting with this release (earlier versions were based on VMware Photon OS).

---

## G

**GRUB**: the boot manager used by Azure Linux.

---

## O

**OVMF / edk2**: open-source UEFI firmware used to boot the Azure Linux ISO in QEMU/KVM VMs.

---

## P

**Podman**: the native container engine on Fedora-derived systems like Azure Linux 4.0 (used instead of Docker/moby).

---

## R

**RPM**: Red Hat Package Manager — the package format used by Fedora, RHEL, and Azure Linux.

---

## S

**SELinux**: Security-Enhanced Linux — mandatory access control, enforcing by default on Azure Linux 4.0.

**systemd**: the init system and service manager (version 258 in Azure Linux 4.0).

---

## T

**TOML**: the configuration file format used to build Azure Linux 4.0 images, replacing the earlier `.spec`-based build system.

**tdnf**: the older, C-based package manager used by Azure Linux 3.0 and earlier, now replaced by `dnf5` in 4.0.

---

## U

**UEFI**: Unified Extensible Firmware Interface — required to boot the Azure Linux 4.0 installer.
