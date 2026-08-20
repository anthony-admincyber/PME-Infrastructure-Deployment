# 03 — Promotion de SRV-02-DC2 et mise en place du contrôleur de domaine secondaire

## 📌 Présentation

Cette troisième étape du projet **LOGIFLEX Infrastructure** consiste à intégrer `SRV-02-DC2` à l'infrastructure Active Directory précédemment créée sur `SRV-01-DC1`.

Après la création de la forêt `logiflex.infra` et la promotion de `SRV-01-DC1`, le second serveur est ajouté au domaine afin de mettre en place un **second contrôleur de domaine**.

Cette architecture permet notamment d'améliorer :

- la disponibilité des services Active Directory ;
- la redondance du service DNS ;
- la continuité de l'authentification ;
- la réplication des objets Active Directory ;
- la résilience de l'infrastructure en cas d'indisponibilité d'un contrôleur de domaine.

### Architecture cible

```text
                         LOGIFLEX
                            │
                     logiflex.infra
                            │
             ┌──────────────┴──────────────┐
             │                             │
       SRV-01-DC1                    SRV-02-DC2
      192.168.10.10                  192.168.10.11
             │                             │
        AD DS / DNS                   AD DS / DNS
             │                             │
             └───────────┬─────────────────┘
                         │
                    Réplication AD
```


> 🎯 **Objectif :** transformer `SRV-02-DC2` en contrôleur de domaine supplémentaire de la forêt `logiflex.infra`.

---

# 1\. 🖥️ Prérequis

Avant de procéder à la promotion, `SRV-02-DC2` doit être correctement préparé.

Les paramètres retenus sont les suivants :

| Élément | Configuration |
| --- | --- |
| Nom du serveur | SRV-02-DC2 |
| Système | Windows Server 2025 Datacenter Evaluation |
| Adresse IP | 192.168.10.11/24 |
| Passerelle | 192.168.10.2 |
| Réseau | 192.168.10.0/24 |
| Domaine cible | logiflex.infra |
| Contrôleur principal | SRV-01-DC1 |
| DNS principal | 192.168.10.10 |

Le serveur a préalablement été :

-   renommé ;
-   mis à jour ;
-   configuré avec une adresse IP statique ;
-   connecté au réseau LOGIFLEX ;
-   testé en connectivité avec `SRV-01-DC1`.

---

# 2\. 🔎 Vérification de la connectivité

Avant l'intégration au domaine, la communication entre les deux serveurs est vérifiée.

### Test du contrôleur de domaine principal

<img width="691" height="239" alt="image" src="https://github.com/user-attachments/assets/4468d356-8b9d-4171-aabd-425590eefd80" />

### Vérification de la configuration réseau

<img width="825" height="534" alt="image" src="https://github.com/user-attachments/assets/4e310734-419e-4932-9342-25b40302b3b9" />

---

# 3\. 🌐 Configuration DNS de SRV-02-DC2

Avant son intégration au domaine, `SRV-02-DC2` doit pouvoir résoudre le domaine Active Directory.

Le serveur utilise temporairement `SRV-01-DC1` comme serveur DNS principal :

DNS préféré :

192.168.10.10

Cette configuration permet à `SRV-02-DC2` de résoudre :

logiflex.infra

ainsi que les enregistrements nécessaires à la découverte des services Active Directory.

### Vérification

<img width="514" height="175" alt="image" src="https://github.com/user-attachments/assets/7814079f-cbf7-455c-ae4e-ded844a639b1" />

---

# 4\. 🧩 Installation du rôle Active Directory Domain Services

Le rôle **Active Directory Domain Services (AD DS)** est installé sur `SRV-02-DC2`.

L'installation est réalisée depuis **Gestionnaire de serveur**.

### Procédure

1.  Ouvrir **Gestionnaire de serveur**.
2.  Sélectionner **Gérer**.
3.  Sélectionner **Ajouter des rôles et fonctionnalités**.
4.  Choisir **Installation basée sur un rôle ou une fonctionnalité**.
5.  Sélectionner :

SRV-02-DC2

1.  Sélectionner :

Services de domaine Active Directory

1.  Accepter l'installation des fonctionnalités requises.
2.  Lancer l'installation.

Le rôle AD DS est désormais disponible sur le serveur.

> ℹ️ L'installation du rôle AD DS ne transforme pas encore le serveur en contrôleur de domaine. La promotion sera réalisée à l'étape suivante.

---

# 5\. 🛠️ Promotion de SRV-02-DC2

