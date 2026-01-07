# MindDump iOS Architecture Guidelines

SwiftUI's declarative, state-driven nature makes **MVVM the natural architectural fit** for MindDump. Start with simple MVVM using the `@Observable` macro (iOS 17+) or `ObservableObject` (iOS 16+), feature-based folder organization, and Apple's native frameworks for voice dictation and AI. This approach delivers the fastest path to MVP while maintaining a clean foundation for scaling beyond it. The architecture emphasizes simplicity—extracting logic into ViewModels and services, leveraging SwiftUI's built-in navigation with `NavigationStack`, and using SwiftData for persistence. By treating AI as pluggable and network services as protocol-based, MindDump can ship quickly on iPhone 11+ while remaining future-aware without being overengineered.
---

## Architecture patterns favor MVVM for SwiftUI apps

SwiftUI has "MVVM built-in" through its `@State`, `@Observable`, and `Binding` mechanisms. For MindDump, **MVVM with a services layer** provides the optimal balance of MVP speed and incremental scalability.

The MVVM-C pattern (MVVM with Coordinators) adds complexity without proportional benefit for most SwiftUI apps. Modern `NavigationStack` with path-based navigation handles programmatic navigation elegantly, making dedicated Coordinators unnecessary until your app exceeds 50+ screens with complex flows. VIPER is explicitly not recommended for SwiftUI—its Router becomes redundant with declarative navigation, its Presenter shrinks to near-uselessness with native state bindings, and its per-view boilerplate discourages the small, composable views that SwiftUI encourages.

For complex screens, use a dedicated ViewModel that holds presentation logic and state. For simple views displaying data passed from parents, skip the ViewModel entirely—let the View render directly from model properties. This pragmatic approach avoids boilerplate while maintaining testability where it matters.

```swift
@Observable 
class NoteListViewModel {
    var notes: [Note] = []
    var isLoading = false
    private let noteService: NoteServiceProtocol
    
    init(noteService: NoteServiceProtocol) {
        self.noteService = noteService
    }
    
    @MainActor
    func loadNotes() async {
        isLoading = true
        defer { isLoading = false }
        notes = (try? await noteService.fetchNotes()) ?? []
    }
}
```
---

## Folder structure should follow features, not layers

Feature-based organization keeps related code together and scales naturally as MindDump grows. Each feature folder contains its Views, ViewModels, and Models, making it easy to find code and eventually extract features into Swift Packages.

