# 03 — Création et déploiement de SRV-V-DC1 sur Hyper-V

## 📌 Présentation

Après la préparation de l'hôte `SRV-01-HV` et l'installation du rôle Hyper-V, cette étape consiste à créer la première machine virtuelle de l'infrastructure LOGIFLEX.

La machine virtuelle créée est :

`SRV-V-DC1`

Elle est destinée à devenir le **premier contrôleur de domaine Active Directory** de l'environnement.

Elle assurera notamment les services :

- Active Directory Domain Services ;
- DNS ;
- authentification des utilisateurs et ordinateurs ;
- gestion centralisée des identités ;
- support de la future réplication Active Directory.

À ce stade, la machine virtuelle est uniquement créée et le système d'exploitation Windows Server 2025 est déployé.

La promotion en contrôleur de domaine et la configuration des services Active Directory seront réalisées dans une étape ultérieure.

---

# 🏗️ 1. Positionnement de VM-DC1 dans l'architecture

La machine `SRV-V-DC1` est hébergée sur Hyper-V, lui-même exécuté sur `SRV-01-HV`.

L'architecture est donc la suivante :

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
                               ▼
                           SRV-V-DC1
                      Windows Server 2025
                               │
                     AD DS / DNS (à venir)
```

---

# 🖥️ 2. Caractéristiques de la machine virtuelle

La machine virtuelle `SRV-V-DC1` est configurée avec des ressources adaptées à un environnement de laboratoire.

| Élément | Configuration |
| --- | --- |
| Nom | SRV-V-DC1 |
| Génération | Génération 2 |
| Système | Windows Server 2025 |
| Fonction future | Contrôleur de domaine |
| Services futurs | AD DS / DNS |
| Processeurs virtuels | 2 vCPU |
| Mémoire | 4 Go |
| Disque système | 40 Go |
| Réseau | vSwitch-LAB |

> 💡 Les ressources sont volontairement limitées afin de respecter les contraintes matérielles de l'environnement de laboratoire.

Dans une infrastructure de production, le dimensionnement serait déterminé en fonction notamment :

-   du nombre d'utilisateurs ;
-   du nombre d'objets Active Directory ;
-   des services hébergés ;
-   de la charge attendue ;
-   des exigences de disponibilité.

---

# ⚙️ 3. Création de la machine virtuelle

La création est réalisée depuis **Gestionnaire Hyper-V**.

## Procédure

1.  Ouvrir **Gestionnaire Hyper-V**.
2.  Sélectionner l'hôte `SRV-01-HV`.
3.  Cliquer sur **Nouveau**.
4.  Sélectionner **Ordinateur virtuel**.
5.  Lancer l'assistant de création.
<img width="1201" height="634" alt="image" src="https://github.com/user-attachments/assets/86d4214b-65e1-4e0d-99e7-4eee4df9e62e" />


---

# 🏷️ 4. Nom et emplacement

La machine virtuelle reçoit le nom :

`SRV-V-DC1`

Les fichiers sont stockés sur le volume dédié à Hyper-V :

```
D:\Hyper-V\
```

L'organisation retenue permet de séparer les fichiers de la machine virtuelle et les fichiers du système d'exploitation de l'hôte.

Exemple :

```
D:\Hyper-V\
│
├── Virtual Machines\
│   └── SRV-V-DC1\
│
└── Virtual Hard Disks\
    └── SRV-V-DC1\
```

<img width="1207" height="623" alt="image" src="https://github.com/user-attachments/assets/326aa6f6-e5e6-4250-97ef-33177c7e4c13" />


---

# 🧩 5. Choix de la génération

La machine virtuelle est créée en **Génération 2**.

Ce choix permet notamment de bénéficier :

-   du démarrage UEFI ;
-   du Secure Boot ;
-   de fonctionnalités modernes de virtualisation ;
-   d'une meilleure intégration avec les systèmes d'exploitation récents.

```
VM-DC1
   │
   ▼
Génération 2
   │
   ├── UEFI
   ├── Secure Boot
   └── Fonctionnalités modernes Hyper-V
```

<img width="1199" height="630" alt="image" src="https://github.com/user-attachments/assets/a5a812d7-d02a-465e-ad7c-a4e4d49d8c45" />

---

# 🧠 6. Configuration de la mémoire

Une mémoire de démarrage de :

`4096 Mo`

est attribuée à `SRV-V-DC1`.

Cette allocation permet de disposer de ressources suffisantes pour Windows Server 2025 ainsi que pour les futurs services :

-   Active Directory Domain Services ;
-   DNS ;
-   administration du serveur.

La mémoire dynamique pourra être utilisée ou ajustée selon les besoins observés dans le laboratoire.

> ⚠️ Les ressources de la machine virtuelle doivent rester cohérentes avec les ressources disponibles sur `SRV-01-HV` et sur l'hôte physique.

<img width="1202" height="629" alt="image" src="https://github.com/user-attachments/assets/d6a3d30f-625e-4ac5-8abf-1cec96ac37f6" />

---

# 🌐 7. Configuration du réseau virtuel

La machine virtuelle est connectée au commutateur virtuel configuré sur `SRV-01-HV`.

```
                         SRV-V-DC1
                           │
                           ▼
                    vSwitch-LAB
                           │
                           ▼
                     SRV-01-HV
                           │
                           ▼
                  Réseau du laboratoire
                    192.168.10.0/24
