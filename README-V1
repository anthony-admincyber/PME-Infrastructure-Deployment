# 🏢 LOGIFLEX — Infrastructure Système, Réseau & Sécurité






\

## 📌 Présentation

**LOGIFLEX Solutions** est une entreprise fictive spécialisée dans les solutions logicielles dédiées à la Supply Chain, aux WMS/TMS et à l'automatisation d'entrepôts.

Ce repository présente la conception et le déploiement progressif d'une **infrastructure d'entreprise hybride, virtualisée et sécurisée**, reproduite dans un environnement de laboratoire.

L'objectif est de mettre en œuvre une infrastructure cohérente répondant aux problématiques rencontrées dans une PME :

* centralisation des identités ;
* administration des systèmes ;
* disponibilité des services ;
* stockage centralisé ;
* supervision ;
* sauvegarde ;
* durcissement de l'infrastructure ;
* contrôle des accès ;
* continuité de service.

> 🎯 **Projet personnel orienté Infrastructure / Système / Réseau / Cybersécurité**

---

# 🎯 Objectifs du projet

Le projet vise à reproduire les principales briques techniques d'un SI d'entreprise.

### Infrastructure

* Déployer Windows Server 2025.
* Mettre en place un domaine Active Directory.
* Déployer deux contrôleurs de domaine.
* Centraliser le DNS.
* Structurer l'annuaire avec des OU et des groupes de sécurité.
* Mettre en place une infrastructure Hyper-V.

### Réseau

* Définir un plan d'adressage IPv4.
* Configurer les communications inter-serveurs.
* Contrôler les flux réseau nécessaires aux différents services.
* Documenter les dépendances réseau.

### Stockage

* Déployer un serveur TrueNAS.
* Mettre en œuvre du stockage iSCSI.
* Mettre en place des partages NFS/SMB.
* Préparer une stratégie de stockage adaptée aux différents services.

### Services Linux

* Déployer Ubuntu Server.
* Installer SQL Server sous Linux.
* Intégrer les services Linux à l'environnement d'entreprise.
* Déployer Centreon pour la supervision.

### Sécurité

* Appliquer le principe du moindre privilège.
* Séparer les comptes utilisateurs et administrateurs.
* Structurer les groupes de sécurité selon une logique AGDLP.
* Préparer une stratégie de durcissement Windows/Linux.
* Contrôler les services et ports exposés.
* Mettre en place une supervision permettant de détecter les anomalies.

### Continuité

* Déployer Veeam Backup & Replication.
* Sauvegarder les composants critiques.
* Tester la restauration.
* Documenter les scénarios de perte de service.

---

# 🏗️ Architecture globale

```text
                         INTERNET
                            │
                            ▼
                    ┌───────────────┐
                    │    ROUTER /   │
                    │   FIREWALL    │
                    └───────┬───────┘
                            │
                     192.168.10.0/24
                            │
              ┌─────────────┴─────────────┐
              │                           │
       ┌──────▼──────┐             ┌──────▼──────┐
       │ SRV-01-DC1  │◄───────────►│ SRV-02-DC2  │
       │ Windows 2025│   AD / DNS  │ Windows 2025│
       │ AD DS / DNS │   Replication│ AD DS / DNS │
       │ Hyper-V     │             │ Hyper-V     │
       └──────┬──────┘             └──────┬──────┘
              │                           │
              └─────────────┬─────────────┘
                            │
                ┌───────────┼───────────┐
                │           │           │
          ┌─────▼─────┐ ┌──▼────────┐ ┌▼───────────┐
          │ VM-SQL     │ │ VM-Centreon│ │ NAS-SAN01 │
          │ Linux      │ │ Ubuntu     │ │ TrueNAS   │
          │ SQL Server │ │ SNMP       │ │ iSCSI/NFS │
          └────────────┘ └────────────┘ └───────────┘
```

> 📐 L'architecture complète et les schémas détaillés sont disponibles dans le dossier [`/architecture`](./architecture/).

---

# 🌐 Plan d'adressage

| Hôte          | OS                  |      Adresse IP | Fonction              |
| ------------- | ------------------- | --------------: | --------------------- |
| `SRV-01-DC1`  | Windows Server 2025 | `192.168.10.10` | AD DS / DNS / Hyper-V |
| `SRV-02-DC2`  | Windows Server 2025 | `192.168.10.11` | AD DS / DNS / Hyper-V |
| `VM-SQL-PROD` | Linux               | `192.168.10.20` | SQL Server            |
| `VM-Centreon` | Ubuntu 24.04        | `192.168.10.30` | Supervision           |
| `NAS-SAN01`   | TrueNAS             | `192.168.10.50` | Stockage              |

