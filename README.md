<div align="center">
  
  <br></br>
  
  <a href="https://github.com/0xCyberLiTech">
    <img src="https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&size=50&duration=6000&pause=1000000000&color=FF0048&center=true&vCenter=true&width=1100&lines=%3EPROXMOX_" alt="Titre dynamique FIREWALL" />
  </a>
  
  <br></br>

  <h2>Laboratoire numérique pour la cybersécurité, Linux & IT.</h2>

  <p align="center">
    <a href="https://0xcyberlitech.github.io/">
      <img src="https://img.shields.io/badge/Portfolio-0xCyberLiTech-181717?logo=github&style=flat-square" alt="🌐 Portfolio" />
    </a>
    <a href="https://github.com/0xCyberLiTech">
      <img src="https://img.shields.io/badge/Profil-GitHub-181717?logo=github&style=flat-square" alt="🔗 Profil GitHub" />
    </a>
    <a href="https://github.com/0xCyberLiTech/Firewall/releases/latest">
      <img src="https://img.shields.io/github/v/release/0xCyberLiTech/Firewall?label=version&style=flat-square&color=blue" alt="📦 Dernière version" />
    </a>
    <a href="https://github.com/0xCyberLiTech/Firewall/blob/main/CHANGELOG.md">
      <img src="https://img.shields.io/badge/📄%20Changelog-Firewall-blue?style=flat-square" alt="📄 CHANGELOG Firewall" />
    </a>
    <a href="https://github.com/0xCyberLiTech?tab=repositories">
      <img src="https://img.shields.io/badge/Dépôts-publics-blue?style=flat-square" alt="📂 Dépôts publics" />
    </a>
    <a href="https://github.com/0xCyberLiTech/Firewall/graphs/contributors">
      <img src="https://img.shields.io/badge/👥%20Contributeurs-cliquez%20ici-007ec6?style=flat-square" alt="👥 Contributeurs Firewall" />
    </a>
  </p>
  
</div>

<div align="center">
  <img src="https://img.icons8.com/fluency/96/000000/cyber-security.png" alt="CyberSec" width="80"/>
</div>

<div align="center">
  <p>
    <strong>Cybersécurité</strong> <img src="https://img.icons8.com/color/24/000000/lock--v1.png"/> • <strong>Linux Debian</strong> <img src="https://img.icons8.com/color/24/000000/linux.png"/> • <strong>Sécurité informatique</strong> <img src="https://img.icons8.com/color/24/000000/shield-security.png"/>
  </p>
</div>

---

<div align="center">
  
## À propos & Objectifs.

</div>

Ce projet propose des solutions innovantes et accessibles en cybersécurité, avec une approche centrée sur la simplicité d’utilisation et l’efficacité. Il vise à accompagner les utilisateurs dans la protection de leurs données et systèmes, tout en favorisant l’apprentissage et le partage des connaissances.

Le contenu est structuré, accessible et optimisé SEO pour répondre aux besoins de :
- 🎓 Étudiants : approfondir les connaissances
- 👨‍💻 Professionnels IT : outils et pratiques
- 🖥️ Administrateurs système : sécuriser l’infrastructure
- 🛡️ Experts cybersécurité : ressources techniques
- 🚀 Passionnés du numérique : explorer les bonnes pratiques

---

<div align="center" style="margin-bottom: 10px;">

### **Sommaire**

🟢 **Actif** – Dépôt totalement accessible  
🟠 **Partiel** – Dépôt partiellement accessible  
🔴 **Inactif** – Dépôt inaccessible ou indisponible

</div>

---

<div align="center">

