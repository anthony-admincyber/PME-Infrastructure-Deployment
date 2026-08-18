# 01 — Préparation du serveur et installation des rôles Service de domaine Active Directory / DNS

## 📌 Présentation

```text
                                                  LOGIFLEX
                                                     │
                                              logiflex.infra
                                                     │
                                          ┌──────────┴──────────┐
                                          │                     │
                                    SRV-01-DC1            SRV-02-DC2
                                   192.168.10.10          192.168.10.11
                                    AD DS + DNS            AD DS + DNS
```


Cette première étape du projet **LOGIFLEX Infrastructure** consiste à préparer les serveurs `SRV-01-DC1` et `SRV-02-DC2` qui constitueront l'infrastructure Active Directory de l'entreprise.

La préparation est réalisée progressivement afin de disposer de deux serveurs Windows Server 2025 correctement identifiés, configurés sur le réseau, mis à jour et prêts à assurer les services d'infrastructure.

Cette première phase se concentre principalement sur la préparation de `SRV-01-DC1` et l'installation initiale des rôles **AD DS** et **DNS**.

Le second serveur `SRV-02-DC2` est préparé en parallèle et sera intégré au domaine lors d'une étape ultérieure afin d'assurer la redondance et la haute disponibilité des services Active Directory et DNS.

Cette phase comprend notamment :

- le renommage des serveurs selon la convention de nommage LOGIFLEX ;
- l'installation des dernières mises à jour disponibles ;
- la configuration des adresses IP statiques ;
- la préparation de l'environnement Windows Server ;
- la validation de la connectivité réseau ;
- la désactivation temporaire du pare-feu Microsoft Defender pendant la phase de préproduction ;
- l'installation des rôles **AD DS** et **DNS** sur `SRV-01-DC1`.

> ⚠️ **Important :** la désactivation du pare-feu et des protections pendant cette phase est temporaire et concerne uniquement la préparation de l'environnement de laboratoire. Ces paramètres seront réévalués et durcis lors de la phase de durcissement de l'infrastructure.

---

## 1. 🖥️ Environnement

| Élément | `SRV-01-DC1` | `SRV-02-DC2` |
|---|---|---|
| **Système** | Windows Server 2025 Datacenter Evaluation | Windows Server 2025 Datacenter Evaluation |
| **Hyperviseur** | VMware® Workstation Pro 26H1 | VMware® Workstation Pro 26H1 |
| **Adresse IP** | `192.168.10.10/24` | `192.168.10.11/24` |
| **Passerelle** | `192.168.10.2` | `192.168.10.2` |
| **Domaine** | `logiflex.infra` | `logiflex.infra` |
| **Rôle prévu** | Premier contrôleur de domaine | Contrôleur de domaine additionnel |
| **Services** | AD DS / DNS / Hyper-V | AD DS / DNS / Hyper-V / Veeam |

---

## 2. 🏷️ Renommage des serveurs

Les serveurs Windows Server ont été préparés puis renommés afin de respecter la convention de nommage définie pour l'infrastructure LOGIFLEX.

Le premier serveur utilise le nom :

`SRV-01-DC1`

Le second serveur utilise le nom :

`SRV-02-DC2`

Cette convention permet d'identifier rapidement la fonction et le numéro du serveur :

* `SRV` → Serveur
* `01` → Numéro du serveur
* `DC1` → Premier contrôleur de domaine

Cette standardisation facilite l'administration, la supervision et l'identification des serveurs dans l'ensemble des outils d'infrastructure.

#### Vérification

<img width="471" height="155" alt="image" src="https://github.com/user-attachments/assets/4a5d2490-eb36-41cd-9ff0-d18cea6b1076" />


---

## 🌐 3. Configuration de l'adresse IP statique

Le serveur `SRV-01-DC1` utilise une adresse IPv4 statique.  
Cette configuration est nécessaire pour un serveur d'infrastructure destiné à héberger notamment les services AD DS et DNS.

**Configuration retenue :**

| Paramètre | Valeur |
| :--- | :--- |
| **Adresse IP** | `192.168.10.10` |
| **Masque** | `255.255.255.0` |
| **Réseau** | `192.168.10.0/24` |
| **Passerelle** | `192.168.10.2` |

Le second contrôleur de domaine utilisera :
* `SRV-02-DC2` : `192.168.10.11`

