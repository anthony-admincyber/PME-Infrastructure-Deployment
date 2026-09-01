# 🏢 Contexte d'Entreprise & Organisation — LOGIFLEX

---

## 📌 1. Présentation de l'entreprise

**LOGIFLEX Solutions** est une entreprise fictive spécialisée dans les solutions logicielles dédiées à la **Supply Chain**, aux systèmes de gestion d'entrepôts (**WMS**) et aux solutions de transport (**TMS**).

L'entreprise conçoit, déploie et maintient des solutions destinées à accompagner les organisations dans l'optimisation de leurs activités logistiques.

Dans le cadre de son développement, LOGIFLEX met en place une infrastructure informatique centralisée afin de :

- centraliser la gestion des identités ;
- améliorer l'administration des systèmes ;
- structurer les accès aux ressources ;
- renforcer la sécurité de l'infrastructure ;
- préparer la supervision des services ;
- mettre en place une stratégie de sauvegarde ;
- améliorer la continuité des services.

L'infrastructure s'appuie notamment sur un domaine **Active Directory** centralisé :

```text
logiflex.infra
```

L'objectif est de disposer d'une architecture cohérente permettant de reproduire les principaux composants d'un système d'information d'entreprise dans un environnement de laboratoire.

---

<br>

# 👥 2. Organisation de l'entreprise

LOGIFLEX compte fictivement **45 collaborateurs** répartis entre plusieurs pôles métiers.

Pour les besoins du laboratoire Active Directory, un échantillon représentatif de **12 comptes utilisateurs** est créé.

| Département / Pôle | Missions principales | Effectif théorique | Comptes de démonstration |
| --- | --- | --- | --- |
| Direction Générale | Pilotage et gestion stratégique | 4 | 2 |
| DSI | Infrastructure, systèmes, réseau, sécurité et support | 6 | 3 |
| R&D & Ingénierie | Développement et ingénierie logicielle | 15 | 2 |
| Commerce & Marketing | Développement commercial et marketing | 10 | 2 |
| RH & Finance | Gestion RH, comptabilité et finance | 5 | 2 |
| Consulting | Déploiement et accompagnement client | 5 | 1 |
| TOTAL |  | 45 | 12 |

Cette organisation permet de reproduire différents profils utilisateurs et différents besoins d'accès aux ressources.

---

<br>

# 👤 3. Utilisateurs de démonstration

Les comptes utilisateurs suivants sont utilisés dans l'environnement de laboratoire.

| Collaborateur | Identifiant | Département | Fonction |
| --- | --- | --- | --- |
| Elena ROSTOVA | erostova | Direction | Direction générale |
| Liam O'CONNOR | loconnor | Direction | Juridique et conformité |
| Marcus VANCE | mvance | DSI | Sécurité et infrastructure |
| Amina AL-MANSOOR | aalmansoor | DSI | Systèmes et réseaux |
| Kenji TANAKA | ktanaka | DSI | Support informatique |
| Mateo SILVA | msilva | R&D | Développement |
| Sven LINDQVIST | slindqvist | R&D | Cloud et DevOps |
| Sarah JENKINS | sjenkins | Commerce & Marketing | Développement commercial |
| Carlos MENDEZ | cmendez | Commerce & Marketing | Marketing |
| Fatou DIOP | fdiop | RH & Finance | Ressources humaines |
| Lukas WEBER | lweber | RH & Finance | Finance |
| Priya PATEL | ppatel | Consulting | Déploiement client |

> ℹ️ Les utilisateurs présents dans cette maquette sont fictifs et servent uniquement à représenter différents profils et besoins métiers dans l'environnement Active Directory.

---

<br>

# 🌳 4. Architecture logique Active Directory

L'annuaire Active Directory est structuré autour d'une unité d'organisation principale :

```
DC=logiflex,DC=infra
│
└── OU=LOGIFLEX
```

La structure retenue distingue trois grands périmètres :

-   **T0 — Administration et identité** ;
-   **T1 — Serveurs et services d'infrastructure** ;
-   **T2 — Utilisateurs et postes de travail**.

L'organisation complète est la suivante :

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
        │   │
        │   ├── OU=01_Direction
        │   ├── OU=02_DSI
        │   ├── OU=03_RD_Ingenierie
        │   ├── OU=04_Commerce_Marketing
        │   ├── OU=05_RH_Finance
        │   └── OU=06_Consulting
        │
        ├── OU=Postes_Clients
        │
        ├── OU=Admins
        │
        └── OU=Groupes
```

Cette organisation permet de distinguer les différents périmètres fonctionnels et techniques de l'infrastructure.

---

<br>

# 🔐 5. Organisation des périmètres d'administration

L'infrastructure prépare une séparation progressive des différents périmètres d'administration.

```
                     INFRASTRUCTURE LOGIFLEX
                              │
               ┌──────────────┼──────────────┐
               │              │              │
               ▼              ▼              ▼
             TIER 0         TIER 1         TIER 2
               │              │              │
        Identité et AD      Serveurs     Utilisateurs
         Administration     Services       et postes
