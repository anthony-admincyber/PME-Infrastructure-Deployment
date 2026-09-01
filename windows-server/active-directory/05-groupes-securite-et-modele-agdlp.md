# 05 — Création des groupes de sécurité et préparation du modèle AGDLP

## 📌 Présentation

Après la mise en place de la structure hiérarchique de l'annuaire Active Directory, cette étape consiste à créer et organiser les **groupes de sécurité** de l'environnement **LOGIFLEX**.

Les groupes de sécurité constituent le pivot de la gestion des identités et du contrôle d'accès basé sur les rôles (**RBAC**). L'attribution directe de droits aux utilisateurs est proscrite au profit d'une gouvernance structurée.

> 💡 **Articulation méthodologique : Tiering (ANSSI) vs AGDLP (Microsoft / RBAC)**
> 
> * **Le modèle en 3 Tiers (ANSSI-PA-099) :** Définit les **périmètres de sécurité et les frontières étanches** afin de bloquer l'élévation de privilèges et les déplacements latéraux (Tier 0 pour l'identité, Tier 1 pour les serveurs et données, Tier 2 pour les postes et utilisateurs).
> * **Le modèle AGDLP :** Fournit la **mécanique technique d'attribution des droits (RBAC)** en évitant d'assigner des permissions directement aux comptes utilisateurs.
> 
> Dans l'environnement **LOGIFLEX**, la méthode AGDLP est appliquée **à l'intérieur de chaque Tier**, avec une règle d'étanchéité stricte : aucun groupe d'un Tier inférieur ne peut être imbriqué dans un groupe d'un Tier supérieur.

> 🎯 **Objectif :** Déployer les groupes de sécurité métiers et administratifs dans l'annuaire `logiflex.infra` et préparer les conteneurs d'accès aux futures ressources (fichiers, bases de données, supervision).

---

# 1. 🏗️ Principe de gestion des accès et modèle AGDLP

Le modèle **AGDLP** (*Account $\rightarrow$ Global Group $\rightarrow$ Domain Local Group $\rightarrow$ Permission*) structure le cycle d'autorisation :

| Composant | Rôle dans l'infrastructure | Exemple LOGIFLEX |
| :--- | :--- | :--- |
| **A** (*Account*) | Identité nominative de l'utilisateur ou du compte de service | `anthony.robert` |
| **G** (*Global Group*) | Regroupement logique selon le rôle métier ou l'équipe | `GG_DSI` |
| **DL** (*Domain Local Group*) | Représentation d'une autorisation sur une ressource précise | `DL_Partage_DSI_RW` |
| **P** (*Permission*) | Niveau de droit appliqué sur la ressource (ACL NTFS / Partage) | Modification / Écriture |

```text
               UTILISATEUR
             anthony.robert
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

> ℹ️ Les groupes globaux (**GG**) sont créés dans cette phase. Les groupes locaux de domaine (**DL**) et les listes de contrôle d'accès (**ACL**) seront instanciés lors du déploiement des serveurs de fichiers et applicatifs (`SRV-V-SQL`, etc.).

# 2\. 👥 Typologie des groupes créés

Les groupes de sécurité sont répartis en deux catégories distinctes au sein des Unités d'Organisation :

Plaintext

```
LOGIFLEX
│
├── Groupes d'administration (Tiers T0, T1, T2)
│
└── Groupes métiers (Tier 2)
```

# 3\. 👔 Définition des groupes métiers (Tier 2)

Les groupes globaux suivants représentent les différents pôles d'activité de l'entreprise LOGIFLEX :

| Groupe de sécurité | Portée | Département / Fonction |
| --- | --- | --- |
| GG_Direction | Globale | Direction générale et membres du comité de direction |
| GG_DSI | Globale | Direction des systèmes d'information, ingénieurs et techniciens |
| GG_RD_Ingenierie | Globale | Pôle Recherche & Développement, ingénierie logicielle |
| GG_Commerce_Marketing | Globale | Équipe commerciale et marketing |
| GG_RH | Globale | Gestion des ressources humaines et paie |
| GG_Finance | Globale | Comptabilité, finance et contrôle de gestion |
| GG_Consulting | Globale | Consultants métier et prestations clients |

Convention de nommage retenue :

👉 **`GG_<Nom_Du_Service>`** (Type : _Sécurité_, Portée : _Globale_).

# 4\. 🔐 Définition des groupes d'administration (Modèle ANSSI)

Afin d'isoler les périmètres administratifs et d'interdire la latéralisation des attaques, des groupes dédiés sont créés pour chaque niveau de confiance :

PDF

| Groupe d'administration | Tier | Périmètre d'intervention |
| --- | --- | --- |
| GG_T0_Admins | Tier 0 | Contrôleurs de domaine, annuaire Active Directory, zones DNS racines[cite: 1] |
| GG_T1_ServerAdmins | Tier 1 | Serveurs membres d'infrastructure (SRV-V-SQL, SRV-V-Centreon, Veeam)[cite: 1] |
| GG_T2_WorkstationAdmins | Tier 2 | Parc de postes clients de travail, imprimantes, support utilisateur[cite: 1] |

Plaintext

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
       & Contrôleurs       Membres        Clients
```

# 5\. 💻 Création automatisée avec PowerShell

La création de l'ensemble des groupes au sein de leurs Unités d'Organisation respectives est exécutée sur `SRV-V-DC1` :

PowerShell