```
MindDump/
├── App/
│   ├── MindDumpApp.swift
│   ├── MainTabView.swift
│   ├── AppNavigation.swift
│   └── ContentView.swift
├── Features/
│   ├── Notes/
│   │   ├── Views/
│   │   │   ├── NotesListView.swift
│   │   │   ├── NoteDetailView.swift
│   │   │   ├── BlankNoteEditorView.swift
│   │   │   ├── NoteCard.swift
│   │   │   ├── NoteActionsMenu.swift
│   │   │   └── UIKit/
│   │   │       ├── NotesTableViewController.swift
│   │   │       ├── NotesTableViewRepresentable.swift
│   │   │       └── NoteTableViewCell.swift
│   │   ├── ViewModels/
│   │   │   └── NotesViewModel.swift
│   │   └── Models/
│   │       ├── Note.swift
│   │       ├── Purpose.swift
│   │       ├── NotePurpose.swift
│   │       └── Status.swift
│   ├── Concepts/
│   │   ├── Views/
│   │   │   ├── ConceptsListView.swift (PENDIENTE)
│   │   │   ├── ConceptDetailView.swift (PENDIENTE)
│   │   │   └── ConceptCard.swift (PENDIENTE)
│   │   └── ViewModels/
│   │       └── ConceptsViewModel.swift (PENDIENTE)
│   ├── Purposes/
│   │   ├── Views/
│   │   │   ├── PurposesListView.swift (PENDIENTE)
│   │   │   └── PurposeDetailView.swift (PENDIENTE)
│   │   └── ViewModels/
│   │       └── PurposesViewModel.swift (PENDIENTE)
│   ├── Todos/
│   │   ├── Views/
│   │   │   ├── TodosListView.swift (PENDIENTE)
│   │   │   ├── TodoRow.swift (PENDIENTE)
│   │   │   └── TodoDetailSheet.swift (PENDIENTE)
│   │   └── ViewModels/
│   │       └── TodosViewModel.swift (PENDIENTE)
│   ├── VoiceInput/
│   │   ├── Views/
│   │   │   └── DictateSheet.swift
│   │   └── ViewModels/
│   │       └── VoiceDictationViewModel.swift
│   ├── Prioritize/
│   │   ├── Views/
│   │   │   ├── PrioritizeView.swift
│   │   │   └── SwipeableCard.swift
│   │   └── ViewModels/
│   │       └── PrioritizeViewModel.swift
│   ├── Settings/
│   │   └── Views/
│   │       └── SettingsView.swift
│   ├── Auth/
│   │   ├── Views/
│   │   │   ├── OnboardingView.swift (PENDIENTE)
│   │   │   ├── LoginView.swift (PENDIENTE)
│   │   │   └── GoogleSignInButton.swift (PENDIENTE)
│   │   └── ViewModels/
│   │       └── AuthViewModel.swift (PENDIENTE)
│   └── Search/
│       ├── Views/
│       │   ├── SearchBar.swift (PENDIENTE)
│       │   └── SearchResultsView.swift (PENDIENTE)
│       └── ViewModels/
│           └── SearchViewModel.swift (PENDIENTE)
├── Core/
│   ├── Services/
│   │   ├── NoteService.swift
│   │   ├── ConceptService.swift (PENDIENTE rename from FolderService)
│   │   ├── TranscriptionService.swift
│   │   ├── SampleDataService.swift
│   │   ├── PaintingService.swift (PENDIENTE)
│   │   ├── AuthService.swift (PENDIENTE)
│   │   ├── SearchService.swift (PENDIENTE)
│   │   ├── ExportService.swift (PENDIENTE)
│   │   ├── ProcessingService.swift (PENDIENTE)
│   │   ├── EmbeddingService.swift (PENDIENTE)
│   │   └── StatsService.swift (PENDIENTE)
│   ├── Repositories/
│   │   ├── NoteRepository.swift
│   │   ├── ConceptRepository.swift (PENDIENTE rename from FolderRepository)
│   │   ├── PurposeRepository.swift (PENDIENTE)
│   │   └── TodoRepository.swift (PENDIENTE)
│   ├── Networking/
│   │   ├── APIClient.swift
│   │   ├── APIEndpoint.swift
│   │   ├── APIError.swift
│   │   ├── DTOs/
│   │   │   ├── NoteDTO.swift
│   │   │   ├── ConceptDTO.swift
│   │   │   ├── PurposeDTO.swift (PENDIENTE)
│   │   │   ├── TodoDTO.swift (PENDIENTE)
│   │   │   ├── PaintingDTO.swift (PENDIENTE)
│   │   │   ├── AuthResponseDTO.swift (PENDIENTE)
│   │   │   ├── UserDTO.swift (PENDIENTE)
│   │   │   ├── StatsDTO.swift (PENDIENTE)
│   │   │   ├── SearchResultDTO.swift (PENDIENTE)
│   │   │   ├── ProcessingStatusDTO.swift (PENDIENTE)
│   │   │   ├── SettingsDTO.swift
│   │   │   └── PaginatedResponse.swift
│   │   └── Mappers/
│   │       ├── NoteMapper.swift
│   │       ├── ConceptMapper.swift
│   │       ├── PurposeMapper.swift (PENDIENTE)
│   │       ├── TodoMapper.swift (PENDIENTE)
│   │       ├── UserMapper.swift (PENDIENTE)
│   │       └── StatsMapper.swift (PENDIENTE)
│   └── Models/
│       ├── User.swift
│       ├── UserSettings.swift
│       ├── KeyConcept.swift
│       ├── Todo.swift (PENDIENTE)
│       ├── Painting.swift (PENDIENTE)
│       ├── Stats.swift (PENDIENTE)
│       └── SearchResult.swift (PENDIENTE)
├── Shared/
│   ├── Components/
│   │   ├── Badge.swift
│   │   ├── CategoryBadge.swift
│   │   ├── IconButton.swift
│   │   ├── BottomSheet.swift
│   │   ├── OverlayMenu.swift
│   │   ├── FlowLayout.swift
│   │   ├── FAB.swift
│   │   └── FloatingActionMenu.swift
│   ├── Extensions/
│   └── Utilities/
├── DesignSystem/
│   ├── Tokens/
│   │   ├── Colors.swift
│   │   ├── Typography.swift
│   │   ├── FontRegistration.swift
│   │   └── Spacing.swift
│   ├── Styles/
│   │   └── (Custom view modifiers y button styles)
│   └── UIKit/
│       ├── UIColor+DesignSystem.swift
│       ├── UIFont+DesignSystem.swift
│       └── UIEdgeInsets+Spacing.swift
└── Resources/
    ├── Fonts/
    ├── Images/
    └── Paintings/
```