Une fois le rôle AD DS installé, une notification apparaît dans **Gestionnaire de serveur**.

L'assistant de configuration Active Directory Domain Services est lancé avec :

Promouvoir ce serveur en contrôleur de domaine

---

## 5.1 Type de déploiement

L'option suivante est sélectionnée :

Ajouter un contrôleur de domaine à un domaine existant

Domaine cible :

logiflex.infra

Cette configuration permet d'ajouter `SRV-02-DC2` à la forêt Active Directory déjà créée.

---

# 6\. 🔐 Authentification au domaine

L'assistant demande un compte disposant des privilèges nécessaires pour ajouter un contrôleur de domaine.

Le compte utilisé possède les droits d'administration appropriés sur le domaine :

logiflex.infra

> 🔒 Aucun mot de passe ou secret d'authentification n'est enregistré dans le dépôt GitHub.

---

# 7\. 🌳 Options du contrôleur de domaine

Lors de la promotion, les rôles suivants sont sélectionnés :

-   **Serveur DNS** ;
-   **Catalogue global (GC)**.

Le serveur devient ainsi un contrôleur de domaine capable de participer aux services essentiels de l'infrastructure Active Directory.

### Configuration retenue

Contrôleur de domaine :

SRV-02-DC2

  

Domaine :

logiflex.infra

  

DNS :

Activé

  

Catalogue global :

Activé

  

RODC :

Désactivé

Le serveur est configuré comme contrôleur de domaine inscriptible et non comme **Read-Only Domain Controller (RODC)**.

---

# 8\. 📁 Chemins AD DS

L'assistant permet de définir les emplacements des différents composants Active Directory.

Les éléments concernés sont notamment :

- Base de données AD DS
- NTDS.dit
- Fichiers journaux
- Logs
- SYSVOL

Les emplacements sont conservés dans la configuration prévue pour le serveur.

Ces composants sont indispensables au fonctionnement du contrôleur de domaine et à la réplication Active Directory.

---

# 9\. 🔄 Réplication Active Directory

Lors de la promotion de `SRV-02-DC2`, les données Active Directory présentes sur `SRV-01-DC1` sont répliquées vers le nouveau contrôleur de domaine.

La réplication permet notamment de synchroniser :

-   les utilisateurs ;
-   les groupes ;
-   les ordinateurs ;
-   les unités d'organisation ;
-   les stratégies ;
-   les informations DNS intégrées à Active Directory.

Architecture obtenue :

             SRV-01-DC1
            192.168.10.10
                  │
                  │
          Réplication AD DS
                  │
                  ▼
             SRV-02-DC2
            192.168.10.11

Cette architecture évite que l'ensemble des services d'annuaire repose sur un seul serveur.

---

# 10\. 🌐 Installation et intégration DNS

Lors de la promotion du serveur, le rôle DNS est également configuré.

`SRV-02-DC2` devient ainsi un serveur DNS supplémentaire pour le domaine :

logiflex.infra

Les zones DNS intégrées à Active Directory peuvent être répliquées entre les contrôleurs de domaine.

### Architecture DNS

                logiflex.infra
                      │
             ┌────────┴────────┐
             │                 │
        SRV-01-DC1        SRV-02-DC2
        DNS + AD DS       DNS + AD DS
             │                 │
             └──── Réplication ┘

Cette redondance permet de limiter la dépendance à un seul serveur DNS.

---

# 11\. 🔄 Redémarrage et finalisation

Une fois la configuration terminée, l'assistant effectue les opérations nécessaires à la promotion du serveur.

Le serveur est ensuite redémarré.

Après redémarrage, `SRV-02-DC2` fonctionne en tant que contrôleur de domaine supplémentaire de :

logiflex.infra

---

# 12\. 🔎 Vérification du contrôleur de domaine

Après redémarrage, plusieurs contrôles sont réalisés.

### Vérification du nom du serveur

<img width="340" height="53" alt="image" src="https://github.com/user-attachments/assets/d0d6aea2-af44-4e0c-a883-b1dc9bf6e469" />


### Vérification du domaine

<img width="1118" height="673" alt="image" src="https://github.com/user-attachments/assets/1e455e87-662f-491a-b679-754b618f7b76" />


### Vérification de la forêt

<img width="970" height="325" alt="image" src="https://github.com/user-attachments/assets/8e3be419-72e8-43ff-8611-430806cb0864" />

---

# 13\. 🖥️ Vérification des contrôleurs de domaine

La commande suivante permet d'identifier les contrôleurs de domaine présents dans le domaine :

