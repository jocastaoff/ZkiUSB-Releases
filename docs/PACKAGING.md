# Packaging ZkiUSB

## Fichiers créés

Ce dossier contient plusieurs méthodes pour créer un installateur pour ZkiUSB :

### 1. Inno Setup (Recommandé)

| Fichier | Description |
|---------|-------------|
| `ZkiUSB.iss` | Script Inno Setup pour créer l'installateur |
| `build-installer.bat` | Script batch automatique (clic-droit → Exécuter) |
| `Package.ps1` | Script PowerShell avancé avec options |
| `LICENSE.txt` | Licence affichée pendant l'installation |

**Utilisation :**
```bash
# Simple
build-installer.bat

# PowerShell avancé
.\Package.ps1 -CreateInstaller
```

### 2. Version Portable

| Fichier | Description |
|---------|-------------|
| `create-portable.bat` | Crée une archive ZIP portable |

**Utilisation :**
```bash
create-portable.bat
# → ZkiUSB-Portable.zip
```

### 3. Microsoft Store (MSIX)

| Fichier | Description |
|---------|-------------|
| `ZkiUSB.wapproj` | Projet Packaging Windows |
| `Package.appxmanifest` | Manifeste du package |

**Nécessite :** Visual Studio avec charge de travail "Développement UWP"

## Structure des sorties

```
installer/
├── ZkiUSB-Setup-1.0.0.exe    # Installateur Windows
└── ZkiUSB-Setup-1.0.0.log    # Log de création

publish/
├── ZkiUSB.UI.exe             # Exécutable portable
└── *.dll                     # Dépendances

ZkiUSB-Portable.zip           # Archive portable
```

## Prérequis

1. **.NET 8 SDK** - Déjà nécessaire pour le développement
2. **Inno Setup 6** - Téléchargement : https://jrsoftware.org/isdl.php

## Installation silencieuse (entreprise)

```bash
# Installation sans interface
ZkiUSB-Setup-1.0.0.exe /VERYSILENT /NORESTART

# Avec chemin personnalisé
ZkiUSB-Setup-1.0.0.exe /VERYSILENT /DIR="D:\Applications\ZkiUSB"

# Créer un raccourci bureau
ZkiUSB-Setup-1.0.0.exe /VERYSILENT /TASKS=desktopicon
```

## Désinstallation

```bash
# Via le menu Démarrer
# OU
"%ProgramFiles%\ZkiUSB\unins000.exe" /SILENT
```

## Personnalisation

### Modifier la version

Éditer `ZkiUSB.iss` :
```pascal
#define MyAppVersion "1.1.0"
```

### Modifier l'icône

Remplacer `ZkiUSB.ico` et relancer le build.

### Ajouter une bannière

Créer `wizard-image.bmp` (164×314 px) et ajouter dans `[Files]` :
```pascal
[Files]
Source: "wizard-image.bmp"; DestDir: "{tmp}"; Flags: dontcopy
```

## Dépannage

| Problème | Solution |
|----------|----------|
| ISCC.exe non trouvé | Vérifier le chemin dans `build-installer.bat` |
| Compilation échoue | Vérifier avec `dotnet build` |
| Fichiers manquants | Vérifier que `dotnet publish` a été exécuté |
| Installateur corrompu | Supprimer `installer/` et relancer |
