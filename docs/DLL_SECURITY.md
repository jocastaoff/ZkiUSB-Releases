# Securite DLL - Documentation Technique

## Vue d'ensemble

Le systeme de securite DLL de ZkiUSB fournit une protection proactive contre les malwares USB en controlant le chargement des bibliotheques dynamiques (DLL).

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      ZkiUSB.UI                                   │
│              (Onglet Securite - DllSecurityView)                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│              DllSecurityViewModel                                │
│         (UI, commandes, gestion des evenements)                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    IDllSecurityService                           │
│                    (Interface publique)                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   DllSecurityService                             │
│    (Detection, verification, blocage, historique)               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│              DllSecurityNative (Win32 API)                       │
│         (WinTrust, PSAPI, verification signatures)              │
└─────────────────────────────────────────────────────────────────┘
```

## Modes de protection

### Mode Permissif
- Alertes uniquement
- Ne bloque que les malwares connus (blacklist)
- Journalisation complete

### Mode Modere (defaut)
- Bloque les menaces connues
- Alerte sur les suspects
- Verification des signatures
- Blocage des DLL USB active

### Mode Strict
- Whitelist uniquement
- Toutes les DLL non approuvees bloquees
- Verification stricte des signatures
- Blocage total des DLL USB

## Types de menaces

| Type | Description | Severite |
|------|-------------|----------|
| `KnownMalware` | Hash dans liste noire | Critique |
| `SuspiciousName` | Pattern suspect (autorun, recycler) | Moyen |
| `UsbOrigin` | Provenance USB | Moyen |
| `Unsigned` | Non signe | Faible |
| `InvalidSignature` | Signature invalide | Eleve |
| `UntrustedPublisher` | Editeur non approuve | Moyen |
| `SystemDllHijacking` | Remplacement DLL systeme | Critique |
| `SuspiciousLocation` | Emplacement temporaire | Moyen |
| `UnknownHash` | Hash inconnu (mode strict) | Faible |

## Integration USB

Quand une cle USB est connectee :

1. Detection du peripherique via `UsbDeviceService`
2. Declenchement automatique du scan de securite
3. Analyse des processus ayant charge des DLL du peripherique
4. Notification en temps reel si menace detectee
5. Journalisation dans l'historique

## Configuration

### Via l'interface
- Selection du mode (Permissif/Modere/Strict)
- Activation du blocage USB
- Activation du blocage des DLL non signees
- Scan manuel des processus

### Programmation

```csharp
// Configuration par defaut
var config = DllSecurityConfig.CreateDefault();

// Configuration stricte
var strictConfig = DllSecurityConfig.CreateStrict();

// Configuration personnalisee
var customConfig = new DllSecurityConfig
{
    Mode = ProtectionMode.Moderate,
    BlockUsbOriginDlls = true,
    BlockUnsignedDlls = false,
    StrictMode = false,
    EnableHeuristics = true,
    AutoScanIntervalMs = 5000
};
```

## API publique

### Interface IDllSecurityService

```csharp
// Evenements
event EventHandler<DllBlockedEventArgs> DllBlocked;
event EventHandler<ThreatDetectedEventArgs> ThreatDetected;
event EventHandler<ProtectionStatusEventArgs> ProtectionStatusChanged;

// Methodes principales
Task InitializeAsync(DllSecurityConfig config);
void StartProtection();
void StopProtection();
Task<DllVerificationResult> VerifyDllAsync(string dllPath);
Task<List<BlockedDllInfo>> ScanProcessAsync(int processId);
Task<List<BlockedDllInfo>> ScanUsbRelatedProcessesAsync(string driveLetter);
Task AddToWhitelistAsync(string hash);
Task AddToBlacklistAsync(string hash, string reason);
```

## Securite native

### WinTrust
Verification des signatures Authenticode via WinVerifyTrust

### PSAPI
Enumeration des modules charges par processus

### Heuristique
Detection basee sur :
- Patterns de noms de fichiers
- Chemins suspects
- Similarite avec DLL systeme
- Attributs de fichier

## Fichiers cles

| Fichier | Description |
|---------|-------------|
| `IDllSecurityService.cs` | Interface du service |
| `DllSecurityService.cs` | Implementation |
| `DllSecurityViewModel.cs` | ViewModel UI |
| `DllSecurityView.xaml` | Vue XAML |
| `DllSecurityNative.cs` | Fonctions Win32 |
| `BlockedDllInfo.cs` | Modele de donnees |
| `DllSecurityConfig.cs` | Configuration |
| `DllThreatType.cs` | Types de menaces |
| `SecurityEnums.cs` | Enumerations partagees |

## Export de rapports

Format CSV avec les colonnes :
- Date de detection
- Type de menace
- Severite
- Nom DLL
- Processus
- Raison du blocage
- Chemin complet
- Hash SHA256
- Etat de signature

## Notes de securite

1. **Execution** : Le service necessite des privileges utilisateur standard
2. **Performance** : Impact minimal (< 1% CPU en scan automatique)
3. **Faux positifs** : Possibilite d'ajouter a la whitelist
4. **Persistance** : Configuration sauvegardee en memoire (pas de persistence fichier)

## Roadmap

- [ ] Politiques GPO
- [ ] Integration Windows Defender
- [ ] Quarantaine automatique
- [ ] Analyse comportementale
- [ ] Integration SIEM
