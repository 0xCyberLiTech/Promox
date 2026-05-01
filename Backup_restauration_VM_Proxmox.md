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

<table>
<tr>
<td align="center"><b>🖥️ Infrastructure & Sécurité</b></td>
<td align="center"><b>💻 Développement & Web</b></td>
<td align="center"><b>🤖 Intelligence Artificielle</b></td>
</tr>
<tr>
<td align="center">
  <a href="https://www.kernel.org/"><img src="https://skillicons.dev/icons?i=linux" width="48" title="Linux" /></a>
  <a href="https://www.debian.org"><img src="https://skillicons.dev/icons?i=debian" width="48" title="Debian" /></a>
  <a href="https://www.gnu.org/software/bash/"><img src="https://skillicons.dev/icons?i=bash" width="48" title="Bash" /></a>
  <br/>
  <a href="https://nginx.org"><img src="https://skillicons.dev/icons?i=nginx" width="48" title="Nginx" /></a>
  <a href="https://git-scm.com"><img src="https://skillicons.dev/icons?i=git" width="48" title="Git" /></a>
</td>
<td align="center">
  <a href="https://www.python.org"><img src="https://skillicons.dev/icons?i=python" width="48" title="Python" /></a>
  <a href="https://flask.palletsprojects.com"><img src="https://skillicons.dev/icons?i=flask" width="48" title="Flask" /></a>
  <a href="https://developer.mozilla.org/docs/Web/HTML"><img src="https://skillicons.dev/icons?i=html" width="48" title="HTML5" /></a>
  <br/>
  <a href="https://developer.mozilla.org/docs/Web/CSS"><img src="https://skillicons.dev/icons?i=css" width="48" title="CSS3" /></a>
  <a href="https://developer.mozilla.org/docs/Web/JavaScript"><img src="https://skillicons.dev/icons?i=js" width="48" title="JavaScript" /></a>
  <a href="https://code.visualstudio.com"><img src="https://skillicons.dev/icons?i=vscode" width="48" title="VS Code" /></a>
</td>
<td align="center">
  <a href="https://ollama.com"><img src="https://img.shields.io/badge/Ollama-000000?style=for-the-badge&logo=ollama&logoColor=white" alt="Ollama" /></a>
  <br/><br/>
  <a href="https://anthropic.com"><img src="https://img.shields.io/badge/Anthropic-D97757?style=for-the-badge&logo=anthropic&logoColor=white" alt="Anthropic" /></a>
</td>
</tr>
</table>

<br/>

<sub>🔒 Projets proposés par <a href="https://github.com/0xCyberLiTech">0xCyberLiTech</a> · Développés en collaboration avec <a href="https://claude.ai">Claude AI</a> (Anthropic) 🔒</sub>

</div>
