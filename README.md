<div align="center">

<img src="ZkiUSB.ico" width="120" height="120" alt="ZkiUSB Logo">

# 🔌 ZkiUSB

### Logiciel professionnel de détection et réparation de clés USB

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/murattatar/ZkiUSB/releases)
[![.NET](https://img.shields.io/badge/.NET-8.0-512BD4.svg)](https://dotnet.microsoft.com/)
[![Platform](https://img.shields.io/badge/platform-Windows-0078D6.svg)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/license-Proprietary-red.svg)](LICENSE.txt)

<img src="https://img.shields.io/badge/Architecture-Clean%20Architecture-success">
<img src="https://img.shields.io/badge/Pattern-MVVM-success">
<img src="https://img.shields.io/badge/UI-Liquid%20Glass-purple">

</div>

---

## 📋 Table des matières

- [📖 Introduction](#-introduction)
- [✨ Fonctionnalités](#-fonctionnalités)
- [🖼️ Preview](#️-preview)
- [💾 Installation](#-installation)
- [🚀 Utilisation](#-utilisation)
- [🏗️ Architecture](#️-architecture)
- [🛡️ Sécurité DLL](#️-sécurité-dll)
- [📝 Documentation](#-documentation)
- [🤝 Contribution](#-contribution)
- [📄 Licence](#-licence)

---

## 📖 Introduction

**ZkiUSB** est un logiciel desktop Windows moderne développé en C# dédié à la **détection**, l'**analyse** et la **résolution de problèmes** liés aux clés USB.

Conçu avec une approche **sécurité-données-first**, il offre une interface utilisateur intuitive et professionnelle avec un design **Liquid Glass** moderne pour gérer vos périphériques de stockage amovibles.

---

## ✨ Fonctionnalités

### 🔍 Détection & Identification USB

- Détection automatique des clés USB à la connexion
- Affichage des informations détaillées :
  - Nom et fabricant
  - Capacité totale et espace utilisé
  - Système de fichiers (FAT32, NTFS, exFAT, etc.)
  - Statut du périphérique (Sain, RAW, Corrompu, etc.)
  - Numéro de série
  - Lettre de lecteur

### 🔬 Analyse & Diagnostic

| Type d'analyse | Description | Durée |
|----------------|-------------|-------|
| **Vérification rapide** | Scan rapide des problèmes courants | ~5s |
| **Diagnostic complet** | Analyse approfondie de tous les aspects | ~30s |

**Vérifications effectuées :**
- Accessibilité du périphérique
- Système de fichiers
- Partitions
- Secteur de boot
- Erreurs logiques
- Bad sectors (via SMART)

### 🛠️ Actions de réparation

| Action | Description | Risque données |
|--------|-------------|----------------|
| 🔧 Vérifier/Réparer FS | Chkdsk avec correction automatique | Faible |
| 🏗 Reconstruire partitions | Recréation de la table des partitions | Élevé |
| 💾 Récupération données | Copie des fichiers vers un emplacement sûr | Aucun |
| ⚠️ Formater | Formatage complet du périphérique | Total |
| 🔄 Changer lettre | Attribution d'une nouvelle lettre de lecteur | Aucun |

### 🛡️ Protection DLL (SEC INJ)

Système de sécurité avancé détectant et bloquant les DLL malveillantes provenant de clés USB :

- 🔒 **Blocage temps réel** des DLL suspectes
- 🔍 **Vérification de signature** numérique
- 📋 **Whitelist/Blacklist** par hash SHA256
- 🚨 **Détection heuristique** de patterns suspects
- 📊 **Journal complet** des menaces bloquées

---

## 🖼️ Preview

<div align="center">

| Écran d'accueil | Diagnostic | Sécurité DLL |
|-----------------|------------|--------------|
| ![Accueil](https://github.com/jocastaoff/ZkiUSB/raw/main/screenshots/home.png) | ![Diagnostic](https://github.com/jocastaoff/ZkiUSB/raw/main/screenshots/diagnostic.png) | ![SEC INJ](https://github.com/jocastaoff/ZkiUSB/raw/main/screenshots/security.png) |

</div>

> 📸 **Ajoutez vos captures d'écran** dans le dossier `screenshots/` :
> - `home.png` - Écran principal avec liste USB
> - `diagnostic.png` - Écran de diagnostic
> - `security.png` - Onglet SEC INJ (protection DLL)

---

## 💾 Installation

### 📥 Téléchargement

Téléchargez la dernière version :

- **💿 Installateur Windows** : `ZkiUSB-Setup-1.0.0.exe` (~50 MB)
- **📦 Version Portable** : `ZkiUSB-Portable.zip` (~45 MB)

➡️ [Page des releases](https://github.com/murattatar/ZkiUSB/releases)

### 🖥️ Prérequis

- **OS** : Windows 10/11 (x64)
- **Runtime** : .NET 8.0 (inclus dans l'installateur)
- **Privilèges** : Administrateur (pour certaines opérations)

---

## 🚀 Utilisation

### Démarrage rapide

1. **Lancez** ZkiUSB
2. **Branchez** une clé USB (elle sera automatiquement détectée)
3. **Sélectionnez** le périphérique dans la liste
4. **Choisissez** une action :
   - ⚡ Vérification rapide
   - 🔍 Diagnostic complet
   - 🔧 Réparation

### ⚠️ Avertissements de sécurité

> **IMPORTANT**
>
> - Le formatage **EFFACE TOUTES LES DONNÉES**
> - Une **double confirmation** est requise pour le formatage
> - **Sauvegardez** vos données avant toute réparation
> - Certaines opérations nécessitent les **droits administrateur**

---

## 🏗️ Architecture

Le projet suit une **Clean Architecture** avec séparation stricte des responsabilités :

```
┌─────────────────────────────────────────────────────────────┐
│                         ZkiUSB.UI                           │
│                   (Interface WPF - MVVM)                    │
│              Liquid Glass Design + WPF-UI                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      ZkiUSB.Services                        │
│              (Services USB, Disque, WMI)                    │
│         USB Detection | Diagnostics | Repair | Security     │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                        ZkiUSB.Core                          │
│              (Modèles, Interfaces, Enums)                   │
│               Aucune dépendance externe                     │
└─────────────────────────────────────────────────────────────┘
```

### Technologies utilisées

| Couche | Technologies |
|--------|--------------|
| **UI** | WPF, WPF-UI, CommunityToolkit.Mvvm |
| **Services** | WMI, P/Invoke, System.Management |
| **Core** | .NET 8, C# 12 |
| **Tests** | xUnit, Moq |

---

## 🛡️ Sécurité DLL (SEC INJ)

### Présentation

Le système **SEC INJ** (Secure DLL Injection Prevention) protège votre ordinateur contre les malwares USB en contrôlant le chargement des DLL.

### Types de menaces détectées

| Type | Description | Action |
|------|-------------|--------|
| `KnownMalware` | Hash connu dans la liste noire | 🚫 Blocage |
| `UsbOrigin` | DLL provenant d'une clé USB | 🚫 Blocage |
| `SuspiciousName` | Pattern suspect (autorun, recycler) | ⚠️ Alerte |
| `Unsigned` | DLL non signée numériquement | ⚠️ Alerte |
| `SystemDllHijacking` | Tentative de remplacement système | 🚫 Blocage |

### Modes de protection

- **Permissif** : Alertes uniquement
- **Modéré** : Blocage des menaces connues (défaut)
- **Strict** : Whitelist uniquement

---

## 📝 Documentation

- 📘 **[Documentation Technique](DOCUMENTATION_TECHNIQUE.md)** - Architecture détaillée
- 📗 **[Changelog](CHANGELOG.md)** - Historique des versions
- 📕 **[Guide d'installation](INSTALLER.md)** - Création de l'installateur
- 📙 **[Packaging](PACKAGING.md)** - Guide du packager

---

## 🤝 Contribution

Les contributions sont les bienvenues ! Voir [CONTRIBUTING.md](CONTRIBUTING.md) pour les guidelines.

### Rapport de bugs

Si vous rencontrez un problème :

1. Vérifiez les [issues existantes](https://github.com/murattatar/ZkiUSB/issues)
2. Créez une nouvelle issue avec :
   - Description du problème
   - Étapes de reproduction
   - Version de Windows
   - Logs d'erreur (dans `%LocalAppData%\ZkiUSB\CrashLogs`)

### Développement

```bash
# Cloner le repository
git clone https://github.com/murattatar/ZkiUSB.git
cd ZkiUSB

# Restaurer les packages
dotnet restore

# Compiler
dotnet build --configuration Release

# Exécuter
dotnet run --project ZkiUSB.UI
```

---

## 📄 Licence

Ce logiciel est distribué sous **licence propriétaire**.

```
Copyright © 2026 Murat Tatar - Tous droits réservés

Ce logiciel est fourni "en l'état", sans garantie d'aucune sorte.
L'utilisation est soumise aux termes de la licence EULA.
```

➡️ Voir [LICENSE.txt](LICENSE.txt) pour le texte complet.

---

## 👤 Auteur

**Murat Tatar**

- 🏗️ Développement et conception : 2026
- 📧 Contact : murat.tatar.76610.gmail.com
- 🌐 GitHub : [@jocastaoff](https://github.com/jocastaoff)

---

<div align="center">

**⭐ Star ce projet si vous le trouvez utile !**

Made with ❤️ in France

</div>
