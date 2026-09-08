# 07 — 🔐 Création et gestion des comptes privilégiés

<br> <br>

## 📌 Présentation

Après la création des comptes utilisateurs standards et la préparation du modèle de tiering, cette étape consiste à mettre en place les **comptes administratifs nominatifs** de l'environnement LOGIFLEX.

L'objectif est de séparer les comptes utilisés pour les activités quotidiennes des comptes utilisés pour l'administration de l'infrastructure.

Cette séparation permet notamment de :

- limiter l'exposition des comptes disposant de privilèges élevés ;
- améliorer la traçabilité des opérations d'administration ;
- appliquer le principe du moindre privilège ;
- séparer les différents périmètres d'administration ;
- préparer les futures restrictions d'utilisation des comptes privilégiés.

> 🎯 **Objectif :** créer des comptes administratifs dédiés, les organiser selon leur niveau d'administration et préparer leur intégration aux groupes de sécurité correspondants.

---

# 1. 🛡️ Principe de séparation des comptes

Un compte utilisateur standard ne doit pas être utilisé pour effectuer des opérations d'administration sensibles.

Dans l'environnement LOGIFLEX, un administrateur dispose donc de deux types de comptes :

```text
┌──────────────────────────────┐
│       COMPTE STANDARD        │
│                              │
│       prenom.nom             │
└──────────────┬───────────────┘
               │
               ▼
       Usage quotidien
       Applications
       Messagerie
       Ressources métier


┌──────────────────────────────┐
│      COMPTE PRIVILÉGIÉ       │
│                              │
│  adm-<niveau>-<identifiant>  │
└──────────────┬───────────────┘
               │
               ▼
       Administration
       Serveurs
       Active Directory
       Postes clients
```
Le compte standard est utilisé pour les activités quotidiennes.

Le compte privilégié est utilisé uniquement lorsque l'administrateur doit effectuer une opération nécessitant des droits élevés.

---

# 2\. 🏗️ Modèle de tiering

Le laboratoire LOGIFLEX utilise un modèle de séparation en trois niveaux :

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
└── Stations de travail
```

Le niveau attribué à un compte correspond à son **périmètre d'administration**.

> ℹ️ Le tiering présenté dans ce laboratoire constitue une organisation pédagogique inspirée des principes de séparation des privilèges. Il ne constitue pas une reproduction stricte d'un modèle de sécurité particulier.

---

# 3\. 👤 Comptes administratifs prévus

Les comptes administratifs sont associés nominativement aux administrateurs du scénario LOGIFLEX.

| Administrateur | Compte standard | Niveau | Compte privilégié | Périmètre |
| --- | --- | --- | --- | --- |
| Marcus VANCE | mvance | T0 | adm-t0-mvance | Active Directory / services critiques |
| Amina AL-MANSOOR | aalmansoor | T1 | adm-t1-aalmansoor | Serveurs |
| Kenji TANAKA | ktanaka | T2 | adm-t2-ktanaka | Postes clients |

> 🔐 Ces comptes sont des **comptes administratifs nominatifs de démonstration**. Le niveau T0/T1/T2 définit le périmètre technique d'administration et non le niveau hiérarchique de l'utilisateur.

---

# 4\. 🗂️ Organisation des comptes privilégiés dans Active Directory

Les comptes administratifs sont séparés des comptes utilisateurs standards.

L'organisation retenue est la suivante :

```
LOGIFLEX
│
├── T0_Administration
│   │
│   ├── Admins
│   ├── Groupes
│   ├── Comptes_Service
│   └── Postes_Administration
│
├── T1_Serveurs
│   │
│   ├── Admins
│   ├── Groupes
│   ├── Serveurs_Membres
│   └── Comptes_Service
│
└── T2_Utilisateurs_Postes
    │
    ├── Admins
    ├── Groupes
    ├── Postes_Clients
    └── Utilisateurs
```

Les comptes administratifs sont placés dans l'OU `Admins` correspondant à leur niveau.

```
adm-t0-mvance
        ↓
T0_Administration
        ↓
Admins


adm-t1-aalmansoor
        ↓
T1_Serveurs
        ↓
Admins


adm-t2-ktanaka
        ↓
T2_Utilisateurs_Postes
        ↓
