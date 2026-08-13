# 🏢 Déploiement d'une Infrastructure Système & Réseau Sécurisée (Scénario PME)

## 📌 Présentation du Projet
Ce projet consiste à concevoir, déployer et superviser une infrastructure sous Windows Server 2025 et Linux dans un environnement PME fictif (*LogiFlex SA*).
L'objectif est d'implémenter un annuaire Active Directory, un cluster de virtualisation emboîtée (Nested Hyper-V), un stockage SAN (TrueNAS), une base de données métier (SQL Server sur Linux) ainsi que les solutions de sauvegarde (Veeam) et de supervision (Centreon).

---

## 📐 Architecture & Topologie Réseau

* **Réseau Lab** : `192.168.10.0/24` (Passerelle : `192.168.10.2`)
* **Domaine Active Directory** : `Logiflex.infra`

### Cartographie des Hôtes & Services

| Hôte / VM | Système d'Exploitation | Adresse IP | Rôle / Services |
| :--- | :--- | :--- | :--- |
| **`SRV-01`** | Windows Server 2025 | `192.168.10.10` | Contrôleur de Domaine (AD DS), DNS, DHCP, Hôte Hyper-V |
| **`VM-SQL-PROD`** | Linux / SQL Server | `192.168.10.20` | Base de données métier (Authentification AD/Kerberos) |
| **`SRV-02-MGMT`** | Windows Server 2025 | `192.168.10.11` | Serveur Membre, Console Veeam Backup, Hôte Hyper-V |
| **`VM-Centreon`** | Ubuntu 24.04 LTS | `192.168.10.30` | Serveur de supervision SNMP / Alerting |
| **`NAS-SAN01`** | TrueNAS | `192.168.10.50` | Stockage SAN/NAS (Partages iSCSI & NFS) |

---

## 🚀 Étape 1 : Préparation des Hôtes & Déploiement Active Directory

### 1. Préparation du Master & Clonage (Sysprep)
- Installation initiale de Windows Server 2025 sur `SRV-01`.
- Exécution de `Sysprep` pour réinitialiser le SID avant clonage.
- Clonage complet vers `SRV-02-MGMT` sous VMware Workstation.

### 2. Validation de la Connectivité Réseau
- Configuration de l'IP statique sur SRV-01-DC01, ouverture du flux ICMP via PowerShell et test de réponse Ping réussi vers SRV-02 (192.168.10.11).

<img width="854" height="391" alt="image" src="https://github.com/user-attachments/assets/3a1d8b3f-da03-46ee-b92f-3027e7f20285" />

### 3. Promotion du Contrôleur de Domaine (`Logiflex.infra`)
- Installation du rôle AD DS et des outils de administration RSAT.
- Promotion du serveur `SRV-01` en tant que forêt racine `Logiflex.infra`
- Validation de l'ensemble des conditions préalables au déploiement.
<img width="759" height="557" alt="image" src="https://github.com/user-attachments/assets/c0522e96-ed4f-4071-a0bf-b800fe488800" />

### 4. Jonction du serveur SRV-02-MGMT au Domaine
- Configuration IP & DNS : Paramétrage de l'IP statique (`192.168.10.11`) et pointage du DNS primaire vers le contrôleur de domaine `SRV-01-DC01` (`192.168.10.10`).
- Jonction Active Directory : Intégration réussie de la machine `SRV-02-MGMT` au domaine FQDN `logiflex.infra`.
- Authentification : Validation de la jonction avec le compte administrateur du domaine `LOGIFLEX\Administrateur`.

<img width="1469" height="826" alt="image" src="https://github.com/user-attachments/assets/775e6cf7-fa76-4412-a19b-f48e02d6c940" />

### 5. Centralisation et Gestion Multi-Serveurs (Server Manager)

* **Administration unifiée :** Intégration du serveur membre `SRV-02-MGMT` au sein du *Gestionnaire de serveur* du contrôleur de domaine `SRV-01-DC01`.
* **Flux de gestion & WinRM :** Validation des flux de gestion à distance, de la résolution DNS et de l'authentification Kerberos inter-serveurs.
* **Supervision :** Visibilité en temps réel de l'état du parc, des journaux d'événements et des services sur les deux nœuds de l'infrastructure.

<img width="1875" height="356" alt="image" src="https://github.com/user-attachments/assets/bd241362-8611-4f0f-8407-2edaba93fa3a" />

