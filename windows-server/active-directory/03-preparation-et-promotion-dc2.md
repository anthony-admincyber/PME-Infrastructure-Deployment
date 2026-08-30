# 03 — Préparation et promotion de SRV-02-DC2


### 📌 Présentation

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
       SRV-V-DC1                     SRV-02-DC2
      192.168.10.20                  192.168.10.21
             │                             │
        AD DS / DNS                   AD DS / DNS
             │                             │
             └───────────┬─────────────────┘
                         │
                    Réplication AD
```


> 🎯 **Objectif :** transformer `SRV-02-DC2` en contrôleur de domaine supplémentaire de la forêt `logiflex.infra`.

---

## PHASE 1 — Préparation du serveur

### 1\. 🖥️ Prérequis

Avant de procéder à la promotion, `SRV-02-DC2` doit être correctement préparé.

Les paramètres retenus sont les suivants :

| Élément | Configuration |
| --- | --- |
| Nom du serveur | SRV-02-DC2 |
| Système | Windows Server 2025 Standard Edition |
| Adresse IP | 192.168.10.21/24 |
| Passerelle | 192.168.10.2 |
| Réseau | 192.168.10.0/24 |
| Domaine cible | logiflex.infra |
| Contrôleur principal | SRV-V-DC1 |
| DNS principal | 192.168.10.20 |

---

### 🖥️ 2. Préparation de SRV-02-DC2

Avant son intégration à l'infrastructure Active Directory, le serveur `SRV-02-DC2` doit être préparé afin de garantir une configuration cohérente avec l'environnement LOGIFLEX.

Cette phase comprend notamment :

-   le renommage du serveur ;
-   l'installation des mises à jour ;
-   la configuration d'une adresse IP statique ;
-   la configuration du serveur DNS ;
-   la validation de la connectivité avec le premier contrôleur de domaine ;
-   la vérification de la résolution DNS du domaine `logiflex.infra`.

> ⚠️ À ce stade, `SRV-02-DC2` n'est pas encore membre du domaine et ne constitue pas encore un contrôleur de domaine.

L'architecture est alors la suivante :

```
                         LOGIFLEX
                            │
                     logiflex.infra
                            │
                     ┌──────┴──────┐
                     │             │
                     ▼             │
                SRV-V-DC1          │
              192.168.10.20        │
                AD DS / DNS        │
                     │             │
                     │             ▼
                     │       SRV-02-DC2
                     │      192.168.10.21
                     │    Serveur autonome
                     │
                     └── DNS principal
```

---

### 🏷️ 3. Renommage du serveur

Avant son intégration au domaine, le serveur reçoit le nom :

```
SRV-02-DC2
```

La convention de nommage retenue permet d'identifier rapidement la fonction du serveur :

-   `SRV` → Serveur ;
-   `02` → Second serveur ;
-   `DC2` → Second contrôleur de domaine.

Le renommage est réalisé avant l'installation des services Active Directory.

Après modification du nom de l'ordinateur, le serveur est redémarré afin que la nouvelle identité soit correctement prise en compte.

Le processus est le suivant :

```
Nom initial
    │
    ▼
Renommage
    │
    ▼
SRV-02-DC2
    │
    ▼
Redémarrage
    │
    ▼
Vérification
```

<img width="839" height="179" alt="image" src="https://github.com/user-attachments/assets/ea6836f2-3c99-4508-a6f6-6202cad882ad" />

---

### 🔄 4. Installation des mises à jour

Avant l'installation des rôles Active Directory, `SRV-02-DC2` est mis à jour.

Cette étape permet notamment de :

-   appliquer les correctifs de sécurité disponibles ;
-   installer les mises à jour cumulatives ;
-   corriger d'éventuelles vulnérabilités connues ;
-   disposer d'une base système à jour avant la promotion.

Une fois les mises à jour installées, le serveur est redémarré si nécessaire.

> 💡 La mise à jour des serveurs avant le déploiement des rôles d'infrastructure permet de partir d'une base système cohérente et maintenue.

---

### 🌐 5. Configuration de l'adresse IP statique

`SRV-02-DC2` reçoit une adresse IPv4 statique.

Configuration retenue :

| Paramètre | Valeur |
| --- | --- |
| Nom | SRV-02-DC2 |
| Adresse IP | 192.168.10.21 |
| Masque | 255.255.255.0 |
| Préfixe | /24 |
| Réseau | 192.168.10.0/24 |
| Passerelle | 192.168.10.2 |

L'utilisation d'une adresse IP statique permet de garantir une identification réseau stable du futur contrôleur de domaine.

<img width="389" height="247" alt="image" src="https://github.com/user-attachments/assets/06c5b3bc-7b96-4425-aeb0-7eb108b51269" />


```
SRV-02-DC2
192.168.10.21
      │
      ▼
192.168.10.0/24
      │
      ▼
