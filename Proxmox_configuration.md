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
  
# Proxmox - Donner un accès Internet aux VMs sur le réseau interne vmbr1 en 10.100.100.0/24 alors que la sortie Internet est via vmbr0.

</div>

## Résumé rapide

- Vérifier / ajouter `vmbr1` sur l'hôte.  
- Activer le forwarding IPv4 (runtime + persistant).  
- Ajouter règles NAT (MASQUERADE) et règles FORWARD.  
- Sauvegarder les règles (netfilter-persistent ou `iptables-save`).

---

## 1) Exemple /etc/network/interfaces (sur l'hôte Proxmox)

Ajouter IP pour `vmbr1` :

```ini
auto vmbr1
iface vmbr1 inet static
    address 10.100.100.1/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0
```

---

## 2) Activer le bridge et le routage + règles NAT (exécuter sur l'hôte Proxmox)

Exemples de commandes :

# activer vmbr1 (si nécessaire).

```bash
ifreload -a   # ou `ifup vmbr1`
```
# activer forwarding IPv4 immédiatement.

```bash
sysctl -w net.ipv4.ip_forward=1
```

# rendre persistant.

```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
```

```bash
sysctl -p
```
# ajouter NAT/forwarding (adapter si vmbr0 a un autre nom).

```bash
iptables -t nat -A POSTROUTING -s 10.100.100.0/24 -o vmbr0 -j MASQUERADE
```

```bash
iptables -A FORWARD -i vmbr0 -o vmbr1 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

```bash
iptables -A FORWARD -i vmbr1 -o vmbr0 -j ACCEPT
```

# sauvegarder les règles (Debian/Proxmox).

```bash
apt update
```

```bash
apt install -y iptables-persistent
```

```bash
netfilter-persistent save
```

---

## 3) Configuration des VMs branchées sur vmbr1

- IP statique exemple : `10.100.100.10/24`  
- Gateway : `10.100.100.1`  
- DNS : `1.1.1.1`, `1.0.0.1` (ou votre DNS)  
Ou via DHCP si vous installez un serveur DHCP sur `vmbr1`.

---

## 4) Remarques importantes

- Si Proxmox utilise le firewall `pve-firewall`, autorisez le forwarding/NAT ou gérez via l’interface web (Datacenter → Firewall).  
- Pour plus de contrôle (DHCP, règles avancées), créez une VM-routeur (pfSense/Debian) avec 1 NIC sur `vmbr1` et 1 NIC sur `vmbr0`.  
- Pour persistance et modernité, vous pouvez aussi utiliser `nftables` à la place d'`iptables`.

Suggestion : Génère un `interfaces` complet prêt à coller + script d'installation automatique des règles.

Exemple : d’un fichier /etc/network/interfaces prêt à être copié et collé.

### /etc/network/interfaces (extrait pertinent)

```ini
auto lo
iface lo inet loopback

# vmbr0 - gestion / accès Proxmox (déjà configuré par vous)
auto vmbr0
iface vmbr0 inet static
    address 10.129.1.20/24
    gateway 10.129.1.1
    bridge-ports nic2
    bridge-stp off
    bridge-fd 0

# vmbr1 - réseau interne pour VMs (10.100.100.0/24)
auto vmbr1
iface vmbr1 inet static
    address 10.100.100.1/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0
```

## Script d'installation automatique (setup_vmbr1_nat.sh)

Sauvegarde le fichier `interfaces`, ajoute `vmbr1` si absent, active le forwarding, ajoute les règles `iptables`, installe `iptables-persistent` et sauvegarde les règles.

```bash
#!/usr/bin/env bash
set -e

# Variables - adaptez si nécessaire
INTERNAL_NET="10.100.100.0/24"
INTERNAL_GW="10.100.100.1/24"
BR_INTERNAL="vmbr1"
BR_EXTERNAL="vmbr0"
INTERFACES_FILE="/etc/network/interfaces"
BACKUP_DIR="/root/interfaces-backups"
SYSCTL_CONF="/etc/sysctl.d/99-proxmox-vmbr1.conf"

if [ "$(id -u)" -ne 0 ]; then
  echo "Exécutez en root." >&2
  exit 1
fi

mkdir -p "$BACKUP_DIR"
cp -a "$INTERFACES_FILE" "$BACKUP_DIR/interfaces.$(date +%s)"

