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

# Mise à jour Proxmox VE 9.1 (Trixie) — sans abonnement entreprise

Exécutez les commandes en root. Cette procédure désactive le dépôt enterprise, ajoute le dépôt "no-subscription" officiel et installe la clé de confiance.

---

## 0) - Pré-requis et précautions

- Sauvegardez la configuration et assurez-vous d'avoir un accès console local ou KVM (éviter de perdre l'accès SSH pendant la manipulation).  
- Ne supprimez pas définitivement les fichiers de sources : sauvegardez-les et renommez-les pour les désactiver.  
- Vérifiez toujours l'empreinte de la clé avant l'installation.

Empreinte recommandée : `24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E`

---

## 1) - Désactiver les sources `enterprise` (sauvegarde + rename)

```bash
# sauvegarder et désactiver (ne pas supprimer)
cp /etc/apt/sources.list.d/pve-enterprise.sources /root/pve-enterprise.sources.bak || true
mv /etc/apt/sources.list.d/pve-enterprise.sources /etc/apt/sources.list.d/pve-enterprise.sources.disabled || true

# si présent, faire de même pour ceph.sources
cp /etc/apt/sources.list.d/ceph.sources /root/ceph.sources.bak || true
mv /etc/apt/sources.list.d/ceph.sources /etc/apt/sources.list.d/ceph.sources.disabled || true
```

Remarque : conservez les fichiers `.disabled` pour revenir en arrière facilement.

---

## 2) - Importer la clé Proxmox (méthode recommandée)

Si votre serveur peut contacter les keyservers HKPS :

```bash
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E
gpg --export 24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E | gpg --dearmor -o /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
chmod 644 /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
```

Vérifier l'empreinte :

```bash
gpg --with-fingerprint /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
# ou
gpg --fingerprint 24B30F06ECC1836A4E5EFECBA7BCD1420BFE778E
```

### Fallback (si keyserver bloqué)

```bash
curl -fsSL -o /root/proxmox-release-trixie.gpg https://fr.cdn.proxmox.com/debian/proxmox-release-trixie.gpg
gpg --dearmor -o /tmp/proxmox-release-trixie.gpg.bin /root/proxmox-release-trixie.gpg
install -o root -g root -m 644 /tmp/proxmox-release-trixie.gpg.bin /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
rm -f /tmp/proxmox-release-trixie.gpg.bin /root/proxmox-release-trixie.gpg
```

---

## 3) - Créer le fichier sources `pve-no-subscription`

Créer `/etc/apt/sources.list.d/pve-no-subscription.sources` avec le contenu suivant :

```text
Types: deb
URIs: http://download.proxmox.com/debian/pve
Signed-By: /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
Suites: trixie
Components: pve-no-subscription
```

Exemple de création sûre en une commande :

```bash
cat > /etc/apt/sources.list.d/pve-no-subscription.sources <<'EOF'
Types: deb
URIs: http://download.proxmox.com/debian/pve
Signed-By: /etc/apt/trusted.gpg.d/proxmox-release-trixie.gpg
Suites: trixie
Components: pve-no-subscription
EOF
```

Remarque : vérifiez le nom de la suite (`trixie`) si vous utilisez une autre version de Debian/Proxmox.

---

## 4) - Mettre à jour les index et upgrader

```bash
apt update
# vérifier les paquets proposés avant d'exécuter l'upgrade
apt list --upgradable
# si OK
apt full-upgrade
```

Conseil : sur un serveur de production, préférez `apt upgrade` + redémarrage planifié, ou tester d'abord sur un noeud ou VM.

---

## 5) - Nettoyage / vérification

- Vérifiez que le paquet `pve-manager` vient bien du dépôt `download.proxmox.com` :

```bash
apt policy pve-manager
```

- Si vous devez revenir en arrière : restaurez `/etc/apt/sources.list.d/pve-enterprise.sources.disabled` en `.sources` et supprimez le fichier `pve-no-subscription.sources`, puis `apt update`.

---

<div align="center">
  <a href="https://github.com/0xCyberLiTech" target="_blank" rel="noopener">
    <img src="https://skillicons.dev/icons?i=linux,debian,bash,docker,nginx,git,vim,python,markdown" alt="Skills" width="440">
  </a>
</div>

<div align="center">
  <b>🔒 Un guide proposé par <a href="https://0xcyberlitech.com/">0xCyberLiTech</a> • Pour des tutoriels accessibles à tous. 🔒</b>
</div>

