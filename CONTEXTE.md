# 🏢 Contexte d'Entreprise & Organisation — LOGIFLEX

---

## 📌 Présentation de l'entreprise

**LOGIFLEX Solutions** est une entreprise fictive spécialisée dans les solutions logicielles dédiées à la **Supply Chain**, aux systèmes de gestion d'entrepôt (**WMS**), aux systèmes de transport (**TMS**) et à l'automatisation des environnements logistiques.

L'entreprise conçoit, déploie et maintient des solutions destinées à accompagner les organisations dans la gestion de leurs flux logistiques et de leurs infrastructures numériques.

Dans le cadre d'un projet de modernisation et de sécurisation de son système d'information, LOGIFLEX met en place une infrastructure centralisée reposant notamment sur :

- Active Directory ;
- Windows Server ;
- DNS ;
- virtualisation ;
- services Linux ;
- supervision ;
- sauvegarde ;
- gestion centralisée des identités ;
- contrôle des accès ;
- sécurisation progressive de l'infrastructure.

L'objectif du laboratoire est de reproduire, à échelle réduite, une infrastructure représentative d'une PME disposant de plusieurs services métiers et de besoins techniques variés.

---

# 👥 Organisation de l'entreprise

LOGIFLEX compte fictivement **45 collaborateurs**, répartis entre plusieurs pôles métiers.

Pour les besoins de la maquette Active Directory, un échantillon représentatif de **12 comptes utilisateurs** est provisionné.

| Département / Pôle | Principales missions | Effectif réel | Comptes de démonstration |
| --- | --- | :---: | :---: |
| Direction | Pilotage stratégique et gouvernance | 4 | 2 |
| DSI | Infrastructure, cybersécurité et support | 6 | 3 |
| R&D / Ingénierie | Développement et ingénierie logicielle | 15 | 2 |
| Commerce / Marketing | Développement commercial et marketing | 10 | 2 |
| RH / Finance | Gestion RH, comptabilité et finance | 5 | 2 |
| Consulting | Déploiement et accompagnement client | 5 | 1 |
| **TOTAL** | — | **45** | **12** |

---

# 🏢 Départements LOGIFLEX

## 01 — Direction

La Direction assure notamment :

- la gouvernance de l'entreprise ;
- la définition de la stratégie ;
- la prise de décision ;
- les relations avec les partenaires ;
- la conformité et le pilotage global.

---

## 02 — DSI

La Direction des Systèmes d'Information assure notamment :

- l'administration des systèmes ;
- l'administration réseau ;
- la cybersécurité ;
- la gestion des infrastructures ;
- le support informatique ;
- la supervision ;
- la gestion des incidents.

---

## 03 — R&D / Ingénierie

Le pôle Recherche et Développement assure notamment :

- le développement logiciel ;
- l'évolution des solutions WMS et TMS ;
- les tests ;
- l'intégration ;
- les activités DevOps.

---

## 04 — Commerce / Marketing

Ce département assure notamment :

- le développement commercial ;
- la gestion des clients ;
- le marketing ;
- les partenariats ;
- la promotion des solutions LOGIFLEX.

---

## 05 — RH / Finance

Ce pôle assure notamment :

- la gestion des ressources humaines ;
- l'administration du personnel ;
- la comptabilité ;
- le contrôle financier ;
- le suivi administratif.

---

## 06 — Consulting

Le pôle Consulting accompagne les clients dans :

- le déploiement des solutions ;
- l'intégration ;
- la formation ;
- l'accompagnement technique ;
- la gestion de projet.

---

# 👤 Jeu de données Active Directory

Afin de disposer d'un environnement de démonstration réaliste, plusieurs comptes utilisateurs représentatifs des différents services sont créés.

| Collaborateur | Identifiant | Département | Fonction |
| --- | --- | --- | --- |
| Elena ROSTOVA | `erostova` | Direction | Direction générale |
| Liam O'CONNOR | `loconnor` | Direction | Juridique et conformité |
| Marcus VANCE | `mvance` | DSI | Responsable infrastructure et sécurité |
| Amina AL-MANSOOR | `aalmansoor` | DSI | Ingénieure systèmes et réseaux |
| Kenji TANAKA | `ktanaka` | DSI | Technicien support |
| Mateo SILVA | `msilva` | R&D / Ingénierie | Développeur Backend |
| Sven LINDQVIST | `slindqvist` | R&D / Ingénierie | Ingénieur Cloud / DevOps |
| Sarah JENKINS | `sjenkins` | Commerce / Marketing | Responsable commercial |
| Carlos MENDEZ | `cmendez` | Commerce / Marketing | Marketing produit |
| Fatou DIOP | `fdiop` | RH | Responsable RH |
| Lukas WEBER | `lweber` | Finance | Contrôleur financier |
| Priya PATEL | `ppatel` | Consulting | Consultante déploiement |

---

# 🏷️ Convention de nommage des utilisateurs

Les comptes utilisateurs suivent une convention simple et homogène :

```text
Première lettre du prénom + nom
```

Exemples :

```
Marcus VANCE
mvance

Amina AL-MANSOOR
aalmansoor

Carlos MENDEZ
cmendez
```

Cette convention permet :

-   une identification rapide des comptes ;
-   une homogénéité dans l'annuaire ;
-   une administration plus simple ;
-   une meilleure lisibilité lors des audits.

