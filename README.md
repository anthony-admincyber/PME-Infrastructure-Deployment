# 🏢 LOGIFLEX — Infrastructure Système, Virtualisation & Cybersécurité

> Projet personnel de conception et de déploiement d'une infrastructure informatique d'entreprise virtualisée et sécurisée.

---

# 📌 Présentation

**LOGIFLEX Solutions** est une entreprise fictive spécialisée dans les solutions logicielles dédiées à la Supply Chain, aux WMS/TMS et à l'automatisation d'entrepôts.

Ce repository présente la conception et le déploiement progressif d'une **infrastructure système virtualisée et sécurisée**, reproduite dans un environnement de laboratoire.

Le projet permet de mettre en pratique différentes compétences liées à l'administration d'infrastructures :

-   administration Windows Server ;
-   virtualisation ;
-   Active Directory ;
-   DNS ;
-   administration Linux ;
-   supervision ;
-   sauvegarde ;
-   gestion des identités ;
-   sécurisation des infrastructures ;
-   PowerShell ;
-   résolution d'incidents ;
-   documentation technique.

> 🎯 **Projet personnel orienté Administration Système, Infrastructure et Cybersécurité**

---

# 🎯 Objectifs du projet

L'objectif est de reproduire une architecture cohérente correspondant aux besoins d'une PME.

## 🖥️ Infrastructure

-   Déployer Windows Server 2025.
-   Mettre en œuvre une infrastructure virtualisée.
-   Configurer Hyper-V.
-   Utiliser la virtualisation imbriquée.
-   Déployer plusieurs machines virtuelles.
-   Administrer les ressources système.

## 🪪 Identité

-   Déployer Active Directory Domain Services.
-   Créer un domaine Active Directory.
-   Mettre en place deux contrôleurs de domaine.
-   Centraliser l'authentification.
-   Organiser les utilisateurs et les ordinateurs.
-   Structurer les unités d'organisation.
-   Mettre en œuvre des groupes de sécurité.
-   Préparer la séparation des périmètres d'administration.
-   Mettre en œuvre progressivement le modèle AGDLP.

## 🌐 Réseau

-   Configurer un plan d'adressage IPv4.
-   Configurer les communications entre serveurs.
-   Mettre en œuvre un commutateur virtuel Hyper-V.
-   Configurer les services DNS.
-   Vérifier la connectivité des composants.

## 🐧 Services Linux

-   Déployer des serveurs Linux virtualisés.
-   Héberger un service SQL Server.
-   Déployer une plateforme de supervision.
-   Intégrer les services Linux dans l'environnement.

## 📊 Supervision

-   Déployer Centreon.
-   Superviser les serveurs.
-   Contrôler les ressources système.
-   Superviser les services.
-   Identifier les anomalies.

## 💾 Sauvegarde

-   Déployer Veeam Backup & Replication.
-   Configurer une stratégie de sauvegarde.
-   Utiliser un repository dédié.
-   Tester les restaurations.
-   Documenter les procédures de reprise.

## 🔐 Sécurité

-   Appliquer le principe du moindre privilège.
-   Séparer les comptes utilisateurs et administrateurs.
-   Structurer les groupes de sécurité.
-   Mettre en œuvre des stratégies de groupe.
-   Réduire la surface d'attaque.
-   Configurer les pare-feux.
-   Renforcer la journalisation.
-   Mettre en œuvre progressivement des mesures de durcissement.

---

# 🏗️ Architecture globale

L'environnement repose sur une architecture de **virtualisation imbriquée**.

```
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
              │                                 ├── AD DS
              │                                 ├── DNS
              ▼                                 ├── Réplication AD
           Hyper-V                              │
              │                                 └── Repository Veeam
      ┌───────┼────────────┐
      │       │            │
      ▼       ▼            ▼
 SRV-V-DC1 SRV-V-SQL  SRV-V-Centreon
      │       │            │
      │       │            │
   AD DS     SQL       Supervision
    DNS    Server       Centreon
```

---

# 🧩 Architecture technique

## 🖥️ Poste physique

| Composant | Configuration |
| --- | --- |
| Système | Windows 11 Pro |
| Mémoire disponible | 32 Go |
| Hyperviseur principal | VMware Workstation Pro |

Le poste physique constitue l'environnement principal du laboratoire.

VMware Workstation Pro est utilisé pour héberger les serveurs Windows Server nécessaires à l'infrastructure.

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
 ┌──────────┼────────────┐
 │          │            │
 ▼          ▼            ▼