```

Cette configuration permettra à `VM-DC1` de communiquer avec :

-   `SRV-01-HV` ;
-   `SRV-02-DC2` ;
-   `SRV-V-SQL` ;
-   `SRV-V-Centreon` ;
-   les autres composants du laboratoire.

L'adressage IP définitif sera configuré après l'installation du système d'exploitation.

<img width="1205" height="636" alt="image" src="https://github.com/user-attachments/assets/741ff62f-0ca3-48aa-a238-637e28c85615" />

---

# 💾 8. Création du disque virtuel

Un disque virtuel est créé pour accueillir le système d'exploitation de `SRV-V-DC1`.

Configuration retenue :

| Élément | Configuration |
| --- | --- |
| Type | VHDX |
| Taille maximale | 40 Go |
| Emplacement | D:\Hyper-V\Virtual Hard Disks\SRV-V-DC1\ |
| Type d'allocation | Expansion dynamique |

Le format **VHDX** est utilisé afin de bénéficier des fonctionnalités modernes d'Hyper-V.

L'utilisation d'un disque à expansion dynamique permet d'optimiser l'espace disponible dans l'environnement de laboratoire.

> 💡 Le disque virtuel n'occupe pas immédiatement sa taille maximale sur le stockage physique. Son espace augmente progressivement en fonction des données réellement stockées.

<img width="1206" height="632" alt="image" src="https://github.com/user-attachments/assets/3e45374c-ab07-4bb7-ba08-85eb34bc55a7" />

---

# 📀 9. Récupération de l'image ISO pour l'installation de Windows Server 2025

## Mise à disposition de l'image ISO via les dossiers partagés VMware

Afin de rendre l'image d'installation de Windows Server 2025 accessible depuis l'hôte de virtualisation `SRV-01-HV`, un dossier partagé VMware a été configuré entre la machine physique et la machine virtuelle.

La machine physique, exécutant Windows 11 Pro, contient les différentes images ISO utilisées dans le cadre du laboratoire. La fonctionnalité **Shared Folders** de VMware Workstation Pro permet de rendre ces fichiers accessibles depuis `SRV-01-HV`, sans avoir à les télécharger ou à les copier plusieurs fois.

Cette méthode permet notamment :

- de centraliser les images ISO sur la machine physique ;
- de faciliter leur utilisation par les différents environnements virtualisés ;
- de limiter les transferts de fichiers ;
- d'éviter la duplication inutile des fichiers ISO ;
- de simplifier le déploiement des futures machines virtuelles.

<img width="828" height="451" alt="Configuration des dossiers partagés VMware" src="https://github.com/user-attachments/assets/fe126518-dca3-4092-b66e-ba46e50b05f9" />

### Prérequis

L'utilisation des dossiers partagés VMware nécessite la présence et le bon fonctionnement des **VMware Tools** sur la machine virtuelle `SRV-01-HV`.

### Configuration du partage

Depuis VMware Workstation Pro :

1. Accéder aux paramètres de la machine virtuelle `SRV-01-HV`.
2. Sélectionner l'onglet **Options**.
3. Ouvrir la section **Shared Folders**.
4. Activer l'option **Always enabled**.
5. Ajouter un dossier partagé à l'aide de l'option **Add...**.
6. Sélectionner le dossier de la machine physique contenant les images ISO utilisées pour le laboratoire.

Les fichiers sont ensuite accessibles depuis `SRV-01-HV` via le chemin réseau :

```text
\\vmware-host\Shared Folders
```
<img width="697" height="196" alt="image" src="https://github.com/user-attachments/assets/8130f092-2f10-4e8f-b56a-a0e830fc688a" />

---

# ⚡ 10. Configuration du processeur via paramètres de l'ordinateur virtuel

La machine virtuelle reçoit :

`2 processeurs virtuels`

Cette configuration est adaptée aux besoins de la maquette.

```
SRV-01-HV
      │
      └── SRV-V-DC1
             │
             └── 2 vCPU