```
# ==============================================================================
# SCRIPT DE CRÉATION DES GROUPES DE SÉCURITÉ GLOBAUX - LOGIFLEX
# ==============================================================================

Import-Module ActiveDirectory
$BaseOU = "OU=LOGIFLEX,DC=logiflex,DC=infra"

# 1. Création des groupes d'administration (T0, T1, T2)
New-ADGroup -Name "GG_T0_Admins" -GroupScope Global -GroupCategory Security `
    -Path "OU=Groupes,OU=T0_Administration,$BaseOU" `
    -Description "Administrateurs du Tier 0 (Active Directory / DC)"

New-ADGroup -Name "GG_T1_ServerAdmins" -GroupScope Global -GroupCategory Security `
    -Path "OU=Groupes,OU=T1_Serveurs,$BaseOU" `
    -Description "Administrateurs du Tier 1 (Serveurs membres et services)"

New-ADGroup -Name "GG_T2_WorkstationAdmins" -GroupScope Global -GroupCategory Security `
    -Path "OU=Admins,OU=T2_Utilisateurs_Postes,$BaseOU" `
    -Description "Administrateurs du Tier 2 (Postes clients)"

# 2. Création des groupes globaux métiers (Tier 2)
$GroupesMetiers = @(
    @{ Name = "GG_Direction"; Desc = "Membres de la Direction générale" },
    @{ Name = "GG_DSI"; Desc = "Collaborateurs de la DSI" },
    @{ Name = "GG_RD_Ingenierie"; Desc = "Pôle Recherche et Développement" },
    @{ Name = "GG_Commerce_Marketing"; Desc = "Équipe Commerciale et Marketing" },
    @{ Name = "GG_RH"; Desc = "Service Ressources Humaines" },
    @{ Name = "GG_Finance"; Desc = "Pôle Finance et Comptabilité" },
    @{ Name = "GG_Consulting"; Desc = "Consultants métiers" }
)

foreach ($Grp in $GroupesMetiers) {
    New-ADGroup -Name $Grp.Name -GroupScope Global -GroupCategory Security `
        -Path "OU=Groupes,OU=T2_Utilisateurs_Postes,$BaseOU" `
        -Description $Grp.Desc
}

Write-Host "Tous les groupes de sécurité ont été créés avec succès." -ForegroundColor Green
```

# 6\. 🗂️ Positionnement des groupes dans l'annuaire

Les groupes sont positionnés dans les Unités d'Organisation adaptées à leur périmètre :

Plaintext

```
LOGIFLEX
│
├── T0_Administration
│   ├── Admins
│   ├── Groupes
│   │   └── GG_T0_Admins
│   ├── Comptes_Service
│   └── Postes_Administration
│
├── T1_Serveurs
│   ├── Admins
│   ├── Groupes
│   │   └── GG_T1_ServerAdmins
│   ├── Serveurs_Membres
│   └── Comptes_Service
│
└── T2_Utilisateurs_Postes
    ├── Utilisateurs
    ├── Postes_Clients
    ├── Admins
    │   └── GG_T2_WorkstationAdmins
    └── Groupes
        ├── GG_Commerce_Marketing
        ├── GG_Consulting
        ├── GG_Direction
        ├── GG_DSI
        ├── GG_Finance
        ├── GG_RD_Ingenierie
        └── GG_RH
```

# 7\. 🔎 Vérification et audit des groupes créés

### Contrôle PowerShell des groupes créés :

PowerShell

```
Get-ADGroup -Filter 'Name -like "GG_*"' -SearchBase "OU=LOGIFLEX,DC=logiflex,DC=infra" | 
    Format-Table Name, GroupScope, GroupCategory, DistinguishedName -AutoSize
```

### Vérification des propriétés d'un groupe (`GG_DSI`) :

PowerShell

```
Get-ADGroup -Identity "GG_DSI" -Properties Description, MemberOf, Members
```

# 8\. 📊 Bilan de l'étape

| Composant | Rôle | État |
| --- | --- | --- |
| GG_T0_Admins | Groupe d'administration Tier 0 | 🟢 |
| GG_T1_ServerAdmins | Groupe d'administration Tier 1 | 🟢 |
| GG_T2_WorkstationAdmins | Groupe d'administration Tier 2 | 🟢 |
| Groupes métiers (7 départements) | Direction, DSI, R&D, Commerce, RH, Finance, Consulting | 🟢 |
| Portée des groupes (Globale) | Optimisation de la réplication et du modèle AGDLP | 🟢 |
| Positionnement dans les OU | Respect de la segmentation ANSSI[cite: 1] | 🟢 |
| Création des comptes utilisateurs | Population des comptes standards et administrateurs | 🟡 |
| Groupes locaux de domaine (DL) | Création lors de l'intégration des ressources | 🔴 |
| Attribution des permissions NTFS | Mise en œuvre sur partages et serveurs | 🔴 |

# 🎯 Résultat

L'infrastructure Active Directory `logiflex.infra` dispose désormais d'une structure de groupes de sécurité prête pour l'application du modèle **AGDLP** et du principe de moindre privilège\[cite: 1\].

Plaintext

```
                        logiflex.infra
                              │
               ┌──────────────┴──────────────┐
               │                             │
        GROUPES MÉTIERS            GROUPES D'ADMINISTRATION
          (Fonctions)                   (Tiers ANSSI)
               │                             │
    GG_DSI, GG_RH, GG_DIR...        T0 / T1 / T2 Admins
```

## ➡️ Étape suivante

La prochaine étape (`06-gestion-utilisateurs-et-comptes-privilegies.md`) sera consacrée au **provisionnement des comptes utilisateurs nominatifs** et à la **création des comptes d'administration dédiés** (`adm-t0.*`, `adm-t1.*`, `adm-t2.*`).

```

<FollowUp label="Veux-tu qu'on rédige la page 06 pour la création des utilisateurs et comptes à privilèges ?" query="Rédige la documentation Markdown de la page 06 : 06-gestion-utilisateurs-et-comptes-privilegies.md pour provisionner les utilisateurs et les comptes d'administration T0/T1/T2."/>
```
