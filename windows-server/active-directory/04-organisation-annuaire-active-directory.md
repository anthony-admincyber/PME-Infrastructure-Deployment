# 04 — Organisation et structuration de l'annuaire Active Directory

<br> <br>

## 📌 Présentation

Après la création de la forêt Active Directory `logiflex.infra` et la mise en place du second contrôleur de domaine, cette étape consiste à organiser et structurer l'annuaire Active Directory de l'environnement **LOGIFLEX**.

L'objectif n'est pas uniquement de créer des utilisateurs et des groupes, mais de mettre en place une structure permettant :

-   d'organiser les objets Active Directory ;
-   de faciliter l'administration de l'environnement ;
-   de séparer les différents niveaux de privilèges ;
-   de préparer l'application des stratégies de groupe ;
-   de limiter les risques liés aux comptes privilégiés ;
-   de faciliter la gestion future des serveurs et postes de travail ;
-   de préparer une administration basée sur les rôles.

L'organisation retenue s'inspire d'un modèle de **cloisonnement logique des environnements d'administration**, avec une séparation progressive entre les différents niveaux de sensibilité de l'infrastructure.

> 🎯 **Objectif :** mettre en place une structure Active Directory claire, évolutive et adaptée à la séparation des utilisateurs, des ressources et des comptes d'administration.

---

# 🏗️ 1. Architecture Active Directory après la mise en place des deux contrôleurs de domaine

L'infrastructure LOGIFLEX dispose désormais de deux contrôleurs de domaine.

```
                         LOGIFLEX
                            │
                     logiflex.infra
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
         SRV-V-DC1                   SRV-02-DC2
       192.168.10.20                192.168.10.21
              │                           │
           AD DS                        AD DS
            DNS                          DNS
              │                           │
              └───────────┬───────────────┘
                          │
                    Réplication AD
```

Les services Active Directory et DNS sont désormais disponibles sur les deux contrôleurs de domaine.

La prochaine étape consiste à structurer les objets qui seront progressivement intégrés dans l'annuaire.

---

# 🔐 2. Principe de séparation des niveaux d'administration

Dans une infrastructure Active Directory, tous les comptes ne présentent pas le même niveau de sensibilité.

Un compte utilisateur standard, un compte permettant d'administrer un poste de travail et un compte administrant Active Directory ne doivent pas disposer du même niveau de privilèges.

La structure retenue distingue donc trois périmètres principaux :

```
                         TIER 0
                            │
             Identité et administration critique
                            │
                ┌───────────┴───────────┐
                │                       │
          Active Directory         DNS / DC
                │
                ▼
                         TIER 1
                            │
              Administration des serveurs
                            │
                ┌───────────┼───────────┐
                │           │           │
             SQL         Centreon      Veeam
                │
                ▼
                         TIER 2
                            │
               Utilisateurs et postes
                            │
                ┌───────────┼───────────┐
                │           │           │
           Utilisateurs    Postes    Support
```

Cette organisation permet de préparer une séparation progressive des privilèges entre :

-   les comptes utilisateurs standards ;
-   les comptes d'administration des postes ;
-   les comptes d'administration des serveurs ;
-   les comptes disposant de privilèges sur l'infrastructure Active Directory.

---

# 🗂️ 3. Structure des unités d'organisation

Une unité d'organisation principale est créée afin de regrouper les objets spécifiques à l'environnement LOGIFLEX.

```
DC=logiflex,DC=infra
│
├── OU=Domain Controllers
│
└── OU=LOGIFLEX
    │
    ├── OU=T0_Administration
    │
    ├── OU=T1_Serveurs
    │
    └── OU=T2_Utilisateurs_Postes
```

Les contrôleurs de domaine restent dans l'unité d'organisation native :

```
OU=Domain Controllers
```

Cette unité d'organisation contient notamment :

```
SRV-V-DC1
SRV-02-DC2
```

Les autres objets de l'environnement sont ensuite organisés selon leur fonction et leur niveau d'administration.

---

# 🔴 4. Organisation du Tier 0

Le **Tier 0** représente le périmètre le plus sensible de l'infrastructure.

Il concerne principalement les composants permettant de contrôler les identités et l'environnement Active Directory.

La structure suivante est mise en place :

```
OU=T0_Administration
│
├── OU=Admins
│
├── OU=Groupes
│
├── OU=Comptes_Service
│
└── OU=Postes_Administration
```

Ce niveau pourra notamment contenir :

