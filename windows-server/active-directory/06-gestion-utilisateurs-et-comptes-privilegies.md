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
    },

    @{
        FirstName = "Mateo"
        LastName  = "Silva"
        Username  = "msilva"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_RD_Ingenierie"
    },

    @{
        FirstName = "Sven"
        LastName  = "Lindqvist"
        Username  = "slindqvist"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_RD_Ingenierie"
    },

    @{
        FirstName = "Sarah"
        LastName  = "Jenkins"
        Username  = "sjenkins"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Commerce_Marketing"
    },

    @{
        FirstName = "Carlos"
        LastName  = "Mendez"
        Username  = "cmendez"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Commerce_Marketing"
    },

    @{
        FirstName = "Fatou"
        LastName  = "Diop"
        Username  = "fdiop"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_RH"
    },

    @{
        FirstName = "Lukas"
        LastName  = "Weber"
        Username  = "lweber"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Finance"
    },

    @{
        FirstName = "Priya"
        LastName  = "Patel"
        Username  = "ppatel"
        OU        = "OU=Utilisateurs,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_Consulting"
    }
)
```

<img width="932" height="693" alt="image" src="https://github.com/user-attachments/assets/257ed529-d71f-4497-a3bd-1663ca7aad6e" />
<img width="942" height="748" alt="image" src="https://github.com/user-attachments/assets/84dd61c7-aea3-43c0-a4f6-dd6d8d659937" />
<img width="934" height="454" alt="image" src="https://github.com/user-attachments/assets/65d0a997-96de-411d-9ae2-f7ad23b7b5ee" />


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

<img width="1031" height="668" alt="image" src="https://github.com/user-attachments/assets/7efe962d-9570-4f54-b3ef-dffa18b96463" />

<img width="1433" height="493" alt="image" src="https://github.com/user-attachments/assets/0b782cd2-a894-4bb6-851d-9ff39043cb67" />

---

#  🔐 Attribution des utilisateurs aux groupes

Après la création des comptes, chaque utilisateur est associé au **groupe global correspondant à son département**.

Cette organisation permet de séparer :

-   les comptes utilisateurs ;
-   les rôles métiers ;
-   les permissions sur les ressources ;
-   les futures autorisations d'administration.

La logique retenue est la suivante :

```
Utilisateur
     │
     ▼
Compte Active Directory
     │
     ▼
Groupe global (GG_*)
     │
     ▼
Groupe Domain Local (DL_*)
     │
     ▼
Permission sur la ressource
```

Cette organisation prépare la mise en œuvre du modèle **AGDLP** :

```
A → G → DL → P
│   │    │    │
│   │    │    └── Permission
│   │    └────── Domain Local Group
│   └─────────── Global Group
└─────────────── Account
```

## 👥 Répartition des 12 utilisateurs

| Utilisateur | Département | Groupe global |
| --- | --- | --- |
| erostova | Direction | GG_Direction |
| loconnor | Direction | GG_Direction |
| mvance | DSI | GG_DSI |
| aalmansoor | DSI | GG_DSI |
| ktanaka | DSI | GG_DSI |
| msilva | R&D / Ingénierie | GG_RD_Ingenierie |
| slindqvist | R&D / Ingénierie | GG_RD_Ingenierie |
| sjenkins | Commerce / Marketing | GG_Commerce_Marketing |
| cmendez | Commerce / Marketing | GG_Commerce_Marketing |
| fdiop | RH | GG_RH |
| lweber | Finance | GG_Finance |
| ppatel | Consulting | GG_Consulting |

---

## ⚙️ Attribution avec PowerShell

L'ajout des utilisateurs aux groupes peut être automatisé avec PowerShell.

```powershell
Import-Module ActiveDirectory

Add-ADGroupMember -Identity "GG_Direction" -Members "erostova","loconnor"

Add-ADGroupMember -Identity "GG_DSI" -Members "mvance","aalmansoor","ktanaka"

Add-ADGroupMember -Identity "GG_RD_Ingenierie" -Members "msilva","slindqvist"

Add-ADGroupMember -Identity "GG_Commerce_Marketing" -Members "sjenkins","cmendez"

Add-ADGroupMember -Identity "GG_RH" -Members "fdiop"

Add-ADGroupMember -Identity "GG_Finance" -Members "lweber"

Add-ADGroupMember -Identity "GG_Consulting" -Members "ppatel"
```

L'utilisation de groupes permet d'éviter d'attribuer directement des permissions aux comptes utilisateurs.

Par exemple :

```
mvance
   │
   ▼
GG_DSI
   │
   ▼
DL_Partage_DSI_RW
   │
   ▼
