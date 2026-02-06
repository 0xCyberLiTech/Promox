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
| **IPTABLES**           | Introduction et configuration.                        | [<img src="https://img.shields.io/badge/EXPLORER-4CAF50?style=for-the-badge&logo=github&logoColor=white">](FIREWALL-IPTABLES-INTRODUCTION-CONFIGURATION.md)         |
| **UFW**                | Installation et configuration sur **Debian 12 & 13**. | [<img src="https://img.shields.io/badge/EXPLORER-4CAF50?style=for-the-badge&logo=github&logoColor=white">](FIREWALL-UFW-INTRODUCTION-INSTALLATION-CONFIGURATION.md) |
| **SECURITE RENFORCEE** | Associer UFW avec Fail2ban.                           | [<img src="https://img.shields.io/badge/EXPLORER-4CAF50?style=for-the-badge&logo=github&logoColor=white">](FIREWALL-Associer-UFW-avec-Fail2ban-sur-Debian-12-13.md) |

</div>

---

# 💡 **Qu'est-ce qu'un firewall ?**

## Définition détaillée d’un Firewall

Un **firewall** (pare-feu) est un dispositif matériel ou logiciel qui surveille, filtre et contrôle le trafic réseau entre différentes zones de confiance (ex : Internet ↔ réseau interne). Il applique des règles pour autoriser, refuser ou rediriger les paquets selon des critères précis : adresses IP, ports, protocoles, état de connexion, etc.

### Rôles principaux :
- **Filtrage** : Bloquer ou autoriser des flux selon des règles.
- **Journalisation** : Tracer les accès et tentatives.
- **Traduction d’adresses (NAT)** : Masquer ou rediriger des adresses IP.
- **Segmentation** : Isoler des zones (DMZ, LAN, WAN).
- **Inspection d’état** : Suivre l’état des connexions (stateful).

---

## 1. Schéma général : Positionnement d’un firewall

```
   [Internet]
        |
   +----v----+
   | Firewall|
   +----+----+
        |
   [Réseau privé]
```

---

## 2. Logique de décision d’un firewall (filtrage)

```
+-------------------+
|   Paquet réseau   |
+-------------------+
         |
         v
+------------------------+
|  Règle correspondante ?|
+------------------------+
   | Oui         | Non
   v             v
[Action]     [Bloqué]
```

---

## 3. Filtrage stateless vs stateful

- **Stateless** : Le firewall examine chaque paquet indépendamment, sans tenir compte du contexte.
- **Stateful** : Le firewall garde en mémoire l’état des connexions (ex : TCP SYN/ACK), ce qui permet de n’autoriser que les paquets attendus dans une session.

### Schéma : Suivi d’état (stateful)

```
[Client] ---- SYN ----> [Firewall] ----> [Serveur]
         <--- SYN/ACK --
         ---- ACK ------>
```
Le firewall autorise les paquets de réponse uniquement s’ils correspondent à une connexion initiée.

---

## 4. Translation d’adresses (NAT)

La **NAT** (Network Address Translation) permet de faire correspondre des adresses IP privées à une adresse publique, pour masquer le réseau interne ou rediriger des ports.

### Schéma : NAT simple

```
[LAN: 192.168.1.10] --+
[LAN: 192.168.1.11] --+--> [Firewall NAT] --> [Internet: 203.0.113.5]
```
Tous les flux sortants semblent provenir de l’IP publique du firewall.

### Schéma : Redirection de port (Port Forwarding)

```
[Internet:203.0.113.5:2222] --> [Firewall NAT] --> [LAN:192.168.1.10:22]
```
Le firewall redirige le port 2222 externe vers le port 22 d’une machine interne.

---

## 5. DMZ (Zone Démilitarisée)

Une **DMZ** est une zone réseau intermédiaire, isolée du LAN, où l’on place les serveurs accessibles depuis Internet (web, mail, etc.).  
Cela limite les risques pour le réseau interne en cas de compromission d’un serveur public.

### Schéma : DMZ

```
           [Internet]
                |
           +----v----+
           | Firewall|
           +----+----+
                |
      +---------+----------+
      |                    |
   [DMZ]                [LAN]
(serveurs web, etc.)   (PC internes)
```

---

## 6. Exemple de règles logiques (table de filtrage)

| Source           | Destination      | Port | Action    | État connexion |
|------------------|-----------------|------|-----------|---------------|
| Internet         | DMZ (web)       | 80   | Autoriser | NEW/ESTABLISHED|
| Internet         | LAN             | *    | Refuser   | *             |
| LAN              | Internet        | 80   | Autoriser | ESTABLISHED   |
| DMZ              | LAN             | *    | Refuser   | *             |

---

## 7. Schéma éclaté : Flux réseau avec firewall, NAT et DMZ

```
[Internet]
    |
    v
+-------------------+
|     Firewall      |
|-------------------|
| - Filtrage        |
| - NAT             |
| - Suivi d'état    |
+---+-----------+---+
    |           |
   DMZ         LAN
(serveur web) (PC internes)
```

- Le firewall filtre et traduit les adresses.
- Les flux autorisés vers la DMZ n’atteignent pas le LAN.
- Les connexions sortantes du LAN passent par la NAT.

---

## 8. Résumé pédagogique

- Un firewall contrôle le trafic selon des règles précises.
- Il peut être stateless (simple) ou stateful (plus sûr).
- Il gère la translation d’adresses (NAT) pour masquer ou rediriger.
- Il permet de segmenter le réseau (DMZ) pour limiter les risques.
- Les schémas aident à visualiser la logique et la circulation des flux.

---

<div align="center">
  <a href="https://github.com/0xCyberLiTech" target="_blank" rel="noopener">
    <img src="https://skillicons.dev/icons?i=linux,debian,bash,docker,nginx,git,vim,python,markdown" alt="Skills" width="440">
  </a>
</div>

<div align="center">
  <b>🔒 Un guide proposé par <a href="https://0xcyberlitech.com/">0xCyberLiTech</a> • Pour des tutoriels accessibles à tous. 🔒</b>
</div>

