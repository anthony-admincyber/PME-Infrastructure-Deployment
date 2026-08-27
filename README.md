# 🏢 LOGIFLEX — Infrastructure Système, Virtualisation & Cybersécurité

> Projet personnel de conception et de déploiement d'une infrastructure informatique d'entreprise virtualisée et sécurisée.

---

# 📌 Présentation

**LOGIFLEX Solutions** est une entreprise fictive spécialisée dans les solutions logicielles dédiées à la Supply Chain, aux WMS/TMS et à l'automatisation d'entrepôts.

Ce repository présente la conception et le déploiement progressif d'une **infrastructure système virtualisée**, reproduite dans un environnement de laboratoire.

Le projet permet de mettre en pratique différentes compétences liées à l'administration d'infrastructures :

- administration Windows Server ;
- virtualisation ;
- Active Directory ;
- DNS ;
- administration Linux ;
- supervision ;
- sauvegarde ;
- gestion des identités ;
- sécurisation des infrastructures ;
- PowerShell ;
- résolution d'incidents ;
- documentation technique.

> 🎯 **Projet personnel orienté Administration Système, Infrastructure et Cybersécurité**

---

# 🎯 Objectifs du projet

L'objectif est de reproduire une architecture cohérente correspondant aux besoins d'une PME.

Le projet couvre notamment :

## 🖥️ Infrastructure

- Déployer Windows Server 2025.
- Mettre en œuvre une infrastructure virtualisée.
- Configurer Hyper-V.
- Utiliser la virtualisation imbriquée.
- Déployer plusieurs machines virtuelles.
- Administrer les ressources système.

## 🪪 Identité

- Déployer Active Directory Domain Services.
- Créer un domaine Active Directory.
- Mettre en place deux contrôleurs de domaine.
- Centraliser l'authentification.
- Organiser les utilisateurs et les ordinateurs.
- Structurer les unités d'organisation.
- Mettre en œuvre des groupes de sécurité.

## 🌐 Réseau

- Configurer un plan d'adressage IPv4.
- Configurer les communications entre serveurs.
- Mettre en œuvre un commutateur virtuel Hyper-V.
- Configurer les services DNS.
- Vérifier la connectivité des composants.

## 🐧 Services Linux

- Déployer des serveurs Linux virtualisés.
- Héberger un service SQL.
- Déployer une plateforme de supervision.
- Intégrer les services Linux dans l'environnement.

## 📊 Supervision

- Déployer Centreon.
- Superviser les serveurs.
- Contrôler les ressources système.
- Superviser les services.
- Identifier les anomalies.

## 💾 Sauvegarde

- Déployer Veeam.
- Configurer une stratégie de sauvegarde.
- Utiliser un repository dédié.
- Tester les restaurations.
- Documenter les procédures de reprise.

## 🔐 Sécurité

- Appliquer le principe du moindre privilège.
- Séparer les comptes utilisateurs et administrateurs.
- Structurer les groupes de sécurité.
- Mettre en œuvre des stratégies de groupe.
- Réduire la surface d'attaque.
- Configurer les pare-feux.
- Renforcer la journalisation.
- Mettre en œuvre des mesures de durcissement.

---

# 🏗️ Architecture globale

L'environnement repose sur une architecture de **virtualisation imbriquée**.

```text
                         POSTE PHYSIQUE
                        Windows 11 Pro
                               │
                               ▼
                  VMware Workstation Pro
                               │
              ┌────────────────┴────────────────┐
              │                                 │
              ▼                                 ▼
         SRV-01-HV                        SRV-02-DC2
     Windows Server 2025              Windows Server 2025
              │                                 │
              │                                 │
              ▼                                 ├── AD DS
           Hyper-V                              ├── DNS
              │                                 │
      ┌───────┼────────┐                        └── Repository Veeam
      │       │        │
      ▼       ▼        ▼
   VM-DC1   VM-SQL   VM-Centreon
      │       │        │
      │       │        │
   AD DS     SQL     Supervision
   DNS     Comptabilité  Centreon
```

# 🧩 Architecture technique

## 🖥️ Poste physique

| Composant | Configuration |
| --- | --- |
| Système | Windows 11 Pro |
| Mémoire disponible | 32 Go |
| Hyperviseur principal | VMware Workstation Pro |

Le poste physique constitue l'environnement principal du laboratoire.

