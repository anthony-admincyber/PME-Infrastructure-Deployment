# 02 — Installation et configuration Hyper-V sur SRV-01-HV


## 📌 Présentation

Après avoir déployé et préparé les deux serveurs Windows Server 2025, cette étape consiste à transformer `SRV-01-HV` en **hôte de virtualisation Hyper-V**.

`SRV-01-HV` est lui-même une machine virtuelle exécutée dans **VMware Workstation Pro** sur le poste physique Windows 11 Pro.

L'objectif est donc de mettre en œuvre une architecture de **virtualisation imbriquée (Nested Virtualization)** :

```text
                     Windows 11 Pro
                           │
                           ▼
                 VMware Workstation Pro
                           │
                           ▼
                    SRV-01-HV
                 Windows Server 2025
                           │
                           ▼
                        Hyper-V
                           │
                ┌──────────┼──────────┐
                │          │          │
                ▼          ▼          ▼
             VM-DC1     VM-SQL    VM-Centreon

```

Cette approche permet de reproduire dans un environnement de laboratoire une architecture proche de celle pouvant être rencontrée dans une infrastructure d'entreprise.

---

# 🏗️ 1. Architecture de virtualisation

L'environnement repose sur deux niveaux de virtualisation.

### Niveau 1 — VMware Workstation

Le poste physique Windows 11 Pro héberge les deux serveurs Windows Server 2025.

```
Windows 11 Pro
      │
      ▼
VMware Workstation Pro
      │
      ├── SRV-01-HV
      │
      └── SRV-02-DC2
```

### Niveau 2 — Hyper-V

`SRV-01-HV` devient ensuite l'hôte Hyper-V.

```
SRV-01-HV
Windows Server 2025
      │
      ▼
    Hyper-V
      │
      ├── VM-DC1
      ├── VM-SQL
      └── VM-Centreon
```

Cette architecture constitue une **virtualisation imbriquée**.

---

# ⚙️ 2. Activation de la virtualisation imbriquée

Avant l'installation d'Hyper-V, la virtualisation matérielle doit être exposée à `SRV-01-HV`.

Dans VMware Workstation, les fonctionnalités de virtualisation du processeur sont activées pour la machine virtuelle.

L'option permettant de virtualiser les extensions matérielles du processeur doit être activée.

```
VMware Workstation
       │
       ▼
   SRV-01-HV
       │
       ▼
Virtualisation matérielle
       │
       ▼
     Hyper-V
```

Cette configuration permet au système Windows Server 2025 exécuté dans `SRV-01-HV` d'utiliser les fonctionnalités nécessaires à Hyper-V.

> ℹ️ Cette configuration est spécifique à l'environnement de laboratoire. Dans une infrastructure physique, Hyper-V utiliserait directement les fonctions de virtualisation du processeur du serveur hôte.

---

### ⚠️ Résolution d'incident : Déblocage du VT-x sous Windows 11 (VBS / Credential Guard)

Lors de l'activation de la virtualisation imbriquée sur l'hôte physique Windows 11, un conflit matériel peut survenir : la sécurité basée sur la virtualisation (**VBS / Credential Guard / Protection LSA**) monopolise les instructions `Intel VT-x / EPT`, empêchant VMware Workstation de les déléguer à la machine virtuelle.

**Procédure de remédiation appliquée sur l'hôte physique :**

