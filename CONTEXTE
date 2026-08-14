# 🏢 Contexte d'Entreprise & Organisation — LOGIFLEX

## 1. À propos de LOGIFLEX
**LOGIFLEX** est un éditeur de logiciels spécialisé dans l'optimisation de la chaîne logistique (*Supply Chain*) et la gestion d'entrepôts. L'entreprise conçoit, héberge et maintient des solutions SaaS et On-Premise pour ses clients industriels.

Dans le cadre d'une refonte globale de son Système d'Information, LOGIFLEX souhaite moderniser et centraliser l'administration de ses ressources réseau via un domaine **Active Directory (AD DS)** sécurisé.

---

## 2. Pôles Métiers & Effectifs

L'entreprise est structurée en **5 départements fonctionnels** sous la direction du SI :

| Département | Code UO | Rôle & Missions Principales |
| :--- | :--- | :--- |
| **Administration / SI** | `01-ADMINS` | Gestion de l'infrastructure, cybersécurité, support N3 et administration AD. |
| **Infrastructures / System** | `02-SERVERS` | Gestion du parc de serveurs, des bases de données et des sauvegardes. |
| **Opérations & Support** | `03-WORKSTATIONS` | Support utilisateur N1/N2, déploiement des postes et assistance client. |
| **Pôles Métiers / Projets** | `04-GROUPS` | Gestion des projets clients, logistique opérationnelle et intégration. |
| **Collaborateurs / Métiers** | `05-USERS` | Ensemble des équipes opérationnelles (R&D, Commercial, Finance, RH). |

---

## 3. Répertoire des Utilisateurs (Jeu de données de test)

Voici l'annuaire cible des comptes utilisateurs à créer pour simuler l'activité de l'entreprise :

| Nom & Prénom | Identifiant (`sAMAccountName`) | Service / Fonction | UO Rattachée | Groupe Principal |
| :--- | :--- | :--- | :--- | :--- |
| **Marc DUPONT** | `mdupont` | Responsable Infrastructure & Sécurité | `01-ADMINS` | `GS_Admins_IT` |
| **Sophie MARTIN** | `smartin` | Ingénieure Système / Admin AD | `01-ADMINS` | `GS_Admins_IT` |
| **Julien BENOIT** | `jbenoit` | Technicien Support & Proximité | `05-USERS` | `GS_Support_IT` |
| **Alice ROUSSEL** | `aroussel` | Lead Développeuse Software | `05-USERS` | `GS_Dev_Team` |
| **Thomas MOREAU** | `tmoreau` | Ingénieur d'Affaires Senior | `05-USERS` | `GS_Commercials` |
| **Claire LEMOINE** | `clemoine` | Gestion des RH & Recrutement | `05-USERS` | `GS_RH` |

---

## 4. Objectifs Techniques & Sécurité

1. **Isolation des privilèges :** Séparer les comptes d'administration quotidienne des comptes à hauts privilèges.
2. **Stratégie GPO ciblée :** Appliquer des politiques de restriction adaptées à chaque groupe métier (ex: bloquer l'accès aux paramètres système pour les commerciaux).
3. **Partitionnement réseau :** Structurer les Unités d'Organisation pour refléter les politiques de sécurité (Tiering Model).
