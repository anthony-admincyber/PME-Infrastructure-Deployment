# 🖥️ 01 — Virtualisation et préparation des serveurs Windows Server 2025

## 📌 Présentation

Cette première étape du projet **LOGIFLEX Infrastructure** consiste à mettre en place le socle de virtualisation du laboratoire.

L'environnement physique utilisé pour le projet repose sur un poste **Windows 11 Pro disposant de 32 Go de RAM**.  
L'hyperviseur **VMware Workstation Pro** est utilisé afin de créer et d'héberger deux machines virtuelles exécutant **Windows Server 2025**.

Ces deux serveurs constitueront ensuite le socle de l'infrastructure LOGIFLEX.

```text
                         PC PHYSIQUE
                       Windows 11 Pro
                         32 Go RAM
                              │
                              ▼
                 ┌────────────────────────┐
                 │   VMware Workstation   │
                 └───────────┬────────────┘
                             │
                 ┌───────────┴───────────┐
                 │                       │
                 ▼                       ▼
        ┌─────────────────┐     ┌─────────────────┐
        │   SRV-01-HV     │     │   SRV-02-DC2    │
        │  Windows Server │     │  Windows Server │
        │      2025       │     │      2025       │
        │                 │     │                 │
        │     Hyper-V     │     │ AD DS / DNS     │
        │     activé      │     │                 │
        └────────┬────────┘     └─────────────────┘
                 │
                 ▼
              Hyper-V
                 │
        ┌────────┼─────────┐
        │        │         │
        ▼        ▼         ▼
      VM-DC1   VM-SQL   VM-Centreon
                           │
                        VM-Veeam
```

> 🎯 **Objectif :** démontrer la mise en œuvre d'une infrastructure virtualisée, la création de machines virtuelles Windows Server 2025 et l'utilisation de la virtualisation imbriquée avec Hyper-V.

---

# 🏗️ 1. Architecture de virtualisation

L'architecture repose sur deux niveaux de virtualisation.

### Niveau 1 — VMware

Le poste physique Windows 11 Pro héberge les deux serveurs Windows Server 2025 avec **VMware Workstation Pro**.

```
Windows 11 Pro
      │
      ▼
VMware Workstation
      │
      ├───────────────┐
      ▼               ▼
SRV-01-HV         SRV-02-DC2
Windows 2025      Windows 2025
```

### Niveau 2 — Hyper-V

`SRV-01-HV` sera configuré ultérieurement comme hôte **Hyper-V**.

Des machines virtuelles supplémentaires seront alors hébergées à l'intérieur de ce serveur.

```
SRV-01-HV
Windows Server 2025
        │
        ▼
      Hyper-V
        │
        ├── VM-DC1
        ├── VM-SQL
        ├── VM-Centreon
        └── VM-Veeam
```

Cette architecture constitue une **virtualisation imbriquée (Nested Virtualization)**.

---

# 🖥️ 2. Environnement physique

| Élément | Configuration |
| --- | --- |
| Système hôte | Windows 11 Pro |
| Mémoire RAM | 32 Go |
| Hyperviseur | VMware Workstation Pro |
| Virtualisation | VMware + Hyper-V imbriqué |
| Réseau laboratoire | 192.168.10.0/24 |
| Passerelle | 192.168.10.2 |
| Domaine prévu | logiflex.infra |

Les ressources matérielles disponibles étant limitées, leur allocation est réalisée en fonction du rôle de chaque serveur et des besoins des machines virtuelles.

L'objectif est de conserver suffisamment de ressources pour le système hôte tout en permettant aux différents services du laboratoire de fonctionner correctement.

---

# 🖥️ 3. Création de `SRV-01-HV`

La première machine virtuelle Windows Server 2025 est destinée à devenir le serveur principal de l'infrastructure.

```
Nom : SRV-01-HV
OS : Windows Server 2025
IP : 192.168.10.10/24
Passerelle : 192.168.10.2
```

Son rôle évoluera progressivement au cours du projet.

### Rôles prévus

-   Hyper-V ;
-   Active Directory ;
-   DNS ;
-   hébergement de machines virtuelles ;
-   services d'infrastructure.

> ℹ️ La VM `SRV-01-HV` constitue l'hôte Hyper-V de la maquette. Les services applicatifs seront séparés dans des machines virtuelles dédiées.

---

# 🖥️ 4. Création de `SRV-02-DC2`

La seconde machine virtuelle Windows Server 2025 constitue le second serveur de l'infrastructure.

```
Nom : SRV-02-DC2
OS : Windows Server 2025
IP : 192.168.10.21/24
Passerelle : 192.168.10.2
```