**LAN :** `192.168.10.0/24`

**Domaine Active Directory :** `logiflex.infra`

---

# 🪟 Active Directory

Le domaine `logiflex.infra` constitue le cœur de l'infrastructure d'identité.

## Architecture logique

```text
DC=logiflex,DC=infra
│
└── OU=LOGIFLEX
    │
    ├── OU=Departements
    │   ├── 01_Direction
    │   ├── 02_DSI
    │   ├── 03_RD_Ingenierie
    │   ├── 04_Commerce_Marketing
    │   ├── 05_RH_Finance
    │   └── 06_Consulting
    │
    ├── OU=Ordinateurs
    │   ├── Serveurs
    │   └── Postes_Clients
    │
    ├── OU=Groupes_securite
    │
    └── OU=Comptes_privileges
```

### Groupes de sécurité

Le modèle **AGDLP** est utilisé afin de séparer les comptes, les groupes et les permissions.

Principaux groupes :

* `GS_Direction`
* `GS_Admins_Infra`
* `GS_Support_IT`
* `GS_Dev_Team`
* `GS_Commercials`
* `GS_RH`
* `GS_Finance`
* `GS_Consulting`

Cette organisation permet notamment de préparer :

* le déploiement des GPO ;
* la gestion des droits NTFS ;
* la gestion des partages ;
* la séparation des privilèges ;
* le modèle de Tiering administratif.

---

# 🌐 DNS

Le DNS est intégré à Active Directory.

Les objectifs sont :

* résolution directe des ressources ;
* résolution inverse ;
* enregistrements dynamiques sécurisés ;
* support de l'authentification Kerberos ;
* fonctionnement correct de la réplication AD DS ;
* résolution des équipements supervisés.

Une zone de recherche inversée est également configurée :

```text
10.168.192.in-addr.arpa
```

---

# 🔐 Sécurité & Hardening

La sécurité est intégrée progressivement au projet plutôt qu'ajoutée uniquement à la fin.

## Principes appliqués

### Least Privilege

Les utilisateurs disposent uniquement des droits nécessaires à leur fonction.

### Séparation des privilèges

Les comptes d'administration sont séparés des comptes utilisateurs standards.

### Tiering

Une OU dédiée aux comptes privilégiés prépare la mise en œuvre d'un modèle d'administration par niveaux.

### Durcissement

Le projet prévoit notamment :

* GPO de sécurité ;
* configuration du pare-feu Windows ;
* réduction de la surface d'exposition ;
* contrôle des services ;
* gestion des correctifs ;
* sécurisation DNS ;
* supervision des événements ;
* contrôle des comptes privilégiés.

> ⚠️ Certaines mesures de sécurité sont volontairement appliquées progressivement pendant le développement du laboratoire afin de faciliter le déploiement initial. Le durcissement final intervient dans une phase dédiée.

---

# 📊 Supervision

**Centreon** est utilisé afin de centraliser la supervision de l'infrastructure.

La supervision permettra notamment de suivre :

* disponibilité des serveurs ;
* CPU ;
* mémoire ;
* stockage ;
* services ;
* interfaces réseau ;
* disponibilité des équipements ;
* événements critiques.

L'objectif est de passer d'une administration réactive à une approche **proactive**.

---

# 💾 Sauvegarde & Continuité

**Veeam Backup & Replication** est intégré à l'architecture afin de mettre en œuvre une stratégie de sauvegarde.

Les scénarios étudiés comprennent notamment :

* sauvegarde des machines virtuelles ;
* restauration d'une VM ;
* restauration après incident ;
* vérification de l'intégrité des sauvegardes ;
* documentation des procédures de reprise.

L'objectif n'est pas uniquement de **faire une sauvegarde**, mais de vérifier qu'elle permet réellement de **restaurer le service**.

---

# 🧪 Validation & Tests

Chaque composant est validé progressivement.

Exemples de tests :

| Domaine          | Tests                                 |
| ---------------- | ------------------------------------- |
| Réseau           | Ping / résolution / connectivité      |
| DNS              | Résolution directe et inverse         |
| Active Directory | Authentification / réplication        |
| Kerberos         | Authentification inter-services       |
| Hyper-V          | Démarrage / fonctionnement des VM     |
| Stockage         | Accès iSCSI / NFS / SMB               |
| SQL              | Connexion et disponibilité            |
| Centreon         | Détection / supervision / alerting    |
| Veeam            | Backup / restauration                 |
| Sécurité         | Vérification des règles et privilèges |

