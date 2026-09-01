# 05 — Création des groupes de sécurité et préparation du modèle AGDLP

<br><br>

## 📌 Présentation

Après la mise en place de la structure hiérarchique de l'annuaire Active Directory, cette étape consiste à créer et organiser les **groupes de sécurité** de l'environnement **LOGIFLEX**.

Les groupes de sécurité constituent un élément central de la gestion des identités et du contrôle d'accès basé sur les rôles (**RBAC**).

L'objectif est d'éviter l'attribution directe de permissions aux utilisateurs et de mettre en place une gestion des accès plus structurée, évolutive et facilement administrable.

Cette étape prépare notamment :

-   la gestion des accès selon les fonctions ;
-   l'application du principe du moindre privilège ;
-   la séparation des rôles métiers et administratifs ;
-   la création future des groupes d'accès aux ressources ;
-   l'attribution des permissions aux serveurs et services ;
-   la mise en œuvre progressive du modèle AGDLP ;
-   la préparation de la séparation des périmètres d'administration.

> 💡 **Articulation entre modèle AGDLP et séparation des périmètres d'administration**
> 
> Le modèle **AGDLP** permet de structurer l'attribution des droits aux ressources en évitant l'attribution directe de permissions aux comptes utilisateurs.
> 
> La séparation des périmètres d'administration permet quant à elle de distinguer les différents niveaux de privilèges associés à l'administration de l'infrastructure.
> 
> Dans l'environnement **LOGIFLEX**, ces deux approches sont complémentaires :
> 
> -   **AGDLP** est utilisé pour structurer l'attribution des accès aux ressources ;
> -   les groupes métiers permettent de regrouper les utilisateurs selon leur fonction ;
> -   les groupes d'administration permettent de préparer la séparation des différents périmètres techniques ;
> -   les comptes utilisateurs standards et les comptes d'administration seront séparés ;
> -   les droits seront attribués selon le principe du moindre privilège.

> 🎯 **Objectif :** déployer les groupes de sécurité métiers et administratifs dans l'annuaire `logiflex.infra` et préparer les futurs conteneurs d'accès aux ressources de l'infrastructure.

---

# 1\. 🏗️ Principe de gestion des accès et modèle AGDLP

Le modèle **AGDLP** permet de structurer l'attribution des droits aux ressources :

```
Account
    ↓
Global Group
    ↓
Domain Local Group
    ↓
Permission
```

Dans l'environnement LOGIFLEX :

| Composant | Rôle | Exemple |
| --- | --- | --- |
| A — Account | Compte utilisateur nominatif ou compte de service | prenom.nom |
| G — Global Group | Regroupement selon une fonction ou un rôle | GG_DSI |
| DL — Domain Local Group | Groupe représentant un accès à une ressource | DL_Partage_DSI_RW |
| P — Permission | Permission appliquée à la ressource | Lecture / Modification |

Le fonctionnement peut être représenté de la manière suivante :

```
               UTILISATEUR
             prenom.nom
                   │
                   ▼
             GROUPE GLOBAL
                GG_DSI
                   │
                   ▼
        GROUPE LOCAL DE DOMAINE
           DL_Partage_DSI_RW
                   │
                   ▼
               RESSOURCE
             \\SRV\Partage_DSI
                   │
                   ▼
              PERMISSION
           Lecture / Écriture
```

Cette organisation permet de modifier l'accès d'un utilisateur en agissant principalement sur son appartenance à un groupe, plutôt que de modifier directement les permissions des ressources.

> ℹ️ Dans cette étape, les **groupes globaux** sont créés afin de représenter les rôles métiers et administratifs.
> 
> Les **groupes locaux de domaine** seront créés ultérieurement, lors du déploiement des ressources nécessitant une gestion des accès spécifique.

---

# 2\. 👥 Typologie des groupes créés

Les groupes de sécurité sont répartis en deux catégories principales :

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

Les groupes métiers permettent de structurer les utilisateurs selon leur fonction dans l'entreprise.

Les groupes d'administration permettent quant à eux de préparer la séparation des différents périmètres techniques et des privilèges associés.

---

# 3\. 👔 Création des groupes métiers

Les groupes globaux suivants représentent les différents pôles d'activité de l'entreprise LOGIFLEX :

| Groupe de sécurité | Portée | Département / Fonction |
| --- | --- | --- |
| GG_Direction | Globale | Direction générale |
| GG_DSI | Globale | Direction des systèmes d'information |
| GG_RD_Ingenierie | Globale | Recherche & Développement |
| GG_Commerce_Marketing | Globale | Commerce et marketing |
| GG_RH | Globale | Ressources humaines |
| GG_Finance | Globale | Finance et comptabilité |
| GG_Consulting | Globale | Consultants et prestations |

