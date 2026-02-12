# Changelog - ZkiUSB

**Auteur:** Murat Tatar  
**Projet:** ZkiUSB - Logiciel de détection et réparation de clés USB

---

## [1.0.1] - 12 février 2026

### ✨ Améliorations

- **Onglet Santé entièrement fonctionnel**
  - Design Liquid Glass complet avec animations de flottement
  - Synchronisation automatique avec les périphériques USB connectés
  - Affichage des statistiques globales (Sain/Avertissement/Critique)
  - Données réelles de santé pour chaque périphérique
  - Historique détaillé avec timestamps
  - Prédictions de durée de vie basées sur l'historique

### 🔧 Corrections

- Correction de la propriété PlaceholderText incompatible WPF
- Correction de la propriété Background.Opacity
- Correction des références DeviceInfo dans les événements USB

---

## [1.0.0] - 2026

### ✨ Ajouté

- **Architecture complète**
  - Séparation en 3 projets: Core, Services, UI
  - Pattern MVVM avec CommunityToolkit.Mvvm
  - Injection de dépendances

- **Détection USB**
  - Détection automatique des clés USB via WMI
  - Surveillance en temps réel (connexion/déconnexion)
  - Affichage des informations détaillées

- **Diagnostic**
  - Diagnostic complet (6 vérifications)
  - Vérification rapide
  - Rapport détaillé avec recommandations
  - Détection des problèmes:
    - Système de fichiers RAW
    - Partitions endommagées
    - Secteurs défectueux
    - Erreurs logiques

- **Réparation**
  - Réparation du système de fichiers (chkdsk)
  - Reconstruction des partitions
  - Récupération de données
  - Formatage (avec double confirmation)
  - Changement de lettre de lecteur

- **Interface utilisateur**
  - Thème sombre moderne
  - Dégradés bleu/violet
  - Animations fluides
  - Design responsive
  - Affichage de la progression en temps réel

- **Sécurité**
  - Confirmations avant actions destructives
  - Avertissements de risque de données
  - Sauvegarde automatique proposée

### 📦 Dépendances

- .NET 8.0
- System.Management 8.0.0
- CommunityToolkit.Mvvm 8.2.2
- WPF-UI 3.0.4
- Microsoft.Extensions.DependencyInjection 8.0.0

### 📝 Documentation

- README complet
- Documentation technique détaillée
- Commentaires dans le code (auteur: Murat Tatar)

---

## Roadmap

### Version 1.1.0 - Publiée le 10 février 2026

#### ✨ Nouvelles fonctionnalités

- [x] **Vérification d'authenticité des clés USB**
  - Détection des clés USB contrefaites (fausse capacité)
  - Test complet de la capacité réelle vs. annoncée
  - Génération d'un rapport détaillé avec verdict

- [x] **Onglet "Santé" complet** 🎉
  - Historique complet de chaque clé USB (par numéro de série)
  - Affichage de la "Santé" sur une timeline
  - Prédiction de défaillance (tendance, risque, durée de vie estimée)
  - Système de tags personnalisés ("Backup Janvier", "Client X", etc.)
  - Notes par périphérique
  - Export CSV de l'inventaire complet
  - Design Liquid Glass moderne et cohérent

#### 🔧 Améliorations techniques

- Persistance JSON locale des données de santé
- Algorithmes de prédiction basés sur l'historique
- Calcul automatique du score de santé (0-100)
- Détection des tendances (amélioration, stable, dégradation, critique)

### Version 1.2.0 (prévue)

- [ ] Benchmark performance des clés USB
- [ ] Test de vitesse lecture/écriture
- [ ] Analyse approfondie des bad sectors
- [ ] Récupération de données avancée

### Version 2.0.0 (prévue)

- [ ] Support des disques durs externes
- [ ] Support des cartes SD/microSD
- [ ] Interface web optionnelle
- [ ] API REST pour intégration

---

## Notes de version

### 1.0.0

Cette version initiale établit les fondations solides du produit avec:
- Une architecture propre et extensible
- Une interface utilisateur moderne et intuitive
- Des fonctionnalités complètes de détection et réparation
- Une approche sécurité-données-first

Le logiciel est prêt pour un usage en production après tests approfondis.

---

**Auteur:** Murat Tatar  
**Copyright:** © 2026 Murat Tatar
