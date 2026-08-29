# 02 — Promotion de SRV-V-DC1 et création de la forêt Active Directory

## 📌 Présentation

Cette deuxième étape du projet **LOGIFLEX Infrastructure** consiste à promouvoir le serveur `SRV-V-DC1` en tant que **premier contrôleur de domaine** de l'infrastructure.

La promotion permet de créer la première forêt Active Directory de l'environnement et d'établir le domaine : **logiflex.infra**


### À l'issue de cette étape, `SRV-V-DC1` assurera notamment les fonctions suivantes :

-   Contrôleur de domaine Active Directory ;
-   Authentification des utilisateurs et des ordinateurs ;
-   Service d'annuaire AD DS ;
-   Service DNS associé au domaine ;
-   Catalogue global ;
-   Première source de réplication Active Directory pour le futur `SRV-02-DC2`.

### Architecture après promotion

                                            LOGIFLEX
                                         logiflex.infra
                                               │
                                               │
                                        ┌──────┴──────┐
                                        │             │
                                   SRV-V-DC1     SRV-02-DC2
                                 192.168.10.20   192.168.10.21
                                        │         
                                     AD DS         
                                      DNS
                                       │
                                 Premier DC
                                 de la forêt

> ℹ️ `SRV-02-DC2` sera promu dans une étape ultérieure afin de devenir le second contrôleur de domaine et d'assurer la redondance des services Active Directory et DNS.

---

# 1\. 🔎 Pré-requis

Avant de procéder à la promotion, plusieurs éléments ont été vérifiés lors de l'étape précédente.

| Élément | État |
| --- | --- |
| Windows Server 2025 installé | 🟢 |
| Nom du serveur SRV-V-DC1 | 🟢 |
| Adresse IP statique | 🟢 |
| Connectivité réseau | 🟢 |
| Rôle AD DS installé | 🟢 |
| Rôle DNS installé | 🟢 |
| Serveur prêt pour la promotion | 🟢 |

|Configuration réseau de `SRV-V-DC1`| Valeur |
| --- | --- |
| Nom | SRV-V-DC1 |
| IP | 192.168.10.20 |
| Masque | 255.255.255.0 |
| Passerelle | 192.168.10.2 |
| Domaine Active Directory | logiflex.infra |

---

# 2\. ⚙️ Lancement de la promotion

La promotion du serveur est réalisée depuis **Gestionnaire de serveur**.

Après l'installation du rôle **Service de domaine Active Directory**, une notification indique qu'une configuration supplémentaire est nécessaire.

Sélectionner :

Promouvoir ce serveur en contrôleur de domaine

<img width="1022" height="274" alt="image" src="https://github.com/user-attachments/assets/52c88c6d-7e36-41e9-9cbe-5f0073a1687c" />

Cette opération lance l'assistant de configuration des services de domaine Active Directory.

---

# 3\. 🌳 Création d'une nouvelle forêt

`SRV-01-DC1` constitue le premier contrôleur de domaine de l'environnement.

L'option suivante est donc sélectionnée :

Ajouter une nouvelle forêt

Le nom du domaine racine est défini comme suit :

```text
logiflex.infra
```

<img width="1016" height="662" alt="image" src="https://github.com/user-attachments/assets/0d8ee846-202b-4c55-93d5-c4277bff789f" />


### Pourquoi utiliser `logiflex.infra` ?

Le suffixe `.infra` permet de distinguer le domaine interne de l'infrastructure LOGIFLEX.

Le domaine Active Directory devient :

```text
logiflex.infra
```

Le serveur pourra alors être identifié par son nom FQDN :

```text
SRV-V-DC1.logiflex.infra
```

---

# 4\. 🔐 Options du contrôleur de domaine

L'assistant propose ensuite les options fonctionnelles du contrôleur de domaine.

Configuration retenue :

| Paramètre | Configuration |
| --- | --- |
| Forêt | Nouvelle forêt |
| Domaine racine | logiflex.infra |
| DNS | Activé |
| Catalogue global | Activé |
| RODC | Désactivé |
| Niveau fonctionnel de la forêt | Windows Server 2025 |
| Niveau fonctionnel du domaine | Windows Server 2025 |

Le **Catalogue global** est activé afin de permettre au contrôleur de domaine de participer aux recherches globales dans l'annuaire.

Le rôle **Read-Only Domain Controller (RODC)** n'est pas retenu pour ce serveur puisqu'il s'agit du premier contrôleur de domaine de la forêt.

<img width="1022" height="698" alt="image" src="https://github.com/user-attachments/assets/cc9aeded-06d5-493e-969a-15969e8a512c" />