| Projet                 | Description                                           | Accès Rapide                                                                                                                                                        |
|------------------------|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Proxmox** | Configuration. | [<img src="https://img.shields.io/badge/EXPLORER-red?style=for-the-badge&logo=github&logoColor=white">](https://github.com/0xCyberLiTech/Promox/blob/main/Proxmox_configuration.md) |
| **A venir** | A venir. | [<img src="https://img.shields.io/badge/EXPLORER-red?style=for-the-badge&logo=github&logoColor=white">]() |
| **A venir** | A venir. | [<img src="https://img.shields.io/badge/EXPLORER-red?style=for-the-badge&logo=github&logoColor=white">]() |

</div>

---

# Proxmox VE — Guide pédagogique

Ce document présente Proxmox Virtual Environment (Proxmox VE) de manière pédagogique, avec explications clés et schémas ASCII pour usage sur GitHub.

---

## Qu'est-ce que Proxmox VE ?
Proxmox VE est une plateforme open source de virtualisation pour serveurs qui combine :
- la virtualisation complète via KVM (machines virtuelles),
- les conteneurs légers via LXC,
- la gestion de stockage et réseau, les sauvegardes, la haute disponibilité (HA) et le clustering.

Proxmox fournit une interface web, une API REST et des outils en ligne de commande.

---

## Concepts clés
- **Nœud (Node)** : un serveur physique exécutant Proxmox VE.
- **Cluster** : ensemble de nœuds partageant la configuration et permettant HA et migration.
- **KVM (qm)** : hyperviseur pour machines virtuelles (VM).
- **LXC (pct)** : conteneurs légers.
- **Stockage** : local, NFS, iSCSI, Ceph (RBD), LVM, ZFS.
- **Réseau** : bridge Linux (`vmbr`), bonds, VLANs, SDN (avancé).
- **HA & Quorum** : gestion des pannes et redémarrage automatique des VM/CT sur d'autres nœuds.

---

## Schémas ASCII pédagogiques

### 1) Topologie simple — Nœud unique

```
+--------------------------------+
|   Serveur physique (Node A)    |
|  +--------------------------+  |
|  |       Proxmox VE         |  |
|  |  +-----+   +---------+   |  |
|  |  | KVM |   |  LXC    |   |  |
|  |  | VMs |   |  CTs    |   |  |
|  |  +-----+   +---------+   |  |
|  |  bridge vmbr0 -> NIC     |  |
|  |  Storage: local ZFS/LVM  |  |
|  +--------------------------+  |
+--------------------------------+
```

Explication : sur un seul serveur, Proxmox gère des VM KVM et des conteneurs LXC, connectés via un bridge vers la carte réseau et stockés sur des volumes locaux.

---

### 2) Cluster Proxmox avec stockage Ceph

```
       +----------------------------------------+
       |               Cluster VE               |
       |                                        |
+------+-------+   +------+-------+   +------+-------+
| Node A (pve) |   | Node B (pve) |   | Node C (pve) |
|  Proxmox VE  |   |  Proxmox VE  |   |  Proxmox VE  |
|  VMs & CTs   |   |  VMs & CTs   |   |  VMs & CTs   |
+------+-------+   +------+-------+   +------+-------+
       |                |                    |
       |   Ceph public  |     Ceph public    |
       +------ OSDs & MONs (RADOS) ----------+
                      |
              RBD (block devices)
                      |
              Clients via librbd

Quorum & Corosync pour la gestion du cluster et des composants HA
```

Explication : Ceph fournit du stockage distribué, accessible par tous les nœuds. Le cluster Proxmox utilise `pvecm`/Corosync pour la configuration et l'orchestration HA.

---

### 3) Réseau typique avec bridges et VLANs

```
Internet
   |
  Router
   |
+-------+        +-------------------------------------+
| Switch|--------|            Host (Node)              |
+-------+        |  +-------------------------------+  |
                 |  |     Physical NIC (eth0)       |  |
                 |  |  +-------------------------+  |  |
                 |  |  | bond0 (opt)             |  |  |
                 |  |  +-------------------------+  |  |
                 |  |  | vmbr0 (bridge)          |  |  |
                 |  |  |  - VLAN 10 -> vmbr0.10  |  |  |
                 |  |  |  - VLAN 20 -> vmbr0.20  |  |  |
                 |  |  +-------------------------+  |  |
                 |  |  | VM/CT NICs attached     |  |  |
                 |  |  +-------------------------+  |  |
                 |  +-------------------------------+  |
                 +-------------------------------------+
```

Explication : on utilise des bridges (`vmbr`) pour connecter VM/CT au réseau physique; les VLANs et bonds améliorent isolation et résilience.

---

## Commandes utiles
- Afficher la version :

```
pveversion
```

- Statut du cluster :

```
pvecm status
```

- Lister VMs :

```
qm list
```

- Lister containers :

```
pct list
```

- Sauvegarder une VM (vzdump) :

```
vzdump 101 --storage local --mode snapshot
```

- Créer un container LXC minimal (exemple) :

```
pct create 101 local:vztmpl/ubuntu-22.04-standard_22.04-1_amd64.tar.gz --ostype ubuntu --cores 2 --memory 2048
```

- Importer un disque Ceph RBD en tant que storage :

```
pveceph install
pvesm add rbd ceph-store --pool vm --monhost a.b.c.d
```

---

## Exemples rapides
- Créer une VM (ligne de commande) :

```
qm create 100 --name vm100 --memory 4096 --net0 virtio,bridge=vmbr0 --scsi0 local-lvm:32
qm importdisk 100 disk-image.qcow2 local-lvm
qm set 100 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-100-disk-0
qm start 100
```

- Migrer une VM vers un autre nœud (live migration) :

```
qm migrate 100 target-node
```

---

## Bonnes pratiques (résumé)
- Utiliser un cluster avec un minimum de 3 nœuds pour le quorum.
- Séparer le réseau de stockage (Ceph) et le réseau de VM.
- Sauvegarder régulièrement avec `vzdump` et tester les restaurations.
- Monitorer via Grafana/Prometheus ou l'interface intégrée.
- Tester la migration et le basculement HA avant production.

---

## Ressources
- Documentation officielle : https://www.proxmox.com/en/proxmox-ve
- Manuel Proxmox VE : https://pve.proxmox.com/wiki/Main_Page

---

<div align="center">
  <a href="https://github.com/0xCyberLiTech" target="_blank" rel="noopener">
    <img src="https://skillicons.dev/icons?i=linux,debian,bash,docker,nginx,git,vim,python,markdown" alt="Skills" width="440">
  </a>
</div>

<div align="center">
  <b>🔒 Un guide proposé par <a href="https://0xcyberlitech.com/">0xCyberLiTech</a> • Pour des tutoriels accessibles à tous. 🔒</b>
</div>