\\SRV-FS01\Partage-DSI
```

Ainsi, si un utilisateur change de fonction, il suffit de modifier son appartenance aux groupes concernés plutôt que de modifier individuellement les permissions sur chaque ressource.

<img width="969" height="293" alt="image" src="https://github.com/user-attachments/assets/6c3133a7-3df6-4e0c-b21e-ff69e38170c3" />

---

## 🔎 Vérification des appartenances

Les appartenances peuvent être vérifiées avec :

```powershell
Get-ADGroupMember -Identity "GG_DSI"
```

<img width="1041" height="441" alt="image" src="https://github.com/user-attachments/assets/e6f3cc6a-e1c0-44b6-9f66-3372070d3e6a" />


Pour contrôler l'ensemble des groupes :

```powershell
$Groups = @(
    "GG_Direction",
    "GG_DSI",
    "GG_RD_Ingenierie",
    "GG_Commerce_Marketing",
    "GG_RH",
    "GG_Finance",
    "GG_Consulting"
)

foreach ($Group in $Groups) {
    Write-Host "`n===== $Group =====" -ForegroundColor Cyan

    Get-ADGroupMember -Identity $Group |
        Select-Object Name, SamAccountName, ObjectClass |
        Format-Table -AutoSize
}
```

<img width="578" height="632" alt="image" src="https://github.com/user-attachments/assets/9cc24159-c826-4f7b-9cff-4ffa3b98fe9d" />
<img width="570" height="580" alt="image" src="https://github.com/user-attachments/assets/4ccf3b09-905c-417a-b88a-808e07a970d5" />




Cette vérification permet de contrôler que les **12 comptes** ont été correctement associés à leur groupe métier.

---

# 8\. 🔐 Préparation des comptes privilégiés

Les comptes utilisateurs standards ne doivent pas être utilisés pour administrer les composants critiques de l'infrastructure.

LOGIFLEX applique donc une logique de **séparation entre les comptes standards et les comptes d'administration**.

```
┌──────────────────────────────┐
│       COMPTE STANDARD        │
│                              │
│       prenom.nom             │
└──────────────┬───────────────┘
               │
               │ Usage quotidien
               ▼
       Poste utilisateur
       Messagerie
       Applications
       Ressources métier


┌──────────────────────────────┐
│      COMPTE ADMINISTRATIF    │
│                              │
│   adm-<niveau>-<identifiant> │
└──────────────┬───────────────┘
               │
               │ Administration uniquement
               ▼
       Infrastructure
       Serveurs
       Active Directory
       Postes clients
```

Cette séparation limite notamment le risque qu'un compte disposant de privilèges élevés soit utilisé pour des activités quotidiennes telles que la navigation Web ou la messagerie.

---

## 👤 Utilisateurs concernés

Dans le scénario LOGIFLEX, les comptes d'administration sont principalement associés aux membres de la **DSI**.

Les trois utilisateurs DSI sont :

| Utilisateur | Fonction | Compte standard |
| --- | --- | --- |
| Marcus VANCE | DSI | mvance |
| Amina AL-MANSOOR | DSI | aalmansoor |
| Kenji TANAKA | DSI | ktanaka |

Des comptes administratifs distincts pourront être créés selon le niveau d'administration nécessaire.

### 🔐 Modèle de comptes privilégiés

| Administrateur | Niveau | Compte prévu | Périmètre |
| --- | --- | --- | --- |
| Marcus VANCE | T0 | adm-t0-mvance | Active Directory / services critiques |
| Amina AL-MANSOOR | T1 | adm-t1-aalmansoor | Serveurs |
| Kenji TANAKA | T2 | adm-t2-ktanaka | Postes clients |

La logique retenue est :

```
T0
│
├── Active Directory
├── Contrôleurs de domaine
└── Services d'identité critiques

T1
│
├── Serveurs membres
├── Services applicatifs
└── Infrastructure serveur

T2
│
├── Postes clients
└── Administration des stations de travail
```

> 🔐 Cette organisation s'inspire du principe de **tiering administratif** et des principes de séparation des privilèges. Elle vise à limiter l'utilisation d'un compte disposant de privilèges élevés sur des systèmes de niveau inférieur.

---

## 🛡️ Principe de séparation

Un administrateur ne doit pas utiliser son compte privilégié pour ses activités quotidiennes.

```
                    ADMINISTRATEUR
                          │
              ┌───────────┴───────────┐
              │                       │
              ▼                       ▼
        Compte standard          Compte privilégié
          mvance                 adm-t0-mvance
              │                       │
              ▼                       ▼
       Usage quotidien          Administration
       Applications             Active Directory
       Messagerie               Services critiques
```

Cette séparation permet de réduire la surface d'attaque associée aux comptes à privilèges.

> ⚠️ La création complète des comptes privilégiés, leur placement dans les OU dédiées, ainsi que les restrictions d'utilisation feront l'objet d'une **étape dédiée au durcissement et à la gestion des privilèges**.
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
