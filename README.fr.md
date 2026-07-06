<!-- Badges -->
![Dernier commit](https://img.shields.io/github/last-commit/valorisa/azure-linux-4.0-starter)
![Problèmes](https://img.shields.io/github/issues/valorisa/azure-linux-4.0-starter)
![Étoiles](https://img.shields.io/github/stars/valorisa/azure-linux-4.0-starter)
![Licence](https://img.shields.io/badge/licence-MIT-blue)
![Statut](https://img.shields.io/badge/statut-preview%20publique-orange)

# Azure Linux 4.0 — Guide de démarrage pour débutants

Bienvenue dans le **Guide de démarrage Azure Linux 4.0**, un tutoriel complet et accessible aux débutants pour installer et tester la distribution Linux de Microsoft (dérivée de Fedora) sur votre propre matériel ou en VM locale.

> **Statut preview** : Azure Linux 4.0 a été annoncé à Microsoft Build (juin 2026) et est actuellement en **preview publique**. Microsoft ne le supporte officiellement que sur Azure ; l'utiliser en local (ISO) relève d'un support communautaire, réservé à l'évaluation, pas à la production.

## Table des matières
- [Pourquoi Azure Linux 4.0 ?](#pourquoi-azure-linux-40)
- [Qu'est-ce qu'Azure Linux ?](#quest-ce-quazure-linux)
- [Configuration requise](#configuration-requise)
- [Étapes d'installation](#étapes-dinstallation)
- [Configuration post-installation](#configuration-post-installation)
- [Dépannage](#dépannage)
- [Foire aux questions](#foire-aux-questions)
- [Contribuer](#contribuer)
- [Licence](#licence)

## Pourquoi Azure Linux 4.0 ?

Plus des deux tiers des cœurs de calcul Azure tournent déjà sous Linux. Azure Linux 4.0 est la distribution maison de Microsoft — désormais basée sur Fedora, contrairement aux versions précédentes issues de VMware Photon OS — optimisée pour le cloud et les serveurs, et disponible pour la première fois en ISO téléchargeable pour un test local hors Azure.

Pour les débutants, c'est l'occasion d'explorer une distribution de niveau entreprise tout en apprenant les bases de l'administration système : gestion des paquets avec le nouvel outil `dnf5`, SELinux, et le packaging RPM.

## Qu'est-ce qu'Azure Linux ?

Azure Linux (anciennement CBL-Mariner) est une distribution Linux open source développée par Microsoft. La version 4.0 marque un tournant : elle est construite directement sur un instantané de Fedora 43, utilise des fichiers de configuration TOML (remplaçant les anciens fichiers `.spec`), et embarque :

- **Noyau 6.18 LTS**, optimisé pour Hyper-V et les performances des VM Azure.
- **dnf5**, une réécriture complète du gestionnaire de paquets (remplaçant l'ancien outil `tdnf`).
- **SELinux en mode enforcing par défaut.**
- **systemd 258.**
- **OpenSSL 3.5** avec cryptographie post-quantique (Kyber, Dilithium).

> **Important** : Azure Linux n'est PAS un système d'exploitation de bureau. Il n'a pas d'interface graphique. Vous devez être à l'aise avec la ligne de commande.

## Configuration requise

| Composant | Exigence |
|-----------|----------|
| **Processeur** | x86_64 ou ARM64, support de virtualisation recommandé |
| **RAM** | 4 Go recommandés pour l'installateur (l'empreinte de la preview installée est d'environ 1,1 Go) |
| **Stockage** | 16 Go+ recommandés |
| **Firmware** | **UEFI requis** (l'installateur crée une partition EFI ; le BIOS legacy n'est pas supporté) |
| **Réseau** | Connexion Internet pour les mises à jour |

## Étapes d'installation

### Étape 1 : Télécharger l'ISO, la somme de contrôle et la signature

Microsoft publie l'image 4.0 derrière des liens courts `aka.ms` plutôt que sur la page GitHub Releases classique :

```bash
wget -O AzureLinux-4.0-x86_64.iso https://aka.ms/azurelinux-4.0-x86_64.iso
wget https://aka.ms/azurelinux-4.0-x86_64-iso-checksum
wget https://aka.ms/azurelinux-4.0-x86_64-iso-checksum-signature
```

> Le paramètre `-O` est important : le fichier de somme de contrôle référence exactement le nom `AzureLinux-4.0-x86_64.iso`.

### Étape 2 : Vérifier l'ISO (ne sautez pas cette étape)

```bash
wget https://raw.githubusercontent.com/microsoft/azurelinux/refs/heads/4.0/base/comps/azurelinux-repos/RPM-GPG-KEY-azurelinux-4.0-primary
gpg --import RPM-GPG-KEY-azurelinux-4.0-primary
gpg --verify AzureLinux-4.0-x86_64-iso-checksum-signature AzureLinux-4.0-x86_64-iso-checksum
tr -d '\r' < AzureLinux-4.0-x86_64-iso-checksum | sha256sum --check -
```

La vérification de signature doit indiquer une signature valide de la clé Azure Linux, et la vérification de somme de contrôle doit afficher `OK`. En cas d'échec, retéléchargez — ne démarrez jamais une image non vérifiée.

### Étape 3 : Créer une clé USB bootable (Linux/macOS) ou démarrer une VM directement

Pour du matériel physique :

```bash
sudo dd if=AzureLinux-4.0-x86_64.iso of=/dev/sdX bs=4M status=progress
```

**Remplacez `/dev/sdX`** par votre périphérique USB réel. **Attention** : cela efface toutes les données du périphérique.

Pour une VM locale, vous pouvez aussi démarrer l'ISO directement avec QEMU/KVM (firmware UEFI/OVMF requis) ou Hyper-V — voir le [guide officiel d'installation ISO en VM locale](https://github.com/microsoft/azurelinux/blob/4.0/docs/iso-installer-in-local-vm.md) de Microsoft pour les paramètres QEMU exacts.

> **Secure Boot** : les ISO de la preview Azure Linux 4.0 ne sont pas encore signées Secure Boot. Désactivez Secure Boot dans les paramètres de votre VM/firmware avant de démarrer.

### Étape 4 : Suivre l'installateur Anaconda

Azure Linux 4.0 utilise le même installateur texte Anaconda que Fedora/RHEL :

1. **Sélectionnez la langue et la disposition du clavier.**
2. **Partitionnement du disque** : le partitionnement automatique est recommandé pour les débutants.
3. **Définir le mot de passe root** : choisissez un mot de passe fort. **Ne l'oubliez pas.**
4. **Créer un compte utilisateur** : ajoutez un utilisateur non-root pour l'usage quotidien (recommandé).
5. Résolvez tous les avertissements `[!]` affichés dans le résumé de l'installateur avant de continuer.

### Étape 5 : Redémarrer

Une fois l'installation terminée, redémarrez, retirez la clé USB (ou détachez l'ISO de votre VM), et connectez-vous avec votre compte utilisateur.

## Configuration post-installation

### 1. Mettre à jour le système (dnf5)

```bash
sudo dnf5 update
sudo dnf5 upgrade
```

### 2. Installer les outils de base

Les images Azure Linux sont volontairement minimalistes — même `less` est absent par défaut.

```bash
sudo dnf5 install -y vim git curl wget net-tools
```

### 3. Configurer SSH

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

### 4. Vérifier l'état de SELinux

```bash
sudo sestatus
```

SELinux est en mode enforcing par défaut. Considérez tout message `avc: denied` comme un signal à investiguer, pas comme une raison de désactiver SELinux directement.

### 5. Installer Podman (optionnel, pour les conteneurs)

Azure Linux 4.0 étant dérivé de Fedora, Podman — et non Docker/moby — est l'outil de conteneurisation natif :

```bash
sudo dnf5 install -y podman
sudo systemctl enable --now podman.socket
```

## Dépannage

| Problème | Solution |
|----------|----------|
| **Le réseau ne fonctionne pas** | Vérifiez `ip a` et `nmcli device status`. Assurez-vous que votre interface est active et que le DHCP est configuré. |
| **SELinux bloque des applications** | Consultez les logs avec `sudo ausearch -m avc -ts recent`. Passez temporairement en mode permissif pour tester : `sudo setenforce 0`, puis réactivez avec `sudo setenforce 1`. |
| **Pas d'Internet après installation** | Vérifiez `/etc/resolv.conf` ; ajoutez `nameserver 8.8.8.8` s'il manque. |
| **Paquet introuvable** | Azure Linux embarque deux dépôts par défaut : `azurelinux-base` et `azurelinux-microsoft`. Vérifiez qu'ils sont actifs avec `dnf5 repolist`, puis `sudo dnf5 makecache` avant de réessayer. |
| **Mot de passe root oublié** | Démarrez en mode récupération (`e` au menu GRUB, ajoutez `init=/bin/bash`), remontez `/` en lecture-écriture (`mount -o remount,rw /`), puis `passwd`. |
| **Échec au démarrage à cause de Secure Boot** | Normal sur cette preview — désactivez Secure Boot dans les paramètres du firmware ; l'ISO n'est pas encore signée pour ça. |

## Foire aux questions

**Q : Azure Linux 4.0 est-il prêt pour la production ?**
R : Non — c'est une preview publique depuis mi-2026. Microsoft la recommande pour l'évaluation et les tests, avec un support formel et des SLA uniquement via l'Azure Marketplace.

**Q : Puis-je installer Azure Linux sur un Raspberry Pi ?**
R : Pas via cet ISO — il cible le matériel serveur/VM x86_64 et ARM64, pas spécifiquement le Raspberry Pi.

**Q : Azure Linux est-il gratuit ?**
R : Oui, il est open source (licence MIT) et gratuit.

**Q : A-t-il un environnement de bureau ?**
R : Non. C'est une distribution serveur uniquement, volontairement livrée sans interface graphique.

**Q : Comment le désinstaller ?**
R : Effacez le disque avec n'importe quel installateur d'OS, ou écrasez le secteur de démarrage avec `dd`.

## Contribuer

Les contributions sont les bienvenues ! Veuillez ouvrir un ticket (*issue*) ou soumettre une demande de fusion (*pull request*).

## Licence

Ce projet est sous licence MIT.

**Bonne exploration !** 🚀