SRV-V-DC1  SRV-V-SQL  SRV-V-Centreon
```

Cette architecture permet de mettre en pratique la **virtualisation imbriquée** (_Nested Virtualization_).

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

## SRV-V-DC1

**Fonction principale :**

> Premier contrôleur de domaine

Services :

-   Active Directory Domain Services ;
-   DNS ;
-   authentification ;
-   gestion centralisée des identités.

---

## SRV-02-DC2

**Fonctions principales :**

> Second contrôleur de domaine et serveur utilisé pour le stockage des sauvegardes dans le cadre du laboratoire.

Services :

-   Active Directory Domain Services ;
-   DNS ;
-   réplication Active Directory ;
-   repository de sauvegarde.

---

## SRV-V-SQL

**Fonction principale :**

> Serveur de base de données.

Services prévus :

-   Linux ;
-   SQL Server ;
-   service de base de données.

---

## SRV-V-Centreon

**Fonction principale :**

> Supervision de l'infrastructure.

Services prévus :

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
| SRV-V-DC1 | 192.168.10.20 | AD DS / DNS |
| SRV-02-DC2 | 192.168.10.21 | AD DS / DNS |
| SRV-V-SQL | 192.168.10.30 | SQL Server |
| SRV-V-Centreon | 192.168.10.40 | Supervision |

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
         SRV-V-DC1            SRV-02-DC2
          AD DS/DNS            AD DS/DNS
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

L'annuaire est structuré afin de distinguer les différents périmètres d'administration et les ressources de l'entreprise.

```
DC=logiflex,DC=infra
│
└── OU=LOGIFLEX
    │
    ├── OU=T0_Administration
    │   │
    │   ├── OU=Admins
    │   ├── OU=Groupes
    │   ├── OU=Comptes_Service
    │   └── OU=Postes_Administration
    │
    ├── OU=T1_Serveurs
    │   │
    │   ├── OU=Admins
    │   ├── OU=Groupes
    │   ├── OU=Serveurs_Membres
    │   └── OU=Comptes_Service
    │
    └── OU=T2_Utilisateurs_Postes
        │
        ├── OU=Utilisateurs
        ├── OU=Postes_Clients
        ├── OU=Admins
        └── OU=Groupes
```

Cette organisation permet notamment de préparer :

-   la séparation des périmètres administratifs ;
-   la gestion des utilisateurs ;
-   la gestion des serveurs membres ;
-   l'organisation des comptes privilégiés ;
-   l'application future de stratégies spécifiques ;
-   une gestion plus structurée des groupes de sécurité.

> ℹ️ La structure Active Directory est mise en œuvre progressivement. Les restrictions et mécanismes de sécurité associés aux différents périmètres seront documentés dans les étapes dédiées.

---

# 👥 Groupes de sécurité

Les groupes de sécurité constituent une base importante de la gestion des accès.

Deux catégories principales sont utilisées :

```
LOGIFLEX
│
├── Groupes d'administration
│   │
│   ├── Tier 0
│   ├── Tier 1
│   └── Tier 2
│
└── Groupes métiers
    │
    ├── Direction
    ├── DSI
    ├── R&D / Ingénierie
    ├── Commerce / Marketing
    ├── RH
    ├── Finance
    └── Consulting
```

## Groupes d'administration

| Groupe | Périmètre |
| --- | --- |
| GG_T0_Admins | Administration Active Directory et contrôleurs de domaine |
| GG_T1_ServerAdmins | Administration des serveurs et services |
| GG_T2_WorkstationAdmins | Administration des postes clients |

## Groupes métiers

| Groupe | Fonction |
| --- | --- |
| GG_Direction | Direction générale |
| GG_DSI | Direction des systèmes d'information |
| GG_RD_Ingenierie | Recherche & Développement |
| GG_Commerce_Marketing | Commerce et marketing |
| GG_RH | Ressources humaines |
| GG_Finance | Finance et comptabilité |
| GG_Consulting | Consulting et prestations |

---

# 🔐 Gestion des accès

Le projet applique progressivement plusieurs principes de sécurité.

## Principe du moindre privilège

Les utilisateurs disposent uniquement des droits nécessaires à leurs fonctions.

## Séparation des comptes

Les comptes administrateurs sont séparés des comptes utilisateurs standards.

## Groupes de sécurité

Les droits sont attribués aux groupes plutôt qu'individuellement aux utilisateurs.

Une logique inspirée du modèle **AGDLP** est utilisée pour préparer l'attribution future des accès aux ressources.

```
Account
   │
   ▼
Global Group
   │
   ▼
Domain Local Group
   │
   ▼
Permission
```

Exemple :

```
UTILISATEUR
    │
    ▼
GG_DSI
    │
    ▼
DL_Ressource_RW
    │
    ▼
RESSOURCE
    │
    ▼