Les deux serveurs pourront ainsi communiquer de manière stable pour les mécanismes d'authentification, de résolution DNS et de réplication Active Directory.

#### Vérification

<img width="681" height="248" alt="image" src="https://github.com/user-attachments/assets/b1a24a78-986c-437f-b1c0-8f2c105acaa6" />



---

## 🔄 4. Mise à jour de Windows Server

Avant l'installation des rôles d'infrastructure, le serveur a été entièrement mis à jour via Windows Update.  
L'objectif est de disposer d'une base système :

* à jour ;
* stable ;
* corrigée des vulnérabilités connues ;
* prête à recevoir les rôles d'infrastructure.

Les mises à jour comprennent notamment les correctifs de sécurité et les mises à jour cumulatives disponibles pour **Windows Server 2025**.  
L'option permettant de recevoir les mises à jour concernant d'autres produits Microsoft a également été prise en compte afin de maintenir les composants et dépendances de l'environnement à jour.

#### Vérification
Dans **Server Manager**, l'état du serveur permet de vérifier les informations relatives aux dernières mises à jour installées.
<img width="685" height="77" alt="image" src="https://github.com/user-attachments/assets/bf43a0df-7142-4931-af7e-59a79cb39940" />

---

## 🔥 5. Configuration temporaire du pare-feu

Pendant la phase de préparation et de préproduction, le pare-feu Microsoft Defender a été temporairement désactivé.  
Cette mesure permet de limiter les blocages lors de l'initialisation des différents flux nécessaires au déploiement de l'infrastructure.

Les communications concernées pourront notamment inclure :
* DNS ;
* RPC ;
* réplication Active Directory ;
* WinRM ;
* administration distante ;
* communications inter-serveurs.

> ⚠️ **Mesure temporaire**  
> Cette désactivation ne constitue pas la configuration de sécurité finale du serveur.  
> Le pare-feu sera réactivé et fera l'objet d'une configuration plus restrictive lors de la phase de durcissement.

L'objectif final sera de mettre en œuvre un filtrage correspondant uniquement aux flux nécessaires :

```text
Préparation
     ↓
Installation
     ↓
Configuration
     ↓
Durcissement
     ↓
Pare-feu réactivé
     ↓
Filtrage des flux nécessaires
     ↓
Validation
```

Cette démarche permet de distinguer clairement la phase de **Build** de la phase de **Hardening**.

---

## 🛡️ 6. État de la sécurité Windows

La préparation du serveur conserve les mécanismes de protection Microsoft disponibles sur le système.  
La capture de Server Manager permet notamment de constater :

* l'état du pare-feu Microsoft Defender ;
* la protection antivirus ;
* l'état des mises à jour ;
* la version de Windows Server ;
* le nom du serveur ;
* le domaine auquel le serveur est associé.

La configuration de sécurité sera approfondie ultérieurement dans le cadre de la phase de durcissement de l'infrastructure. Cette phase comprendra notamment :

* configuration du pare-feu ;
* stratégies de groupe ;
* gestion des comptes privilégiés ;
* réduction de la surface d'attaque ;
* journalisation ;
* supervision ;
* contrôle des services.

---

## 🔎 7. Validation de la connectivité réseau

Avant l'installation et la configuration des services d'infrastructure, la connectivité réseau du serveur est vérifiée.

**Test de la passerelle :**

<img width="649" height="236" alt="image" src="https://github.com/user-attachments/assets/ce43918c-a5b9-40ee-9778-b5c7682d8c85" />


**Test vers le futur contrôleur de domaine secondaire :**

Le serveur `SRV-02-DC2` étant préparé en parallèle, sa connectivité est également vérifiée depuis `SRV-01-DC1`.

<img width="648" height="240" alt="image" src="https://github.com/user-attachments/assets/cc4ac3ca-c1cb-477a-aca2-14625f9e85fa" />


**Objectifs de validation :**
* L'adresse IP ;
* Le masque de sous-réseau ;
* La passerelle par défaut ;
* L'interface réseau utilisée ;
* L'absence de perte de connectivité.

---

## ⚙️ 8. Installation du rôle Active Directory Domain Services

Une fois le serveur correctement préparé, le rôle **Active Directory Domain Services (AD DS)** est installé.  
L'installation est réalisée depuis **Gestionnaire de serveur**.

