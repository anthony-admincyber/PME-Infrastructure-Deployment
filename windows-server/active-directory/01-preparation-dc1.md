# 01 — Préparation de SRV-V-DC1 et installation des rôles AD DS / DNS

## 📌 Présentation

Cette étape marque le début du déploiement de l'infrastructure **Active Directory** de l'environnement LOGIFLEX.

Après la création de la machine virtuelle `SRV-V-DC1` sur l'hôte Hyper-V `SRV-01-HV`, cette phase consiste à préparer le système d'exploitation Windows Server 2025 avant sa promotion en tant que premier contrôleur de domaine.

L'architecture de virtualisation repose sur une infrastructure imbriquée :

```text
                         Machine physique
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

La machine virtuelle `SRV-V-DC1` est destinée à devenir le **premier contrôleur de domaine** de l'environnement LOGIFLEX.

Elle assurera notamment les services suivants :

-   Active Directory Domain Services ;
-   DNS ;
-   authentification des utilisateurs et des ordinateurs ;
-   gestion centralisée des identités ;
-   gestion des objets Active Directory ;
-   support de la future réplication Active Directory.

Cette phase comprend notamment :

-   la vérification de l'identité du serveur ;
-   la configuration de l'adresse IP statique ;
-   l'installation des mises à jour disponibles ;
-   la validation de la connectivité réseau ;
-   la vérification de la configuration DNS ;
-   l'installation des rôles **AD DS** et **DNS** ;
-   la préparation du serveur avant sa promotion en contrôleur de domaine.

> ⚠️ La promotion de `SRV-V-DC1` et la création de la forêt Active Directory ne sont pas réalisées dans cette étape. Elles seront documentées dans la page suivante : `02-promotion-dc1.md`.

---

# 🖥️ 1. Environnement

La machine virtuelle `SRV-V-DC1` est exécutée sur l'hyperviseur Hyper-V installé sur l'hôte `SRV-01-HV`.

| Élément | Configuration |
| --- | --- |
| Machine virtuelle | SRV-V-DC1 |
| Système d'exploitation | Windows Server 2025 |
| Hyperviseur direct | Hyper-V |
| Hôte Hyper-V | SRV-01-HV |
| Hyperviseur de premier niveau | VMware Workstation Pro |
| Adresse IP | 192.168.10.20/24 |
| Passerelle | 192.168.10.2 |
| Domaine prévu | logiflex.infra |
| Rôle prévu | Premier contrôleur de domaine |
| Services prévus | AD DS / DNS |

L'infrastructure Active Directory prévue est composée de deux contrôleurs de domaine :

```
                              LOGIFLEX
                                 │
                          logiflex.infra
                                 │
                      ┌──────────┴──────────┐
                      │                     │
                      ▼                     ▼
                 SRV-V-DC1            SRV-V-DC2
                192.168.10.20        192.168.10.21
                   AD DS                AD DS
                    DNS                  DNS
```

`SRV-V-DC1` sera le premier contrôleur de domaine et hébergera la première instance de l'annuaire Active Directory.

`SRV-V-DC2` sera intégré ultérieurement afin d'assurer la redondance des services Active Directory et DNS.

---

# 🏷️ 2. Renommage et vérification du serveur

La machine virtuelle créée lors de l'étape précédente reçoit initialement le nom défini lors de sa création dans Hyper-V.

Avant son intégration dans l'infrastructure Active Directory, le nom du système d'exploitation est configuré afin de respecter la convention de nommage retenue pour le projet LOGIFLEX.

Le nom attribué au serveur est :

```text
SRV-V-DC1
```

Cette convention permet d'identifier rapidement la fonction de la machine :

-   `SRV` → Serveur ;
-   `V` → Machine virtuelle ;
-   `DC1` → Premier contrôleur de domaine.

Cette convention facilite l'identification des différents composants de l'infrastructure, notamment dans :

-   Active Directory ;
-   DNS ;
-   Hyper-V ;
-   les outils de supervision ;
-   les solutions de sauvegarde ;
-   les journaux système.

## ⚙️ Renommage du serveur

Le renommage est réalisé avant l'installation et la configuration complète des services Active Directory.

Il peut être effectué depuis :

-   les paramètres système ;
-   Server Manager ;
-   PowerShell.

<img width="515" height="655" alt="image" src="https://github.com/user-attachments/assets/bd9c4a35-6bca-4b66-9f85-075cb53fdeff" />


Après modification du nom de l'ordinateur, un redémarrage du serveur est nécessaire afin que le nouveau nom soit entièrement pris en compte par le système.

Le processus est donc le suivant :

```text
Nom initial du système
        │
        ▼
