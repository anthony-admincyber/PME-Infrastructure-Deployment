# 🏢 Contexte d'Entreprise & Organisation — LOGIFLEX

---

## 1. Présentation de l'Entreprise

**LOGIFLEX Solutions** est un éditeur de logiciels international spécialisé dans l'optimisation de la chaîne logistique (*Supply Chain Management*) et l'automatisation d'entrepôts intelligents (*WMS / TMS*). L'entreprise conçoit, déploie et supervise des solutions cloud et sur site pour des acteurs industriels mondiaux.

Dans le cadre d'un plan de modernisation et de durcissement de son Système d'Information, LOGIFLEX standardise son infrastructure autour d'un domaine Active Directory (**AD DS**) centralisé afin d'unifier l'authentification, d'appliquer une politique de sécurité stricte (*Zero Trust / Least Privilege*) et de faciliter l'administration multi-serveurs.


---
<br>

## 2. Répartition des Effectifs & Départements (Total : 45 Collaborateurs)

L'entreprise compte 45 employés répartis sur 6 pôles métiers. Pour la maquette d'infrastructure, un échantillon représentatif de 12 comptes utilisateurs est provisionné dans l'annuaire :

| Département / Pôle | Rôle & Missions | Effectif Réel | Comptes Démo (AD) |
| :--- | :--- | :---: | :---: |
| **Direction Générale (Management)** | Pilotage stratégique, affaires juridiques et conformité. | 4 | 2 |
| **Direction des Systèmes d'Information (DSI)** | Administration des infrastructures, cybersécurité, support N1/N2/N3. | 6 | 3 |
| **R&D & Ingénierie Logicielle** | Développement d'applications, architectures Cloud, DevOps et QA. | 15 | 2 |
| **Commerce & Marketing International** | Développement commercial, marketing produit et gestion des partenariats. | 10 | 2 |
| **Ressources Humaines & Finance** | Gestion des talents, comptabilité générale et contrôle financier. | 5 | 2 |
| **Consulting & Intégration Client** | Déploiement chez les clients, formation et gestion de projets sur site. | 5 | 1 |
| **TOTAL** | — | **45** | **12** |

---
<br>

## 3. Répertoire des Utilisateurs (Jeu de Données Active Directory)

Afin de refléter la dimension internationale de l'entreprise, les collaborateurs sont issus d'horizons variés :

| Collaborateur | Identifiant (`Pnom`) | Service / Département | Poste & Responsabilités | Groupe AD Principal |
| :--- | :--- | :--- | :--- | :--- |
| **Elena ROSTOVA** | `erostova` | Direction Générale | Chief Executive Officer (CEO) | `GS_Direction` |
| **Liam O'CONNOR** | `loconnor` | Direction Générale | Legal & Compliance Officer | `GS_Direction` |
| **Marcus VANCE** | `mvance` | Systèmes d'Information (DSI) | Responsable Sécurité & Infra (RSSI) | `GS_Admins_Infra` |
| **Amina AL-MANSOOR** | `aalmansoor` | Systèmes d'Information (DSI) | Ingénieure Systèmes & Réseaux | `GS_Admins_Infra` |
| **Kenji TANAKA** | `ktanaka` | Systèmes d'Information (DSI) | Technicien Support & Postes | `GS_Support_IT` |
| **Mateo SILVA** | `msilva` | R&D & Ingénierie | Lead Développeur Backend | `GS_Dev_Team` |
| **Sven LINDQVIST** | `slindqvist` | R&D & Ingénierie | Ingénieur Cloud & DevOps | `GS_Dev_Team` |
| **Sarah JENKINS** | `sjenkins` | Commerce & Marketing | VP Global Sales & Partnerships | `GS_Commercials` |
| **Carlos MENDEZ** | `cmendez` | Commerce & Marketing | Lead Product Marketing | `GS_Commercials` |
| **Fatou DIOP** | `fdiop` | RH & Finance | Directrice des Ressources Humaines | `GS_RH` |
| **Lukas WEBER** | `lweber` | RH & Finance | Contrôleur Financier & Comptable | `GS_Finance` |
| **Priya PATEL** | `ppatel` | Consulting & Intégration | Consultante Déploiement WMS | `GS_Consulting` |

