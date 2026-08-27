# 03 — Création et déploiement de VM-DC1 sur Hyper-V

## 📌 Présentation

Après la préparation de l'hôte `SRV-01-HV` et l'installation du rôle Hyper-V, cette étape consiste à créer la première machine virtuelle de l'infrastructure LOGIFLEX.

La machine virtuelle créée est :

`VM-DC1`

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

La machine `VM-DC1` est hébergée sur Hyper-V, lui-même exécuté sur `SRV-01-HV`.

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
                           VM-DC1
                      Windows Server 2025
                               │
                     AD DS / DNS (à venir)
```

---

# 🖥️ 2. Caractéristiques de la machine virtuelle

La machine virtuelle `VM-DC1` est configurée avec des ressources adaptées à un environnement de laboratoire.

| Élément | Configuration |
| --- | --- |
| Nom | VM-DC1 |
| Génération | Génération 2 |
| Système | Windows Server 2025 |
| Fonction future | Contrôleur de domaine |
| Services futurs | AD DS / DNS |
| Processeurs virtuels | 2 vCPU |
| Mémoire | 4 Go |
| Disque système | 40 Go |
| Réseau | vSwitch-LOGIFLEX |

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
4.  Sélectionner **Machine virtuelle**.
5.  Lancer l'assistant de création.

---

# 🏷️ 4. Nom et emplacement

La machine virtuelle reçoit le nom :

`VM-DC1`

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
│   └── VM-DC1\
│
└── Virtual Hard Disks\
    └── VM-DC1\
```

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

---

# 🧠 6. Configuration de la mémoire

Une mémoire de démarrage de :

`4096 Mo`

est attribuée à `VM-DC1`.

Cette allocation permet de disposer de ressources suffisantes pour Windows Server 2025 ainsi que pour les futurs services :

-   Active Directory Domain Services ;
-   DNS ;
-   administration du serveur.

La mémoire dynamique pourra être utilisée ou ajustée selon les besoins observés dans le laboratoire.

> ⚠️ Les ressources de la machine virtuelle doivent rester cohérentes avec les ressources disponibles sur `SRV-01-HV` et sur l'hôte physique.

---

# ⚡ 7. Configuration du processeur

La machine virtuelle reçoit :

`2 processeurs virtuels`

Cette configuration est adaptée aux besoins de la maquette.

```
SRV-01-HV
      │
      └── VM-DC1
             │
             └── 2 vCPU
```

Le nombre de processeurs pourra être ajusté en fonction de la consommation réelle des ressources.

---

# 💾 8. Création du disque virtuel

Un disque virtuel est créé pour accueillir le système d'exploitation de `VM-DC1`.

Configuration retenue :

| Élément | Configuration |
| --- | --- |
| Type | VHDX |
| Taille maximale | 40 Go |
| Emplacement | D:\Hyper-V\Virtual Hard Disks\VM-DC1\ |
| Type d'allocation | Expansion dynamique |

Le format **VHDX** est utilisé afin de bénéficier des fonctionnalités modernes d'Hyper-V.

L'utilisation d'un disque à expansion dynamique permet d'optimiser l'espace disponible dans l'environnement de laboratoire.

> 💡 Le disque virtuel n'occupe pas immédiatement sa taille maximale sur le stockage physique. Son espace augmente progressivement en fonction des données réellement stockées.

---

# 🌐 9. Configuration du réseau virtuel

La machine virtuelle est connectée au commutateur virtuel configuré sur `SRV-01-HV`.

```
                         VM-DC1
                           │
                           ▼
                    vSwitch-LOGIFLEX
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
-   `VM-SQL` ;
-   `VM-Centreon` ;
-   les autres composants du laboratoire.

L'adressage IP définitif sera configuré après l'installation du système d'exploitation.

---

# 📀 10. Installation de Windows Server 2025

L'image ISO de **Windows Server 2025** est ensuite associée au lecteur DVD virtuel de la machine.

La machine virtuelle est démarrée afin de lancer l'installation du système d'exploitation.

Les principales étapes sont :

1.  Démarrer `VM-DC1`.
2.  Démarrer sur le média d'installation.
3.  Sélectionner la version de Windows Server 2025.
4.  Configurer le disque système.
5.  Lancer l'installation.
6.  Définir le mot de passe administrateur.
7.  Redémarrer la machine virtuelle.

À l'issue de cette étape, `VM-DC1` dispose d'une installation fonctionnelle de Windows Server 2025.

---

# 🔎 11. Vérification du fonctionnement

Après l'installation, plusieurs contrôles sont réalisés.

### Vérification dans Hyper-V

La machine virtuelle doit apparaître dans le Gestionnaire Hyper-V :

```
SRV-01-HV
│
└── Machines virtuelles
      │
      └── VM-DC1
            │
            └── État : En cours d'exécution
```

### Vérification du système

Les informations suivantes sont contrôlées :

-   démarrage correct de Windows Server ;
-   reconnaissance des ressources matérielles virtuelles ;
-   fonctionnement de la mémoire ;
-   fonctionnement du processeur ;
-   disponibilité de l'interface réseau ;
-   accès à la console de la machine virtuelle.

---

# 🧪 12. Validation

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

# 🎯 Résultat

À l'issue de cette étape, la machine virtuelle `VM-DC1` est créée et opérationnelle sur l'hôte Hyper-V `SRV-01-HV`.

L'architecture évolue désormais de la manière suivante :

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
                    │
                    ▼
                  Hyper-V
                    │
                    ▼
                  VM-DC1
             Windows Server 2025
                    │
                    ▼
             AD DS / DNS (à venir)
```

`VM-DC1` constitue désormais la base de la future infrastructure Active Directory LOGIFLEX.

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la **préparation de VM-DC1** avant son intégration dans l'infrastructure Active Directory.

Les actions prévues seront notamment :

-   renommage du serveur ;
-   installation des mises à jour ;
-   configuration d'une adresse IP statique ;
-   configuration DNS ;
-   validation de la connectivité réseau ;
-   préparation à l'installation des rôles AD DS et DNS.

```
Création de VM-DC1
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
