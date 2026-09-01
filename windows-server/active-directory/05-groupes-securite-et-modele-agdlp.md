# 05 — Création des groupes de sécurité et préparation du modèle AGDLP

## 📌 Présentation

Après la mise en place de la structure logique de l'annuaire Active Directory, cette étape consiste à créer et organiser les **groupes de sécurité** de l'environnement LOGIFLEX.

Les groupes de sécurité constituent un élément central de la gestion des identités et des accès.

L'objectif est d'éviter l'attribution directe de permissions aux utilisateurs et de privilégier une gestion basée sur les groupes.

La démarche retenue prépare notamment la mise en œuvre du modèle :

```
AGDLP
```

Ce modèle permet de structurer progressivement la gestion des accès selon le principe suivant :

```
Accounts
    ↓
Global Groups
    ↓
Domain Local Groups
    ↓
Permissions
```

Dans le contexte LOGIFLEX, cette organisation permettra notamment de faciliter :

-   la gestion des utilisateurs ;
-   l'attribution des droits ;
-   la gestion des accès aux ressources ;
-   l'application du principe du moindre privilège ;
-   la séparation des responsabilités ;
-   l'administration des environnements ;
-   l'audit des droits attribués.

> 🎯 **Objectif : créer une structure de groupes cohérente et préparer la gestion centralisée des accès aux ressources de l'infrastructure LOGIFLEX.**

---

# 1\. 🏗️ Principe de gestion des accès

Dans une infrastructure Active Directory, il est préférable d'éviter d'attribuer directement des permissions à chaque utilisateur.

La logique retenue est la suivante :

```
UTILISATEUR
     │
     ▼
GROUPE DE SÉCURITÉ
     │
     ▼
GROUPE D'ACCÈS
     │
     ▼
RESSOURCE
     │
     ▼
PERMISSION
```

Cette organisation permet de centraliser la gestion des droits.

Par exemple :

```
Anthony
   │
   ▼
GG_DSI
   │
   ▼
Accès aux ressources DSI
   │
   ▼
Permissions attribuées
```

Ainsi, lorsqu'un utilisateur change de fonction, il est possible de modifier son appartenance aux groupes plutôt que de modifier directement les permissions sur chaque ressource.

---

# 2\. 🧩 Présentation du modèle AGDLP

Le modèle **AGDLP** signifie :

```
A  → Accounts
G  → Global Groups
DL → Domain Local Groups
P  → Permissions
```

Il représente une méthode structurée de gestion des accès dans Active Directory.

## Fonctionnement

```
UTILISATEURS
     │
     ▼
GROUPES GLOBAUX
     │
     ▼
GROUPES LOCAUX DE DOMAINE
     │
     ▼
RESSOURCES
     │
     ▼
PERMISSIONS
```

### Exemple

```
Utilisateur
    │
    ▼
GG_DSI
    │
    ▼
DL_Fichier_DSI_RW
    │
    ▼
Partage DSI
    │
    ▼
Lecture / Écriture
```

Dans cette organisation :

-   l'utilisateur représente le compte utilisé par une personne ;
-   le groupe global représente généralement une fonction ou une appartenance métier ;
-   le groupe local de domaine représente l'accès à une ressource ;
-   la permission est appliquée sur la ressource concernée.

> ℹ️ À ce stade du projet, la création des groupes globaux constitue la première étape de la mise en œuvre du modèle AGDLP. Les groupes locaux de domaine et l'attribution des permissions seront utilisés lors du déploiement des ressources concernées.

---

# 3\. 👥 Organisation des groupes dans LOGIFLEX

Les groupes de sécurité sont organisés selon leur fonction.

Deux grandes catégories sont préparées :

```
LOGIFLEX
│
├── Groupes métiers
│
└── Groupes d'administration
```

Cette séparation permet de distinguer :

-   les droits liés aux fonctions des utilisateurs ;
-   les droits liés à l'administration de l'infrastructure.

---

# 4\. 👔 Création des groupes métiers

Les groupes globaux suivants sont créés afin de représenter les différents départements de l'entreprise LOGIFLEX.

| Groupe | Département |
| --- | --- |
| GG_Direction | Direction |
| GG_DSI | Direction des systèmes d'information |
| GG_RD_Ingenierie | Recherche et développement / Ingénierie |
| GG_Commerce_Marketing | Commerce et Marketing |
| GG_RH | Ressources humaines |
| GG_Finance | Finance |
| GG_Consulting | Consulting |

La convention de nommage utilisée est :

```
GG_<Fonction>
```

Par exemple :

```
GG_DSI
```

correspond au groupe global associé aux collaborateurs de la Direction des Systèmes d'Information.

