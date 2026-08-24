# 🖥️ 01 — Virtualisation et préparation des serveurs Windows Server 2025

## 📌 Présentation

Cette première étape du projet **LOGIFLEX Infrastructure** consiste à mettre en place l'environnement de virtualisation puis à déployer et préparer les deux serveurs Windows Server 2025 qui constitueront la base de l'infrastructure Active Directory.

L'environnement de laboratoire repose sur :

- **Windows 11 Pro** comme système hôte ;
- **VMware Workstation Pro** comme hyperviseur de laboratoire ;
- deux machines virtuelles **Windows Server 2025 Datacenter Evaluation** ;
- un réseau virtuel dédié à l'environnement LOGIFLEX.

Les deux serveurs seront ensuite utilisés pour construire l'infrastructure Active Directory :

```text
                         WINDOWS 11 PRO
                               │
                               │
                    VMware Workstation Pro
                               │
              ┌────────────────┴────────────────┐
              │                                 │
       ┌──────▼──────┐                   ┌──────▼──────┐
       │ SRV-01-DC1  │                   │ SRV-02-DC2  │
       │ Windows 2025│◄──── AD / DNS ───►│ Windows 2025│
       │ 192.168.10.10│                   │192.168.10.11│
       └─────────────┘                   └─────────────┘
              │                                 │
              └──────────────┬──────────────────┘
                             │
                     logiflex.infra
