# 01 — Préparation du serveur et installation des rôles AD DS / DNS

## 📌 Présentation

Cette première étape du projet **LOGIFLEX Infrastructure** consiste à préparer le serveur `SRV-01-DC1` avant sa promotion en contrôleur de domaine.

L'objectif est de disposer d'un serveur Windows Server 2025 correctement identifié, mis à jour, configuré sur le réseau et prêt à accueillir les services d'infrastructure nécessaires à Active Directory.

Cette phase de préparation comprend notamment :

- le renommage du serveur selon la convention de nommage LOGIFLEX ;
- l'installation des dernières mises à jour disponibles ;
- la configuration d'une adresse IP statique ;
- la préparation de l'environnement Windows Server ;
- la désactivation temporaire du pare-feu Microsoft Defender pendant la phase de préproduction ;
- la préparation du serveur pour l'installation d'AD DS et DNS.

> ⚠️ **Important :** la désactivation du pare-feu et des protections pendant cette phase est temporaire et concerne uniquement la préparation de l'environnement de laboratoire. Ces paramètres seront réévalués et durcis lors de la phase de préproduction / durcissement de l'infrastructure.

---

# 🖥️ Environnement

| Élément | Configuration |
|---|---|
| Serveur | `SRV-01-DC1` |
| Système | Windows Server 2025 Datacenter Evaluation |
| Hyperviseur |VMware® Workstation Pro 26H1 |
| Domaine | `logiflex.infra` |
| Adresse IP | `192.168.10.10/24` |
| Passerelle | `192.168.10.2` |
| Rôle prévu | Contrôleur de domaine principal |
| Services prévus | AD DS / DNS / Hyper-V |

---

# 1. 🏷️ Renommage du serveur

Le serveur Windows Server a initialement été préparé puis renommé afin de respecter la convention de nommage définie pour l'infrastructure LOGIFLEX.

Le nom retenu est :

```text
SRV-01-DC1

  *Résultat attendu :* `SRV-01-DC1`

* **Vérification de l'adresse IP :**
  ```cmd
  ipconfig /all
  ```
  *Adresse attendue :* `192.168.10.10`

---

### 📊 13. Bilan de l'étape

| Élément | État |
| :--- | :---: |
| Windows Server 2025 | 🟢 |
| Renommage `SRV-01-DC1` | 🟢 |
| Mises à jour Windows | 🟢 |
| Adresse IP statique | 🟢 |
| Connectivité réseau | 🟢 |
| Préparation du serveur | 🟢 |
| Rôle AD DS | 🟢 |
| Rôle DNS | 🟢 |
| Création de la forêt | 🟡 |
| Promotion DC1 | 🟡 |
| Durcissement final | 🟡 |

---

### 🎯 Résultat

À l'issue de cette étape, `SRV-01-DC1` dispose d'une base système prête pour le déploiement de l'annuaire **LOGIFLEX**.

Le serveur est :
* correctement nommé ;
* mis à jour ;
* configuré avec une adresse IP statique ;
* préparé pour les communications d'infrastructure ;
* équipé des rôles AD DS et DNS.

La prochaine étape consiste à promouvoir `SRV-01-DC1` en tant que premier contrôleur de domaine et à créer la nouvelle forêt Active Directory.