-   les comptes d'administration Active Directory ;
-   les groupes disposant de privilèges élevés ;
-   les comptes de service sensibles ;
-   les futurs postes d'administration dédiés.

Exemple de compte :

```
adm-t0.arobert
```

Ce type de compte est réservé aux opérations d'administration du périmètre Active Directory.

---

# 🟠 5. Organisation du Tier 1

Le **Tier 1** correspond principalement au périmètre des serveurs et des services d'infrastructure.

La structure suivante est retenue :

```
OU=T1_Serveurs
│
├── OU=Admins
│
├── OU=Groupes
│
├── OU=Serveurs_Membres
│
└── OU=Comptes_Service
```

Les futurs serveurs de l'environnement pourront notamment être intégrés dans cette unité d'organisation.

```
T1_Serveurs
│
├── SRV-V-SQL
│
├── SRV-V-CENTREON
│
└── SRV-V-VEEAM
```

Les comptes d'administration des serveurs seront séparés des comptes utilisateurs standards.

Exemple :

```
adm-t1.arobert
```

Ce compte sera destiné aux opérations d'administration réalisées sur les serveurs du périmètre Tier 1.

---

# 🟢 6. Organisation du Tier 2

Le **Tier 2** correspond au périmètre utilisateur.

Il regroupe principalement :

-   les utilisateurs standards ;
-   les postes de travail ;
-   les groupes métiers ;
-   les comptes utilisés pour l'administration des postes de travail.

La structure suivante est retenue :

```
OU=T2_Utilisateurs_Postes
│
├── OU=Utilisateurs
│
├── OU=Postes_Clients
│
├── OU=Groupes
│
└── OU=Admins
```

Cette organisation permettra notamment de distinguer clairement les utilisateurs des ressources informatiques.

---

# 👤 7. Séparation des comptes utilisateurs et administrateurs

Un principe important de l'infrastructure LOGIFLEX consiste à ne pas utiliser un compte fortement privilégié pour les activités quotidiennes.

Un administrateur peut donc disposer de plusieurs comptes.

```
Utilisateur
    │
    ├── Compte standard
    │
    ├── Compte administration T2
    │
    ├── Compte administration T1
    │
    └── Compte administration T0
```

Exemple :

| Type de compte | Exemple | Utilisation |
| --- | --- | --- |
| Compte utilisateur | anthony.robert | Activités quotidiennes |
| Compte administrateur T2 | adm-t2.arobert | Administration des postes |
| Compte administrateur T1 | adm-t1.arobert | Administration des serveurs |
| Compte administrateur T0 | adm-t0.arobert | Administration Active Directory |

L'objectif est de limiter l'utilisation des comptes disposant de privilèges élevés.

---

# 👥 8. Organisation des groupes de sécurité

Les groupes de sécurité permettent d'attribuer les droits selon les fonctions et les responsabilités.

La structure prévoit deux grandes catégories de groupes.

## Groupes d'administration

```
GG_T0_Admins
GG_T1_ServerAdmins
GG_T2_WorkstationAdmins
```

Ces groupes permettront d'attribuer progressivement les droits nécessaires à l'administration des différents périmètres.

---

## Groupes métiers

Les groupes métiers permettront de regrouper les utilisateurs selon leur fonction.

Exemple :

```
GG_Direction
GG_DSI
GG_RD_Ingenierie
GG_Commerce_Marketing
GG_RH
GG_Finance
GG_Consulting
```

Ces groupes seront utilisés ultérieurement pour :

-   l'attribution des accès ;
-   la gestion des permissions ;
-   l'application de certaines stratégies ;
-   la gestion des futures ressources de l'entreprise.

---

# 🔑 9. Préparation du modèle RBAC et AGDLP

La gestion des autorisations sera progressivement organisée selon une approche basée sur les rôles.

Le modèle suivant sera utilisé lorsque les ressources nécessitant une gestion des accès seront déployées :

```
Utilisateur
    │
    ▼
Groupe Global
    │
    ▼
Groupe Local de Domaine
    │
    ▼
Ressource
    │
    ▼
Permission
```

Cette logique peut être représentée ainsi :

```
A
│
▼
G
│
▼
DL
│
▼
P
```

Soit :

```
Account
   ↓
Global Group
   ↓
Domain Local Group
   ↓
Permission
```

Les groupes globaux représenteront principalement les utilisateurs ou les rôles.

Les groupes locaux de domaine seront utilisés pour attribuer les autorisations sur les ressources.

Cette organisation sera particulièrement utile lorsque les services suivants seront intégrés :