# Ajouter vmbr1 si absent
if ! grep -q "iface ${BR_INTERNAL} inet" "$INTERFACES_FILE"; then
  cat >> "$INTERFACES_FILE" <<EOF

# vmbr1 - réseau interne pour VMs (ajouté par script)
auto ${BR_INTERNAL}
iface ${BR_INTERNAL} inet static
    address ${INTERNAL_GW}
    bridge-ports none
    bridge-stp off
    bridge-fd 0

EOF
  echo "Section ${BR_INTERNAL} ajoutée dans ${INTERFACES_FILE}."
else
  echo "Section ${BR_INTERNAL} déjà présente dans ${INTERFACES_FILE}, aucune modification."
fi

# Appliquer la configuration réseau pour vmbr1
if command -v ifreload >/dev/null 2>&1; then
  ifreload -a || true
else
  # tenter d'activer uniquement vmbr1
  ifdown ${BR_INTERNAL} 2>/dev/null || true
  ifup ${BR_INTERNAL} || true
fi

# Activer forwarding IPv4 (runtime + persistant via /etc/sysctl.d)
sysctl -w net.ipv4.ip_forward=1
cat > "${SYSCTL_CONF}" <<EOF
# Activation du forwarding pour VMs sur ${BR_INTERNAL}
net.ipv4.ip_forward=1
EOF
sysctl --system

# Ajouter règles NAT/forwarding (idempotent simple)
iptables -t nat -C POSTROUTING -s "${INTERNAL_NET}" -o "${BR_EXTERNAL}" -j MASQUERADE 2>/dev/null || \
iptables -t nat -A POSTROUTING -s "${INTERNAL_NET}" -o "${BR_EXTERNAL}" -j MASQUERADE

iptables -C FORWARD -i "${BR_EXTERNAL}" -o "${BR_INTERNAL}" -m state --state RELATED,ESTABLISHED -j ACCEPT 2>/dev/null || \
iptables -A FORWARD -i "${BR_EXTERNAL}" -o "${BR_INTERNAL}" -m state --state RELATED,ESTABLISHED -j ACCEPT

iptables -C FORWARD -i "${BR_INTERNAL}" -o "${BR_EXTERNAL}" -j ACCEPT 2>/dev/null || \
iptables -A FORWARD -i "${BR_INTERNAL}" -o "${BR_EXTERNAL}" -j ACCEPT

# Installer iptables-persistent pour persister les règles (non interactif)
export DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get install -y iptables-persistent netfilter-persistent || true

# Sauvegarder règles (compatibilité iptables-persistent)
if command -v netfilter-persistent >/dev/null 2>&1; then
  netfilter-persistent save
else
  mkdir -p /etc/iptables
  iptables-save > /etc/iptables/rules.v4
fi

echo "Configuration terminée."
echo " - Bridge interne: ${BR_INTERNAL} (${INTERNAL_GW})"
echo " - Réseau interne: ${INTERNAL_NET}"
echo " - NAT via: ${BR_EXTERNAL}"
echo "Vérifiez les VMs: IP exemples 10.100.100.10/24, gateway 10.100.100.1, DNS 8.8.8.8"
```

---

## Commande pour déployer

Copier le script sur l'hôte Proxmox et exécuter :

```bash
scp setup_vmbr1_nat.sh root@proxmox-host:/root/
```
```bash
ssh root@proxmox-host 'chmod +x /root/setup_vmbr1_nat.sh && /root/setup_vmbr1_nat.sh'
```

Ou exécuter localement :

```bash
sudo chmod +x setup_vmbr1_nat.sh
```
```bash
./setup_vmbr1_nat.sh
```

## Notes & précautions

- Le script sauvegarde `/etc/network/interfaces` avant modification.  
- Si vous administrez Proxmox à distance via `vmbr0`, ne modifiez pas la section `vmbr0` sauf si vous savez ce que vous faites.  
- Testez d'abord sur une console locale si possible (pour éviter verrouillage SSH).  
- Pour une solution plus propre et plus flexible (DHCP, firewall partagé, haute disponibilité), préférez une VM-routeur (pfSense/Debian) avec 2 interfaces (une sur `vmbr1`, une sur `vmbr0`).

---

## Tâche de rollback

Le script précédent sauvegarde `/etc/network/interfaces` avant modification, donc on peut revenir en arrière. Voici un script de rollback simple et sûr.

### rollback_vmbr1.sh

```bash
#!/usr/bin/env bash
set -e