```

Le nombre de processeurs pourra être ajusté en fonction de la consommation réelle des ressources.

<img width="718" height="685" alt="image" src="https://github.com/user-attachments/assets/60ee6ba1-31b6-463d-8a33-e873884e8752" />

---

# 💿 11. Installation de Windows Server 2025 sur `SRV-V-DC1`

### ⚙️ Démarrage de l'installation

Après l'association du média d'installation, la machine virtuelle `SRV-V-DC1` est démarrée.

La machine virtuelle amorce alors la séquence de démarrage sur l'image ISO de Windows Server 2025.

 <img width="1197" height="688" alt="Démarrage de l'installation de Windows Server 2025 sur SRV-V-DC1" src="https://github.com/user-attachments/assets/f04c4824-a316-4392-99ce-83b76180245d" />

Le processus d'installation de Windows Server peut alors commencer.

Les principales étapes sont les suivantes :

1.  Démarrer la machine virtuelle `SRV-V-DC1`.
2.  Amorcer le démarrage sur le média d'installation Windows Server 2025.
3.  Sélectionner la langue et les paramètres régionaux.
4.  Choisir l'édition de Windows Server adaptée au laboratoire.
5.  Sélectionner le type d'installation.
6.  Configurer le disque virtuel destiné au système d'exploitation.
7.  Lancer l'installation de Windows Server 2025.
<img width="1198" height="464" alt="image" src="https://github.com/user-attachments/assets/58617167-2ace-4149-a40a-9441a595d3ee" />
8.  Définir le mot de passe initial du compte `Administrateur`.
<img width="1199" height="595" alt="image" src="https://github.com/user-attachments/assets/4f966924-93f3-4445-a49f-4741126b665c" />

9.  Redémarrer la machine virtuelle après la fin de l'installation.
10. Effectuer la première ouverture de session.
<img width="1233" height="623" alt="image" src="https://github.com/user-attachments/assets/65da32ce-951a-4e73-b85d-25df0b00311b" />


À l'issue de cette étape, `SRV-V-DC1` dispose d'une installation fonctionnelle de Windows Server 2025.

La prochaine phase consistera à préparer le système d'exploitation avant son intégration dans l'infrastructure Active Directory LOGIFLEX.

---

# 🔎 12. Vérification du fonctionnement

Après l'installation, plusieurs contrôles sont réalisés.

### Vérification dans Hyper-V

La machine virtuelle doit apparaître dans le Gestionnaire Hyper-V :

```
SRV-01-HV
│
└── Machines virtuelles
      │
      └── SRV-V-DC1
            │
            └── État : En cours d'exécution
```

### Vérification du système

Les informations suivantes sont contrôlées :

-   démarrage correct de Windows Server ;
-   reconnaissance des ressources matérielles virtuelles ;
-   fonctionnement de la mémoire ;
-   disponibilité des processeurs virtuels ;
-   disponibilité de l'interface réseau ;
-   accès à la console de la machine virtuelle.

---

# 🧪 13. Validation

| Élément | État |
| --- | --- |
| Machine virtuelle créée | 🟢 |
| Génération 2 | 🟢 |
| Mémoire configurée | 🟢 |
| Processeurs virtuels configurés | 🟢 |
| Disque VHDX créé | 🟢 |
| Commutateur virtuel configuré | 🟢 |
| Windows Server 2025 installé | 🟢 |
| Adresse IP statique | 🔴 |
| Nom définitif du serveur | 🔴 |
| AD DS | 🔴 |
| DNS | 🔴 |
| Promotion en contrôleur de domaine | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 14. Résultat

À l'issue de cette étape, la machine virtuelle `SRV-V-DC1` est créée et opérationnelle sur l'hôte Hyper-V `SRV-01-HV`.

L'architecture évolue désormais de la manière suivante :

```text
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
                  Hyper-V          AD DS / DNS (à venir)
                    │
                    ▼
               SRV-V-DC1
            Windows Server 2025
                    │
                    ▼
              AD DS / DNS (à venir)
```

`VM-DC1` constitue désormais la base de la future infrastructure Active Directory LOGIFLEX.

Le système d'exploitation est installé et la machine virtuelle est prête à être configurée avant la mise en œuvre des services Active Directory et DNS.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **préparation de SRV-V-DC1** avant son intégration dans l'infrastructure Active Directory.

Les actions prévues seront notamment :

-   renommage du serveur ;
-   installation des mises à jour ;
-   configuration d'une adresse IP statique ;
-   configuration DNS ;
-   validation de la connectivité réseau ;
-   préparation à l'installation des rôles AD DS et DNS.

```
Création de SRV-V-DC1
        ↓
Installation Windows Server 2025
        ↓
Préparation du système
        ↓
Configuration réseau
        ↓
Installation AD DS / DNS
        ↓
Promotion en contrôleur de domaine
        ↓
Création de la forêt LOGIFLEX
```