Passerelle
192.168.10.2
```

---

### 🌐 6. Configuration DNS avant l'intégration au domaine

Avant sa promotion, `SRV-02-DC2` doit pouvoir localiser les services Active Directory existants.

Le serveur utilise donc temporairement le premier contrôleur de domaine comme serveur DNS préféré :

```
DNS préféré : 192.168.10.20
```

<img width="395" height="93" alt="image" src="https://github.com/user-attachments/assets/993e56da-8bee-4803-a224-5ee5055510d1" />



Architecture :

```
SRV-02-DC2
192.168.10.21
      │
      │ Requêtes DNS
      ▼
SRV-V-DC1
192.168.10.20
DNS + AD DS
      │
      ▼
logiflex.infra
```

Cette configuration est indispensable pour permettre à `SRV-02-DC2` de localiser :

-   le domaine `logiflex.infra` ;
-   le contrôleur de domaine existant ;
-   les services Active Directory ;
-   les enregistrements DNS nécessaires à la promotion.

> ⚠️ Un futur contrôleur de domaine ne doit pas utiliser un serveur DNS public pour localiser les services Active Directory.

---


# 🔎 7. Validation de la connectivité

Avant l'installation du rôle AD DS, plusieurs vérifications sont réalisées.

### Vérification de la configuration IP

Les paramètres suivants sont contrôlés :

-   adresse IPv4 ;
-   masque ;
-   passerelle ;
-   serveur DNS configuré.

<img width="839" height="582" alt="image" src="https://github.com/user-attachments/assets/2a5064cb-aee8-4170-8ee1-0c56e10642aa" />


### Test de la connectivité avec DC1

La communication avec le premier contrôleur de domaine est vérifiée :

<img width="818" height="362" alt="image" src="https://github.com/user-attachments/assets/1fc366e6-9bc4-4a1e-99d5-c54bed66d4c0" />


Les objectifs sont de confirmer :

-   la connectivité entre les deux serveurs ;
-   la cohérence de l'adressage ;
-   le bon fonctionnement du réseau ;
-   l'accessibilité du serveur DNS.

---

# 🌳 8. Vérification de la résolution du domaine

Avant la promotion, `SRV-02-DC2` doit être capable de résoudre le domaine :

```
logiflex.infra
```

La résolution DNS permet de vérifier que le serveur peut communiquer avec l'infrastructure Active Directory existante.

Cette étape est importante avant :

```
Installation AD DS
        ↓
Promotion DC2
        ↓
Connexion à logiflex.infra
        ↓
Réplication Active Directory
```

<img width="412" height="118" alt="image" src="https://github.com/user-attachments/assets/9cc35332-4280-4d0c-83d1-72aecef9c8d1" />

---

### 9\. 🧩 Installation du rôle Active Directory Domain Services

Le rôle **Active Directory Domain Services (AD DS)** est installé sur `SRV-02-DC2`.

L'installation est réalisée depuis **Gestionnaire de serveur**.

### Procédure

1.  Ouvrir **Gestionnaire de serveur**.
2.  Sélectionner **Gérer**.
3.  Sélectionner **Ajouter des rôles et fonctionnalités**.
4.  Choisir **Installation basée sur un rôle ou une fonctionnalité**.
5.  Sélectionner :

```text
SRV-02-DC2
```

1.  Sélectionner :

```text
Services de domaine Active Directory
```

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

```text
Ajouter un contrôleur de domaine à un domaine existant
```

Domaine cible :

```text
logiflex.infra
```

Cette configuration permet d'ajouter `SRV-02-DC2` à la forêt Active Directory déjà créée.

---

# 6\. 🔐 Authentification au domaine

L'assistant demande un compte disposant des privilèges nécessaires pour ajouter un contrôleur de domaine.

Le compte utilisé possède les droits d'administration appropriés sur le domaine :

```text
logiflex.infra
```

> 🔒 Aucun mot de passe ou secret d'authentification n'est enregistré dans le dépôt GitHub.

---

# 7\. 🌳 Options du contrôleur de domaine

Lors de la promotion, les rôles suivants sont sélectionnés :

-   **Serveur DNS** ;
-   **Catalogue global (GC)**.

Le serveur devient ainsi un contrôleur de domaine capable de participer aux services essentiels de l'infrastructure Active Directory.

### Configuration retenue

Contrôleur de domaine :

```text
SRV-02-DC2
```
  

Domaine :

```text
logiflex.infra
```

DNS :

```text
Activé
```

Catalogue global :

```text
Activé
```  

RODC :

```text
Désactivé
```

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

```text
logiflex.infra
```

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

```text
logiflex.infra
```

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

```text
repadmin /replsummary
```

Cette commande permet d'obtenir une synthèse de l'état de réplication entre les contrôleurs de domaine.

Une vérification détaillée peut également être réalisée avec :

```text
repadmin /showrepl
```

L'objectif est de confirmer l'absence d'erreurs de réplication.

<img width="604" height="322" alt="image" src="https://github.com/user-attachments/assets/8b0efaf6-07fa-4dd8-acec-77db2de8ea0f" />


---

# 15\. 🩺 Diagnostic du contrôleur de domaine

Le diagnostic Active Directory est réalisé avec :

```text
dcdiag
```

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

```text
net share
```

Les partages suivants doivent notamment être présents :

- NETLOGON
- SYSVOL

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

```text
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
```
