# 01 — Installation du rôle Active Directory Domain Services (AD DS)

## 📌 Présentation

Cette première étape du projet **LOGIFLEX Infrastructure** consiste à préparer le serveur `SRV-01-DC1` et à installer le rôle **Active Directory Domain Services (AD DS)**.

Ce serveur deviendra le premier contrôleur de domaine de l'infrastructure et hébergera également le service **DNS**, indispensable au fonctionnement d'Active Directory.

L'objectif est de mettre en place les fondations de l'annuaire d'entreprise avant la création de la forêt `logiflex.infra`.

---

## 🎯 Objectifs

Cette étape permet de :

- préparer le serveur Windows Server 2025 ;
- définir son nom d'hôte ;
- configurer son adressage IPv4 statique ;
- vérifier sa connectivité réseau ;
- installer le rôle **AD DS** ;
- installer le rôle **DNS Server** ;
- préparer la promotion du serveur en contrôleur de domaine ;
- vérifier que les prérequis nécessaires sont satisfaits.

---

# 🖥️ Environnement

| Élément | Configuration |
|---|---|
| Serveur | `SRV-01-DC1` |
| Système | Windows Server 2025 Datacenter |
| Adresse IP | `192.168.10.10/24` |
| Domaine prévu | `logiflex.infra` |
| Rôle | Contrôleur de domaine principal |
| Services | AD DS / DNS / Hyper-V |
| Réseau | `192.168.10.0/24` |

---

# 🌐 Configuration réseau

Le serveur `SRV-01-DC1` est configuré avec une adresse IPv4 statique.

```text
Adresse IP :       192.168.10.10
Masque :           255.255.255.0
Réseau :           192.168.10.0/24
Passerelle :       192.168.10.2