---

# 5\. ⚙️ Création d'un groupe de sécurité Active Directory

Les groupes sont créés depuis la console :

```
Utilisateurs et ordinateurs Active Directory
```

La procédure générale est la suivante :

1.  sélectionner l'OU destinée à recevoir les groupes ;
2.  effectuer un clic droit ;
3.  sélectionner **Nouveau** ;
4.  sélectionner **Groupe** ;
5.  renseigner le nom du groupe ;
6.  sélectionner la portée du groupe ;
7.  sélectionner le type **Sécurité** ;
8.  valider la création.

La configuration retenue pour les groupes métiers est :

```
Type de groupe :
Sécurité

Portée :
Globale
```

---

## 📸 Création des groupes métiers

**Insérer ici une capture montrant les groupes métiers déjà créés :**

```
GG_Commerce_Marketing
GG_Consulting
GG_Direction
GG_DSI
GG_Finance
GG_RD_Ingenierie
GG_RH
```

Cette capture permettra de démontrer concrètement la mise en œuvre de la structure des groupes métiers dans Active Directory.

---

# 6\. 🔐 Groupes d'administration

Des groupes spécifiques sont également créés afin de préparer la séparation des privilèges administratifs.

Les groupes suivants sont utilisés :

| Groupe | Fonction |
| --- | --- |
| GG_T0_Admins | Administration des composants critiques |
| GG_T1_ServerAdmins | Administration des serveurs |
| GG_T2_WorkstationAdmins | Administration des postes clients |

Ces groupes permettent de préparer une séparation logique entre les différents périmètres d'administration.

---

# 7\. 🛡️ Séparation des niveaux administratifs

L'environnement LOGIFLEX utilise une séparation logique des privilèges.

```
                    ADMINISTRATION
                          │
            ┌─────────────┼─────────────┐
            │             │             │
            ▼             ▼             ▼
           T0            T1            T2
            │             │             │
       Identité       Serveurs       Postes
       Active          Membres       Clients
       Directory
```

---

## 🔴 Tier 0 — Administration critique

Le niveau T0 concerne les composants les plus sensibles de l'infrastructure.

Il comprend notamment :

-   Active Directory ;
-   contrôleurs de domaine ;
-   DNS intégré à Active Directory ;
-   comptes disposant de privilèges élevés ;
-   administration de l'annuaire.

Le groupe associé est :

```
GG_T0_Admins
```

---

## 🟠 Tier 1 — Administration des serveurs

Le niveau T1 concerne l'administration des serveurs membres de l'infrastructure.

Les futurs composants concernés pourront notamment inclure :

-   serveurs Windows membres ;
-   serveurs Linux ;
-   serveurs SQL ;
-   services d'infrastructure ;
-   services applicatifs.

Le groupe associé est :

```
GG_T1_ServerAdmins
```

---

## 🟢 Tier 2 — Administration des postes clients

Le niveau T2 concerne principalement :

-   les postes clients ;
-   l'administration des postes utilisateurs ;
-   les opérations de support associées aux environnements utilisateurs.

Le groupe associé est :

```
GG_T2_WorkstationAdmins
```

---

# 8\. 🗂️ Positionnement des groupes dans l'annuaire

Les groupes sont placés dans les unités d'organisation correspondant à leur fonction.

L'organisation logique retenue est la suivante :

```
LOGIFLEX
│
├── T0_Administration
│   │
│   ├── Admins
│   ├── Groupes
│   │   └── GG_T0_Admins
│   ├── Comptes_Service
│   └── Postes_Administration
│
├── T1_Serveurs
│   │
│   ├── Admins
│   ├── Groupes
│   │   └── GG_T1_ServerAdmins
│   ├── Serveurs_Membres
│   └── Comptes_Service
│
└── T2_Utilisateurs_Postes
    │
    ├── Utilisateurs
    ├── Postes_Clients
    ├── Groupes
    │
    └── Admins
```

Les groupes métiers sont associés au périmètre des utilisateurs et de leur fonction dans l'organisation.

Les groupes d'administration sont séparés selon leur niveau d'intervention.

---

# 9\. 📸 Vérification des groupes créés

La console **Utilisateurs et ordinateurs Active Directory** permet de vérifier la présence des groupes créés.

Les groupes métiers visibles dans l'environnement sont notamment :

```
GG_Commerce_Marketing
GG_Consulting
GG_Direction
GG_DSI
GG_Finance
GG_RD_Ingenierie
GG_RH
```

Les groupes d'administration permettent quant à eux de préparer la séparation des privilèges :

```
GG_T0_Admins

GG_T1_ServerAdmins

GG_T2_WorkstationAdmins
```

