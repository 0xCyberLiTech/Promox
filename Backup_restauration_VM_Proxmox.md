<div align="center">
  
  <br></br>
  
  <a href="https://github.com/0xCyberLiTech">
    <img src="https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&size=50&duration=6000&pause=1000000000&color=FF0048&center=true&vCenter=true&width=1100&lines=%3EPROXMOX_" alt="Titre dynamique PROXMOX" />
  </a>
  
  <br></br>

  <h2>Laboratoire numérique — Cybersécurité, Linux & administration.</h2>

  <p align="center">
    <a href="https://0xcyberlitech.github.io/">
      <img src="https://img.shields.io/badge/Portfolio-0xCyberLiTech-181717?logo=github&style=flat-square" alt="Portfolio" />
    </a>
    <a href="https://github.com/0xCyberLiTech">
      <img src="https://img.shields.io/badge/Profil-GitHub-181717?logo=github&style=flat-square" alt="Profil GitHub" />
    </a>
    <a href="https://github.com/0xCyberLiTech/Firewall/releases/latest">
      <img src="https://img.shields.io/github/v/release/0xCyberLiTech/Firewall?label=version&style=flat-square&color=blue" alt="Dernière version" />
    </a>
  </p>
  
</div>

<div align="center">
  <img src="https://img.icons8.com/fluency/96/000000/cyber-security.png" alt="CyberSec" width="80"/>
</div>

---

<div align="center">
## À propos & objectifs
</div>

Ce document explique, étape par étape et de façon pédagogique, comment sauvegarder et restaurer des machines virtuelles (QEMU) et conteneurs (LXC) sur Proxmox VE. Les explications privilégient la sécurité, la simplicité et les bonnes pratiques.

Public visé : étudiants, administrateurs systèmes, professionnels IT et passionnés.

---

# Sauvegarde et restauration Proxmox — Guide pas à pas

**Objectif :** fournir une procédure claire et testable pour sauvegarder et restaurer des VM et conteneurs.

**Prérequis :**
- Accès root sur le nœud Proxmox.
- Proxmox VE installé avec `pve-manager`, `pvesm` et `vzdump`.
- Espace disque suffisant sur le stockage de sauvegarde.
- Pour snapshots cohérents : installez `qemu-guest-agent` dans les VM.

---

## 1) Préparer le stockage

1. Créer un répertoire local pour les sauvegardes :

```bash
sudo mkdir -p /backup
sudo chown root:root /backup
sudo chmod 750 /backup
```

2. Ajouter le répertoire comme storage dans Proxmox :

```bash
pvesm add dir backup --path /backup --content backup --nodes pve --maxfiles 7
```

3. Exemples pour autres types :

```bash
# NFS
pvesm add nfs backup-nfs --server 10.0.0.5 --export /exports/backups --path /mnt/backup-nfs --content backup --maxfiles 14

# LVM Thin
pvesm add lvmthin local-thin --vgname vg_backup --thinpool data --content images,backup
```

Conseils rapides :
- Pour stockage distant, utilisez `pvesm add nfs|cifs` ou montez via `/etc/fstab`.
- Évitez `local-lvm` pour des sauvegardes critiques si le nœud peut être perdu.

---

## 2) Modes et options de `vzdump`

- Modes :
  - `snapshot` : recommandé (si le stockage le supporte).
  - `suspend` : met la VM en pause pendant la sauvegarde.
  - `stop` : arrête la VM (plus sûr, interruption de service).

- Compression : privilégiez `zstd` (bon compromis). Exemple : `--compress zstd`.
- Options utiles : `--dumpdir`, `--exclude-path`, `--ionice 3`, `--mailto adresse@ex`.

Exemples :

```bash
# Sauvegarde d'une VM (ID 101)
vzdump 101 --mode snapshot --compress zstd --storage backup --ionice 3 --mailto mm.sab8572@outlook.fr

# Sauvegarde de toutes les VMs du nœud
vzdump --all --mode snapshot --compress zstd --storage backup --ionice 3

# Sauvegarde directe dans /backup (sans storage Proxmox)
vzdump 101 --mode snapshot --compress zstd --dumpdir /backup
```

