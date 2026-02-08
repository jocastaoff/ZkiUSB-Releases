# Documentation Technique - ZkiUSB

**Auteur:** Murat Tatar  
**Date:** 2026  
**Version:** 1.0.0

---

## 1. Architecture détaillée

### 1.1 Vue d'ensemble

L'application suit le pattern architectural **MVVM** (Model-View-ViewModel) avec une séparation stricte des couches:

```
┌────────────────────────────────────────────────────────────────┐
│                         UI Layer                               │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Views      │  │  ViewModels  │  │    Converters       │  │
│  │  (XAML)      │──│   (C#)       │  │    (XAML↔C#)        │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
                              │
                              ▼ DI (Dependency Injection)
┌────────────────────────────────────────────────────────────────┐
│                      Services Layer                            │
│  ┌────────────────┐ ┌────────────────┐ ┌──────────────────┐   │
│  │ IUsbDeviceSvc  │ │ IDiagnosticSvc │ │  IRepairService  │   │
│  │ Implementation │ │ Implementation │ │  Implementation  │   │
│  └────────────────┘ └────────────────┘ └──────────────────┘   │
└────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌────────────────────────────────────────────────────────────────┐
│                        Core Layer                              │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Models     │  │   Enums      │  │    Interfaces       │  │
│  │  (POCOs)     │  │  (Constants) │  │   (Contracts)       │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

### 1.2 Flux de données

```
User Action → View → ViewModel → Service → Core Model → WMI/Win32 API
                 ↑_________________________|
                   Result/Progress Events
```

---

## 2. Modèles de données (Core)

### 2.1 UsbDeviceInfo

Classe principale représentant un périphérique USB.

```csharp
public class UsbDeviceInfo
{
    public string DeviceId { get; set; }           // Identifiant unique
    public string FriendlyName { get; set; }       // Nom affiché
    public string Manufacturer { get; set; }       // Fabricant
    public string SerialNumber { get; set; }       // N° de série
    public string? DriveLetter { get; set; }       // Lettre (ex: E:)
    public long TotalSize { get; set; }            // Capacité (octets)
    public long UsedSpace { get; set; }            // Utilisé (octets)
    public FileSystemType FileSystem { get; set; } // Type FS
    public UsbDeviceStatus Status { get; set; }    // État
    public List<PartitionInfo> Partitions { get; set; }
    public List<DeviceIssue> Issues { get; set; }
}
```

### 2.2 Enums principaux

```csharp
public enum UsbDeviceStatus
{
    Healthy,        // Fonctionnement normal
    Raw,            // Système RAW (non formaté)
    Corrupted,      // Erreurs détectées
    ReadOnly,       // Lecture seule
    Inaccessible,   // Non accessible
    WriteProtected, // Protection écriture
    Unknown         // État inconnu
}

public enum RepairOperationType
{
    CheckFileSystem,
    RepairFileSystem,
    RebuildPartition,
    RecoverData,
    Format,
    ChangeDriveLetter
}

public enum SeverityLevel
{
    Info,      // Information
    Warning,   // Avertissement
    Critical   // Problème critique
}
```

---

## 3. Services

### 3.1 UsbDeviceService

Gère la détection et la surveillance des périphériques USB.

#### Responsabilités
- Détection initiale des périphériques
- Surveillance en temps réel (connexion/déconnexion)
- Mise en cache des informations

#### Implémentation technique
- Utilise **WMI** (Windows Management Instrumentation)
- Événements `__InstanceCreationEvent` / `__InstanceDeletionEvent`
- Polling fallback toutes les 2 secondes

```csharp
public interface IUsbDeviceService
{
    event EventHandler<UsbDeviceEventArgs> DeviceConnected;
    event EventHandler<UsbDeviceEventArgs> DeviceDisconnected;
    
    Task<List<UsbDeviceInfo>> GetConnectedDevicesAsync();
    Task<UsbDeviceInfo?> GetDeviceInfoAsync(string deviceId);
    void StartMonitoring();
    void StopMonitoring();
}
```

### 3.2 DiagnosticService

Effectue l'analyse des périphériques.

#### Vérifications effectuées

| # | Vérification | Durée estimée | Criticité |
|---|--------------|---------------|-----------|
| 1 | Accessibilité | < 1s | Haute |
| 2 | Partitions | 1-2s | Haute |
| 3 | Système de fichiers | 2-5s | Haute |
| 4 | Secteur de boot | 1-2s | Moyenne |
| 5 | Erreurs logiques | 2-5s | Haute |
| 6 | Bad sectors (SMART) | 2-3s | Moyenne |

#### Progression
```csharp
event EventHandler<DiagnosticProgressEventArgs> ProgressChanged;

