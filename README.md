# Projet Vagrant pour le Déploiement de GLPI


Ce projet configure et déploie 3 machines virtuelles (VM) à l'aide de Vagrant. Chaque VM est configurée pour différents rôles, incluant un serveur GLPI, un poste client des clients avec FusionInventory, et un contrôleur de domaine Active Directory.

## Prérequis

Avant de commencer, assurez-vous d'avoir les logiciels suivants installés :

- [Vagrant](https://www.vagrantup.com/)
- [VirtualBox](https://www.virtualbox.org/)
- Un hôte supportant VirtualBox et capable de gérer les ressources nécessaires.

## Machines Déployées

### 1. **Serveur Debian 9 - GLPI**
- **Nom d'hôte :** `SRV-GLPI-01`
- **Description :** Installe et configure GLPI (v9.3.3) avec une base de données MySQL.
- **Spécifications :**
  - 1 CPU, 1024 Mo de RAM
  - Serveur Web Apache avec PHP 7.0
  - MariaDB pour la gestion de la base de données
- **Rôles Principaux :**
  - Serve GLPI
  - Plugin FusionInventory


### 2. **Client Debian 10 - FusionInventory**
- **Nom d'hôte :** `PC-CLI-02`
- **Description :** Installe l'agent FusionInventory et configure un utilisateur administrateur local.
- **Spécifications :**
  - 1 CPU, 1024 Mo de RAM
- **Rôles Principaux :**
  - Envoie des inventaires matériels et logiciels au serveur GLPI.



### 3. **Client AlmaLinux 8 - FusionInventory**
- **Nom d'hôte :** `PC-CLI-01`
- **Description :** Installe l'agent FusionInventory et configure l'environnement utilisateur.
- **Spécifications :**
  - 1 CPU, 1024 Mo de RAM
- **Rôles Principaux :**
  - Même rôle que le client Debian 10.

### 4. **Serveur Windows Server 2019 - Active Directory**
- **Nom d'hôte :** `SRV-DC-01`
- **Description :** Configure un contrôleur de domaine Active Directory (s2c.lab) et des comptes utilisateurs.
- **Spécifications :**
  - 1 CPU, 2048 Mo de RAM
- **Rôles Principaux :**
  - Fournit un environnement Active Directory pour le domaine `s2c.lab`.