### Mot de passe DSRM

L'assistant demande la définition du mot de passe **Directory Services Restore Mode (DSRM)**.

Ce mot de passe est utilisé lors des opérations de maintenance et de restauration des services Active Directory.

> ⚠️ Le mot de passe DSRM est une information sensible et ne doit jamais être stocké dans le dépôt GitHub.

Dans la documentation publique du projet, aucune valeur réelle n'est donc renseignée.


```text
DSRM
│
├── Mot de passe défini localement
├── Non publié dans GitHub
└── Utilisé uniquement pour les opérations de récupération AD
```


---

# 5\. 🌐 Configuration DNS

Le service DNS constitue un composant essentiel du fonctionnement d'Active Directory.

Lors de la promotion, le rôle DNS est associé au domaine :

```text
logiflex.infra
```

Le DNS permettra notamment la résolution des enregistrements nécessaires aux services Active Directory.

Le serveur devient notamment responsable de la résolution de :

```text
SRV-01-DC1.logiflex.infra
```

ainsi que des différents enregistrements nécessaires au fonctionnement du domaine.

---

# 6. 🌐 Vérification et configuration DNS

Après la promotion, les zones DNS liées à Active Directory pourront être vérifiées depuis :

Gestionnaire DNS

<img width="960" height="304" alt="image" src="https://github.com/user-attachments/assets/c73d893d-40c1-4ab8-8e08-49df49a488e9" />


---


# 7\. 🏷️ Nom NetBIOS

L'assistant propose également le nom NetBIOS du domaine.

Configuration retenue :

```text
LOGIFLEX
```

Le domaine pourra ainsi être référencé sous la forme :

```text
LOGIFLEX\\Administrateur
```

en complément du format UPN :

```text
Administrateur@logiflex.infra
```

<img width="1023" height="693" alt="image" src="https://github.com/user-attachments/assets/3e3b223b-e641-4fd1-b03d-bdd979ae8d11" />


---

# 8\. 📁 Emplacement des fichiers Active Directory

Les emplacements proposés par défaut sont conservés pour cette maquette.

Les principaux éléments sont :

- Base de données Active Directory
- NTDS
- Fichiers journaux
- Logs

Ces éléments constituent des composants essentiels du fonctionnement du contrôleur de domaine.

Le dossier **SYSVOL** contient notamment les fichiers nécessaires à la distribution des stratégies de groupe et aux scripts associés au domaine.

<img width="1020" height="686" alt="image" src="https://github.com/user-attachments/assets/abd536db-b5cc-4f93-b247-7e8ec2fb5683" />


---

# 9\. 🔍 Vérification des prérequis

Avant le lancement de la promotion, l'assistant effectue une série de contrôles.

Cette étape permet notamment de vérifier la compatibilité de la configuration avant la création de la forêt.

Les contrôles portent notamment sur :

-   la configuration réseau ;
-   le nom du serveur ;
-   la configuration DNS ;
-   les composants Active Directory ;
-   les paramètres nécessaires à la promotion.

### Résultat attendu

Tous les contrôles préalables sont validés.

<img width="1023" height="671" alt="image" src="https://github.com/user-attachments/assets/5726213d-1d74-4602-86f2-b7f032583661" />


---

# 10\. 🚀 Installation et promotion

Après validation des paramètres, la promotion du serveur est lancée.

L'assistant :

1.  configure les services Active Directory ;
2.  crée la nouvelle forêt ;
3.  crée le domaine `logiflex.infra` ;
4.  configure le contrôleur de domaine ;
5.  configure les services DNS ;
6.  crée le dossier SYSVOL ;
7.  configure la base NTDS ;
8.  redémarre le serveur.

La machine devient alors le premier contrôleur de domaine de la forêt LOGIFLEX.

### 🚀 Lancement de la promotion

L'assistant procède alors à la configuration du contrôleur de domaine et à la création de la nouvelle forêt Active Directory.

---

# 11\. 🔄 Redémarrage du serveur

La promotion nécessite le redémarrage de `SRV-01-DC1`.

Après redémarrage, le serveur appartient désormais au domaine :

**logiflex.infra**

Le compte administrateur peut être utilisé sous la forme :

**LOGIFLEX\\Administrateur**

ou :

**Administrateur@logiflex.infra**

---

# 12\. 🧪 Vérification du domaine

Après redémarrage, plusieurs contrôles sont réalisés afin de confirmer la réussite de la promotion.

### Vérification du domaine

<img width="1021" height="765" alt="image" src="https://github.com/user-attachments/assets/e5a82a1a-07ef-431c-a322-77a1cf358f34" />