<img width="1033" height="134" alt="image" src="https://github.com/user-attachments/assets/e1cf13f9-556a-476e-bd64-488769f77d65" />


L'infrastructure dispose désormais de deux contrôleurs de domaine.

---

# 14\. 🔄 Vérification de la réplication

La réplication Active Directory est vérifiée avec :

repadmin /replsummary

Cette commande permet d'obtenir une synthèse de l'état de réplication entre les contrôleurs de domaine.

Une vérification détaillée peut également être réalisée avec :

repadmin /showrepl

L'objectif est de confirmer l'absence d'erreurs de réplication.

<img width="604" height="322" alt="image" src="https://github.com/user-attachments/assets/8b0efaf6-07fa-4dd8-acec-77db2de8ea0f" />


---

# 15\. 🩺 Diagnostic du contrôleur de domaine

Le diagnostic Active Directory est réalisé avec :

dcdiag

Cette commande permet notamment de contrôler plusieurs composants essentiels :

-   Active Directory ;
-   DNS ;
-   services du contrôleur de domaine ;
-   réplication ;
-   connectivité ;
-   SYSVOL ;
-   NETLOGON.

Une infrastructure correctement configurée doit présenter des résultats cohérents et ne pas révéler d'erreurs critiques.

<img width="1522" height="805" alt="image" src="https://github.com/user-attachments/assets/624097c4-7159-4a8a-9786-e3e087e4f7be" />
<img width="841" height="731" alt="image" src="https://github.com/user-attachments/assets/b86d8307-2514-4b68-bc82-02e0917d4124" />


---

# 16\. 📂 Vérification SYSVOL et NETLOGON

Les partages nécessaires au fonctionnement du domaine sont vérifiés :

net share

Les partages suivants doivent notamment être présents :

NETLOGON

SYSVOL

Ces partages sont essentiels au fonctionnement des stratégies de groupe et à la distribution de fichiers nécessaires aux postes membres du domaine.

<img width="738" height="241" alt="image" src="https://github.com/user-attachments/assets/4537cdda-ea05-4373-9a22-7081e3a086a9" />


---

# 17\. 🌐 Vérification DNS

La résolution du domaine est vérifiée depuis `SRV-02-DC2`.

### Résolution du domaine

<img width="473" height="147" alt="image" src="https://github.com/user-attachments/assets/d493e3fd-ddf8-4163-afc8-2d5699c93a7e" />


### Résolution du contrôleur principal

<img width="548" height="120" alt="image" src="https://github.com/user-attachments/assets/a2d0074e-bee2-4a51-bd24-35bb37dbdbc9" />


### Résolution du contrôleur secondaire

<img width="547" height="123" alt="image" src="https://github.com/user-attachments/assets/5ec018fc-6b76-4738-9ebe-233ad79f9d44" />


La résolution inverse peut également être contrôlée :

<img width="440" height="126" alt="image" src="https://github.com/user-attachments/assets/cf55d133-e9bb-491d-ad5e-4812ef735a08" />


et :

<img width="435" height="123" alt="image" src="https://github.com/user-attachments/assets/9cefd753-66f8-4117-97e9-e360f60eda5e" />


Ces vérifications permettent de valider les enregistrements DNS associés aux deux contrôleurs de domaine.

---

# 18\. 📊 Bilan de l'étape

| Élément | État |
| --- | --- |
| SRV-02-DC2 préparé | 🟢 |
| Adresse IP statique | 🟢 |
| Connectivité avec DC1 | 🟢 |
| Rôle AD DS installé | 🟢 |
| Rôle DNS installé | 🟢 |
| Domaine logiflex.infra rejoint | 🟢 |
| Promotion DC2 | 🟢 |
| Catalogue global | 🟢 |
| DNS sur DC2 | 🟢 |
| SYSVOL / NETLOGON | 🟢 |
| Réplication AD DS | 🟢 |
| Diagnostic dcdiag | 🟢 |
| Haute disponibilité AD DS | 🟢 |

---

# 🎯 Résultat

`SRV-02-DC2` est désormais intégré à l'infrastructure Active Directory **LOGIFLEX**.

L'environnement dispose maintenant de deux contrôleurs de domaine :

┌───────────────────────────────┐
│       Active Directory        │
│        logiflex.infra         │
└───────────────┬───────────────┘
                │
       ┌────────┴────────┐
       │                 │
       ▼                 ▼
 SRV-01-DC1         SRV-02-DC2
 192.168.10.10      192.168.10.11
 AD DS + DNS        AD DS + DNS
       │                 │
       └───Réplication───┘