This structure separates concerns clearly: Features own UI and presentation logic, Core owns business logic and data access, Shared owns reusable utilities, and DesignSystem owns visual consistency.

---

## Naming conventions follow Swift community standards

Use **PascalCase** for types (classes, structs, enums, protocols) and **camelCase** for variables, properties, and functions. Apply descriptive suffixes that communicate purpose: `View`, `ViewModel`, `Service`, `Repository`.

Views should be named `[Purpose]View`—`NoteListView`, `NoteEditorView`, `SearchView`. ViewModels follow with `[Purpose]ViewModel`—`NoteListViewModel`, `SearchViewModel`. Services take `[Domain]Service`—`NoteService`, `SyncService`, `TranscriptionService`. Repositories use `[Entity]Repository`—`NoteRepository`, `UserRepository`.

For protocols, use `-Protocol` suffix for service abstractions (`NoteServiceProtocol`) or capability suffixes like `-able`, `-ing` for behavior contracts (`Searchable`, `NoteProviding`). Boolean properties should use `is`, `has`, or `should` prefixes—`isLoading`, `hasNotes`, `shouldSync`.

File extensions follow the pattern `Type+Purpose.swift`—`View+Modifiers.swift`, `Date+Formatting.swift`. Each file should contain a single primary type, named after that type.

---

## Design system uses tokens, styles, and environment

Build visual consistency through **design tokens**—named constants for spacing, colors, and typography that prevent magic numbers scattered through views.

```swift
enum Spacing {
    static let xs: CGFloat = 4
    static let sm: CGFloat = 8
    static let md: CGFloat = 16
    static let lg: CGFloat = 24
    static let xl: CGFloat = 32
}

extension Color {
    enum DS {
        static let backgroundPrimary = Color("BackgroundPrimary")
        static let textPrimary = Color("TextPrimary")
        static let accent = Color("AccentPrimary")
    }
}

extension Font {
    enum DS {
        static let title = Font.system(.title2, design: .rounded, weight: .semibold)
        static let body = Font.system(.body)
        static let caption = Font.system(.caption)
    }
}
```

Create reusable **ButtonStyles** and **ViewModifiers** for consistent component behavior. Use SwiftUI's environment system for theming—inject an `@Observable` Theme object at the app root and access it via `@Environment` in child views. Build flexible components using `@ViewBuilder` parameters for customizable content slots.

---

## State management requires clear ownership boundaries

SwiftUI's property wrappers define state ownership. For **local UI state** (toggles, text input, animation flags), use `@State`. For **ViewModels** you're creating and owning, use `@State` with `@Observable` classes (iOS 17+) or `@StateObject` with `ObservableObject` (iOS 16). For **shared app-wide state**, inject via `@Environment`.

The `@Observable` macro (iOS 17+) offers significant advantages over `ObservableObject`: fine-grained property-level tracking means only views reading a specific property re-render when it changes, compared to `ObservableObject` where any `@Published` change triggers all observers. For new iOS 17+ projects, prefer `@Observable`.

Distinguish **transient UI state** from **persistent app state**. Transient state includes keyboard visibility, draft text before saving, sheet presentation, and search query text—keep these as `@State` in views. Persistent state includes saved notes, user preferences, and sync status—manage these in ViewModels backed by SwiftData or services.

```swift
struct NoteEditorView: View {
    // Transient UI state
    @State private var draftText = ""
    @State private var showFormatting = false
    
    // Persistent state via environment
    @Environment(NotesStore.self) var store
    @Bindable var note: Note
}
```
Avoid common pitfalls: don't recreate ViewModels on each view update (hold them at the root), don't store large data arrays in `@State` (use `@Query` or fetch through ViewModels), and always mark async state-updating functions with `@MainActor`.

---

## Navigation uses NavigationStack with typed routes

Modern SwiftUI navigation centers on `NavigationStack` (iOS 16+) with value-based `NavigationLink` and `.navigationDestination(for:)` modifiers. Define a **route enum** that encapsulates all possible destinations:

```swift
enum AppRoute: Hashable {
    case noteDetail(noteID: UUID)
    case settings
    case search(query: String)
    case tagDetail(tagID: UUID)
}

struct RootView: View {
    @State private var path: [AppRoute] = []
    
    var body: some View {
        NavigationStack(path: $path) {
            NoteListView()
                .navigationDestination(for: AppRoute.self) { route in
                    switch route {
                    case .noteDetail(let id): NoteDetailView(noteID: id)
                    case .settings: SettingsView()
                    case .search(let query): SearchView(query: query)
                    case .tagDetail(let id): TagDetailView(tagID: id)
                    }
                }
        }
    }
}
```

For **programmatic navigation**, append to the path array: `path.append(.noteDetail(noteID: note.id))`. For **pop to root**, clear the array: `path.removeAll()`. Use `.sheet(item:)` and `.fullScreenCover(item:)` for modal presentations.

A lightweight **Router** class can centralize navigation state if you need to trigger navigation from ViewModels or handle deep links, but full Coordinator patterns are unnecessary for MVP.

---

## Persistence should start with SwiftData for new projects

For MindDump's note storage, **SwiftData** (iOS 17+) offers seamless SwiftUI integration with minimal boilerplate. Define models with the `@Model` macro:

```swift
@Model
class Note {
    var title: String
    var content: String
    var createdAt: Date
    var modifiedAt: Date
    var isPinned: Bool
    @Relationship(deleteRule: .cascade) var tags: [Tag]
    
    init(title: String = "", content: String = "") {
        self.title = title
        self.content = content
        self.createdAt = .now
        self.modifiedAt = .now
        self.isPinned = false
        self.tags = []
    }
}
```

Use `@Query` in views for automatic data fetching and updates. The **storage decision matrix** guides where to put different data types: **SwiftData** for structured notes and relationships, **UserDefaults/@AppStorage** for simple preferences (theme, sort order), **Keychain** for sensitive data (API tokens), **File System** for large attachments (audio recordings, images), and **NSCache** for temporary computed data (rendered markdown, thumbnails).

If targeting iOS 16, CoreData remains viable—it's battle-tested and powerful, though with steeper learning curves and more boilerplate.

---

## Backend integration uses URLSession with async/await

Apple's native `URLSession` now supports async/await elegantly, eliminating the need for third-party networking libraries like Alamofire for most use cases.

```swift
func fetchNotes() async throws -> [Note] {
    let (data, response) = try await URLSession.shared.data(from: apiURL)
    guard let httpResponse = response as? HTTPURLResponse,
          httpResponse.statusCode == 200 else {
        throw NetworkError.invalidResponse
    }
    return try JSONDecoder().decode([Note].self, from: data)
}
```

Structure your networking with clear **separation between layers**: DTOs match API response shapes exactly, domain models represent clean business logic, and a mapping layer transforms between them. The **Repository pattern** abstracts data sources—whether API or local storage—from the rest of the app:

```swift
protocol NoteRepository {
    func fetchNotes() async throws -> [Note]
    func saveNote(_ note: Note) async throws
}

class NoteRepositoryImpl: NoteRepository {
    private let networkService: NetworkService
    private let localStore: ModelContext
    
    func fetchNotes() async throws -> [Note] {
        // Return local first, sync with remote in background
    }
}
```

For offline handling, use `NWPathMonitor` to detect connectivity changes, but prefer Apple's recommendation of using `waitsForConnectivity: true` on URLSession configuration to let the system handle connectivity gracefully.

---

## AI integration treats native and cloud as swappable services

iPhone 11's **A13 Bionic** with 8-core Neural Engine handles on-device AI effectively. Apple's **Speech framework** provides excellent voice-to-text for dictation with no API costs. The **Natural Language framework** offers built-in sentiment analysis, tokenization, and named entity recognition without custom models.

Design AI services behind **protocols** to enable swapping implementations:

```swift
protocol TranscriptionService {
    func transcribe(audio: Data) async throws -> String
    var isAvailable: Bool { get }
}

class OnDeviceTranscriptionService: TranscriptionService {
    private let recognizer = SFSpeechRecognizer()
    var isAvailable: Bool { recognizer?.isAvailable ?? false }
    
    func transcribe(audio: Data) async throws -> String {
        // Use Speech framework
    }
}

class CloudTranscriptionService: TranscriptionService {
    var isAvailable: Bool { NetworkMonitor.shared.isConnected }
    
    func transcribe(audio: Data) async throws -> String {
        // Call Whisper API
    }
}
```