---

# 🌳 Structure Active Directory

L'annuaire Active Directory est organisé autour de l'unité d'organisation principale `LOGIFLEX`.

```
DC=logiflex,DC=infra
│
└── OU=LOGIFLEX
    │
    ├── OU=Departements
    │   │
    │   ├── OU=01_Direction
    │   ├── OU=02_DSI
    │   ├── OU=03_RD_Ingenierie
    │   ├── OU=04_Commerce_Marketing
    │   ├── OU=05_RH_Finance
    │   └── OU=06_Consulting
    │
    ├── OU=Ordinateurs
    │   │
    │   ├── OU=Serveurs
    │   └── OU=Postes_Clients
    │
    ├── OU=Groupes_securite
    │
    └── OU=Comptes_privileges
```

Cette organisation permet de distinguer :

-   les utilisateurs ;
-   les ordinateurs ;
-   les groupes de sécurité ;
-   les comptes privilégiés.

Elle facilite également :

-   l'application future des GPO ;
-   l'administration de l'annuaire ;
-   la gestion des droits ;
-   les contrôles de sécurité ;
-   les audits.

---

# 🔐 Gestion des groupes de sécurité

Les utilisateurs sont regroupés selon leur fonction à l'aide de groupes de sécurité globaux.

Les groupes métiers permettent de représenter les différents pôles de l'entreprise.

## Groupes métiers

| Groupe | Fonction |
| --- | --- |
| GG_Direction | Direction |
| GG_DSI | Direction des systèmes d'information |
| GG_RD_Ingenierie | Recherche et développement |
| GG_Commerce_Marketing | Commerce et marketing |
| GG_RH | Ressources humaines |
| GG_Finance | Finance et comptabilité |
| GG_Consulting | Consulting |

---

# 🔐 Groupes d'administration

Des groupes spécifiques sont également utilisés pour préparer la séparation des différents périmètres d'administration.

| Groupe | Périmètre |
| --- | --- |
| GG_T0_Admins | Administration Active Directory et services d'identité |
| GG_T1_ServerAdmins | Administration des serveurs et services d'infrastructure |
| GG_T2_WorkstationAdmins | Administration des postes clients |

Cette organisation constitue une préparation progressive à une séparation des environnements d'administration.

---

# 🏗️ Modèle de gestion des accès

La gestion des accès s'appuie progressivement sur le modèle **AGDLP**.

```
UTILISATEUR
     │
     ▼
GROUPE GLOBAL
     │
     ▼
GROUPE LOCAL DE DOMAINE
     │
     ▼
RESSOURCE
     │
     ▼
PERMISSION
```

Dans l'environnement LOGIFLEX :

```
Compte utilisateur
        │
        ▼
GG_DSI
        │
        ▼
DL_Ressource_RW
        │
        ▼
Serveur / Partage / Service
        │
        ▼
Lecture / Modification
```

Les groupes locaux de domaine seront mis en œuvre progressivement lors du déploiement des ressources nécessitant un contrôle d'accès spécifique.

---

# 🛡️ Principes de sécurité

La conception de l'environnement LOGIFLEX prend progressivement en compte plusieurs principes de sécurité.

## Principe du moindre privilège

Les comptes disposent uniquement des droits nécessaires à leurs missions.

---

## Séparation des comptes

Les comptes utilisés pour les activités quotidiennes sont distincts des futurs comptes d'administration.

---

## Gestion par groupes

Les permissions ne sont pas attribuées directement aux utilisateurs lorsque l'utilisation d'un groupe est plus adaptée.

---

## Séparation progressive des périmètres d'administration

Les différents environnements d'administration sont structurés progressivement afin de distinguer :

```
Tier 0
│
├── Services d'identité
├── Active Directory
└── Contrôleurs de domaine

Tier 1
│
├── Serveurs
└── Services d'infrastructure

Tier 2
│
├── Postes clients
└── Support utilisateur
```

> ⚠️ Cette séparation est mise en œuvre progressivement dans le cadre du laboratoire et sera complétée par les futures stratégies de sécurité.

---

# 🧩 Infrastructure associée

L'environnement Active Directory constitue le socle des futures briques de l'infrastructure LOGIFLEX.

Les principaux composants prévus sont :

```
                    LOGIFLEX
                        │
                        ▼
               Active Directory
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
    Windows         Services         Sécurité
    Server           Linux              │
        │               │               │
        ├── AD DS       ├── SQL          ├── GPO
        ├── DNS         └── Centreon     ├── Hardening
        └── Hyper-V                       └── Contrôle des accès
                        │
                        ▼
                   Sauvegarde
                     Veeam
```

---

# 📋 Objectif du scénario

Le scénario LOGIFLEX permet de disposer d'un contexte cohérent pour documenter la construction progressive d'une infrastructure d'entreprise.

Chaque composant déployé répond à un besoin identifié :

| Composant | Objectif |
| --- | --- |
| Active Directory | Centralisation des identités |
| DNS | Résolution des ressources |
| Groupes de sécurité | Gestion des accès |
| Comptes privilégiés | Séparation des privilèges |
| Hyper-V | Virtualisation des services |
| SQL Server | Service applicatif |
| Centreon | Supervision |
| GPO | Configuration centralisée |
| Hardening | Réduction de la surface d'exposition |
| Veeam | Sauvegarde et restauration |