Renommage
        │
        ▼
SRV-V-DC1
        │
        ▼
Redémarrage du serveur
        │
        ▼
Vérification du nouveau nom
```

## 🔎 Vérification

Après le redémarrage, le nom du serveur est vérifié.

La vérification peut être réalisée depuis :

-   les paramètres système ;
-   Server Manager ;
-   PowerShell.

Exemple de vérification avec PowerShell :

<img width="1020" height="357" alt="image" src="https://github.com/user-attachments/assets/96fa8094-6124-4210-acc6-965288157f2f" />


> ℹ️ Le nom du serveur est vérifié avant la promotion en contrôleur de domaine afin de garantir une identification cohérente dans l'infrastructure Active Directory.

---

# 🌐 3. Configuration de l'adresse IP statique

Le serveur `SRV-V-DC1` utilise une adresse IPv4 statique.

Cette configuration est nécessaire pour un serveur d'infrastructure destiné à héberger les services Active Directory et DNS.

## Configuration retenue

| Paramètre | Valeur |
| --- | --- |
| Adresse IP | 192.168.10.20 |
| Masque | 255.255.255.0 |
| Préfixe réseau | /24 |
| Réseau | 192.168.10.0/24 |
| Passerelle | 192.168.10.2 |

<img width="1019" height="590" alt="image" src="https://github.com/user-attachments/assets/cfaadeb3-b3b6-4b8f-a33e-6b58701693d1" />


Le futur contrôleur de domaine secondaire utilisera l'adresse suivante :

```
SRV-V-DC2
192.168.10.21
```

L'utilisation d'adresses IP statiques permet de garantir une identification réseau stable des serveurs d'infrastructure.

```
SRV-V-DC1
192.168.10.20
       │
       ▼
  vSwitch-LAB
       │
       ▼
192.168.10.0/24
       │
       ▼
Passerelle
192.168.10.2
```

---

# 🔎 4. Vérification de la configuration réseau

Après la configuration de l'adresse IPv4 statique, plusieurs paramètres sont vérifiés.

Les informations contrôlées sont notamment :

-   l'adresse IPv4 ;
-   le masque de sous-réseau ;
-   la passerelle par défaut ;
-   l'interface réseau utilisée ;
-   la connectivité avec les autres composants du laboratoire.

La configuration peut être vérifiée avec :

<img width="1016" height="607" alt="image" src="https://github.com/user-attachments/assets/e8c58dd1-b26a-4c8c-8a7c-a5dc73514942" />


Les informations réseau doivent être cohérentes avec le plan d'adressage défini pour l'environnement LOGIFLEX.

---

# 🔄 5. Mise à jour de Windows Server 2025

Avant l'installation des rôles d'infrastructure, le système d'exploitation est mis à jour.

<img width="1020" height="616" alt="image" src="https://github.com/user-attachments/assets/edeb2c91-992a-47bf-8cff-d3159d9334df" />


L'objectif est de disposer d'une base système :

-   à jour ;
-   stable ;
-   corrigée des vulnérabilités connues ;
-   prête à recevoir les rôles d'infrastructure.

Les mises à jour comprennent notamment :

-   les correctifs de sécurité ;
-   les mises à jour cumulatives ;
-   les correctifs système disponibles pour Windows Server 2025.

Une fois les mises à jour installées, le serveur est redémarré si nécessaire.

<img width="1025" height="294" alt="image" src="https://github.com/user-attachments/assets/13ba75a0-7118-4c01-898d-62ef99fec778" />

> 💡 La mise à jour du système avant le déploiement des rôles d'infrastructure permet de limiter l'ajout de correctifs importants après la mise en production des services.

---

# 🛡️ 6. Vérification de l'état de sécurité Windows

Les mécanismes de sécurité intégrés à Windows Server restent actifs pendant la préparation du serveur.

Les éléments suivants sont vérifiés :

-   état du pare-feu Microsoft Defender ;
-   état des mises à jour ;
-   services de sécurité disponibles ;
-   configuration générale du système.

La configuration de sécurité détaillée sera approfondie ultérieurement lors de la phase de durcissement de l'infrastructure.

Cette phase pourra notamment inclure :

-   la configuration des règles du pare-feu ;
-   la réduction de la surface d'attaque ;
-   la gestion des comptes privilégiés ;
-   les stratégies de groupe ;
-   la journalisation ;
-   la supervision ;
-   le contrôle des services.

L'approche retenue est la suivante :

```
Préparation du serveur
        ↓