Elle sera utilisée principalement pour :

-   Active Directory ;
-   DNS ;
-   réplication avec `SRV-01-DC1` ;
-   continuité de service ;
-   repository Veeam.

Le fait de disposer d'un second serveur permet de simuler une infrastructure plus proche d'un environnement d'entreprise.

---

# ⚙️ 5. Création des machines virtuelles dans VMware

La création des deux serveurs est réalisée depuis **VMware Workstation Pro**.
Ici les captures concernent la création du serveur SRV-01-HV.

Pour chaque machine virtuelle, les paramètres suivants sont configurés :

1.  Création d'une nouvelle machine virtuelle.
<img width="616" height="490" alt="image" src="https://github.com/user-attachments/assets/cd88d27f-357e-429d-9f45-35ab50d45195" />

2.  Sélection de l'image ISO Windows Server 2025.
<img width="612" height="484" alt="image" src="https://github.com/user-attachments/assets/4bd49c36-e0e4-4b5e-a774-3070bf5909cd" />

3.  Attribution du nom de la machine virtuelle.
<img width="615" height="502" alt="image" src="https://github.com/user-attachments/assets/bea56493-0717-44f3-88e0-adade5e3d2a5" />

4.  Allocation des processeurs virtuels.
<img width="818" height="254" alt="image" src="https://github.com/user-attachments/assets/92babf4a-1bb5-48fa-90cb-92239524f364" />

5.  Allocation de la mémoire RAM.
<img width="811" height="448" alt="image" src="https://github.com/user-attachments/assets/5066733c-4143-4b8e-9f76-401fe825ebdd" />

9.  Création du disque virtuel.
<img width="301" height="434" alt="image" src="https://github.com/user-attachments/assets/40d7eeb1-e60f-402c-bddf-a56d1667b2ca" />

10.  Configuration de la carte réseau virtuelle.
<img width="1132" height="764" alt="image" src="https://github.com/user-attachments/assets/40ac006e-779e-45ad-803d-ae60557e5e2d" />

11.  Montage de l'image ISO.
12.  Installation de Windows Server 2025.
<img width="814" height="311" alt="image" src="https://github.com/user-attachments/assets/fb15297e-4bde-441e-9023-fcb453d40a0b" />

13. Installation de VMware Tools
<img width="888" height="402" alt="image" src="https://github.com/user-attachments/assets/9aa68b9d-5a7c-4139-939e-e71fba37937f" />

14.  Installation des mises à jour.
<img width="702" height="457" alt="image" src="https://github.com/user-attachments/assets/d05bd451-7c2c-424c-803a-763d15bf9925" />

15. Modification du nom de l'hôte (redémarrage nécessaire)
<img width="322" height="98" alt="image" src="https://github.com/user-attachments/assets/872cd09d-ddaa-4745-8db9-4c3121affcc9" />

16.  Configuration du réseau.
<img width="396" height="452" alt="image" src="https://github.com/user-attachments/assets/3076d309-2f17-4696-bcca-db0d68712c1e" />



---

# 💾 6. Allocation des ressources

Les ressources du poste physique sont réparties entre Windows 11 Pro et les machines virtuelles.

L'allocation est adaptée aux besoins de la maquette et pourra être ajustée en fonction de la charge observée.

| Ressource | Hôte physique | SRV-01-HV | SRV-02-DC2 |
| --- | --- | --- | --- |
| RAM disponible | 32 Go | 8 Go | 6 Go |
| CPU | Processeur physique | 4 | 6 |
| Stockage | SSD / NVMe | 80 Go | 100 Go |
| OS | Windows 11 Pro | Windows Server 2025 Edition Standard | Windows Server 2025 Edition Standard|

> 💡 Les ressources affichées dans VMware représentent les ressources attribuées aux machines virtuelles et non les capacités physiques maximales du poste.

L'objectif est d'éviter une surallocation excessive des ressources afin de conserver une marge suffisante pour le système hôte.

---

# 🌐 7. Configuration réseau VMware

Les deux machines virtuelles sont connectées au réseau du laboratoire.

```
                    VMware Workstation
                           │
                           ▼
                  Réseau virtuel VMware
                           │
                    192.168.10.0/24
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
          SRV-01-HV                SRV-02-DC2
        192.168.10.10             192.168.10.11
```

### Configuration IP

| Serveur | Adresse IP | Masque | Passerelle |
| --- | --- | --- | --- |
| SRV-01-HV | 192.168.10.10 | /24 | 192.168.10.2 |
| SRV-02-DC2 | 192.168.10.11 | /24 | 192.168.10.2 |