For MVP, **start with Apple's Speech framework**—it's free, on-device, and well-optimized. Consider **WhisperKit** (Swift wrapper for on-device Whisper models) for higher accuracy if needed, using the `tiny` or `base` model sizes that run well on A13. Cloud AI (OpenAI, etc.) should be optional fallback, keeping the app functional fully offline.

---

## Performance optimization focuses on avoiding unnecessary redraws

SwiftUI's rendering efficiency depends on **stable identifiers** in `ForEach` loops and **extracted subviews** that isolate state dependencies. When a parent's state changes, only child views that actually depend on that state should re-render.

```swift
// ✅ Extracted subview only redraws when its note changes
struct NoteRow: View {
    let note: Note
    var body: some View {
        VStack(alignment: .leading) {
            Text(note.title).font(.DS.title)
            Text(note.preview).font(.DS.caption)
        }
    }
}
```

Use `List` rather than `LazyVStack` for the main notes list—it provides better memory efficiency through cell recycling (**118-128MB** for 1000 items vs **149-152MB** for LazyVStack) and superior scroll performance with dynamic heights.

For property wrappers, understand the re-render implications: `@State` changes only affect the owning view, `@Observable` properties trigger re-renders only in views that read them (fine-grained), while `ObservableObject` `@Published` changes trigger all observers (coarse-grained).

During development, use `Self._printChanges()` in view bodies to debug which state changes trigger redraws. Profile with Instruments' SwiftUI template to catch performance issues early.

---

## Memory management requires attention to closures and large data

SwiftUI Views are structs and don't participate in ARC, but **ViewModels and services are classes** where retain cycles can occur. Always use `[weak self]` in closures that might outlive the object:

```swift
fetchNotes { [weak self] notes in
    self?.notes = notes
}
```

For **images**, always downsample before display. A 3024×4032 photo uses ~46MB decoded in memory—downsampling to display size (e.g., 300×400) reduces this to ~0.5MB, a 92x improvement critical for iPhone 11's 3GB RAM.

```swift
func downsampledImage(at url: URL, to size: CGSize, scale: CGFloat) -> UIImage? {
    let options = [kCGImageSourceShouldCache: false] as CFDictionary
    guard let source = CGImageSourceCreateWithURL(url as CFURL, options) else { return nil }
    
    let maxPixels = max(size.width, size.height) * scale
    let downsampleOptions = [
        kCGImageSourceCreateThumbnailFromImageAlways: true,
        kCGImageSourceThumbnailMaxPixelSize: maxPixels
    ] as CFDictionary
    
    guard let cgImage = CGImageSourceCreateThumbnailAtIndex(source, 0, downsampleOptions) else { return nil }
    return UIImage(cgImage: cgImage)
}
```

For **audio recordings**, store URLs and stream playback—never load entire audio files into memory. Monitor `scenePhase` to release heavy resources when entering background.

---

## Concurrency uses async/await with structured task management

Mark entire ViewModels with `@MainActor` for compile-time thread safety. Use SwiftUI's `.task` modifier for data loading—it automatically cancels when the view disappears:

```swift
struct NoteListView: View {
    @State private var viewModel = NoteListViewModel()
    
    var body: some View {
        List(viewModel.notes) { note in
            NoteRow(note: note)
        }
        .task {
            await viewModel.loadNotes()
        }
        .refreshable {
            await viewModel.refreshNotes()
        }
    }
}
```

For debounced search, use `.task(id:)` which restarts when the ID changes:

```swift
.task(id: searchQuery) {
    try? await Task.sleep(for: .milliseconds(300))
    guard !Task.isCancelled else { return }
    await viewModel.search(query: searchQuery)
}
```
Check for cancellation in long-running operations with `Task.checkCancellation()` or `Task.isCancelled`. Use `TaskGroup` for parallel operations like syncing multiple notes simultaneously.

---

## Error handling uses centralized types with user-friendly presentation

Define domain-specific errors conforming to `LocalizedError`:

```swift
enum MindDumpError: LocalizedError {
    case networkFailure
    case saveFailed
    case microphonePermissionDenied
    
    var errorDescription: String? {
        switch self {
        case .networkFailure: return "Unable to connect"
        case .saveFailed: return "Failed to save your note"
        case .microphonePermissionDenied: return "Microphone access required"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .networkFailure: return "Check your connection and try again"
        case .saveFailed: return "Your note is saved locally"
        case .microphonePermissionDenied: return "Enable in Settings"
        }
    }
}
```

