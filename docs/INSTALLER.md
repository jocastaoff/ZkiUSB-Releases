# Création de l'installateur ZkiUSB

## Prérequis

1. **Inno Setup 6** (ou plus récent)
   - Téléchargez depuis : https://jrsoftware.org/isdl.php
   - Installez avec les composants par défaut

2. **.NET 8 SDK**
   - Déjà installé si vous compilez le projet

## Méthode 1 : Script Batch (Automatique)

La méthode la plus simple :

```bash
# Double-cliquez sur le fichier
build-installer.bat
```

Le script va :
1. Nettoyer les anciens builds
2. Compiler la solution en Release
3. Publier l'application (single-file, self-contained)
4. Créer l'installateur avec Inno Setup

**Résultat** : `installer\ZkiUSB-Setup-1.0.0.exe`

## Méthode 2 : Manuelle

### Étape 1 : Compiler le projet

```bash
dotnet build ZkiUSB.sln -c Release
```

### Étape 2 : Publier l'application

```bash
# Version portable (recommandée)
dotnet publish ZkiUSB.UI -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true

# Version framework-dependant (plus légère)
dotnet publish ZkiUSB.UI -c Release -r win-x64 --self-contained false
```

### Étape 3 : Créer l'installateur

1. Ouvrez **Inno Setup Compiler**
2. Fichier → Ouvrir → Sélectionnez `ZkiUSB.iss`
3. Appuyez sur **F9** (Build)

## Options de l'installateur

### Installation silencieuse (entreprise)

```bash
ZkiUSB-Setup-1.0.0.exe /VERYSILENT /NORESTART
```

### Désinstallation

```bash
# Via le menu Démarrer
# Ou :
"C:\Program Files\ZkiUSB\unins000.exe"
```

## Structure de l'installateur

```
ZkiUSB-Setup-1.0.0.exe
├── ZkiUSB.UI.exe (application principale)
├── *.dll (bibliothèques)
├── ZkiUSB.ico (icône)
├── README.md
├── CHANGELOG.md
└── DOCUMENTATION_TECHNIQUE.md
```

## Personnalisation

Modifier `ZkiUSB.iss` pour :

- Changer la version : `#define MyAppVersion "1.1.0"`
- Ajouter une licence : `LicenseFile=LICENSE.txt`
- Modifier l'icône : `SetupIconFile=ZkiUSB.ico`

## Distribution

### Version recommandée
- **Self-contained** : Inclut .NET 8 Runtime (~80 MB)
- Fonctionne sur Windows 10/11 sans prérequis

### Version légère
- **Framework-dependant** : Nécessite .NET 8 Runtime (~15 MB)
- Téléchargement plus rapide
- Nécessite .NET 8 Runtime préinstallé

## Signature numérique (optionnel)

Pour signer l'installateur avec un certificat :

```bash
signtool sign /f certificat.pfx /p motdepasse /t http://timestamp.digicert.com installer\ZkiUSB-Setup-1.0.0.exe
```

## Dépannage

### "ISCC.exe non trouvé"
Vérifiez le chemin d'installation de Inno Setup dans `build-installer.bat`

### "La compilation échoue"
Vérifiez que le projet compile en Release :
```bash
dotnet build ZkiUSB.sln -c Release
```

### "Fichiers manquants"
Assurez-vous d'avoir lancé `dotnet publish` avant de créer l'installateur.