La convention de nommage retenue est la suivante :

```
GG_<Nom_Du_Service>
```

Exemple :

```
GG_DSI
GG_RH
GG_Finance
```

Configuration :

```
Type    : Sécurité
Portée  : Globale
```

Ces groupes ont vocation à recevoir les comptes utilisateurs standards correspondant aux différents services de l'entreprise.

---

# 4\. 🔐 Création des groupes d'administration

Afin de préparer une séparation progressive des périmètres d'administration, des groupes spécifiques sont créés.

| Groupe | Périmètre | Fonction |
| --- | --- | --- |
| GG_T0_Admins | Tier 0 | Administration Active Directory et contrôleurs de domaine |
| GG_T1_ServerAdmins | Tier 1 | Administration des serveurs et services d'infrastructure |
| GG_T2_WorkstationAdmins | Tier 2 | Administration des postes clients |

L'organisation cible est la suivante :

```
                       ADMINISTRATION
                             │
             ┌───────────────┼───────────────┐
             │               │               │
             ▼               ▼               ▼
           Tier 0          Tier 1          Tier 2
        GG_T0_Admins  GG_T1_ServerAdmins  GG_T2_WorkstationAdmins
             │               │               │
      Active Directory    Serveurs         Postes
       & contrôleurs       membres         clients
```

Cette organisation prépare notamment :

-   la séparation des comptes d'administration ;
-   la limitation des privilèges ;
-   la réduction des risques liés à l'utilisation de comptes fortement privilégiés ;
-   la limitation des déplacements latéraux ;
-   l'application progressive de règles d'administration différenciées selon les périmètres.

> ⚠️ Cette étape prépare la structure des groupes. La séparation complète des comptes administratifs et les règles de restriction associées seront mises en œuvre progressivement dans les étapes suivantes du projet.

---

# 5\. 💻 Création des groupes avec PowerShell

La création des groupes peut être réalisée depuis `SRV-V-DC1` à l'aide de PowerShell.

## Chargement du module Active Directory

```
Import-Module ActiveDirectory
```

La base de l'arborescence Active Directory est définie :

```
$BaseOU = "OU=LOGIFLEX,DC=logiflex,DC=infra"
```

---

## Création des groupes d'administration

```
New-ADGroup `
    -Name "GG_T0_Admins" `
    -GroupScope Global `
    -GroupCategory Security `
    -Path "OU=Groupes,OU=T0_Administration,$BaseOU" `
    -Description "Administrateurs du périmètre Tier 0"

New-ADGroup `
    -Name "GG_T1_ServerAdmins" `
    -GroupScope Global `
    -GroupCategory Security `
    -Path "OU=Groupes,OU=T1_Serveurs,$BaseOU" `
    -Description "Administrateurs du périmètre Tier 1"

New-ADGroup `
    -Name "GG_T2_WorkstationAdmins" `
    -GroupScope Global `
    -GroupCategory Security `
    -Path "OU=Groupes,OU=T2_Utilisateurs_Postes,$BaseOU" `
    -Description "Administrateurs du périmètre Tier 2"