```

## Tier 0

Le périmètre **Tier 0** regroupe les composants liés à l'identité et aux éléments les plus sensibles de l'infrastructure.

Il comprend notamment :

-   Active Directory ;
-   contrôleurs de domaine ;
-   services d'identité ;
-   composants DNS intégrés à Active Directory ;
-   comptes disposant de privilèges élevés sur l'annuaire.

## Tier 1

Le périmètre **Tier 1** concerne les serveurs et services de l'infrastructure.

Il comprend notamment :

-   serveurs membres ;
-   services applicatifs ;
-   SQL Server ;
-   supervision ;
-   sauvegarde ;
-   comptes administratifs associés aux serveurs.

## Tier 2

Le périmètre **Tier 2** concerne principalement les utilisateurs et les postes de travail.

Il comprend notamment :

-   comptes utilisateurs standards ;
-   postes clients ;
-   groupes métiers ;
-   administration des postes de travail.

> ⚠️ La séparation complète des périmètres et les restrictions associées seront mises en œuvre progressivement au cours du projet.

---

<br>

# 👥 6. Groupes de sécurité

Les groupes de sécurité constituent un élément central de la gestion des accès.

Deux grandes catégories sont distinguées :

-   les groupes métiers ;
-   les groupes d'administration.

## Groupes métiers

Les groupes métiers permettent de regrouper les utilisateurs selon leur fonction.

| Groupe | Fonction |
| --- | --- |
| GG_Direction | Direction générale |
| GG_DSI | Direction des systèmes d'information |
| GG_RD_Ingenierie | Recherche et développement |
| GG_Commerce_Marketing | Commerce et marketing |
| GG_RH | Ressources humaines |
| GG_Finance | Finance et comptabilité |
| GG_Consulting | Consulting et déploiement |

Ces groupes sont positionnés dans :

```
OU=Groupes
OU=T2_Utilisateurs_Postes
OU=LOGIFLEX
```

---

## Groupes d'administration

Des groupes spécifiques permettent de préparer la séparation des périmètres d'administration.

| Groupe | Périmètre |
| --- | --- |
| GG_T0_Admins | Administration de l'identité et Active Directory |
| GG_T1_ServerAdmins | Administration des serveurs |
| GG_T2_WorkstationAdmins | Administration des postes clients |

Leur positionnement est le suivant :

```
T0_Administration
└── Groupes
    └── GG_T0_Admins

T1_Serveurs
└── Groupes
    └── GG_T1_ServerAdmins

T2_Utilisateurs_Postes
└── Groupes
    └── GG_T2_WorkstationAdmins
```

---

<br>

# 🧩 7. Gestion des accès et modèle AGDLP

La gestion des accès aux ressources sera progressivement organisée selon le modèle **AGDLP**.

```
ACCOUNT
    │
    ▼
GLOBAL GROUP
    │
    ▼
DOMAIN LOCAL GROUP
    │
    ▼
PERMISSION
```

Exemple :

```
Utilisateur
    │
    ▼
GG_DSI
    │
    ▼
DL_Ressource_DSI_RW
    │
    ▼
Serveur / Ressource
    │
    ▼
Lecture / Modification
```

Cette organisation permet :

-   d'éviter l'attribution directe de permissions aux utilisateurs ;
-   de simplifier la gestion des accès ;
-   de faciliter les audits ;
-   d'améliorer la lisibilité des droits ;
-   de préparer la gestion des ressources futures.

Les groupes locaux de domaine seront créés lorsque les ressources concernées seront déployées.

---

<br>

# 🖥️ 8. Infrastructure technique

L'environnement LOGIFLEX comprend plusieurs composants techniques.

| Hôte | Adresse IP | Fonction |
| --- | --- | --- |
| SRV-V-DC1 | 192.168.10.20 | Contrôleur de domaine / DNS |
| SRV-02-DC2 | 192.168.10.21 | Contrôleur de domaine / DNS |
| Services futurs | Selon déploiement | SQL, supervision, sauvegarde |

Les contrôleurs de domaine assurent notamment :

-   l'authentification ;
-   la gestion des identités ;
-   la résolution DNS interne ;
-   la réplication Active Directory ;
-   la disponibilité des services d'annuaire.

---

<br>

# 🎯 9. Principes de sécurité retenus

La conception de l'environnement repose progressivement sur plusieurs principes.

### Principe du moindre privilège

Les utilisateurs et administrateurs doivent disposer uniquement des droits nécessaires à leurs missions.

### Séparation des comptes

Les comptes utilisateurs standards doivent être distincts des comptes utilisés pour les opérations d'administration.

### Gestion par groupes

Les accès aux ressources doivent être attribués à des groupes plutôt que directement aux comptes utilisateurs.

### Séparation progressive des périmètres

Les différents périmètres d'administration sont structurés afin de limiter l'utilisation excessive de privilèges.

### Traçabilité

Les choix d'architecture, les configurations et les tests sont documentés progressivement dans le repository.

---

<br>

# 📊 10. Synthèse

L'environnement LOGIFLEX représente une entreprise fictive de **45 collaborateurs**.

Le laboratoire utilise un échantillon de **12 comptes utilisateurs** afin de simuler les différents départements et besoins métiers.

L'architecture Active Directory est organisée autour de trois périmètres :

```
T0 — Administration / Identité
T1 — Serveurs / Services
T2 — Utilisateurs / Postes
```

La gestion des accès s'appuie progressivement sur :

```
Utilisateurs
      │
      ▼
Groupes de sécurité
      │
      ▼
Groupes d'accès aux ressources
      │
      ▼
Permissions
```

Cette organisation constitue le socle fonctionnel et organisationnel de l'ensemble du projet **LOGIFLEX Infrastructure**.

---

## 🚀 Évolutions prévues

Le contexte d'entreprise évoluera en fonction du développement de l'infrastructure.

Les prochaines étapes concerneront notamment :

-   la création des comptes utilisateurs ;
-   la création des comptes d'administration dédiés ;
-   l'affectation aux groupes de sécurité ;
-   le déploiement des serveurs membres ;
-   l'intégration des services SQL ;
-   la supervision ;
-   la sauvegarde ;
-   la mise en place progressive des groupes locaux de domaine ;
-   l'attribution des permissions aux ressources.
