# 06 — Gestion des utilisateurs et préparation des comptes privilégiés

<br><br>

## 📌 Présentation

Après la création des Unités d'Organisation et des groupes de sécurité, cette étape consiste à mettre en place les **comptes utilisateurs de l'environnement LOGIFLEX**.

Les comptes utilisateurs représentent les identités nominatives des collaborateurs de l'entreprise.

L'objectif est de structurer les identités Active Directory afin de préparer :

- l'authentification centralisée ;
- l'attribution des accès selon les fonctions ;
- l'intégration des utilisateurs dans les groupes de sécurité ;
- l'application du principe du moindre privilège ;
- la séparation progressive des comptes standards et administratifs ;
- la préparation des futurs périmètres d'administration.

> 🎯 **Objectif :** créer les comptes utilisateurs standards de l'environnement LOGIFLEX, les positionner dans les Unités d'Organisation correspondantes et les associer aux groupes de sécurité adaptés à leur fonction.

---

# 1. 👥 Organisation des identités

Dans une infrastructure Active Directory, chaque utilisateur doit disposer d'un compte nominatif.

Le compte utilisateur constitue l'identité principale permettant notamment :

- l'authentification ;
- l'accès aux ressources ;
- l'application des stratégies de sécurité ;
- l'attribution des droits ;
- la traçabilité des actions.

Dans l'environnement LOGIFLEX, les comptes sont organisés selon leur département.

```text
LOGIFLEX
│
├── T2_Utilisateurs_Postes
│   │
│   ├── Utilisateurs
│   │
│   ├── Groupes
│   │
│   └── Admins
│
├── T1_Serveurs
│
└── T0_Administration
```

Les utilisateurs standards sont principalement positionnés dans le périmètre :

```
T2_Utilisateurs_Postes
```

---

# 2\. 🏢 Répartition des utilisateurs

L'environnement de démonstration LOGIFLEX utilise un échantillon représentatif de collaborateurs.

Les comptes sont répartis entre les différents pôles métiers de l'entreprise.

| Collaborateur | Identifiant | Département |
| --- | --- | --- |
| Elena ROSTOVA | erostova | Direction |
| Liam O'CONNOR | loconnor | Direction |
| Marcus VANCE | mvance | DSI |
| Amina AL-MANSOOR | aalmansoor | DSI |
| Kenji TANAKA | ktanaka | DSI |
| Mateo SILVA | msilva | R&D / Ingénierie |
| Sven LINDQVIST | slindqvist | R&D / Ingénierie |
| Sarah JENKINS | sjenkins | Commerce / Marketing |
| Carlos MENDEZ | cmendez | Commerce / Marketing |
| Fatou DIOP | fdiop | RH |
| Lukas WEBER | lweber | Finance |
| Priya PATEL | ppatel | Consulting |

Ces comptes représentent différents profils utilisateurs et permettront de tester la gestion des accès par groupe.

---

# 3\. 🧾 Convention de nommage

Une convention de nommage est appliquée afin d'assurer une identification homogène des comptes.

Le format retenu est :

```
première lettre du prénom + nom
```

Exemples :

```
Elena ROSTOVA
→ erostova

Marcus VANCE
→ mvance

Amina AL-MANSOOR
→ aal-mansoor
```

Dans l'environnement de laboratoire, les identifiants ne doivent pas contenir d'espaces.

La convention retenue doit permettre :

-   une identification simple ;
-   une administration cohérente ;
-   une meilleure lisibilité des journaux ;
-   une simplification de la gestion des comptes.

---

# 4\. 🗂️ Positionnement des comptes dans Active Directory

Les comptes utilisateurs standards sont organisés selon leur périmètre et leur fonction.

L'organisation cible est la suivante :

```
LOGIFLEX
│
└── T2_Utilisateurs_Postes
    │
    ├── Utilisateurs
    │   │
    │   ├── Direction
    │   ├── DSI
    │   ├── RD_Ingenierie
    │   ├── Commerce_Marketing
    │   ├── RH
    │   ├── Finance
    │   └── Consulting
    │
    ├── Groupes
    │
    └── Admins
```