Admins
```

Cette organisation permet de préparer l'application de stratégies de groupe et de règles de sécurité différentes selon le niveau d'administration.

---

# 5\. 🧾 Convention de nommage

Une convention spécifique est utilisée pour les comptes privilégiés.

Le format retenu est :

```
adm-<niveau>-<identifiant>
```

Exemples :

```
adm-t0-mvance
adm-t1-aalmansoor
adm-t2-ktanaka
```

La convention permet d'identifier rapidement :

-   le caractère administratif du compte ;
-   son niveau de tiering ;
-   l'administrateur auquel il est associé.

Elle facilite également la lecture des journaux et l'identification des opérations administratives.

---

# 6\. ⚙️ Création des comptes privilégiés

Les comptes sont créés à l'aide du module Active Directory PowerShell.

```
Import-Module ActiveDirectory
```

Les comptes sont définis dans une structure permettant de centraliser leurs caractéristiques :

```
$PrivilegedUsers = @(

    @{
        FirstName = "Marcus"
        LastName  = "Vance"
        Username  = "adm-t0-mvance"
        OU        = "OU=Admins,OU=T0_Administration,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_T0_Admins"
    },

    @{
        FirstName = "Amina"
        LastName  = "Al-Mansoor"
        Username  = "adm-t1-aalmansoor"
        OU        = "OU=Admins,OU=T1_Serveurs,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_T1_ServerAdmins"
    },

    @{
        FirstName = "Kenji"
        LastName  = "Tanaka"
        Username  = "adm-t2-ktanaka"
        OU        = "OU=Admins,OU=T2_Utilisateurs_Postes,OU=LOGIFLEX,DC=logiflex,DC=infra"
        Group     = "GG_T2_WorkstationAdmins"
    }
)
```

> ⚠️ Le mot de passe initial utilisé dans le laboratoire est temporaire. Dans un environnement de production, la gestion des secrets doit être réalisée avec un mécanisme sécurisé adapté.

---

# 7\. 👤 Création automatisée

La création des comptes peut être automatisée avec PowerShell.

```
$Password = ConvertTo-SecureString "MotDePasseTemporaire!" -AsPlainText -Force

