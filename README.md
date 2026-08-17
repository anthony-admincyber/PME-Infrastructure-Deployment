# 🏢 Déploiement d'une Infrastructure Système & Réseau Sécurisée (Scénario PME)

## 📌 Présentation du Projet
Ce projet consiste à concevoir, déployer et superviser une infrastructure hybride sous Windows Server 2025 et Linux dans un environnement d'entreprise simulé (*LogiFlex SA*).

L'objectif est d'implémenter une chaîne complète de services d'infrastructure :
* Un annuaire **Active Directory (AD DS)** hautement disponible et redondé avec deux contrôleurs de domaine.
* Un cluster de virtualisation emboîtée (**Nested Hyper-V**).
* Un stockage centralisé **SAN / NAS (TrueNAS)** via partages iSCSI et NFS.
* Une base de données métier (**SQL Server sur Linux**) intégrée à l'annuaire.
* Une solution de sauvegarde d'entreprise (**Veeam Backup & Replication**).
* Un serveur de supervision et de métrologie (**Centreon** via SNMP).

---

## 📐 Architecture & Topologie Réseau

* **Réseau Local (LAN Lab)** : `192.168.10.0/24` (Passerelle : `192.168.10.2`)
* **Domaine Active Directory** : `Logiflex.infra`

### 🖥️ Cartographie des Hôtes & Services

| Hôte / VM | Système d'Exploitation | Adresse IP | Rôle / Services |
| :--- | :--- | :--- | :--- |
| **SRV-01-DC1** | Windows Server 2025 | `192.168.10.10` | Contrôleur de domaine principal (AD DS), DNS Primaire, DHCP, Hôte Hyper-V |
| **SRV-02-DC2** | Windows Server 2025 | `192.168.10.11` | Contrôleur de domaine secondaire (AD DS), DNS Secondaire, Console Veeam, Hôte Hyper-V |
| **VM-SQL-PROD** | Linux / SQL Server | `192.168.10.20` | Base de données métier (Authentification AD / Kerberos) |
| **VM-Centreon** | Ubuntu 24.04 LTS | `192.168.10.30` | Serveur de supervision SNMP & Alerting |
| **NAS-SAN01** | TrueNAS | `192.168.10.50` | Stockage SAN / NAS (Cibles iSCSI & Partages NFS/SMB) |

---

### 1. Déploiement & Configuration Initiale des Nœuds
* **Installation du Système d'Exploitation :** Déploiement distinct de deux instances **Windows Server 2025 Datacenter** sur les machines virtuelles `SRV-01-DC1` et `SRV-02-DC2`.
* **Standardisation & Paramétrage de Base :**
  * Attribution des noms d'hôtes normalisés (`SRV-01-DC1` et `SRV-02-DC2`).
  * Plan d'adressage IP statique dédié sur le segment LAN du Lab (`192.168.10.0/24`).
* **Ergonomie & Environnement d'Exploitation :**
  * Personnalisation du Bureau administrateur avec l'affichage permanent des icônes système essentielles (*Ce PC*, *Panneau de configuration*, *Réseau*, *Fichiers de l'utilisateur*).
  * Épinglage des consoles MMC et des raccourcis vers les **Outils d'administration Windows** (*Outils RSAT*, *Gestionnaire de serveur*, *PowerShell*) pour fluidifier les opérations d'exploitation quotidiennes.
* **Maintien en Conditions de Sécurité (MCO/MCS) & Patch Management :**
  * Exécution complète du cycle de mises à jour cumulatives et correctifs de sécurité via **Windows Update**.
  * Activation de l'option avancée *« Obtenir des mises à jour pour d'autres produits Microsoft »* afin de garantir le patch régulier des dépendances, frameworks et composants d'infrastructure (.NET, rôles serveurs, agents d'administration).
* **Politique Pare-feu / Sécurité en Phase de Build :**
  * Désactivation temporaire de Microsoft Defender Firewall / protection en temps réel sur les deux nœuds afin d'éliminer tout blocage lors de l'initialisation des flux RPC, WinRM, DNS et de réplication d'annuaire.
  * *Note de durcissement :* Une revue complète de filtrage et un paramétrage granulaire des règles de pare-feu (flux stricts inter-serveurs) seront appliqués lors du durcissement pré-production.

### 2. Validation de la Connectivité Réseau & Diagnostic IP
* **Configuration & Vérification de l'Adressage :** Contrôle des paramètres réseau de l'interface `Ethernet0` sur `SRV-02-DC2` (`192.168.10.11/24`, Passerelle : `192.168.10.1`).
* **Validation du Nom d'Hôte :** Vérification de l'application correcte de la convention de nommage (`hostname` renvoyant `SRV-02-DC2`).
* **Test de Connectivité Inter-Nœuds :** Validation de la liaison réseau bidirectionnelle et de la latence minimale (< 1 ms, 0 % de perte) vers le futur contrôleur racine `SRV-01-DC1` (`192.168.10.10`) via requêtes d'écho ICMPv4.

<img width="1107" height="584" alt="image" src="https://github.com/user-attachments/assets/d83a5a16-c417-4e78-a4fd-da39b49c1cb2" />


---

### 3. Installation des Rôles AD DS / DNS & Promotion du Contrôleur Racine (`Logiflex.infra`)

* **Installation des Rôles :** Déploiement des rôles **AD DS** (*Active Directory Domain Services*) et **Serveur DNS** sur `SRV-01-DC1`.
* **Promotion du Contrôleur Racine :** Initialisation d'une nouvelle forêt et promotion du serveur en tant que premier contrôleur du domaine `logiflex.infra`.
* **Validation de la Configuration Requise :**
  * Contrôle des prérequis système, réseau et des privilèges d'administration.
  * Validation sans erreur bloquante (l'avertissement relatif à la délégation DNS est le comportement standard attendu lors de la création d'une nouvelle zone racine isolée).

<img width="760" height="559" alt="image" src="https://github.com/user-attachments/assets/3faee0d9-0392-4843-b5ee-3cf339bb36e3" />


---

### 4. Jonction du Serveur `SRV-02-DC2` au Domaine
* **Configuration DNS :** Paramétrage de l'IP statique (`192.168.10.11`) et pointage du serveur DNS primaire vers `SRV-01-DC1` (`192.168.10.10`).
* **Jonction Active Directory :** Intégration de la machine `SRV-02-DC2` au domaine FQDN `logiflex.infra`.
* **Authentification administrative :** Validation de l'intégration avec le compte privilégié `LOGIFLEX\Administrateur`.

<img width="1469" height="826" alt="Jonction du serveur membre au domaine Logiflex.infra" src="https://github.com/user-attachments/assets/775e6cf7-fa76-4412-a19b-f48e02d6c940" />

---

### 5. Centralisation et Gestion Multi-Serveurs (*Server Manager*)
* **Administration unifiée :** Ajout du serveur `SRV-02-DC02` au sein de la console *Gestionnaire de serveur* de `SRV-01-DC1`.
* **Validation des flux WinRM :** Vérification du bon fonctionnement de la gestion à distance, de la résolution DNS et de l'authentification Kerberos inter-hôtes.
* **Visibilité globale :** Surveillance centralisée de l'état des services, des rôles et des journaux d'événements pour l'ensemble des serveurs du domaine.

<img width="1655" height="312" alt="image" src="https://github.com/user-attachments/assets/2e995f71-d1fb-4488-ac94-0263c5179e38" />

