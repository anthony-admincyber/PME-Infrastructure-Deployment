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
* **Installation du Système d'Exploitation :** Déploiement distinct de deux instances **Windows Server 2025 Datacenter** sur les machines virtuelles `SRV-01-DC01` et `SRV-02-DC02`.
* **Standardisation & Paramétrage de Base :**
  * Attribution des noms d'hôtes normalisés (`SRV-01-DC01` et `SRV-02-DC02`).
  * Configuration du fuseau horaire et désactivation de la configuration de sécurité renforcée d'Internet Explorer (IE ESC).
  * Plan d'adressage IP statique dédié sur le segment LAN du Lab (`192.168.10.0/24`).
* **Maintien en Conditions de Sécurité (MCO/MCS) & Patch Management :**
  * Exécution complète du cycle de mises à jour cumulatives et correctifs de sécurité via **Windows Update**.
  * Activation de l'option avancée *« Obtenir des mises à jour pour d'autres produits Microsoft »* afin de garantir le patch régulier des dépendances, frameworks et composants d'infrastructure (.NET, rôles serveurs, agents d'administration).
* **Politique Pare-feu / Sécurité en Phase de Build :**
  * Désactivation temporaire de Microsoft Defender Firewall / protection en temps réel sur les deux nœuds afin d'éliminer tout blocage lors de l'initialisation des flux RPC, WinRM, DNS et de réplication d'annuaire.
  * *Note de durcissement :* Une revue complète de filtrage et un paramétrage granulaire des règles de pare-feu (flux stricts inter-serveurs) seront appliqués lors du durcissement pré-production.

### 2. Validation de la Connectivité Réseau
* Attribution de l'adressage IP statique sur `SRV-01-DC1` (`192.168.10.10/24`).
* Ouverture granulaire des flux ICMP (requêtes d'écho entrantes) via PowerShell.
* Validation de la communication réseau bidirectionnelle avec le nœud `SRV-02-DC2` (`192.168.10.11`).

<img width="854" height="391" alt="Validation de la connectivité réseau et ping PowerShell" src="https://github.com/user-attachments/assets/3a1d8b3f-da03-46ee-b92f-3027e7f20285" />

---

### 3. Promotion du Contrôleur de Domaine Racine (`Logiflex.infra`)
* Installation du rôle **AD DS** (*Active Directory Domain Services*) et des outils d'administration à distance (**RSAT**).
* Promotion du serveur `SRV-01-DC1` en tant que premier contrôleur de domaine de la forêt racine `Logiflex.infra`.
* Validation sans avertissement critique de l'ensemble des prérequis système et réseau.

<img width="759" height="557" alt="Vérification des prérequis de promotion Active Directory" src="https://github.com/user-attachments/assets/c0522e96-ed4f-4071-a0bf-b800fe488800" />

---

### 4. Jonction du Serveur `SRV-02-DC02` au Domaine
* **Configuration DNS :** Paramétrage de l'IP statique (`192.168.10.11`) et pointage du serveur DNS primaire vers `SRV-01-DC1` (`192.168.10.10`).
* **Jonction Active Directory :** Intégration de la machine `SRV-02-DC2` au domaine FQDN `Logiflex.infra`.
* **Authentification administrative :** Validation de l'intégration avec le compte privilégié `LOGIFLEX\Administrateur`.

<img width="1469" height="826" alt="Jonction du serveur membre au domaine Logiflex.infra" src="https://github.com/user-attachments/assets/775e6cf7-fa76-4412-a19b-f48e02d6c940" />

---

### 5. Centralisation et Gestion Multi-Serveurs (*Server Manager*)
* **Administration unifiée :** Ajout du serveur `SRV-02-DC02` au sein de la console *Gestionnaire de serveur* de `SRV-01-DC1`.
* **Validation des flux WinRM :** Vérification du bon fonctionnement de la gestion à distance, de la résolution DNS et de l'authentification Kerberos inter-hôtes.
* **Visibilité globale :** Surveillance centralisée de l'état des services, des rôles et des journaux d'événements pour l'ensemble des serveurs du domaine.

<img width="1655" height="312" alt="image" src="https://github.com/user-attachments/assets/2e995f71-d1fb-4488-ac94-0263c5179e38" />