VMware Workstation est utilisé pour héberger les serveurs Windows Server nécessaires à l'infrastructure.

---

## ⚙️ Niveau 1 — VMware Workstation

Deux machines virtuelles Windows Server 2025 sont exécutées dans VMware Workstation.

```
VMware Workstation
│
├── SRV-01-HV
│
└── SRV-02-DC2
```

---

## ⚙️ Niveau 2 — Hyper-V

`SRV-01-HV` est configuré comme hôte Hyper-V.

Il héberge les machines virtuelles nécessaires aux différents services du laboratoire.

```
SRV-01-HV
Windows Server 2025
        │
        ▼
      Hyper-V
        │
 ┌──────┼──────────┐
 │      │          │
 ▼      ▼          ▼
VM-DC1 VM-SQL VM-Centreon
```

Cette architecture permet de mettre en pratique la **virtualisation imbriquée (Nested Virtualization)**.

> ℹ️ La virtualisation imbriquée est utilisée dans ce projet afin de reproduire plusieurs niveaux de virtualisation dans un environnement de laboratoire disposant de ressources matérielles limitées.

---

# 🖥️ Composants de l'infrastructure

## SRV-01-HV

**Fonction principale :**

> Hôte Hyper-V

Services :

-   Hyper-V ;
-   gestion des machines virtuelles ;
-   commutateur virtuel ;
-   administration de l'environnement virtualisé.

---

## VM-DC1

**Fonction principale :**

> Premier contrôleur de domaine

Services prévus :

-   Active Directory Domain Services ;
-   DNS ;
-   authentification ;
-   gestion centralisée des identités.

---

## SRV-02-DC2

**Fonctions principales :**

> Second contrôleur de domaine et serveur de stockage pour les sauvegardes.

Services prévus :

-   Active Directory Domain Services ;
-   DNS ;
-   réplication Active Directory ;
-   repository Veeam.

---

## VM-SQL

**Fonction principale :**

> Serveur de base de données pour l'application de comptabilité.

Services :

-   Linux ;
-   SQL Server ;
-   service de base de données.

---

## VM-Centreon

**Fonction principale :**

> Supervision de l'infrastructure.

Services :

-   Centreon ;
-   supervision des systèmes ;
-   supervision des ressources ;
-   contrôle de disponibilité ;
-   alertes.

---

# 🌐 Plan d'adressage

Le laboratoire utilise le réseau :

```
192.168.10.0/24
```

| Équipement | Adresse IP | Fonction |
| --- | --- | --- |
| Passerelle | 192.168.10.2 | Accès réseau |
| SRV-01-HV | 192.168.10.10 | Hôte Hyper-V |
| VM-DC1 | 192.168.10.20 | AD DS / DNS |
| SRV-02-DC2 | 192.168.10.11 | AD DS / DNS / Repository |
| VM-SQL | 192.168.10.30 | SQL |
| VM-Centreon | 192.168.10.40 | Supervision |

> ℹ️ Les adresses IP pourront être ajustées au fur et à mesure de l'évolution du laboratoire.

---

# 🪪 Active Directory

Le domaine Active Directory utilisé dans le laboratoire est :

```
logiflex.infra
```

L'infrastructure Active Directory repose sur deux contrôleurs de domaine.

```
                 logiflex.infra
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
          VM-DC1              SRV-02-DC2
         AD DS/DNS             AD DS/DNS
             │                     │
             └──────────┬──────────┘
                        │
                  Réplication AD
```

Cette architecture permet de mettre en œuvre :

-   la réplication Active Directory ;
-   la redondance DNS ;
-   la continuité des services d'identité ;
-   plusieurs contrôleurs de domaine.

---

# 🏢 Organisation Active Directory

L'annuaire est structuré afin de séparer les différentes ressources de l'entreprise.

```
DC=logiflex,DC=infra
│
└── OU=LOGIFLEX
    │
    ├── OU=Departements
    │
    ├── OU=Utilisateurs
    │
    ├── OU=Ordinateurs
    │
    ├── OU=Serveurs
    │
    ├── OU=Groupes_Securite
    │
    └── OU=Comptes_Privileges
```

Les départements pourront notamment être organisés selon la structure suivante :

```
Departements
│
├── Direction
├── DSI
├── RD_Ingenierie
├── Commerce_Marketing
├── RH_Finance
└── Consulting
```