#### Procédure
1. Ouvrir **Gestionnaire de serveur**.
2. Sélectionner **Gérer/Ajouter des rôles et fonctionnalités**.
3. Choisir **Installation basée sur un rôle ou une fonctionnalité**.
4. Sélectionner le serveur : `SRV-01-DC1`.
5. Sélectionner : **Service de domaine Active Directory**.
6. Accepter l'installation des fonctionnalités requises.
7. Vérifier la sélection des rôles.
8. Lancer l'installation.

L'installation du rôle AD DS prépare le serveur à devenir un contrôleur de domaine.

> ℹ️ *La promotion du serveur et la création de la forêt Active Directory ne sont pas réalisées dans cette étape. Elles sont documentées dans `02-promotion-dc1.md`.*

---

## 🌐 9. Installation du rôle DNS

Le service **DNS (Domain Name System)** constitue un composant essentiel de l'infrastructure Active Directory LOGIFLEX.

Il permet notamment la résolution des noms de domaine et des enregistrements nécessaires au fonctionnement d'**Active Directory**, de l'authentification **Kerberos**, de la réplication entre contrôleurs de domaine ainsi que des différents services d'infrastructure.

Le service DNS est intégré à Active Directory afin de bénéficier de la réplication des zones entre les contrôleurs de domaine.

Le rôle **DNS Server** a été installé sur `SRV-01-DC1` lors de la préparation de l'infrastructure.


### 🔄 Zone de recherche inversée

La zone de recherche inversée :

`10.168.192.in-addr.arpa`

a été créée afin de permettre la résolution **IP → nom DNS (PTR)**.

Cette zone est intégrée à Active Directory et configurée avec des mises à jour dynamiques sécurisées.

Elle permettra notamment aux services d'infrastructure de retrouver le nom associé à une adresse IP.

Cette résolution inverse sera utile pour :

- les contrôles DNS ;
- l'authentification Kerberos ;
- la supervision ;
- les mécanismes de réplication ;
- les opérations d'administration et de diagnostic réseau.

### ⚙️ Configuration IPv6

Dans le cadre de cette maquette, IPv6 a été désactivé sur les interfaces concernées afin de concentrer la configuration réseau sur le plan d'adressage IPv4 utilisé par l'environnement LOGIFLEX.

Cette configuration pourra être réévaluée dans une infrastructure de production selon les besoins et les politiques réseau retenues.

<img width="716" height="515" alt="image" src="https://github.com/user-attachments/assets/d8dbe8c8-5197-4917-aa3b-2535ef5dcdeb" />


---

## 💻 10. Installation avec PowerShell

L'installation des rôles peut également être réalisée via **PowerShell** :

<img width="861" height="47" alt="image" src="https://github.com/user-attachments/assets/ab166cb2-1952-4fcf-b9f4-e005071252e0" />


La présence des rôles peut ensuite être contrôlée :

<img width="903" height="149" alt="image" src="https://github.com/user-attachments/assets/7223d3fb-5c9b-4938-b0f1-9f496f6c4d1f" />


| Élément                | État |
| ---------------------- | :--: |
| Windows Server 2025    |  🟢  |
| Renommage `SRV-01-DC1` |  🟢  |
| Mises à jour Windows   |  🟢  |
| Adresse IP statique    |  🟢  |
| Connectivité réseau    |  🟢  |
| Préparation du serveur |  🟢  |
| Rôle AD DS installé    |  🟢  |
| Rôle DNS installé      |  🟢  |
| Zone DNS inversée      |  🟢  |
| Promotion DC1          |  🟡  |
| Création de la forêt   |  🟡  |
| Durcissement final     |  🟡  |


---

## 🎯 Résultat

À l'issue de cette étape, `SRV-01-DC1` dispose d'une base système prête à être promue en contrôleur de domaine pour l'infrastructure **LOGIFLEX**.

Le serveur est :

* correctement nommé ;
* entièrement mis à jour ;
* configuré avec une adresse IP statique ;
* validé sur le plan de la connectivité réseau ;
* équipé des rôles **AD DS** et **DNS** ;
* préparé pour la prochaine phase de déploiement de l'annuaire.

La prochaine étape consiste à promouvoir `SRV-01-DC1` en tant que **premier contrôleur de domaine**, puis à créer la forêt Active Directory :

`logiflex.infra`
