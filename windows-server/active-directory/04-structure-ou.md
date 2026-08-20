# 04 — Structuration de l'Active Directory et création des OU

## 📌 Présentation

Après la promotion de `SRV-01-DC1` et `SRV-02-DC2`, l'étape suivante consiste à structurer l'annuaire **Active Directory** de l'infrastructure LOGIFLEX.

L'objectif est de mettre en place une organisation logique permettant de faciliter :

- l'administration des comptes et des ordinateurs ;
- l'application des stratégies de groupe (GPO) ;
- la délégation des droits ;
- la gestion des utilisateurs ;
- la séparation des ressources selon leur fonction ;
- la maintenance et l'évolution de l'annuaire.

La structure retenue doit rester simple, lisible et évolutive afin de pouvoir être adaptée aux besoins futurs de l'entreprise.

---

# 🏢 1. Organisation logique de l'annuaire

Le domaine Active Directory utilisé dans l'infrastructure est :

```text
logiflex.infra
│
├── OU=LOGIFLEX
│   │
│   ├── OU=Utilisateurs
│   │
│   ├── OU=Groupes
│   │
│   ├── OU=Postes
│   │
│   ├── OU=Serveurs
│   │
│   ├── OU=Administrateurs
│   │
│   └── OU=Services
│
└── Objets système