PERMISSION
```

Les groupes globaux permettent de représenter les rôles des utilisateurs.

Les groupes locaux de domaine seront créés ultérieurement lors du déploiement des ressources nécessitant une gestion d'accès spécifique.

---

# 🔐 Sécurité et durcissement

La sécurisation est intégrée progressivement dans le projet.

Les mesures étudiées comprennent notamment :

-   GPO de sécurité ;
-   stratégies de mot de passe ;
-   gestion des comptes privilégiés ;
-   séparation des comptes standards et administratifs ;
-   réduction de la surface d'attaque ;
-   configuration des pare-feux ;
-   gestion des mises à jour ;
-   sécurisation DNS ;
-   contrôle des services ;
-   journalisation ;
-   supervision.

> ⚠️ Certaines mesures de sécurité sont volontairement appliquées progressivement afin de faciliter le déploiement, les tests et l'analyse des différents composants du laboratoire.

---

# 🌐 Réseau du laboratoire

L'ensemble des composants du laboratoire utilise actuellement un réseau commun.

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
    ┌─────────┼───────────┐
    │         │           │
    ▼         ▼           ▼
SRV-V-DC1  SRV-V-SQL  SRV-V-Centreon
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

Cette segmentation permettrait notamment de :

-   limiter les communications ;
-   contrôler les flux entre les zones ;
-   réduire la surface d'exposition ;
-   faciliter l'application de règles de filtrage.

Dans le cadre de ce laboratoire, cette segmentation n'est volontairement pas encore mise en œuvre.

Le projet se concentre actuellement principalement sur :

-   la virtualisation ;
-   Active Directory ;
-   les services Windows ;
-   les services Linux ;
-   la supervision ;
-   la sauvegarde ;
-   le durcissement des systèmes.

> ℹ️ La segmentation réseau pourra constituer une évolution future du laboratoire.

---

# 📊 Supervision

La supervision de l'infrastructure sera assurée par **Centreon**.

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

Une solution **Veeam Backup & Replication** sera utilisée afin de mettre en œuvre une stratégie de sauvegarde.

```
                 Machines virtuelles
                         │
                         ▼
               Veeam Backup & Replication
                         │
                         ▼
                   Repository
                   de sauvegarde
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
| Hyper-V | Démarrage et fonctionnement des VM |
| Linux | Services |
| SQL | Connexion et disponibilité |
| Centreon | Supervision |
| Veeam | Sauvegarde |
| Restauration | Récupération des services |
| Sécurité | Vérification des privilèges |

---

# 📂 Organisation du repository

```
LOGIFLEX-INFRA/
│
├── README.md
│
├── contexte.md
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
│   │   ├── 04-organisation-active-directory.md
│   │   ├── 05-groupes-securite-et-modele-agdlp.md
│   │   └── 06-gestion-utilisateurs-et-comptes-privilegies.md
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
| SRV-V-DC1 | 🟢 |
| Active Directory | 🟢 |
| DNS | 🟢 |
| SRV-02-DC2 | 🟢 |
| Réplication AD | 🟢 |
| Structure des OU | 🟢 |
| Groupes de sécurité | 🟢 |
| Gestion des utilisateurs | 🟡 |
| Comptes privilégiés | 🟡 |
| SRV-V-SQL | 🔴 |
| SRV-V-Centreon | 🔴 |
| Supervision | 🔴 |
| Veeam | 🔴 |
| Repository de sauvegarde | 🔴 |
| GPO / Hardening | 🔴 |
| Tests de restauration | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

Le projet est développé progressivement afin de documenter chaque étape de conception, de déploiement, de configuration, de sécurisation et de validation.

---

# 🎓 Compétences mises en œuvre

Ce projet permet de développer et de mettre en pratique des compétences liées à l'administration d'infrastructures :

`Windows Server` · `VMware Workstation` · `Hyper-V` · `Nested Virtualization` · `Active Directory` · `DNS` · `TCP/IP` · `PowerShell` · `Linux` · `SQL Server` · `Centreon` · `Supervision` · `Veeam` · `Backup & Recovery` · `GPO` · `Hardening` · `IAM` · `AGDLP` · `Least Privilege` · `Troubleshooting`

---

# 🚀 Évolutions prévues

Le laboratoire sera progressivement enrichi avec :

-    création et gestion des comptes utilisateurs ;
-    création des comptes d'administration dédiés ;
-    séparation progressive des comptes standards et administratifs ;
-    déploiement de `SRV-V-SQL` ;
-    installation de SQL Server ;
-    déploiement de `SRV-V-Centreon` ;
-    installation et configuration de Centreon ;
-    supervision des serveurs ;
-    déploiement de Veeam ;
-    configuration du repository ;
-    tests de sauvegarde ;
-    tests de restauration ;
-    GPO de sécurité ;
-    stratégies de mot de passe ;
-    durcissement Windows ;
-    durcissement Linux ;
-    gestion des comptes privilégiés ;
-    renforcement de la journalisation ;
-    analyse des incidents ;
-    documentation des procédures ;
-    évolution éventuelle vers une segmentation réseau.

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
-   gestion des identités ;
-   supervision ;
-   sauvegarde ;
-   cybersécurité ;
-   résolution d'incidents ;
-   documentation technique.

> 🚧 **Projet en cours de développement — l'infrastructure et la documentation évolueront progressivement au fur et à mesure de l'avancement du laboratoire.**