BACKUP_DIR="/root/interfaces-backups"
INTERFACES_FILE="/etc/network/interfaces"
SYSCTL_CONF="/etc/sysctl.d/99-proxmox-vmbr1.conf"
BR_INTERNAL="vmbr1"
BR_EXTERNAL="vmbr0"
INTERNAL_NET="10.100.100.0/24"

if [ "$(id -u)" -ne 0 ]; then
  echo "Exécutez en root." >&2
  exit 1
fi

# 1) Restaurer la dernière sauvegarde de /etc/network/interfaces si disponible
if [ -d "$BACKUP_DIR" ] && ls "$BACKUP_DIR"/interfaces.* >/dev/null 2>&1; then
  LATEST_BACKUP=$(ls -1 "$BACKUP_DIR"/interfaces.* | sort -n | tail -1)
  echo "Restauration de $LATEST_BACKUP -> $INTERFACES_FILE"
  cp -a "$LATEST_BACKUP" "$INTERFACES_FILE"
else
  echo "Aucune sauvegarde trouvée dans $BACKUP_DIR, saut de la restauration du fichier interfaces."
fi

# 2) Appliquer/recharger la configuration réseau (essayer en sécurité)
if command -v ifreload >/dev/null 2>&1; then
  echo "Rechargement des interfaces via ifreload -a"
  ifreload -a || true
else
  echo "Tentative de ifdown/ifup pour ${BR_INTERNAL}"
  ifdown ${BR_INTERNAL} 2>/dev/null || true
  ifup ${BR_INTERNAL} 2>/dev/null || true
fi

# 3) Supprimer les règles iptables ajoutées (si présentes)
echo "Suppression des règles NAT/FORWARD pour ${INTERNAL_NET} via ${BR_EXTERNAL}..."
while iptables -t nat -C POSTROUTING -s "${INTERNAL_NET}" -o "${BR_EXTERNAL}" -j MASQUERADE 2>/dev/null; do
  iptables -t nat -D POSTROUTING -s "${INTERNAL_NET}" -o "${BR_EXTERNAL}" -j MASQUERADE || true
done

while iptables -C FORWARD -i "${BR_EXTERNAL}" -o "${BR_INTERNAL}" -m state --state RELATED,ESTABLISHED -j ACCEPT 2>/dev/null; do
  iptables -D FORWARD -i "${BR_EXTERNAL}" -o "${BR_INTERNAL}" -m state --state RELATED,ESTABLISHED -j ACCEPT || true
done

while iptables -C FORWARD -i "${BR_INTERNAL}" -o "${BR_EXTERNAL}" -j ACCEPT 2>/dev/null; do
  iptables -D FORWARD -i "${BR_INTERNAL}" -o "${BR_EXTERNAL}" -j ACCEPT || true
done

# 4) Retirer le fichier de persistance du forwarding et appliquer
if [ -f "$SYSCTL_CONF" ]; then
  echo "Suppression de $SYSCTL_CONF"
  rm -f "$SYSCTL_CONF"
  sysctl --system || true
else
  echo "Aucun fichier $SYSCTL_CONF trouvé."
fi

# 5) Sauvegarder l'état iptables actuel (persist)
if command -v netfilter-persistent >/dev/null 2>&1; then
  netfilter-persistent save || true
else
  mkdir -p /etc/iptables
  iptables-save > /etc/iptables/rules.v4 || true
fi