Déploiement des services
        ↓
Validation du fonctionnement
        ↓
Configuration des flux nécessaires
        ↓
Durcissement
        ↓
Supervision
```

---

# 🔎 7. Validation de la connectivité réseau

Avant l'installation des services Active Directory, la connectivité réseau est vérifiée.

## Test de la passerelle

La connectivité avec la passerelle du laboratoire est testée :

<img width="1020" height="454" alt="image" src="https://github.com/user-attachments/assets/185b4922-74a9-4c3c-8f35-2f0b0ce1404b" />

## Test de connectivité avec les autres serveurs

La connectivité avec les autres composants du laboratoire est également vérifiée lorsque ceux-ci sont disponibles.

Le futur contrôleur de domaine secondaire utilisera notamment :

```
SRV-V-DC2
192.168.10.21
```

<img width="1016" height="453" alt="image" src="https://github.com/user-attachments/assets/857ed8f9-6c18-44e5-898f-e211ccc93cba" />


Les objectifs de validation sont les suivants :

-   validation de l'adressage IPv4 ;
-   validation du sous-réseau ;
-   validation de la passerelle ;
-   validation de l'interface réseau ;
-   absence de problème de connectivité entre les machines du laboratoire.

---

# ⚙️ 8. Installation du rôle Active Directory Domain Services

Une fois le serveur correctement préparé, le rôle **Active Directory Domain Services (AD DS)** est installé.

L'installation peut être réalisée depuis **Gestionnaire de serveur**.

## Procédure

1.  Ouvrir **Gestionnaire de serveur**.
2.  Sélectionner **Gérer**.
3.  Cliquer sur **Ajouter des rôles et fonctionnalités**.

<img width="1017" height="572" alt="image" src="https://github.com/user-attachments/assets/eeac5e16-7a76-4dae-b8d3-f7e878508df2" />

4.  Choisir **Installation basée sur un rôle ou une fonctionnalité**.
5.  Sélectionner le serveur :

<img width="829" height="674" alt="image" src="https://github.com/user-attachments/assets/f36bb60d-2b2c-4d26-aa30-3ea7d23956d0" />

6.  Sélectionner le rôle : **Service de domaine Active Directory**
7.  Accepter l'installation des fonctionnalités requises.
8.  Vérifier la sélection des rôles.
9.  Lancer l'installation.

<img width="782" height="557" alt="image" src="https://github.com/user-attachments/assets/50fc62c8-814e-499a-b5ca-26924f4e98eb" />


L'installation du rôle AD DS prépare le serveur à devenir un contrôleur de domaine.

À ce stade, le serveur n'est pas encore promu.

La promotion et la création de la forêt seront réalisées dans l'étape suivante.

---

# 🌐 9. Installation du rôle DNS

Le rôle **DNS Server** est également installé sur `SRV-V-DC1`.

DNS constitue un composant fondamental du fonctionnement d'Active Directory.

Il permet notamment :

-   la résolution des noms ;
-   la publication des services Active Directory ;
-   la localisation des contrôleurs de domaine ;
-   la découverte des services via les enregistrements DNS ;
-   la communication entre les composants de l'infrastructure.

L'installation du rôle DNS peut être réalisée depuis l'assistant **Ajouter des rôles et fonctionnalités**.

<img width="981" height="719" alt="image" src="https://github.com/user-attachments/assets/4e90c651-fe4d-4e08-8d25-76703c851ac2" />


Lorsque la promotion du serveur sera réalisée, les zones DNS nécessaires au domaine Active Directory seront créées.

---

# 💻 10. Vérification des rôles avec PowerShell

La présence des rôles installés peut être vérifiée avec PowerShell.

<img width="1018" height="481" alt="image" src="https://github.com/user-attachments/assets/753b2e51-5a07-4c12-b6a5-315a4a06f5da" />

Cette vérification permet de confirmer que le serveur dispose des composants nécessaires avant la promotion en contrôleur de domaine.

---

# 📊 11. Bilan de l'étape

| Élément | État |
| --- | --- |
| Windows Server 2025 installé | 🟢 |
| Nom SRV-V-DC1 vérifié | 🟢 |
| Adresse IP statique configurée | 🟢 |
| Connectivité réseau validée | 🟢 |
| Mises à jour Windows installées | 🟢 |
| État de sécurité vérifié | 🟢 |
| Rôle AD DS installé | 🟢 |
| Rôle DNS installé | 🟢 |
| Promotion en contrôleur de domaine | 🔴 |
| Création de la forêt | 🔴 |
| Configuration définitive DNS | 🔴 |
| Ajout du second contrôleur de domaine | 🔴 |
| Durcissement final | 🔴 |

**🟢 Terminé — 🟡 En cours — 🔴 À réaliser**

---

# 🎯 Résultat

À l'issue de cette étape, `SRV-V-DC1` dispose d'une base système prête à être promue en tant que premier contrôleur de domaine de l'infrastructure LOGIFLEX.

Le serveur est désormais :

-   correctement identifié dans l'infrastructure ;
-   configuré avec une adresse IP statique ;
-   connecté au réseau du laboratoire ;
-   mis à jour ;
-   équipé des rôles **AD DS** et **DNS** ;
-   prêt pour la création de la forêt Active Directory.

L'architecture est désormais la suivante :

```
                         Machine physique
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
                    ┌──────────┴──────────┐
                    │                     │
                    ▼                     ▼
                  AD DS                   DNS
                    │                     │
                    └──────────┬──────────┘
                               │
                               ▼
                    Forêt Active Directory
                        logiflex.infra
                         (à venir)
```

---

## ➡️ Étape suivante

La prochaine étape sera consacrée à la promotion de `SRV-V-DC1` en tant que premier contrôleur de domaine de l'infrastructure LOGIFLEX.

Les principales actions seront les suivantes :

-   promotion du serveur en contrôleur de domaine ;
-   création d'une nouvelle forêt Active Directory ;
-   création du domaine :

```
logiflex.infra
```

-   installation et configuration des composants DNS associés ;
-   validation du fonctionnement d'Active Directory ;
-   vérification de la résolution DNS.

```
Préparation de SRV-V-DC1
        ↓
Configuration réseau
        ↓
Installation AD DS / DNS
        ↓
Promotion en contrôleur de domaine
        ↓
Création de la forêt
        ↓
Création du domaine logiflex.infra
        ↓
Configuration DNS
        ↓
Validation
        ↓
Ajout futur de SRV-V-DC2
```