Cette organisation permet de distinguer :

-   les comptes utilisateurs standards ;
-   les groupes de sécurité ;
-   les comptes d'administration.

> ℹ️ Les comptes administratifs ne sont pas utilisés comme comptes utilisateurs standards.

---

# 5\. 👤 Création des comptes utilisateurs

Les comptes peuvent être créés à l'aide de la console :

```
Utilisateurs et ordinateurs Active Directory
```

ou automatisés à l'aide de PowerShell.

Dans le cadre du laboratoire, PowerShell permet de standardiser la création des comptes.

## Chargement du module Active Directory

```powershell
Import-Module ActiveDirectory
```

---

## Définition de la base du domaine

```powershell
$Domain = "logiflex.infra"
```

Le mot de passe initial est défini de manière temporaire pour les besoins du laboratoire.

```powershell
$Password = ConvertTo-SecureString "MotDePasseTemporaire!" -AsPlainText -Force
```

> ⚠️ Dans une infrastructure réelle, un mot de passe en clair ne doit pas être intégré directement dans un script.

---

# 6\. 💻 Création automatisée des utilisateurs

Les utilisateurs sont définis dans une structure PowerShell.

```powershell
$Users = @(

    @{
        FirstName = "Elena"
        LastName  = "Rostova"
        Username  = "erostova"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Direction"
    },

    @{
        FirstName = "Liam"
        LastName  = "OConnor"
        Username  = "loconnor"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Direction"
    },

    @{
        FirstName = "Marcus"
        LastName  = "Vance"
        Username  = "mvance"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_DSI"
    },

    @{
        FirstName = "Amina"
        LastName  = "Al-Mansoor"
        Username  = "aalmansoor"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_DSI"
    },

    @{
        FirstName = "Kenji"
        LastName  = "Tanaka"
        Username  = "ktanaka"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_DSI"
    }
)
```

<img width="977" height="695" alt="image" src="https://github.com/user-attachments/assets/c16c7d87-447d-41be-841e-dc93e4affcd3" />
<img width="911" height="154" alt="image" src="https://github.com/user-attachments/assets/3a0f7bab-5f0c-4e8f-a159-2e2f5af8627d" />



Les comptes sont ensuite créés automatiquement.

```powershell
foreach ($User in $Users) {

    if (-not (Get-ADUser -Filter "SamAccountName -eq '$($User.Username)'" -ErrorAction SilentlyContinue)) {

        New-ADUser `
            -Name "$($User.FirstName) $($User.LastName)" `
            -GivenName $User.FirstName `
            -Surname $User.LastName `
            -SamAccountName $User.Username `
            -UserPrincipalName "$($User.Username)@$Domain" `
            -Path $User.OU `
            -AccountPassword $Password `
            -Enabled $true `
            -ChangePasswordAtLogon $true

        Write-Host "$($User.Username) créé avec succès." -ForegroundColor Green
    }

    else {

        Write-Host "$($User.Username) existe déjà." -ForegroundColor Yellow
    }
}
```

> ℹ️ Le script vérifie l'existence du compte avant de tenter sa création.

---

# 7\. 🔐 Attribution des utilisateurs aux groupes

Après la création des comptes, les utilisateurs sont associés à leurs groupes de sécurité.

Exemple :

```
Utilisateur
     │
     ▼
Compte Active Directory
     │
     ▼
Groupe global
     │
     ▼
Accès futur aux ressources
```

Exemple pour un utilisateur de la DSI :

```
mvance
   │
   ▼
GG_DSI
   │
   ▼
DL_Ressource_RW
   │
   ▼
Ressource
```

L'ajout à un groupe peut être réalisé avec PowerShell :

```
Add-ADGroupMember `
    -Identity "GG_DSI" `
    -Members "mvance"
