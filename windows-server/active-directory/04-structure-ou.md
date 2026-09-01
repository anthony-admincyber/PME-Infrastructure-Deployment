# 04 — Organisation et structuration de l'annuaire Active Directory

## 📌 Présentation

Après la mise en place des deux contrôleurs de domaine `SRV-V-DC1` et `SRV-02-DC2`, l'infrastructure Active Directory de l'environnement LOGIFLEX est désormais opérationnelle.

Cette étape consiste à organiser l'annuaire afin de préparer la gestion des :

-   utilisateurs ;
-   postes de travail ;
-   serveurs ;
-   groupes ;
-   comptes d'administration ;
-   ressources de l'infrastructure.

L'objectif est de mettre en place une structure Active Directory claire, évolutive et adaptée à une organisation de type PME.

> 🎯 **Objectif :** structurer l'annuaire `logiflex.infra` afin de faciliter l'administration, l'application des stratégies de sécurité et l'évolution future de l'infrastructure.

---

# 🏗️ 1. Architecture Active Directory

L'environnement LOGIFLEX repose désormais sur deux contrôleurs de domaine.

```
                    Active Directory
                     logiflex.infra
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
         SRV-V-DC1                   SRV-02-DC2
       192.168.10.20                192.168.10.21
          AD DS                        AD DS
            DNS                          DNS
             GC                           GC
              │                           │
              └───────────┬───────────────┘
                          │
                    Réplication AD
                          │
                          ▼
                 Objets Active Directory
```

Les contrôleurs de domaine assurent notamment :

-   l'authentification ;
-   la gestion centralisée des identités ;
-   la réplication des objets Active Directory ;
-   la résolution DNS interne ;
-   la distribution des stratégies de groupe.

---

# 🗂️ 2. Organisation retenue

Afin d'organiser les objets Active Directory, plusieurs unités d'organisation sont créées.

La structure retenue est la suivante :

```
logiflex.infra
│
├── OU=Administration
│
├── OU=Utilisateurs
│
├── OU=Groupes
│
├── OU=Postes
│
├── OU=Serveurs
│
└── OU=Services
```

Cette organisation permet de séparer les différents types d'objets présents dans l'annuaire.

---

# 👤 3. Création de l'unité d'organisation Utilisateurs

L'unité d'organisation suivante est créée :

```
OU=Utilisateurs
```

Elle est destinée à accueillir les comptes utilisateurs de l'environnement LOGIFLEX.

```
logiflex.infra
       │
       ▼
OU=Utilisateurs
       │
       ├── Utilisateur 01
       ├── Utilisateur 02
       └── Utilisateur 03
```

Cette organisation permettra notamment d'appliquer ultérieurement des stratégies de groupe spécifiques aux utilisateurs.

---

# 👥 4. Création de l'unité d'organisation Groupes

Une unité d'organisation dédiée aux groupes est créée :

```
OU=Groupes
```

Elle permet de centraliser les groupes utilisés pour gérer les droits et les accès.

Exemple :

```
OU=Groupes
│
├── GG-IT
├── GG-RH
├── GG-DIRECTION
└── GG-USERS
```

> ℹ️ Les groupes Active Directory permettront ultérieurement d'attribuer des droits aux utilisateurs sans gérer les autorisations individuellement.

---

# 💻 5. Création de l'unité d'organisation Postes

Les ordinateurs utilisateurs sont organisés dans une unité d'organisation dédiée :

```
OU=Postes
```

```
OU=Postes
│
├── PC-USER-01
├── PC-USER-02
└── PC-USER-03
```

Cette séparation permettra notamment :

-   l'application de stratégies de groupe spécifiques ;
-   la configuration centralisée des postes ;
-   le déploiement de paramètres de sécurité ;
-   l'organisation des ordinateurs dans l'annuaire.

---

# 🖥️ 6. Création de l'unité d'organisation Serveurs

Une unité d'organisation spécifique est créée pour les serveurs membres du domaine.

```
OU=Serveurs
```

Exemple :

```
OU=Serveurs
│
├── SRV-V-SQL
├── SRV-V-CENTREON
├── SRV-V-VEEAM
└── Autres serveurs membres
```

> ⚠️ Les contrôleurs de domaine ne sont pas déplacés dans cette unité d'organisation.

Les contrôleurs de domaine utilisent l'unité d'organisation spécifique :

```
Domain Controllers
```

---

# 🔐 7. Création de l'unité d'organisation Administration

Une unité d'organisation spécifique est utilisée pour séparer les comptes d'administration des comptes utilisateurs standards.

```
OU=Administration
```

Cette organisation permet notamment de distinguer :

```
Utilisateur standard
        │
        ▼
anthony.robert
```

et :

```
Compte administratif
        │
        ▼
admin.anthony
```

L'objectif est d'éviter l'utilisation permanente d'un compte disposant de privilèges élevés pour les tâches quotidiennes.

> 🔐 Cette séparation constitue une base pour la mise en œuvre du principe de moindre privilège.