1. Désactivation de l'intégrité de la mémoire et de la protection LSA dans les paramètres de sécurité Windows.
2. Désactivation des fonctionnalités concurrentes (Hyper-V hôte, Virtual Machine Platform).
3. Libération du verrou UEFI/BCD en invite de commandes administrateur :
   ```cmd
   bcdedit /set hypervisorlaunchtype off
   mountvol X: /s
   copy %WINDIR%\System32\SecConfig.efi X:\EFI\Microsoft\Boot\SecConfig.efi /Y
   bcdedit /create {0cb3b571-2f2e-4340-a459-ad29140d0737} /d "DebugDecline" /application osloader
   bcdedit /set {0cb3b571-2f2e-4340-a459-ad29140d0737} path "\EFI\Microsoft\Boot\SecConfig.efi"
   bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4340-a459-ad29140d0737}
   bcdedit /set {0cb3b571-2f2e-4340-a459-ad29140d0737} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
   bcdedit /set {0cb3b571-2f2e-4340-a459-ad29140d0737} device partition=X:
   mountvol X: /d

---

# 🔎 3. Vérification des prérequis Hyper-V

Avant l'installation du rôle, les prérequis de virtualisation sont vérifiés.

La commande suivante permet d'obtenir les informations relatives au système et aux fonctionnalités nécessaires à Hyper-V :

<img width="730" height="735" alt="image" src="https://github.com/user-attachments/assets/0025173a-9fb0-45d2-8031-5741e42e7ba3" />


La section relative aux exigences Hyper-V permet de contrôler notamment :

-   la virtualisation du processeur ;
-   SLAT ;
-   la disponibilité des extensions de virtualisation ;
-   la prévention de l'exécution des données.

Les prérequis doivent être disponibles avant de poursuivre l'installation.

---

# 💾 4. Préparation du stockage de l'hyperviseur

Avant l'installation et la configuration du rôle **Hyper-V**, le stockage de `SRV-01-HV` est organisé afin de séparer le système d'exploitation de l'environnement de virtualisation.

Cette organisation permet notamment de :

- séparer les fichiers système des machines virtuelles ;
- faciliter l'administration et la maintenance ;
- limiter l'impact d'une saturation du volume système ;
- améliorer la lisibilité de l'espace de stockage ;
- préparer une organisation dédiée aux fichiers Hyper-V.

---

## 📁 Organisation retenue

Dans le cadre de cette maquette, `SRV-01-HV` dispose d'un espace de stockage total de **100 Go**.

Afin d'optimiser les ressources disponibles tout en respectant une séparation logique entre le système et les machines virtuelles, le stockage est réparti de la manière suivante :

| Volume | Taille | Fonction | Contenu |
| :--- | :---: | :--- | :--- |
| `C:` | 45 Go | Système | Windows Server 2025, rôle Hyper-V et outils d'administration |
| `D:` | 54 Go | Hyper-V | Machines virtuelles, fichiers de configuration et disques virtuels |

Le volume `C:` est réservé au fonctionnement du système d'exploitation et de l'hyperviseur.

Le volume `D:` est dédié à l'environnement de virtualisation Hyper-V et accueillera les fichiers nécessaires au fonctionnement des machines virtuelles.

<img width="751" height="593" alt="image" src="https://github.com/user-attachments/assets/41a2253a-ea12-4045-b540-70ca85416a77" />


> 💡 **Principe d'organisation :** les fichiers des machines virtuelles ne sont pas stockés directement sur le volume système `C:`. Ils sont regroupés sur le volume `D:` dédié à l'environnement Hyper-V.

> ⚠️ **Contrainte de laboratoire :** ce dimensionnement est volontairement limité afin de s'adapter aux ressources disponibles pour la maquette. Dans une infrastructure de production, le stockage serait dimensionné selon les besoins des services, les performances attendues et les exigences de disponibilité.

---

## 🗂️ Organisation des répertoires Hyper-V

Le volume dédié à Hyper-V est organisé afin de séparer les fichiers de configuration des machines virtuelles et leurs disques virtuels.

L'organisation retenue est la suivante :

<img width="407" height="252" alt="image" src="https://github.com/user-attachments/assets/257d15a1-f380-4052-8184-b684ee29e37d" />


# 🧩 5. Installation du rôle Hyper-V

L'installation du rôle **Hyper-V** est réalisée sur `SRV-01-HV`.

Depuis **Gestionnaire de serveur** :

1.  Ouvrir **Gestionnaire de serveur**.
2.  Sélectionner **Gérer**.
3.  Sélectionner **Ajouter des rôles et fonctionnalités**.
4.  Choisir **Installation basée sur un rôle ou une fonctionnalité**.
5.  Sélectionner `SRV-01-HV`.
6.  Sélectionner le rôle **Hyper-V**.
7.  Ajouter les fonctionnalités nécessaires.
<img width="784" height="541" alt="image" src="https://github.com/user-attachments/assets/9e450e1a-0d64-4189-ad17-6868b681fbfb" />

8.  Vérifier la sélection.
9.  Lancer l'installation.
<img width="777" height="555" alt="image" src="https://github.com/user-attachments/assets/c29f8250-1184-4260-b9c5-73a861d69d33" />

10.  Redémarrer le serveur si nécessaire.

Après redémarrage, les outils d'administration Hyper-V sont disponibles.

---

# 💻 6. Installation avec PowerShell

L'installation peut également être automatisée avec PowerShell.

```
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
```

Cette commande permet :

-   d'installer le rôle Hyper-V ;
-   d'installer les outils de gestion ;
-   de redémarrer automatiquement le serveur afin de finaliser l'installation.

### Vérification du rôle

```
Get-WindowsFeature -Name Hyper-V
```

Le résultat doit indiquer que le rôle est installé.

---

# 🖥️ 7. Vérification dans Hyper-V Manager

Après l'installation, **Gestionnaire Hyper-V** est ouvert afin de vérifier que `SRV-01-HV` apparaît correctement comme hôte Hyper-V.

<img width="1418" height="799" alt="image" src="https://github.com/user-attachments/assets/c4474a38-795c-492e-85ea-86f84be2a4df" />

```
Gestionnaire Hyper-V
        │
        ▼
   SRV-01-HV
        │
        ├── Machines virtuelles
        ├── Commutateurs virtuels
        ├── Disques virtuels
        └── Configuration Hyper-V