```

Cette méthode permet d'éviter l'attribution directe de permissions au compte utilisateur.

---

# 8\. 🔐 Préparation des comptes privilégiés

Les comptes utilisateurs standards ne doivent pas être utilisés pour l'administration des composants critiques.

Dans l'environnement LOGIFLEX, la création de comptes d'administration dédiés est préparée.

La logique retenue est la suivante :

```
COMPTE STANDARD
prenom.nom

        │
        │ Usage quotidien
        ▼

Poste utilisateur
Messagerie
Applications


COMPTE ADMINISTRATIF
adm-<niveau>-<identifiant>

        │
        │ Administration uniquement
        ▼

Infrastructure
Serveurs
Active Directory
Postes clients
```

Les comptes privilégiés pourront être distingués selon leur périmètre d'administration.

Exemple :

```
adm-t0-mvance
adm-t1-mvance
adm-t2-mvance
```

| Compte | Périmètre |
| --- | --- |
| adm-t0-* | Administration Active Directory et services critiques |
| adm-t1-* | Administration des serveurs |
| adm-t2-* | Administration des postes clients |

> ⚠️ La création complète des comptes privilégiés et l'application des restrictions associées feront l'objet d'une étape dédiée.

---

# 9\. 🔎 Vérification des comptes créés

Une fois les comptes créés, leur présence peut être vérifiée avec PowerShell.

## Liste des utilisateurs LOGIFLEX

```
Get-ADUser `
    -Filter * `
    -SearchBase "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra" `
    -Properties Department |
    Format-Table Name, SamAccountName, Department, Enabled -AutoSize
```

Cette commande permet notamment de vérifier :

-   la présence des comptes ;
-   leur identifiant ;
-   leur statut ;
-   leur organisation dans Active Directory.

---

## Vérification de l'appartenance aux groupes

Exemple avec un utilisateur :

```
Get-ADPrincipalGroupMembership mvance |
    Select-Object Name, GroupScope
```

Cette commande permet de vérifier les groupes auxquels appartient le compte.

---

# 10\. 📊 Bilan de l'étape

| Composant | Rôle | État |
| --- | --- | --- |
| Comptes utilisateurs standards | Identités nominatives | 🟢 |
| Convention de nommage | Standardisation des identifiants | 🟢 |
| Organisation dans les OU | Structuration des comptes | 🟢 |
| Groupes métiers | Attribution selon les fonctions | 🟢 |
| Attribution aux groupes | Préparation du contrôle d'accès | 🟢 |
| Comptes privilégiés | Structure et convention préparées | 🟡 |
| Comptes Tier 0 | À créer | 🔴 |
| Comptes Tier 1 | À créer | 🔴 |
| Comptes Tier 2 | À créer | 🔴 |
| Restrictions d'administration | À mettre en œuvre | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

L'environnement `logiflex.infra` dispose désormais de comptes utilisateurs organisés selon les fonctions de l'entreprise.

La gestion des identités repose désormais sur la logique suivante :

```
UTILISATEURS
     │
     ▼
COMPTES NOMINATIFS
     │
     ▼
GROUPES DE SÉCURITÉ
     │
     ▼
FUTURES RESSOURCES
     │
     ▼
AUTORISATIONS
```

Les comptes utilisateurs standards constituent désormais la base de la future gestion des accès.

La prochaine étape consistera à poursuivre la sécurisation de l'environnement avec la mise en place progressive des mécanismes liés aux comptes privilégiés, aux stratégies de groupe et au durcissement de l'infrastructure.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **gestion des comptes privilégiés et à la séparation des périmètres d'administration**.

Les principales actions prévues seront notamment :

-   création des comptes d'administration dédiés ;
-   séparation des comptes standards et administratifs ;
-   attribution des comptes aux groupes d'administration ;
-   organisation des comptes privilégiés dans Active Directory ;
-   préparation des règles d'administration différenciées ;
-   mise en œuvre progressive du principe du moindre privilège.