foreach ($User in $PrivilegedUsers) {

    if (-not (Get-ADUser -Filter "SamAccountName -eq '$($User.Username)'" -ErrorAction SilentlyContinue)) {

        New-ADUser `
            -Name "$($User.FirstName) $($User.LastName) - Admin" `
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

Le script vérifie l'existence du compte avant sa création afin d'éviter les doublons.

---

# 8\. 👥 Attribution aux groupes d'administration

Les comptes privilégiés sont ensuite associés aux groupes de sécurité correspondant à leur périmètre.

```
Add-ADGroupMember `
    -Identity "GG_T0_Admins" `
    -Members "adm-t0-mvance"

Add-ADGroupMember `
    -Identity "GG_T1_ServerAdmins" `
    -Members "adm-t1-aalmansoor"

Add-ADGroupMember `
    -Identity "GG_T2_WorkstationAdmins" `
    -Members "adm-t2-ktanaka"
```

La logique devient alors :

```
adm-t0-mvance
       │
       ▼
GG_T0_Admins
       │
       ▼
Administration T0


adm-t1-aalmansoor
       │
       ▼
GG_T1_ServerAdmins
       │
       ▼
Administration T1


adm-t2-ktanaka
       │
       ▼
GG_T2_WorkstationAdmins
       │
       ▼
Administration T2
```

Cette organisation permet de gérer les droits par groupe plutôt que de les attribuer directement aux comptes.

---

# 9\. 🔎 Vérification des comptes

La présence des comptes peut être vérifiée avec :

```
Get-ADUser `
    -Filter 'SamAccountName -like "adm-*"' `
    -SearchBase "OU=LOGIFLEX,DC=logiflex,DC=infra" |
    Select-Object Name, SamAccountName, Enabled, DistinguishedName
```

Le résultat attendu est similaire à :

```
adm-t0-mvance
adm-t1-aalmansoor
adm-t2-ktanaka
```

---

# 10\. 🔐 Vérification des appartenances

Les appartenances aux groupes d'administration peuvent être contrôlées avec :

```
$AdminGroups = @(
    "GG_T0_Admins",
    "GG_T1_ServerAdmins",
    "GG_T2_WorkstationAdmins"
)

foreach ($Group in $AdminGroups) {

    Write-Host "`n===== $Group =====" -ForegroundColor Cyan

    Get-ADGroupMember -Identity $Group |
        Select-Object Name, SamAccountName, ObjectClass |
        Format-Table -AutoSize
}
```

Le résultat attendu est :

```
===== GG_T0_Admins =====
Marcus Vance - Admin    adm-t0-mvance

===== GG_T1_ServerAdmins =====
Amina Al-Mansoor - Admin    adm-t1-aalmansoor

===== GG_T2_WorkstationAdmins =====
Kenji Tanaka - Admin    adm-t2-ktanaka
```

---

# 11\. 🛡️ Séparation entre comptes standards et privilégiés

La séparation peut désormais être représentée de la manière suivante :

```
                 ADMINISTRATEUR
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
       COMPTE STANDARD      COMPTE PRIVILÉGIÉ
             │                   │
             ▼                   ▼
        mvance              adm-t0-mvance
        aalmansoor          adm-t1-aalmansoor
        ktanaka             adm-t2-ktanaka
             │                   │
             ▼                   ▼
      Usage quotidien       Administration
      Applications          Infrastructure
      Messagerie            Services critiques
      Ressources métier
```

Le compte standard reste utilisé pour les activités quotidiennes.

Le compte privilégié n'est utilisé que pour les opérations d'administration correspondant au niveau autorisé.

---

# 12\. ⚠️ Restrictions et durcissement

La création des comptes constitue uniquement la première étape de la gestion des privilèges.

Les mesures de sécurité complémentaires pourront notamment comprendre :

-   restriction des postes depuis lesquels les comptes privilégiés peuvent s'authentifier ;
-   interdiction de l'utilisation des comptes privilégiés pour la navigation Web ;
-   restriction de l'accès aux messageries ;
-   application de GPO spécifiques ;
-   journalisation renforcée des opérations administratives ;
-   contrôle des connexions et authentifications ;
-   limitation des appartenances aux groupes privilégiés ;
-   gestion sécurisée des mots de passe ;
-   mise en place de LAPS pour les comptes administrateurs locaux ;
-   surveillance des événements liés aux comptes à privilèges.

Ces mécanismes seront traités progressivement dans les prochaines étapes de sécurisation.

---

# 13\. 📊 Bilan de l'étape

| Composant | Rôle | État |
| --- | --- | --- |
| Comptes T0 | Administration AD / identité | 🟢 |
| Comptes T1 | Administration serveurs | 🟢 |
| Comptes T2 | Administration postes | 🟢 |
| OU dédiées | Organisation des comptes privilégiés | 🟢 |
| Groupes d'administration | Gestion des périmètres | 🟢 |
| Séparation comptes standards / admins | Réduction de l'exposition | 🟢 |
| Restrictions d'utilisation | Durcissement | 🟡 |
| GPO comptes privilégiés | Contrôle des usages | 🟡 |
| LAPS | Gestion des comptes administrateurs locaux | 🔴 |
| Journalisation renforcée | Traçabilité | 🔴 |

**🟢 Terminé — 🟡 En cours / préparé — 🔴 À réaliser**

---

# 🎯 Résultat

L'environnement `logiflex.infra` dispose désormais de **comptes administratifs nominatifs séparés des comptes utilisateurs standards**.

La gestion des identités repose désormais sur une séparation claire :

```
COMPTE STANDARD
      │
      └── Usage quotidien


COMPTE PRIVILÉGIÉ
      │
      ├── T0 → Active Directory / identité
      ├── T1 → Serveurs
      └── T2 → Postes clients
```

Les comptes privilégiés sont également associés à des groupes d'administration dédiés, permettant de préparer la gestion des autorisations selon les différents périmètres.

Cette organisation constitue une première étape vers une administration plus sécurisée, traçable et conforme au principe du moindre privilège.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **mise en place des stratégies de groupe (GPO)**.

Les principaux objectifs seront notamment :

-   configuration des stratégies de sécurité ;
-   gestion de la politique de mot de passe ;
-   sécurisation des comptes ;
-   configuration du pare-feu Windows ;
-   restrictions liées aux comptes privilégiés ;
-   préparation du durcissement des postes et serveurs ;
-   mise en œuvre progressive des mécanismes de sécurité Active Directory.