---

# 🔐 Gestion des accès

Le projet applique progressivement plusieurs principes de sécurité.

## Principe du moindre privilège

Les utilisateurs disposent uniquement des droits nécessaires à leurs fonctions.

## Séparation des comptes

Les comptes administrateurs sont séparés des comptes utilisateurs standards.

## Groupes de sécurité

Les droits sont attribués à des groupes plutôt qu'individuellement aux utilisateurs.

Une logique inspirée du modèle **AGDLP** sera utilisée :

```
Accounts
   │
   ▼
Global Groups
   │
   ▼
Domain Local Groups
   │
   ▼
Permissions
```

---

# 🔐 Sécurité et durcissement

La sécurisation est intégrée progressivement dans le projet.

Les mesures étudiées comprennent notamment :

-   GPO de sécurité ;
-   stratégies de mot de passe ;
-   gestion des comptes privilégiés ;
-   réduction de la surface d'attaque ;
-   configuration des pare-feux ;
-   gestion des mises à jour ;
-   sécurisation DNS ;
-   contrôle des services ;
-   journalisation ;
-   supervision.

> ⚠️ Certaines mesures de sécurité sont volontairement appliquées progressivement afin de faciliter le déploiement et l'analyse des différents composants du laboratoire.

---

# 🌐 Réseau du laboratoire

L'ensemble des composants du laboratoire utilise un réseau commun.

```
                    192.168.10.0/24
                           │
                           │
                     Passerelle
                     192.168.10.2
                           │
              ┌────────────┴────────────┐
              │                         │
         SRV-01-HV                 SRV-02-DC2
              │
              ▼
          Hyper-V
              │
       vSwitch-LOGIFLEX
              │
      ┌───────┼─────────┐
      │       │         │
      ▼       ▼         ▼
   VM-DC1   VM-SQL   VM-Centreon
```

## 📌 Note sur la segmentation réseau

Dans une infrastructure de production, les différents flux seraient normalement séparés selon les besoins de sécurité.

Une architecture réelle pourrait notamment distinguer :

-   réseau utilisateurs ;
-   réseau serveurs ;
-   réseau administration ;
-   réseau sauvegarde ;
-   réseau supervision ;
-   réseau stockage.

Cette segmentation permettrait notamment de limiter les communications, de contrôler les flux entre les zones et de réduire la surface d'exposition.

Dans le cadre de ce laboratoire, cette segmentation n'est volontairement pas mise en œuvre.

Le projet se concentre principalement sur :

-   la virtualisation ;
-   Active Directory ;
-   les services Windows ;
-   les services Linux ;
-   la supervision ;
-   la sauvegarde ;
-   le durcissement des systèmes.

> ℹ️ L'absence de segmentation dans cette maquette constitue donc un choix volontaire lié aux contraintes et aux objectifs pédagogiques du laboratoire.

---

# 📊 Supervision

La supervision de l'infrastructure est assurée par **Centreon**.

Les éléments surveillés pourront notamment inclure :

-   disponibilité des serveurs ;
-   processeur ;
-   mémoire ;
-   espace disque ;
-   services Windows ;
-   services Linux ;
-   disponibilité réseau ;
-   interfaces réseau ;
-   événements critiques.

L'objectif est de mettre en œuvre une approche :

```
Détection
    ↓
Analyse
    ↓
Identification
    ↓
Remédiation
    ↓
Validation
```

---

# 💾 Sauvegarde et restauration

Une solution Veeam sera utilisée afin de mettre en œuvre une stratégie de sauvegarde.

```
                 Machines virtuelles
                         │
                         ▼
                  Veeam Console
                         │
                         ▼
                   Repository
                  SRV-02-DC2
```

Les tests comprendront notamment :

-   sauvegarde des machines virtuelles ;
-   vérification des sauvegardes ;
-   restauration de fichiers ;
-   restauration d'une machine virtuelle ;
-   validation après restauration.

> 🎯 L'objectif n'est pas uniquement de réaliser une sauvegarde, mais de vérifier que la restauration permet réellement de récupérer le service.

---

# 🧪 Validation et tests

Chaque composant du projet fait l'objet de vérifications.