-   serveurs de fichiers ;
-   bases de données ;
-   applications métier ;
-   services SQL ;
-   autres ressources du domaine.

---

# 🛠️ 10. Création des unités d'organisation

Les unités d'organisation peuvent être créées depuis la console :

```
Utilisateurs et ordinateurs Active Directory
```

L'organisation retenue est la suivante :

```
logiflex.infra
│
├── Domain Controllers
│
└── LOGIFLEX
    │
    ├── T0_Administration
    │   ├── Admins
    │   ├── Groupes
    │   ├── Comptes_Service
    │   └── Postes_Administration
    │
    ├── T1_Serveurs
    │   ├── Admins
    │   ├── Groupes
    │   ├── Serveurs_Membres
    │   └── Comptes_Service
    │
    └── T2_Utilisateurs_Postes
        ├── Utilisateurs
        ├── Postes_Clients
        ├── Groupes
        └── Admins
```

> 💡 La structure est conçue pour évoluer progressivement en fonction du déploiement des nouvelles briques de l'infrastructure.

---

# 🔒 11. Protection des unités d'organisation

Les unités d'organisation créées sont protégées contre la suppression accidentelle.

Cette protection permet de réduire le risque de suppression involontaire d'une structure contenant des objets Active Directory.

Le principe est appliqué notamment aux unités d'organisation principales :

```
LOGIFLEX
│
├── T0_Administration
├── T1_Serveurs
└── T2_Utilisateurs_Postes
```

---

# 🔎 12. Vérification de la structure

Après la création des unités d'organisation, la structure est vérifiée depuis :

```
Utilisateurs et ordinateurs Active Directory
```

Les points suivants sont contrôlés :

-   présence de l'OU principale LOGIFLEX ;
-   présence des unités d'organisation T0 ;
-   présence des unités d'organisation T1 ;
-   présence des unités d'organisation T2 ;
-   séparation des différents types d'objets ;
-   protection contre la suppression accidentelle.

L'objectif est d'obtenir une structure claire et facilement identifiable.

---

# 📊 13. Bilan de l'étape

| Élément | État |
| --- | --- |
| OU principale LOGIFLEX créée | 🟢 |
| Structure Tier 0 créée | 🟢 |
| Structure Tier 1 créée | 🟢 |
| Structure Tier 2 créée | 🟢 |
| OU comptes administrateurs créées | 🟢 |
| OU groupes créées | 🟢 |
| OU serveurs membres créée | 🟢 |
| OU utilisateurs créée | 🟢 |
| OU postes clients créée | 🟢 |
| Protection contre la suppression | 🟢 |
| Groupes métiers | 🟡 |
| Comptes utilisateurs | 🟡 |
| Comptes privilégiés | 🟡 |
| Délégation des droits | 🔴 |
| GPO de sécurité | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

À l'issue de cette étape, l'annuaire Active Directory `logiflex.infra` dispose d'une structure organisée permettant de séparer les différents types d'objets et les principaux niveaux d'administration.

L'infrastructure est désormais préparée pour accueillir progressivement :

-   les comptes utilisateurs ;
-   les groupes métiers ;
-   les comptes d'administration ;
-   les serveurs membres ;
-   les postes clients ;
-   les comptes de service ;
-   les futures stratégies de groupe.

L'architecture logique devient :

```
                    logiflex.infra
                           │
            ┌──────────────┴──────────────┐
            │                             │
      Domain Controllers              LOGIFLEX
            │                             │
            │              ┌──────────────┼──────────────┐
            │              │              │              │
         DC1 / DC2        Tier 0         Tier 1         Tier 2
                            │              │              │
                         Identité       Serveurs     Utilisateurs
                            │              │              │
                         Admins         SQL          Postes
                         Groupes        Veeam        Groupes
                         Services       Centreon     Support
```

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **création et à l'organisation des utilisateurs et des groupes de sécurité**.

Les actions prévues seront notamment :

-   création des comptes utilisateurs ;
-   création des groupes métiers ;
-   création des groupes d'administration ;
-   affectation des utilisateurs aux groupes ;
-   préparation des comptes d'administration dédiés ;
-   mise en œuvre progressive du modèle RBAC ;
-   préparation de la délégation des droits.

```
Structure Active Directory
        ↓
Création des groupes
        ↓
Création des utilisateurs
        ↓
Affectation aux groupes
        ↓
Séparation des comptes privilégiés
        ↓
Délégation des droits
        ↓
Mise en place des GPO
        ↓
Durcissement Active Directory
```