```

À ce stade, aucune machine virtuelle applicative n'est encore créée.

La création des VM fera l'objet de l'étape suivante du projet.

---

# 🌐 8. Création du commutateur virtuel

Afin de permettre aux futures machines virtuelles de communiquer avec le réseau du laboratoire, un **commutateur virtuel Hyper-V** est configuré.

Le commutateur permettra de relier les machines virtuelles au réseau :

```
192.168.10.0/24
```

Architecture logique :

```
                    SRV-01-HV
                        │
                     Hyper-V
                        │
               Commutateur virtuel
                        │
          ┌─────────────┼─────────────┐
          │             │             │
          ▼             ▼             ▼
       VM-DC1        VM-SQL       VM-Centreon
```

Le choix du type de commutateur dépend de l'objectif recherché.

### 🌐 Type de commutateur retenu

Pour cette maquette, un **commutateur virtuel de type Externe** est configuré.

Ce choix permet aux machines virtuelles hébergées sur `SRV-01-HV` de communiquer avec les autres composants du réseau du laboratoire.

Le commutateur virtuel est associé à l'interface réseau utilisée par `SRV-01-HV` afin de permettre aux machines virtuelles Hyper-V d'accéder au réseau :

`192.168.10.0/24`

Les machines virtuelles pourront ainsi communiquer notamment avec :

- `SRV-01-HV` ;
- `SRV-02-DC2` ;
- les autres machines virtuelles ;
- la passerelle du laboratoire.

Dans le cadre de cette architecture utilisant la **virtualisation imbriquée**, `SRV-01-HV` est lui-même hébergé dans VMware Workstation. Le commutateur virtuel Hyper-V est donc configuré afin de s'intégrer au réseau virtuel du laboratoire.

> 💡 Cette configuration permet de reproduire un environnement de virtualisation proche d'une infrastructure d'entreprise tout en conservant une architecture adaptée aux contraintes d'un environnement de laboratoire.

<img width="720" height="682" alt="image" src="https://github.com/user-attachments/assets/1cbb8e70-012e-4ba3-9e7c-4ab1d4874994" />

---

# 🧠 9. Préparation des futures machines virtuelles

L'hôte `SRV-01-HV` est maintenant prêt à recevoir les machines virtuelles du laboratoire.

L'architecture cible prévoit notamment :

| VM | Fonction | Système |
| --- | --- | --- |
| VM-DC1 | Contrôleur de domaine / DNS | Windows Server 2025 |
| VM-SQL | Service SQL pour la comptabilité | Linux |
| VM-Centreon | Supervision | Ubuntu Server |

Ces machines seront déployées progressivement afin de pouvoir documenter séparément leur installation et leur configuration.

---

# 💾 10. Gestion des ressources

La gestion des ressources constitue un point important dans cette architecture car Hyper-V est lui-même exécuté dans une machine virtuelle VMware.

Les ressources disponibles doivent donc être réparties entre plusieurs niveaux :

```
Windows 11 Pro
      │
      ├── VMware
      │     │
      │     ├── SRV-01-HV
      │     │      │
      │     │      └── Hyper-V
      │     │             ├── VM-DC1
      │     │             ├── VM-SQL
      │     │             └── VM-Centreon
      │     │
      │     └── SRV-02-DC2
      │
      └── Ressources système