| Domaine | Tests |
| --- | --- |
| Réseau | Connectivité |
| DNS | Résolution directe et inverse |
| Active Directory | Authentification |
| AD DS | Réplication |
| Hyper-V | Démarrage des VM |
| Linux | Services |
| SQL | Connexion |
| Centreon | Supervision |
| Veeam | Sauvegarde |
| Restauration | Récupération des services |
| Sécurité | Vérification des privilèges |

---

# 📂 Organisation du repository

```
PME-Infrastructure-Deployment/
│
├── README.md
│
├── architecture/
│   ├── architecture-globale.md
│   ├── architecture-globale.drawio
│   └── schemas/
│
├── windows-server/
│   │
│   ├── virtualisation/
│   │   ├── 01-deploiement-windows-server.md
│   │   ├── 02-installation-hyper-v.md
│   │   └── 03-creation-vm-dc1.md
│   │
│   ├── active-directory/
│   │   ├── 01-preparation-dc1.md
│   │   ├── 02-promotion-dc1.md
│   │   ├── 03-ajout-dc2.md
│   │   └── 04-organisation-active-directory.md
│   │
│   ├── dns/
│   │
│   └── gpo/
│
├── linux/
│   ├── sql-server/
│   └── centreon/
│
├── sauvegarde/
│   └── veeam/
│
├── securite/
│   ├── hardening/
│   ├── firewall/
│   └── comptes-privileges/
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

| Composant | État |
| --- | --- |
| Environnement VMware | 🟢 |
| Windows Server 2025 | 🟢 |
| Virtualisation imbriquée | 🟢 |
| Hyper-V | 🟢 |
| VM-DC1 | 🟡 |
| Active Directory | 🔴 |
| DNS | 🔴 |
| SRV-02-DC2 | 🔴 |
| Réplication AD | 🔴 |
| VM-SQL | 🔴 |
| VM-Centreon | 🔴 |
| Supervision | 🔴 |
| Veeam | 🔴 |
| Repository Veeam | 🔴 |
| Hardening | 🔴 |
| Tests de restauration | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎓 Compétences mises en œuvre

Ce projet permet de développer et de mettre en pratique des compétences liées à l'administration d'infrastructures :

`Windows Server` · `VMware Workstation` · `Hyper-V` · `Nested Virtualization` · `Active Directory` · `DNS` · `TCP/IP` · `PowerShell` · `Linux` · `SQL Server` · `Centreon` · `Supervision` · `Veeam` · `Backup & Recovery` · `GPO` · `Hardening` · `IAM` · `Least Privilege` · `Troubleshooting`

---

# 🚀 Évolutions prévues

Le laboratoire sera progressivement enrichi avec :

-   création de `VM-DC1` ;
-   déploiement Active Directory ;
-   configuration DNS ;
-   déploiement du second contrôleur de domaine ;
-   réplication Active Directory ;
-   déploiement de VM-SQL ;
-   installation de SQL Server ;
-   déploiement de Centreon ;
-   supervision des serveurs ;
-   déploiement de Veeam ;
-   configuration du repository ;
-   tests de sauvegarde ;
-   tests de restauration ;
-   GPO de sécurité ;
-   durcissement Windows ;
-   durcissement Linux ;
-   gestion des comptes privilégiés ;
-   journalisation ;
-   analyse des incidents ;
-   documentation des procédures.

---

# 💡 Philosophie du projet

L'objectif du projet n'est pas uniquement d'installer des solutions techniques.

Chaque composant est intégré dans une démarche globale :

```
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
DOCUMENTER
    ↓
AMÉLIORER
```

Chaque étape est documentée afin de conserver une trace :

-   des choix techniques ;
-   des configurations réalisées ;
-   des problèmes rencontrés ;
-   des incidents ;
-   des solutions appliquées ;
-   des résultats des tests.

---

# 👨‍💻 À propos

Projet personnel réalisé dans le cadre de ma montée en compétences vers le métier d'**Administrateur d'Infrastructures Sécurisées**.

Ce laboratoire me permet de mettre en pratique des compétences dans les domaines suivants :

-   administration système ;
-   infrastructures Windows ;
-   virtualisation ;
-   réseaux ;
-   Active Directory ;
-   Linux ;
-   supervision ;
-   sauvegarde ;
-   cybersécurité ;
-   résolution d'incidents ;
-   documentation technique.

> 🚧 **Projet en cours de développement — l'infrastructure et la documentation évolueront progressivement au fur et à mesure de l'avancement du laboratoire.**