```

---

## Création des groupes métiers

Les groupes métiers sont définis dans un tableau PowerShell :

```
$GroupesMetiers = @(
    @{ Name = "GG_Direction"; Desc = "Membres de la Direction générale" },
    @{ Name = "GG_DSI"; Desc = "Collaborateurs de la DSI" },
    @{ Name = "GG_RD_Ingenierie"; Desc = "Pôle Recherche et Développement" },
    @{ Name = "GG_Commerce_Marketing"; Desc = "Équipe Commerce et Marketing" },
    @{ Name = "GG_RH"; Desc = "Service Ressources Humaines" },
    @{ Name = "GG_Finance"; Desc = "Pôle Finance et Comptabilité" },
    @{ Name = "GG_Consulting"; Desc = "Consultants métier" }
)
```

Les groupes sont ensuite créés automatiquement :

```
foreach ($Grp in $GroupesMetiers) {

    if (-not (Get-ADGroup -Filter "Name -eq '$($Grp.Name)'" -ErrorAction SilentlyContinue)) {

        New-ADGroup `
            -Name $Grp.Name `
            -GroupScope Global `
            -GroupCategory Security `
            -Path "OU=Groupes,OU=T2_Utilisateurs_Postes,$BaseOU" `
            -Description $Grp.Desc

        Write-Host "$($Grp.Name) créé avec succès." -ForegroundColor Green
    }

    else {

        Write-Host "$($Grp.Name) existe déjà." -ForegroundColor Yellow
    }
}
```

Cette vérification permet d'éviter la création d'erreurs lors de l'exécution répétée du script.

---

# 6\. 🗂️ Positionnement des groupes dans l'annuaire

Les groupes sont positionnés dans les unités d'organisation correspondant à leur périmètre.

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
    │
    ├── Admins
    │   └── GG_T2_WorkstationAdmins
    │
    └── Groupes
        ├── GG_Commerce_Marketing
        ├── GG_Consulting
        ├── GG_Direction
        ├── GG_DSI
        ├── GG_Finance
        ├── GG_RD_Ingenierie
        └── GG_RH
```

Cette organisation facilite notamment :

-   l'identification des groupes ;
-   la gestion des comptes ;
-   l'application future de stratégies spécifiques ;
-   l'administration des différents périmètres ;
-   les contrôles et audits.

---

# 7\. 🔎 Vérification des groupes créés

Une fois les groupes créés, leur présence et leurs propriétés sont vérifiées.

## Vérification de l'ensemble des groupes

```
Get-ADGroup `
    -Filter 'Name -like "GG_*"' `
    -SearchBase "OU=LOGIFLEX,DC=logiflex,DC=infra" |
    Format-Table Name, GroupScope, GroupCategory, DistinguishedName -AutoSize
```

Cette commande permet notamment de vérifier :

-   le nom du groupe ;
-   sa portée ;
-   sa catégorie ;
-   son emplacement dans l'annuaire.

---

## Vérification d'un groupe spécifique

Exemple avec `GG_DSI` :

```
Get-ADGroup `
    -Identity "GG_DSI" `
    -Properties Description, MemberOf |
    Format-List Name, Description, MemberOf
```

À ce stade, les groupes sont créés mais ne contiennent pas encore nécessairement les comptes utilisateurs définitifs.

L'ajout des comptes sera réalisé dans une étape dédiée.

---

# 8\. 📊 Bilan de l'étape

| Composant | Rôle | État |
| --- | --- | --- |
| GG_T0_Admins | Groupe d'administration Tier 0 | 🟢 |
| GG_T1_ServerAdmins | Groupe d'administration Tier 1 | 🟢 |
| GG_T2_WorkstationAdmins | Groupe d'administration Tier 2 | 🟢 |
| Groupes métiers | Regroupement des utilisateurs selon leur fonction | 🟢 |
| Portée globale | Préparation du modèle AGDLP | 🟢 |
| Positionnement dans les OU | Organisation des groupes selon leur périmètre | 🟢 |
| Comptes utilisateurs standards | À créer et intégrer aux groupes métiers | 🟡 |
| Comptes d'administration dédiés | À créer selon les périmètres d'administration | 🟡 |
| Groupes locaux de domaine | Accès aux ressources | 🔴 |
| Permissions sur les ressources | NTFS, partages et services | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

L'infrastructure Active Directory `logiflex.infra` dispose désormais d'une structure de groupes de sécurité permettant de préparer la gestion des accès selon les rôles.

L'organisation mise en place distingue désormais :

```
                        logiflex.infra
                              │
               ┌──────────────┴──────────────┐
               │                             │
               ▼                             ▼
        GROUPES MÉTIERS            GROUPES D'ADMINISTRATION
               │                             │
       Fonctions métier                 Périmètres techniques
               │                             │
       Direction / DSI / RH             Tier 0 / Tier 1 / Tier 2
               │                             │
               └──────────────┬──────────────┘
                              │
                              ▼
                  FUTURES RESSOURCES
                              │
                              ▼
                 Groupes locaux de domaine
                              │
                              ▼
                       Permissions
```

Cette étape constitue une base importante pour la future mise en œuvre :

-   du contrôle d'accès basé sur les rôles ;
-   du principe du moindre privilège ;
-   des groupes locaux de domaine ;
-   des permissions sur les ressources ;
-   de la séparation des comptes standards et privilégiés ;
-   des futures stratégies de sécurité Active Directory.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **gestion des utilisateurs et des comptes privilégiés**.

Les principales actions prévues seront notamment :

-   création des comptes utilisateurs nominatifs ;
-   organisation des comptes dans les unités d'organisation ;
-   création des comptes d'administration dédiés ;
-   séparation des comptes standards et administratifs ;
-   attribution des comptes aux groupes de sécurité appropriés ;
-   préparation des différents périmètres d'administration ;
-   application progressive du principe du moindre privilège.