### Vérification du contrôleur de domaine

<img width="1017" height="776" alt="image" src="https://github.com/user-attachments/assets/99d47931-3fc5-4ccc-b6da-2909386575ea" />


### Vérification du domaine Active Directory

<img width="1019" height="775" alt="image" src="https://github.com/user-attachments/assets/6d90885e-a2d9-42b5-8984-cf8a6c140507" />


---

# 13\. 🌐 Vérification DNS

La résolution DNS est ensuite vérifiée.

### Résolution du contrôleur de domaine

<img width="1018" height="442" alt="image" src="https://github.com/user-attachments/assets/5d2c2105-9c3f-4182-9a98-0669fbf8a99d" />


---

# 14\. 🗂️ Vérification Active Directory

La console **Utilisateurs et ordinateurs Active Directory** permet de vérifier la création du domaine.

<img width="1015" height="422" alt="image" src="https://github.com/user-attachments/assets/c6968adb-8f67-42aa-beac-d61c49ad64d9" />

Les conteneurs et unités d'organisation standards créés lors de la mise en place du domaine peuvent également être vérifiés.

---

# 15\. 📂 Vérification SYSVOL et NETLOGON

La présence des partages nécessaires au fonctionnement du domaine est vérifiée.

<img width="1014" height="442" alt="image" src="https://github.com/user-attachments/assets/7e9fcc08-5d1a-4c73-814a-1417df63f8aa" />


Ces partages sont nécessaires au fonctionnement normal d'un domaine Active Directory.


---

# 16\. 🔎 Vérification avec dcdiag

L'outil `dcdiag` permet de réaliser un diagnostic du contrôleur de domaine.

Commande :

```text
dcdiag
```

Cette commande permet notamment de contrôler différents composants du contrôleur de domaine :

-   Active Directory ;
-   DNS ;
-   services ;
-   réplication ;
-   connectivité ;
-   disponibilité du contrôleur de domaine.

Une attention particulière sera portée aux éventuelles erreurs DNS et Active Directory.


---

# 17\. 📊 État de l'infrastructure après promotion

Après cette étape, l'infrastructure évolue de :

**AVANT**

```text

                SRV-V-DC1
              192.168.10.10

             Serveur Windows
             AD DS installé
              DNS installé
                    │
                    ▼
                Promotion


```

**APRÈS**

```text

                 LOGIFLEX
              logiflex.infra
                    │
                    │
               SRV-V-DC1
              192.168.10.10
                    │
          ┌─────────┴─────────┐
          │                   │
        AD DS                DNS
          │
     Catalogue global
          │
       SYSVOL
          │
       NETLOGON

```


`SRV-V-DC1` constitue désormais le **premier contrôleur de domaine** de l'infrastructure.

---

# 18\. 📋 Bilan de l'étape

| Élément | État |
| --- | --- |
| SRV-V-DC1 préparé | 🟢 |
| Rôle AD DS installé | 🟢 |
| Rôle DNS installé | 🟢 |
| Nouvelle forêt créée | 🟢 |
| Domaine logiflex.infra créé | 🟢 |
| Contrôleur de domaine DC1 | 🟢 |
| Catalogue global | 🟢 |
| SYSVOL | 🟢 |
| NETLOGON | 🟢 |
| Résolution DNS | 🟢 |
| Diagnostic dcdiag | 🟢 |
| Promotion de DC2 | 🟡 |
| Réplication entre DC1/DC2 | 🟡 |
| Durcissement Active Directory | 🟡 |

---

# 🎯 Résultat

La promotion de `SRV-V-DC1` est terminée.

`SRV-V-DC1` constitue désormais le premier contrôleur de domaine
de la forêt Active Directory `logiflex.infra`.

L'environnement dispose maintenant de :

| Paramètre | Valeur |
| --- | --- |
| Forêt |logiflex.infra | 
| Domaine | logiflex.infra |
| Contrôleur de domaine | SRV-V-DC1 |
| Adresse | 192.168.10.20 |
| DNS | Actif |
| Catalogue global | Actif |
| SYSVOL | Actif |
| NETLOGON | Actif |

Cette étape constitue le socle de l'annuaire Active Directory.

La prochaine étape consistera à promouvoir `SRV-02-DC2` en tant que **second contrôleur de domaine**, puis à vérifier la réplication Active Directory et DNS entre les deux serveurs.

```text
                        SRV-V-DC1
                       192.168.10.10
                             │
                             │
                             │  Réplication AD DS / DNS
                             │
                             ▼
                        SRV-02-DC2
                       192.168.10.11
```
  