📸 **Insérer ici ta capture globale montrant les groupes de sécurité dans Active Directory.**

---

# 10\. 🔎 Vérification des propriétés d'un groupe

Les propriétés des groupes permettent notamment de vérifier :

-   le nom ;
-   le type de groupe ;
-   la portée ;
-   l'appartenance des membres.

Pour les groupes métiers créés dans cette étape, la configuration attendue est :

```
Type :
Groupe de sécurité

Portée :
Globale
```

📸 **Insérer ici une capture des propriétés d'un groupe, par exemple `GG_DSI`.**

---

# 11\. 👤 Gestion future des utilisateurs

Les utilisateurs seront associés aux groupes correspondant à leur fonction.

Exemple :

```
Utilisateur
    │
    ▼
GG_DSI
```

Un utilisateur appartenant à la DSI pourra être membre du groupe :

```
GG_DSI
```

Le même principe sera appliqué aux autres départements.

```
Utilisateur Direction
        │
        ▼
   GG_Direction


Utilisateur RH
        │
        ▼
      GG_RH


Utilisateur Finance
        │
        ▼
   GG_Finance
```

L'objectif est de gérer les droits en fonction de la fonction et du besoin d'accès, et non individuellement pour chaque utilisateur.

---

# 12\. 🔐 Principe du moindre privilège

La création des groupes prépare également l'application du principe du **moindre privilège**.

Un utilisateur ou un administrateur ne doit disposer que des droits nécessaires à ses missions.

Le principe retenu peut être représenté de la manière suivante :

```
BESOIN MÉTIER
      │
      ▼
GROUPE APPROPRIÉ
      │
      ▼
DROITS NÉCESSAIRES
      │
      ▼
PAS DE PRIVILÈGES SUPPLÉMENTAIRES
```

Cette logique permettra de limiter :

-   les privilèges excessifs ;
-   l'attribution directe de droits ;
-   la multiplication des permissions individuelles ;
-   les difficultés d'audit ;
-   les risques liés aux comptes disposant de privilèges élevés.

---

# 13\. 🧪 Validation

Les éléments suivants sont vérifiés à l'issue de cette étape.

| Élément | État |
| --- | --- |
| Structure des OU disponible | 🟢 |
| Groupes métiers créés | 🟢 |
| Groupes globaux configurés | 🟢 |
| GG_Direction créé | 🟢 |
| GG_DSI créé | 🟢 |
| GG_RD_Ingenierie créé | 🟢 |
| GG_Commerce_Marketing créé | 🟢 |
| GG_RH créé | 🟢 |
| GG_Finance créé | 🟢 |
| GG_Consulting créé | 🟢 |
| GG_T0_Admins créé | 🟢 |
| GG_T1_ServerAdmins créé | 🟢 |
| GG_T2_WorkstationAdmins créé | 🟢 |
| Préparation du modèle AGDLP | 🟢 |
| Création des groupes locaux de domaine | 🔴 |
| Attribution des permissions aux ressources | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

À l'issue de cette étape, l'infrastructure Active Directory LOGIFLEX dispose d'une organisation de groupes de sécurité permettant de distinguer :

```
UTILISATEURS
    │
    ▼
GROUPES MÉTIERS
```

et :

```
ADMINISTRATEURS
    │
    ▼
GROUPES D'ADMINISTRATION
    │
    ▼
T0 / T1 / T2
```

L'infrastructure prépare également la mise en œuvre progressive du modèle :

```
UTILISATEURS
     ↓
GROUPES GLOBAUX
     ↓
GROUPES LOCAUX DE DOMAINE
     ↓
RESSOURCES
     ↓
PERMISSIONS
```

Les groupes globaux créés dans cette étape serviront ultérieurement à gérer les accès aux différentes ressources de l'environnement LOGIFLEX.

---

# ➡️ Étape suivante

La prochaine étape pourra être consacrée à la **création et à l'organisation des comptes utilisateurs et des comptes administratifs**.

Les actions prévues seront notamment :

-   création des utilisateurs ;
-   organisation des comptes dans les OU ;
-   association des utilisateurs aux groupes métiers ;
-   création des comptes d'administration dédiés ;
-   séparation entre comptes utilisateurs et comptes administratifs ;
-   préparation de l'administration selon les niveaux T0, T1 et T2.

```
Structure Active Directory
        ↓
Groupes de sécurité
        ↓
Création des utilisateurs
        ↓
Association aux groupes
        ↓
Création des comptes administratifs
        ↓
Séparation des privilèges
        ↓
Déploiement progressif des ressources
        ↓
Mise en œuvre complète d'AGDLP
```