---

## 3) Planification (cron ou GUI)

Cron (exemple quotidien à 02:00) :

```bash
# /etc/cron.d/vzdump-backup
0 2 * * * root vzdump --all --mode snapshot --compress zstd --storage backup --mailto mm.sab8572@outlook.fr

sudo chmod 644 /etc/cron.d/vzdump-backup
sudo systemctl restart cron.service
```

GUI : Datacenter → Backup → Add. Paramètres clés : Nodes, Selection mode (All), Storage, Schedule, Mode (`snapshot`), Compression (`zstd`), Max backups.

---

## 4) Rotation et rétention

- Si vous utilisez un storage Proxmox (`pvesm add dir`), `--maxfiles` gère la suppression automatique.
- Si vous utilisez `--dumpdir`, ajoutez un script de rotation :

```bash
find /backup -type f -mtime +14 -delete
```

---

## 5) Vérifications après sauvegarde

- Lister les fichiers : `ls -lh /backup`
- Lister le storage Proxmox : `pvesm list backup`
- Consulter les logs : `ls -l /var/log/vzdump/` puis `tail -n 200 /var/log/vzdump/*.log`
- Vérifier l'espace disque : `df -h /backup` et `du -sh /backup/* | sort -h`

Astuce : ouvrez régulièrement les logs `vzdump` pour détecter les erreurs précoces.

---

## 6) Restauration — étapes et bonnes pratiques

Restaurer une VM QEMU (adapter le chemin/fichier) :

```bash
# Restaurer le backup vers la VMID 201
qmrestore /backup/vzdump-qemu-101-2026_02_07-02_00_00.vma.zst 201 --storage local-lvm
```

Restaurer un conteneur LXC :

```bash
pct restore 202 /backup/vzdump-lxc-102-2026_02_07-02_00_00.tar.gz
```

Bonnes pratiques :
- Restaurer d'abord sur un VMID/CTID de test.
- Vérifier les services, la connectivité réseau et les fichiers de configuration après restauration.
- Utiliser `--storage` pour choisir où placer les disques restaurés et `--force` si nécessaire.

---

## 7) Dépannage courant

- Sauvegarde échouée : consulter `/var/log/vzdump/*.log` et `journalctl -u pve-cluster`.
- Cohérence douteuse : installez `qemu-guest-agent` dans la VM.
- Cron non exécuté : vérifier permissions et logs système.
- Problème d'envoi mail : tester votre MTA (`postfix`, `msmtp`, `sendmail`).

Test rapide d'envoi mail :

```bash
echo "Test email" | mail -s "Test backup" mm.sab8572@outlook.fr
```

---

## 8) Sécurité et bonnes pratiques

- Restreindre l'accès au répertoire de sauvegarde (root, chmod 750).
- Chiffrer les sauvegardes hors-site (rsync -> destination chiffrée) ou utiliser Proxmox Backup Server (PBS) pour chiffrement natif.
- Conserver au moins une copie hors-site.

---

## 9) Option recommandée : Proxmox Backup Server (PBS)

PBS offre : déduplication, chiffrement, gestion fine des rétentions et intégration native avec Proxmox VE — recommandé pour les environnements de production.

---

## Résumé rapide

- Préparez le stockage.
- Privilégiez `snapshot` si possible.
- Automatisez via cron ou GUI.
- Testez la restauration sur des IDs de test.
- Surveillez l'espace et les logs.

---

<div align="center">
  <a href="https://github.com/0xCyberLiTech" target="_blank" rel="noopener">
    <img src="https://skillicons.dev/icons?i=linux,debian,bash,docker,nginx,git,vim,python,markdown" alt="Skills" width="440">
  </a>
</div>

<div align="center">
  <b>Guide proposé par <a href="https://0xcyberlitech.com/">0xCyberLiTech</a> — tutoriels accessibles et pratiques.</b>
</div>

