# 01 — Mise en place de l'infrastructure virtuelle VMware

## 📌 Présentation

Cette étape du projet **LOGIFLEX Infrastructure** consiste à mettre en place l'environnement de virtualisation qui servira de socle aux différents services de l'infrastructure.

L'objectif est de reproduire une architecture proche de celle que l'on pourrait retrouver dans une PME, en séparant les rôles et services dans différentes machines virtuelles.

La virtualisation permet notamment :

- d'isoler les différents services ;
- de faciliter leur administration ;
- de simplifier les opérations de sauvegarde ;
- de tester différents scénarios de panne ;
- de reproduire une infrastructure d'entreprise dans un environnement de laboratoire ;
- de préparer l'évolution future de l'infrastructure.

---

# 🏗️ Architecture virtuelle

L'infrastructure repose sur deux serveurs physiques/virtuels principaux.

```text
                         ┌─────────────────────────┐
                         │       RÉSEAU LAN        │
                         │      192.168.10.0/24     │
                         └────────────┬────────────┘
                                      │
                ┌─────────────────────┴─────────────────────┐
                │                                           │
        ┌───────▼────────┐                         ┌────────▼────────┐
        │     HV-01      │                         │      HV-02      │
        │     VMware     │                         │      VMware     │
        │                │                         │                 │
        │ ┌────────────┐ │                         │ ┌─────────────┐ │
        │ │ VM-DC1     │ │                         │ │ VM-DC2      │ │
        │ │ AD DS / DNS│ │                         │ │ AD DS / DNS │ │
        │ └────────────┘ │                         │ └─────────────┘ │
        │                │                         │                 │
        │ ┌────────────┐ │                         │ ┌─────────────┐ │
        │ │   VM-SQL   │ │                         │ │ Veeam Repo  │ │
        │ │Comptabilité│ │                         │ │ Repository  │ │
        │ └────────────┘ │                         │ └─────────────┘ │
        │                │                         │                 │
        │ ┌────────────┐ │                         │                 │
        │ │ VM-Centreon│ │                         │                 │
        │ │ Supervision│ │                         │                 │
        │ └────────────┘ │                         │                 │
        │                │                         │                 │
        │ ┌────────────┐ │                         │                 │
        │ │    Veeam   │ │                         │                 │
        │ │   Console  │ │                         │                 │
        │ └────────────┘ │                         │                 │
        └────────────────┘                         └─────────────────┘