// ProgressPercentage: 0-100
// Message: Description de l'étape
// CurrentCheck: Nom de la vérification
```

### 3.3 RepairService

Exécute les opérations de réparation.

#### Méthodes principales

```csharp
Task<RepairResult> RepairFileSystemAsync(UsbDeviceInfo device, RepairOptions options);
Task<RepairResult> RebuildPartitionsAsync(UsbDeviceInfo device);
Task<RepairResult> RecoverDataAsync(UsbDeviceInfo device, string destinationPath);
Task<RepairResult> FormatDeviceAsync(UsbDeviceInfo device, FormatOptions options);
```

#### Commandes système utilisées

| Opération | Commande | Niveau privilège |
|-----------|----------|------------------|
| Réparer FS | `chkdsk /F /X` | Admin recommandé |
| Reconstruire partitions | `diskpart` | Admin requis |
| Formater | `diskpart` | Admin requis |
| Changer lettre | `diskpart` | Admin recommandé |

---

## 4. Interface utilisateur

### 4.1 Structure des vues

```
MainWindow (1200x800)
├── Header (Logo + Titre + Bouton Refresh)
├── Main Content
│   ├── Left Panel (350px)
│   │   └── Device List
│   └── Right Panel
│       ├── Welcome Screen (défaut)
│       └── Device Details
│           ├── Device Info Card
│           ├── Action Buttons
│           ├── Progress Panel
│           └── Diagnostic Report
└── Footer (Status + Copyright)
```

### 4.2 Thème et design

**Palette de couleurs (Dark Theme)**

| Élément | Couleur | Hex |
|---------|---------|-----|
| Background | Slate 900 | #0F172A |
| Surface | Slate 800 | #1E293B |
| Surface Light | Slate 700 | #334155 |
| Primary | Blue 500 | #3B82F6 |
| Secondary | Violet 500 | #8B5CF6 |
| Success | Emerald 500 | #10B981 |
| Warning | Amber 500 | #F59E0B |
| Error | Red 500 | #EF4444 |
| Text Primary | Slate 100 | #F1F5F9 |
| Text Secondary | Slate 400 | #94A3B8 |

### 4.3 ViewModels

#### MainViewModel

```csharp
public partial class MainViewModel : ObservableObject
{
    // Collections
    [ObservableProperty] ObservableCollection<UsbDeviceViewModel> Devices;
    [ObservableProperty] UsbDeviceViewModel? SelectedDevice;
    [ObservableProperty] DiagnosticReportViewModel? DiagnosticReport;
    
    // États
    [ObservableProperty] bool IsScanning;
    [ObservableProperty] bool IsDiagnosing;
    [ObservableProperty] bool IsRepairing;
    
    // Progression
    [ObservableProperty] string StatusMessage;
    [ObservableProperty] int ProgressValue;
    [ObservableProperty] string CurrentOperation;
    
    // Commands
    [RelayCommand] async Task RefreshDevicesAsync();
    [RelayCommand] async Task RunDiagnosticAsync();
    [RelayCommand] async Task RepairFileSystemAsync();
    [RelayCommand] async Task FormatDeviceAsync();
}
```

---

## 5. Injection de dépendances

Configuration dans `App.xaml.cs`:

```csharp
private void ConfigureServices(IServiceCollection services)
{
    // Services - Singleton (état partagé)
    services.AddSingleton<IUsbDeviceService, UsbDeviceService>();
    services.AddSingleton<IDiagnosticService, DiagnosticService>();
    services.AddSingleton<IRepairService, RepairService>();
    
    // ViewModels - Transient (nouvelle instance par fenêtre)
    services.AddTransient<MainViewModel>();
}
```

---

## 6. Gestion des erreurs

### 6.1 Stratégie

| Niveau | Type | Gestion |
|--------|------|---------|
| Service | Exception technique | Catch → Log → RepairResult.Error |
| ViewModel | Exception métier | Try/Catch → Message utilisateur |
| UI | Exception UI | Global handler |

### 6.2 Codes d'erreur

```csharp
// Erreurs de périphérique
NO_DRIVE_LETTER
NO_DISK_NUMBER
DEVICE_NOT_READY
ACCESS_DENIED

