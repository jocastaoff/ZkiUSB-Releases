# Notes de Release - ZkiUSB

## Version 1.0.1 (2026-02-12)

### 🎉 Onglet Santé entièrement fonctionnel

#### ✨ Nouveautés

- **Design Liquid Glass complet**
  - Effet de verre translucide avec bordures givrées
  - Animations de flottement subtiles
  - Ombres naturelles et profondeur
  - Cohérence visuelle avec le reste de l'application

- **Synchronisation automatique des données**
  - Enregistrement automatique des périphériques USB connectés
  - Mise à jour en temps réel de l'historique
  - Statistiques globales (Sain/Avertissement/Critique)

- **Informations de santé détaillées**
  - Score de santé (0-100%)
  - Tendance (📈 📉 ➡️ ⚠️)
  - Durée de vie estimée en jours
  - Risque de défaillance
  - Historique complet des connexions

- **Statistiques de performance**
  - Vitesse de lecture maximale
  - Vitesse d'écriture maximale
  - Nombre de diagnostics effectués
  - Nombre total de connexions

#### 🔧 Corrections techniques

- Correction des erreurs XAML (PlaceholderText, Background.Opacity)
- Correction des références DeviceInfo dans les événements USB
- Amélioration de la stabilité du service de santé

#### 📥 Téléchargement

- Installateur : `ZkiUSB-Setup-1.0.1.exe`

---

## Version 1.0.0 (2026-02-08)

### 🎉 Première version stable

#### ✨ Fonctionnalités

- **🔌 Détection USB automatique**
  - Détection à chaud des clés USB
  - Informations détaillées (capacité, système de fichiers, statut)
  - Support FAT32, NTFS, exFAT

- **🔬 Diagnostic intelligent**
  - Vérification rapide (~5s)
  - Diagnostic complet (~30s)
  - Analyse des secteurs de boot, partitions, erreurs logiques

- **🛠️ Outils de réparation**
  - Réparation du système de fichiers (Chkdsk)
  - Reconstruction de partitions
  - Récupération de données
  - Formatage sécurisé avec double confirmation
  - Nettoyage forcé

- **🛡️ Protection DLL (SEC INJ)**
  - Détection temps réel des menaces USB
  - Blocage des DLL malveillantes
  - Vérification de signature numérique
  - Heuristique de détection
  - Whitelist/Blacklist

- **🎨 Interface Liquid Glass**
  - Design moderne et fluide
  - Animations subtiles
  - Thème sombre
  - Interface responsive

#### 🏗️ Architecture

- Clean Architecture modulaire
- Pattern MVVM
- .NET 8.0
- WPF avec WPF-UI
- Injection de dépendances

#### 📦 Installation

- Installateur Windows (Inno Setup)
- Version portable disponible
- .NET 8 inclus dans l'installateur

---

## Créer une nouvelle release

### Étapes

1. Mettre à jour `CHANGELOG.md`
2. Modifier la version dans `ZkiUSB.iss` :
   ```pascal
   #define MyAppVersion "1.1.0"
   ```
3. Créer un tag Git :
   ```bash
   git tag -a v1.1.0 -m "Release v1.1.0"
   git push origin v1.1.0
   ```
4. Exécuter `build-installer.bat`
5. Créer la release GitHub avec :
   - `ZkiUSB-Setup-X.X.X.exe`
   - `ZkiUSB-Portable.zip`
   - Notes de release

### Template de release

```markdown
## 🎉 Nouveautés

### ✨ Fonctionnalités
- Feature 1
- Feature 2

### 🐛 Corrections
- Fix 1
- Fix 2

### 🔧 Améliorations
- Improvement 1
- Improvement 2

### 📥 Téléchargement
- [Installateur]( lien )
- [Portable]( lien )

**Checksums:**
```
SHA256: xxx
```
```