---

# 📂 Organisation du repository

```text
LOGIFLEX-INFRA/
│
├── README.md
│
├── architecture/
│   ├── architecture-globale.drawio
│   ├── architecture-globale.png
│   ├── architecture-reseau.png
│   └── architecture-ad.png
│
├── windows-server/
│   ├── active-directory/
│   ├── dns/
│   ├── gpo/
│   └── hardening/
│
├── linux/
│   ├── sql-server/
│   └── centreon/
│
├── stockage/
│   ├── truenas/
│   ├── iscsi/
│   ├── nfs/
│   └── smb/
│
├── sauvegarde/
│   └── veeam/
│
├── securite/
│   ├── hardening/
│   ├── firewall/
│   ├── comptes-privileges/
│   └── supervision/
│
├── documentation/
│   ├── procedures/
│   ├── tests/
│   └── incidents/
│
└── screenshots/
```

---

# 📈 État d'avancement

| Composant                        | État |
| -------------------------------- | :--: |
| Windows Server 2025              |  🟢  |
| Active Directory                 |  🟢  |
| DNS                              |  🟢  |
| Contrôleur de domaine secondaire |  🟢  |
| Structure OU                     |  🟢  |
| Groupes de sécurité              |  🟢  |
| Hyper-V                          |  🟡  |
| TrueNAS                          |  🟡  |
| iSCSI / NFS / SMB                |  🟡  |
| SQL Server Linux                 |  🟡  |
| Centreon                         |  🟡  |
| Veeam                            |  🟡  |
| GPO / Hardening                  |  🟡  |
| Tests de restauration            |  🔴  |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

Le projet est volontairement développé **par étapes**, afin de documenter chaque phase de conception, déploiement, sécurisation et validation.

---

# 🎓 Compétences mises en œuvre

Ce projet permet de travailler sur plusieurs domaines liés au métier d'**Administrateur d'Infrastructures Sécurisées** :

`Windows Server` · `Active Directory` · `DNS` · `TCP/IP` · `Hyper-V` · `Linux` · `SQL Server` · `TrueNAS` · `iSCSI` · `NFS` · `SMB` · `Centreon` · `SNMP` · `Veeam` · `GPO` · `Hardening` · `IAM` · `Least Privilege` · `Supervision` · `Backup & Recovery`

---

# 🚀 Évolutions prévues

Le laboratoire sera progressivement enrichi avec :

* [ ] GPO de sécurité
* [ ] Stratégie de mot de passe
* [ ] LAPS / gestion des comptes administrateurs locaux
* [ ] Durcissement Windows Server
* [ ] Durcissement Linux
* [ ] Segmentation réseau / VLAN
* [ ] Firewalling inter-zones
* [ ] Supervision Centreon
* [ ] Centralisation des logs
* [ ] Sauvegarde Veeam
* [ ] Tests de restauration
* [ ] Scénarios de panne AD
* [ ] Tests de continuité de service
* [ ] Documentation PRA/PCA
* [ ] Analyse de risques
* [ ] Documentation finale de l'architecture

---

# 💡 Philosophie du projet

Ce laboratoire ne cherche pas uniquement à reproduire une infrastructure technique.

L'objectif est de démontrer une démarche complète :

```text
CONCEVOIR
    ↓
DÉPLOYER
    ↓
CONFIGURER
    ↓
SÉCURISER
    ↓
SUPERVISER
    ↓
SAUVEGARDER
    ↓
TESTER
    ↓
DOCUMENTer
    ↓
AMÉLIORER
```

Chaque évolution du projet est documentée afin de conserver une **traçabilité technique** des choix réalisés, des problèmes rencontrés et des solutions mises en œuvre.

---

## 👨‍💻 À propos

Projet personnel réalisé dans le cadre de ma montée en compétences vers le métier d'**Administrateur d'Infrastructures Sécurisées**.

L'objectif est de mettre en pratique les connaissances acquises en :

* administration système ;
* réseaux ;
* virtualisation ;
* Linux ;
* Active Directory ;
* cybersécurité ;
* supervision ;
* sauvegarde ;
* gouvernance et sécurisation des infrastructures.

**Projet en cours de développement — de nouvelles briques et documentations seront ajoutées progressivement.**