// Erreurs d'opération
CHKDSK_FAILED
DISKPART_ERROR
FORMAT_CANCELLED
OPERATION_TIMEOUT
```

---

## 7. Sécurité

### 7.1 Vérifications avant opérations destructives

```csharp
public async Task<RepairResult> FormatDeviceAsync(...)
{
    // 1. Confirmation utilisateur obligatoire
    if (!options.UserConfirmed)
        return FailureResult("Confirmation requise", "NO_CONFIRMATION");
    
    // 2. Vérification du périphérique
    if (!await IsDeviceConnectedAsync(device.DeviceId))
        return FailureResult("Périphérique non connecté", "DEVICE_DISCONNECTED");
    
    // 3. Backup proposé
    if (options.CreateBackup)
        await RecoverDataAsync(device, backupPath);
    
    // 4. Exécution
    ...
}
```

### 7.2 Double confirmation formatage

```
[Utilisateur clique sur Formater]
        ↓
[Dialog 1] "ATTENTION: EFFACEMENT TOTAL"
        ↓ (Si Oui)
[Dialog 2] "Dernière chance! Confirmez-vous?"
        ↓ (Si Oui)
[Exécution]
```

---

## 8. Tests

### 8.1 Scénarios de test recommandés

#### Détection
- [ ] Branchement clé USB fonctionnelle
- [ ] Branchement clé USB corrompue
- [ ] Débranchement pendant diagnostic
- [ ] Plusieurs clés simultanées

#### Diagnostic
- [ ] Clé saine
- [ ] Clé RAW
- [ ] Clé corrompue
- [ ] Clé pleine

#### Réparation
- [ ] Réparation FS sur clé avec erreurs
- [ ] Formatage rapide
- [ ] Formatage complet
- [ ] Récupération de données

### 8.2 Tests manuels

```powershell
# Créer une clé de test virtuelle (admin)
# ou utiliser une clé USB physique de test

# Simuler une clé corrompue (ne pas faire sur données importantes!)
diskpart
select disk X
attributes disk clear readonly
clean
exit
```

---

## 9. Performance

### 9.1 Optimisations

- **Async/Await**: Toutes les opérations I/O sont asynchrones
- **WMI Events**: Surveillance sans polling constant
- **Lazy Loading**: Les informations sont chargées à la demande
- **Cancellation Tokens**: Annulation possible des opérations longues

### 9.2 Métriques

| Opération | Temps moyen | Mémoire |
|-----------|-------------|---------|
| Démarrage | < 2s | ~50 MB |
| Détection | < 1s | - |
| Diagnostic rapide | 5-10s | - |
| Diagnostic complet | 30-60s | - |
| Réparation FS | 2-15 min | - |
| Formatage rapide | 2-10s | - |

---

## 10. Extension

### 10.1 Ajouter un nouveau type de diagnostic

```csharp
// 1. Ajouter la méthode dans IDiagnosticService
Task<DiagnosticCheck> CheckCustomAsync(UsbDeviceInfo device);

// 2. Implémenter dans DiagnosticService
public async Task<DiagnosticCheck> CheckCustomAsync(UsbDeviceInfo device)
{
    var check = new DiagnosticCheck { Name = "Custom Check", ... };
    // ... logique de vérification
    return check;
}

// 3. Intégrer dans RunDiagnosticsAsync
ReportProgress(60, "Custom check...", "Custom");
report.Checks.Add(await CheckCustomAsync(device));
```

### 10.2 Ajouter une nouvelle opération de réparation

```csharp
// 1. Ajouter l'enum
public enum RepairOperationType
{
    // ... existants
    CustomOperation
}

// 2. Ajouter dans IRepairService
Task<RepairResult> CustomOperationAsync(UsbDeviceInfo device);

// 3. Implémenter et exposer dans l'UI
```

---

## 11. Débogage

### 11.1 Logs

Activer les logs détaillés:
```csharp
// Dans DiagnosticService, RepairService
System.Diagnostics.Debug.WriteLine($"Info: {message}");
```

### 11.2 Outils utiles

- **WMI Explorer**: Inspecter les classes WMI
- **Process Monitor**: Surveillance des accès disque
- **Event Viewer**: Logs système Windows

---

## 12. Références

### Documentation officielle

- [WPF Documentation](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/)
- [WMI .NET](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page)
- [MVVM Pattern](https://docs.microsoft.com/en-us/archive/msdn-magazine/2009/february/patterns-wpf-apps-with-the-model-view-viewmodel-design-pattern)
- [CommunityToolkit.Mvvm](https://docs.microsoft.com/en-us/windows/communitytoolkit/mvvm/introduction)

---

**Fin du document technique**

Pour toute question technique, contacter l'auteur: Murat Tatar