```

Une allocation excessive des CPU ou de la mémoire pourrait dégrader les performances de l'ensemble du laboratoire.

La consommation des ressources sera donc surveillée au fur et à mesure de la création des VM.

---

# 🔎 11. Vérifications

Plusieurs contrôles sont réalisés après l'installation.

### Vérification du rôle Hyper-V

```
Get-WindowsFeature -Name Hyper-V
```

### Vérification du service Hyper-V

```
Get-Service vmms
```

Le service **Hyper-V Virtual Machine Management** doit être opérationnel.

### Vérification des commutateurs virtuels

```
Get-VMSwitch
```

Cette commande permet de vérifier la présence et la configuration du commutateur virtuel.

### Vérification des machines virtuelles

```
Get-VM
```

À ce stade, la commande peut ne retourner aucune VM si celles-ci n'ont pas encore été créées.

---

# 🧪 12. Validation

| Élément | État |
| --- | --- |
| Virtualisation imbriquée VMware | 🟢 |
| Prérequis Hyper-V | 🟢 |
| Rôle Hyper-V | 🟢 |
| Outils de gestion Hyper-V | 🟢 |
| Gestionnaire Hyper-V | 🟢 |
| Commutateur virtuel | 🟢 |
| VM-DC1 | 🔴 |
| VM-SQL | 🔴 |
| VM-Centreon | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

À l'issue de cette étape, `SRV-01-HV` est configuré comme **hôte Hyper-V** au sein de l'environnement VMware.

L'architecture obtenue est la suivante :

```
                         Windows 11 Pro
                               │
                               ▼
                     VMware Workstation Pro
                               │
                    ┌──────────┴──────────┐
                    │                     │
                    ▼                     ▼
               SRV-01-HV             SRV-02-DC2
            Windows Server 2025    Windows Server 2025
                    │                     │
                    ▼                     ▼
                  Hyper-V              AD DS / DNS
                    │
             ┌──────┼──────┐
             │      │      │
             ▼      ▼      ▼
           VM-DC1 VM-SQL VM-Centreon
```

Cette étape permet de mettre en pratique les compétences suivantes :

`Hyper-V` · `Nested Virtualization` · `VMware Workstation` · `Windows Server 2025` · `Virtualisation` · `PowerShell` · `Réseau virtuel` · `Gestion des ressources`

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **création et au déploiement des machines virtuelles Hyper-V**, en commençant par :

`VM-DC1` → Windows Server 2025 → Active Directory / DNS