echo "Rollback terminé."
echo " - Vérifiez /etc/network/interfaces et la connectivité."
echo " - Si nécessaire, restaurez manuellement une sauvegarde spécifique depuis $BACKUP_DIR."
```

---

## Recommandations courtes et commandes à exécuter sur l’hôte Proxmox

- Emplacement pour usage unique / admin root : `/root/proxmox-scripts`.  
- Emplacement pour usage système / multi-admin : `/usr/local/sbin`.  
- Nommer clairement : `setup_vmbr1_nat.sh` et `rollback_vmbr1.sh`.  
- Rendre exécutables et conserver une sauvegarde des originaux (ou un repo git simple).

# créer un dossier central et y copier les scripts

```bash
mkdir -p /root/proxmox-scripts
```
```bash
cp ./setup_vmbr1_nat.sh ./rollback_vmbr1.sh /root/proxmox-scripts/
```

# rendre exécutables

```bash
chmod +x /root/proxmox-scripts/setup_vmbr1_nat.sh
```
```bash
chmod +x /root/proxmox-scripts/rollback_vmbr1.sh
```

# exécution (exemple)

```bash
sudo /root/proxmox-scripts/setup_vmbr1_nat.sh
```
# rollback

```bash
sudo /root/proxmox-scripts/rollback_vmbr1.sh
```

---

## Schéma ASCII (Option NAT sur l'hôte)

```text
                                + <-------------------------------------------->  Box  <---------------------------------> Internet 
                                |                                                 IP 192.168.1.1
                                |                                                 MASK 255.255.255.0 (24)
                                |                                                 DNS 1.1.1.1 1.0.0.1
                                |                                                 DHGCP 192.168.1.100 192.168.1.150
                                |                                                 DMZ 192.168.1.20
                         nic2 (physique) 
                                |
                                |
                                |
                              vmbr0
                                |
                          auto vmbr0                 
                          iface vmbr0 inet static
                            address 192.168.1.20/24
                            gateway 192.168.1.1
                            bridge-ports nic2
                            bridge-stp off
                            bridge-fd 0          
                                |
         +--------------------vmbr0---------------------+
         |               sortie Internet                |
         |                                              |
         |                                              |  
         |                                              |
         |                                              |
    Proxmox Host                                     (admin)  -- iptables MASQUERADE/NAT --> vmbr0
         |                                              |
         |                                              |
         |         table de routage Proxmox VE          |

Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 vmbr0
10.100.100.0    0.0.0.0         255.255.255.0   U     0      0        0 vmbr1
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 vmbr0
         
         +----------------------------------------------+
         |    Host IP: 1192.168.1.20/24 (vmbr0)         |
         |    Internal bridge: vmbr1 -> 10.100.100.1    |
         +----------------------------------------------+         
                      |                                           auto vmbr1
                      |                                           iface vmbr1 inet static
                      |                                             address 10.100.100.1/24
                      |                                             bridge-ports nic1
                      |                                             bridge-stp off           
                      +------------------------------> vmbr1 <----> bridge-fd 0 <----> vmbr1 <----------> nic1 <----------------->  PC-01
                      |                                                                                                             IP 10.100.100.13
                      |                                                                                                             MASK 255.255.255.0 (24)
                      |                                                                                                             GW 10.100.100.1
                      |                                                                                                             DNS1 1.1.1.1
                      |                                                                                                             DNS2 1.0.0.1 
         +-------------------------------+--------------------------------+                                                                                                 
         |                               |                                |
       VM-01                           VM-02                            VM-03                                      
         |                               |                                | 
       ens18                           ens18                            ens18
         ^                               ^                                ^
         |                               |                                |
         v                               v                                v
         IP 10.100.100.10                IP 10.100.100.11                 IP 10.100.100.12
         MASK 255.255.255.0 (24)         MASK 255.255.255.0 (24)          MASK 255.255.255.0 (24) 
         GW 10.100.100.1                 GW 10.100.100.1                  GW 10.100.100.1
         DNS1 1.1.1.1                    DNS1 1.1.1.1                     DNS1 1.1.1.1
         DNS2 1.0.0.1                    DNS2 1.0.0.1                     DNS2 1.0.0.1


```

---

## Tables de routage (exemples)

```text
Route VM-01 / VM-02 / VM-03 / PC-01

Destination    Passerelles   Genmask         Indic Metric Ref Use Iface
0.0.0.0        10.100.100.1  0.0.0.0         UG    0      0   0   ens18
10.100.1.0     0.0.0.0       255.255.255.0   U     0      0   0   ens18
```

```text
Table des routages du serveur PROMOX

default via 10.100.100.1 dev vmbr0 proto kernel onlink
10.100.100.0/24 dev vmbr0 proto kernel scope link src ………. 
```
---

<div align="center">
  <a href="https://github.com/0xCyberLiTech" target="_blank" rel="noopener">
    <img src="https://skillicons.dev/icons?i=linux,debian,bash,docker,nginx,git,vim,python,markdown" alt="Skills" width="440">
  </a>
</div>

<div align="center">
  <b>🔒 Un guide proposé par <a href="https://0xcyberlitech.com/">0xCyberLiTech</a> • Pour des tutoriels accessibles à tous. 🔒</b>
</div>