Cette configuration permettra ensuite aux deux serveurs de communiquer pour :

-   Active Directory ;
-   DNS ;
-   réplication ;
-   administration ;
-   supervision ;
-   sauvegarde.

---

# 🔄 8. Mise en place de la virtualisation imbriquée

Une particularité de ce laboratoire est l'utilisation de la **virtualisation imbriquée**.

`SRV-01-HV` est lui-même une machine virtuelle VMware, mais il sera également utilisé comme hôte Hyper-V.

```
┌──────────────────────────────────────┐
│ Windows 11 Pro                       │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ VMware Workstation               │ │
│ │                                  │ │
│ │ ┌──────────────────────────────┐ │ │
│ │ │ SRV-01-HV                    │ │ │
│ │ │ Windows Server 2025          │ │ │
│ │ │                              │ │ │
│ │ │ Hyper-V                      │ │ │
│ │ │      │                       │ │ │
│ │ │      ├── VM-DC1              │ │ │
│ │ │      ├── VM-SQL              │ │ │
│ │ │      ├── VM-Centreon         │ │ │
│ │ │      └── VM-Veeam            │ │ │
│ │ └──────────────────────────────┘ │ │
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

Cette configuration permet de reproduire plusieurs niveaux d'infrastructure avec un matériel limité.

> ⚠️ Cette architecture est destinée à la **maquette et à l'apprentissage**. En environnement de production, les rôles et les charges seraient généralement répartis sur une architecture physique ou virtualisée dimensionnée en conséquence.

---

# ⚙️ 9. Préparation de `SRV-01-HV` pour Hyper-V

Après l'installation de Windows Server 2025, `SRV-01-HV` sera préparé pour recevoir le rôle Hyper-V.

La configuration comprend notamment :

-   activation de la virtualisation imbriquée dans VMware ;
-   vérification de la prise en charge de la virtualisation matérielle ;
-   installation du rôle Hyper-V ;
-   configuration du commutateur virtuel ;
-   création des futures machines virtuelles.

L'objectif est de transformer `SRV-01-HV` en plateforme d'hébergement des services applicatifs du laboratoire.

---

# 🧪 10. Vérifications

Après création des deux machines virtuelles, plusieurs contrôles sont réalisés.

### Vérification du nom

```
hostname
```

Résultats attendus :

```
SRV-01-HV
```

et :

```
SRV-02-DC2
```

### Vérification de la configuration réseau

```
ipconfig /all
```

### Test de la passerelle

```
ping 192.168.10.2
```

### Test entre les deux serveurs

Depuis `SRV-01-HV` :

```
ping 192.168.10.11
```

Depuis `SRV-02-DC2` :

```
ping 192.168.10.10
```

L'objectif est de confirmer que les deux machines virtuelles peuvent communiquer correctement avant de poursuivre le déploiement des services.

---


# 📊 11. Validation

| Élément | État |
| --- | --- |
| Windows 11 Pro | 🟢 |
| VMware Workstation | 🟢 |
| SRV-01-HV créé | 🟢 |
| SRV-02-DC2 créé | 🟢 |
| Windows Server 2025 installé | 🟢 |
| Allocation CPU/RAM | 🟢 |
| Configuration réseau | 🟢 |
| Connectivité inter-serveurs | 🟢 |
| Virtualisation imbriquée | 🟡 |
| Hyper-V sur SRV-01-DC1 | 🟡 |
| Création des VM Hyper-V | 🔴 |

---

# 🎯 Résultat

À l'issue de cette étape, les deux serveurs Windows Server 2025 sont déployés dans VMware Workstation et disposent d'une configuration de base cohérente.

```
Windows 11 Pro
       │
       ▼
VMware Workstation
       │
       ├──────────────────┐
       ▼                  ▼
    SRV-01-HV          SRV-02-DC2
Windows Server 2025  Windows Server 2025
       │                  │
       ▼                  ▼
    Hyper-V            AD DS / DNS
       │
       ├── VM-DC1
       ├── VM-SQL
       ├── VM-Centreon
       └── VM-Veeam
```

Cette étape permet de mettre en pratique les compétences suivantes :

`VMware Workstation` · `Virtualisation` · `Windows Server 2025` · `Réseau` · `Gestion des ressources` · `Virtualisation imbriquée` · `Hyper-V`

La prochaine étape consiste à préparer `SRV-01-DC1` et `SRV-02-DC2` pour le déploiement de l'infrastructure **Active Directory / DNS**.