Present errors via SwiftUI alerts for critical issues requiring action, inline messages for form validation, and consider toast-style notifications for transient errors. Implement retry logic for network requests with exponential backoff on retryable status codes (408, 429, 500, 502, 503, 504).

---

## Security stores sensitive data in Keychain only

Never store API tokens or sensitive credentials in UserDefaults—it's plaintext. Use **Keychain** via a wrapper library like KeychainSwift:

```swift
import KeychainSwift

class TokenManager {
    private let keychain = KeychainSwift()
    
    func saveToken(_ token: String) {
        keychain.set(token, forKey: "api_token")
    }
    
    func getToken() -> String? {
        keychain.get("api_token")
    }
}
```

For voice dictation, add required **Info.plist** entries with user-facing explanations:
- `NSMicrophoneUsageDescription`: "MindDump needs microphone access to transcribe voice notes"
- `NSSpeechRecognitionUsageDescription`: "MindDump uses speech recognition to convert voice to text"

Handle all authorization states gracefully—guide users to Settings when permissions are denied, show unavailable states when restricted.

---

## Testing uses concrete class injection

Use concrete classes with default parameters: `init(noteService: NoteService = NoteService())`. Only extract protocols when you need to swap implementations (on-device vs cloud transcription). For tests, inject configured instances rather than mocks.
---

## Build configuration separates development from production

Create multiple Xcode configurations and schemes for environment separation. Use **.xcconfig files** for environment-specific values:

```
// Development.xcconfig
API_BASE_URL = https://api-dev.minddump.app

// Production.xcconfig
API_BASE_URL = https://api.minddump.app
```

Reference in Info.plist as `$(API_BASE_URL)` and access via `Bundle.main.object(forInfoDictionaryKey:)`.

For feature flags, start simple with compile-time checks:

```swift
struct FeatureFlags {
    #if DEBUG
    static let experimentalFeatures = true
    #else
    static let experimentalFeatures = false
    #endif
}
```

Graduate to remote feature flags (Firebase Remote Config) post-MVP when you need runtime control.

---

## App lifecycle responds to scenePhase for state preservation

Use `@Environment(\.scenePhase)` to respond to app lifecycle transitions:

```swift
@main
struct MindDumpApp: App {
    @Environment(\.scenePhase) private var scenePhase
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .onChange(of: scenePhase) { _, newPhase in
            switch newPhase {
            case .active: resumeOperations()
            case .inactive: break
            case .background: saveState()
            @unknown default: break
            }
        }
    }
}
```

Save critical state immediately when entering background—iOS may terminate your app without warning. Release heavy resources (large images, audio players) to reduce memory pressure.

---

## Target iOS 16+ minimum for optimal SwiftUI features

For a 2025 launch targeting iPhone 11+, **iOS 16 as minimum** provides excellent reach (~94% of devices) while unlocking essential modern SwiftUI features: `NavigationStack` for proper navigation, Charts framework, ShareLink, and PhotosPicker.

If you can accept slightly narrower reach (~89%), **iOS 17 minimum** adds the `@Observable` macro for simpler state management, SwiftData for persistence, and improved ScrollView APIs.

iPhone 11 supports up to iOS 18, so all modern features are available on your target hardware. Use `#available` checks sparingly for iOS 17+ features if you choose iOS 16 minimum.

---

## Summary of MVP architecture decisions

| Decision | Recommendation |
|----------|----------------|
| Architecture | MVVM with @Observable (iOS 17+) or ObservableObject (iOS 16+) |
| Folder structure | Feature-based organization |
| Navigation | NavigationStack with typed route enum |
| Persistence | SwiftData (iOS 17+) or CoreData (iOS 16) |
| State management | @State for local, @Observable/@StateObject for ViewModels, @Environment for shared |
| Voice dictation | Apple Speech framework (on-device, free) |
| Networking | URLSession with async/await |
| AI services | Protocol-based, swappable between on-device and cloud |
| Testing | Unit tests on ViewModels with dependency injection |
| Minimum iOS | iOS 16 (broader reach) or iOS 17 (better SwiftUI) |

This architecture ships quickly while providing a clean foundation for growth. Add complexity only when specific pain points emerge—not preemptively. The simplest viable approach wins for MVP.