---

# ⚙️ 8. Création de l'unité d'organisation Services

L'unité d'organisation suivante est créée :

```
OU=Services
```

Elle permet notamment d'organiser les comptes utilisés par les services de l'infrastructure.

Exemple :

```
OU=Services
│
├── svc_backup
├── svc_monitoring
└── svc_database
```

Ces comptes pourront être utilisés ultérieurement pour certains services de l'environnement LOGIFLEX.

> ⚠️ Aucun mot de passe ou secret associé à ces comptes n'est enregistré dans le dépôt GitHub.

---

# 🧩 9. Création des unités d'organisation

La création des unités d'organisation est réalisée depuis :

```
Utilisateurs et ordinateurs Active Directory
```

Le processus est le suivant :

1.  Ouvrir **Utilisateurs et ordinateurs Active Directory** ;
2.  sélectionner le domaine :

```
logiflex.infra
```

1.  cliquer avec le bouton droit ;
2.  sélectionner :

```
Nouveau
```

1.  sélectionner :

```
Unité d'organisation
```

1.  définir le nom de l'unité d'organisation ;
2.  valider la création.

Les unités d'organisation créées sont ensuite visibles dans l'annuaire.

---

# 🔎 10. Vérification de la structure

Après la création, la structure Active Directory est vérifiée.

Le résultat attendu est le suivant :

```
logiflex.infra
│
├── Administration
│
├── Utilisateurs
│
├── Groupes
│
├── Postes
│
├── Serveurs
│
├── Services
│
└── Domain Controllers
     │
     ├── SRV-V-DC1
     └── SRV-02-DC2
```

Cette organisation permet de distinguer clairement :

-   les utilisateurs ;
-   les comptes administratifs ;
-   les groupes ;
-   les ordinateurs ;
-   les serveurs ;
-   les comptes de service ;
-   les contrôleurs de domaine.

---

# 🧪 11. Vérification avec PowerShell

La structure Active Directory peut également être vérifiée avec PowerShell.

Les unités d'organisation sont contrôlées afin de confirmer leur présence dans le domaine.

Exemple de logique de vérification :

```
Active Directory
       │
       ▼
Vérification des OU
       │
       ▼
Vérification des objets
       │
       ▼
Validation de la structure
```

Les contrôles permettent notamment de vérifier :

-   la présence des unités d'organisation ;
-   leur emplacement dans le domaine ;
-   la cohérence de la structure ;
-   la présence des objets associés.

---

# 🔐 12. Principes de sécurité retenus

La structure Active Directory est conçue afin de faciliter la mise en œuvre progressive de plusieurs principes de sécurité.

Les principaux objectifs sont :

-   séparation des comptes standards et administratifs ;
-   centralisation de la gestion des identités ;
-   organisation des ressources ;
-   application ciblée des stratégies de groupe ;
-   limitation des privilèges ;
-   amélioration de la traçabilité ;
-   simplification de l'administration.

L'approche retenue est la suivante :

```
Identité
   │
   ▼
Utilisateur
   │
   ▼
Groupe
   │
   ▼
Autorisation
   │
   ▼
Ressource
```

Cette organisation permettra de gérer les accès aux ressources de manière centralisée.

---

# 📊 13. Bilan de l'étape

| Élément | État |
| --- | --- |
| OU Administration | 🟢 |
| OU Utilisateurs | 🟢 |
| OU Groupes | 🟢 |
| OU Postes | 🟢 |
| OU Serveurs | 🟢 |
| OU Services | 🟢 |
| Structure Active Directory vérifiée | 🟢 |
| Séparation des comptes | 🟢 |
| Organisation des ressources | 🟢 |
| Création des utilisateurs | 🟡 |
| Création des groupes définitifs | 🟡 |
| Gestion des droits | 🟡 |
| Stratégies de groupe | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

À l'issue de cette étape, l'annuaire Active Directory `logiflex.infra` dispose d'une structure organisée permettant de gérer les principaux objets de l'infrastructure.

```
                    logiflex.infra
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
 Administration       Utilisateurs          Groupes
        │
        │
        ▼
      Postes ─────────────── Serveurs
        │                       │
        ▼                       ▼
 Utilisateurs             Infrastructure
```

L'infrastructure Active Directory est désormais prête pour la création des comptes, des groupes et la gestion des accès aux différentes ressources.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **création des utilisateurs, des groupes et à la mise en œuvre d'une gestion centralisée des droits**.

Les actions prévues seront notamment :

-   création des utilisateurs ;
-   création des groupes de sécurité ;
-   ajout des utilisateurs aux groupes ;
-   application du principe de moindre privilège ;
-   préparation de l'attribution des droits aux ressources ;
-   préparation des futures stratégies de groupe.

```
Structure Active Directory
        ↓
Création des utilisateurs
        ↓
Création des groupes
        ↓
Affectation aux groupes
        ↓
Gestion des droits
        ↓
Stratégies de groupe
        ↓
Sécurisation de l'environnement
```