---
<br>

## 4. Architecture Logique Active Directory

L'annuaire est structuré pour séparer distinctement les entités organisationnelles (utilisateurs, services) des composants techniques (serveurs, postes, groupes) :

```text
DC=logiflex,DC=infra
├── OU=LOGIFLEX (OU Racine d'entreprise)
│   ├── OU=Departements (Contient les comptes utilisateurs selon le métier pour ciblage GPO)
│   │   ├── OU=01_Direction
│   │   ├── OU=02_DSI
│   │   ├── OU=03_RD_Ingenierie
│   │   ├── OU=04_Commerce_Marketing
│   │   ├── OU=05_RH_Finance
│   │   └── OU=06_Consulting
│   ├── OU=Ordinateurs (Contient tous les postes et serveurs gérés)
│   │   ├── OU=Serveurs        --> Serveurs membres (ex: SRV-02-MGMT, VM-SQL-PROD)
│   │   └── OU=Postes_Clients  --> Futures machines clientes Windows 10/11
│   ├── OU=Groupes_securite    --> Centralisation des groupes GS_* (contrôle d'accès et partages)
│   └── OU=Comptes_privileges  --> Comptes d'administration dédiés (Modèle de Tiering)
```
<br>

### 📸 Vue de l'implémentation

<br>

### Structure des UO
![Structure des UO](./assets/images/ad-structure-ou.png.png)

* **`OU=LOGIFLEX` (Racine)** : Conteneur principal isolant l'ensemble des objets de l'organisation des conteneurs par défaut de Windows.
* **`OU=Departements`** : Structure hiérarchique regroupant les utilisateurs par pôle métier (`01_Direction` à `06_Consulting`) afin de permettre un ciblage précis des stratégies de groupe (GPO).
* **`OU=Ordinateurs`** : Séparation logique stricte entre les serveurs d'infrastructure (`OU=Serveurs`) et les machines clientes (`OU=Postes_Clients`).
* **`OU=Groupes_securite`** : Centralisation des groupes globaux de sécurité (`GS_*`) pour la gestion granulaire des droits d'accès NTFS et des partages.
* **`OU=Comptes_privileges`** : Zone dédiée aux comptes d'administration à hauts privilèges (respect du modèle de moindre privilège et préparation au *Tiering Model*).
---
<br>

### Groupes de sécurité
![Groupes de sécurité](./assets/images/groupes_de_sécurité.png)

* **Arborescence (`OU=LOGIFLEX`)** : Vue hiérarchique montrant le conteneur `Groupes_securite` isolé à la racine de l'organisation.
* **Stratégie AGDLP (Account -> Global -> Domain Local -> Permission)** : Déploiement des 8 groupes de sécurité globaux (`GS_*`) affectés aux départements métiers et équipes techniques :
  * `GS_Direction` : Comité de direction et conformité.
  * `GS_Admins_Infra` : Administrateurs systèmes, réseau et sécurité.
  * `GS_Support_IT` : Équipe support et assistance utilisateurs.
  * `GS_Dev_Team` : Équipes développement backend et DevOps.
  * `GS_Commercials` : Pôle commercial et marketing.
  * `GS_RH` : Direction des ressources humaines.
  * `GS_Finance` : Contrôle de gestion et comptabilité.
  * `GS_Consulting` : Consultants métiers et déploiement.
---
<br>

#### Provisionnement des Utilisateurs par Département (`OU=02_DSI`)
![Utilisateurs du département DSI](./assets/images/ad-utilisateurs-dsi.png)

* Exemple d'implémentation au sein de l'unité d'organisation `02_DSI` regroupant les comptes nominatifs de l'équipe technique avec la convention de nommage standardisée (`première lettre du prénom + nom`).
---
<br>

#### Affectation et Contrôle des Groupes de Sécurité
![Propriétés de groupe utilisateur](./assets/images/ad-user-properties-groups.png)

* Validation de l'affectation du compte utilisateur à son groupe global de sécurité dédié (`GS_Admins_Infra`), garantissant l'application du modèle de moindre privilège sans élévation directe non contrôlée.
