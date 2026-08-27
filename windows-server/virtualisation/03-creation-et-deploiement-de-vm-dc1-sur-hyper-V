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
