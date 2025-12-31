# MindDump Development Guidelines

Codebase-specific instructions for building MindDump features. These guidelines ensure consistency and maintainability across all new features.

---

## Core Architecture

### MVVM Pattern
- **ViewModels orchestrate, they don't implement** business logic
- Use `@Observable` macro (iOS 17+) for ViewModels
- Mark ViewModels with `@MainActor` for compile-time thread safety
- Keep ViewModels focused on presentation logic and state management

### Feature-Based Organization
```
Features/
├── [FeatureName]/
│   ├── Views/
│   ├── ViewModels/
│   └── Models/ (only if used EXCLUSIVELY in this feature)
Core/
├── Models/ (shared across 2+ features)
├── Services/
└── Repositories/
Shared/
└── Components/ (generic, reusable across features/apps)
```

**Placement Rules:**
- **Models**: Feature-specific → `Features/[Name]/Models/` | Used by 2+ features → `Core/Models/`
- **Components**: Generic/reusable → `Shared/` | Feature-specific → `Features/[Name]/Views/`
- **Logic**: Feature-specific → ViewModels | Reusable business logic → Services

**Service Documentation:**
After creating a new Service or Repository, document it in `SERVICE_INVENTORY.md`:
- **When**: Immediately after completing implementation
- **How**: One concise sentence explaining what it does (not how)
- **Why**: Spot duplication early and identify refactoring opportunities

**Component Documentation:**
After creating a new View or Shared Component, document it in `COMPONENT_INVENTORY.md`:
- **When**: Immediately after completing implementation
- **How**: One concise sentence explaining what it displays/does (not how)
- **Why**: Avoid rebuilding existing components and identify reuse opportunities

**Model Documentation:**
After creating a new Model, document it in `MODEL_INVENTORY.md`:
- **When**: Immediately after completing implementation
- **How**: One concise sentence explaining what data it represents (not how)
- **Why**: Avoid duplicating data structures and understand model relationships across features

**Networking Documentation:**
After creating API clients, endpoints, or networking utilities, document them in `NETWORKING_INVENTORY.md`:
- **When**: Immediately after completing implementation
- **How**: One concise sentence explaining what endpoint/client it handles (not how)
- **Why**: Avoid duplicate endpoints, maintain consistent API patterns, and identify networking responsibilities

---

## State Management

| Property Wrapper | Use Case | Example |
|-----------------|----------|---------|
| `@State` | Local UI state in views | `@State private var isExpanded = false` |
| `@State` + `@Observable` | ViewModel owned by view | `@State private var viewModel = NoteListViewModel()` |
| `@Environment` | Shared app-wide state | `@Environment(NotesStore.self) var store` |

**Rules:**
- Transient UI state (toggles, drafts, sheet presentation) → `@State` in views
- Persistent state (notes, preferences, sync status) → ViewModels or SwiftData
- Never recreate ViewModels on each render—hold them at the root

---

## Design System

**ALWAYS use design tokens. NEVER hard-code values.**

```swift
// Define tokens in DesignSystem/Tokens/
enum Spacing {
    static let xs: CGFloat = 4
    static let sm: CGFloat = 8
    static let md: CGFloat = 16
}

extension Color.DS {
    static let primary = Color("Primary")
    static let backgroundPrimary = Color("BackgroundPrimary")
}

// Use in views
.padding(.md)
.foregroundStyle(Color.DS.primary)
```

**First task for any feature: Choose existing DESIGN TOKENS values or ask me if new ones are needed**

---

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Views | `[Purpose]View` | `NoteListView`, `NoteEditorView` |
| ViewModels | `[Purpose]ViewModel` | `NoteListViewModel` |
| Services | `[Domain]Service` | `NoteService`, `TranscriptionService` |
| Repositories | `[Entity]Repository` | `NoteRepository` |
| Protocols | `[Name]Protocol` or `-able`/`-ing` | `NoteServiceProtocol`, `Searchable` |
| Booleans | `is`, `has`, `should` prefix | `isLoading`, `hasNotes` |
| Extensions | `Type+Purpose.swift` | `View+Modifiers.swift` |

---

## Navigation

Use `NavigationStack` with typed routes:

```swift
enum AppRoute: Hashable {
    case noteDetail(noteID: UUID)
    case settings
}

@State private var path: [AppRoute] = []

NavigationStack(path: $path) {
    NoteListView()
        .navigationDestination(for: AppRoute.self) { route in
            switch route {
            case .noteDetail(let id): NoteDetailView(noteID: id)
            case .settings: SettingsView()
            }
        }
}
```

---

## Data & Persistence

- **SwiftData** for notes and structured data
- **UserDefaults/@AppStorage** for simple preferences (theme, sort order)
- **Keychain** for API tokens and sensitive credentials (NEVER UserDefaults)
- **File System** for large attachments (audio, images)

---

## Dependency Injection

Use **concrete classes with default parameters**:

```swift
class NoteListViewModel {
    private let noteService: NoteServiceProtocol

    init(noteService: NoteServiceProtocol = NoteService()) {
        self.noteService = noteService
    }
}
```

Only extract protocols when you need swappable implementations (e.g., on-device vs cloud AI).

---

## Concurrency & Performance

### async/await
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
    }
}
```

### Performance Rules
- **Extract subviews** to isolate state dependencies
- Use `List` (not `LazyVStack`) for main note lists—better memory efficiency
- Use `[weak self]` in closures that might outlive the object
- **Downsample images** before display to reduce memory (3024×4032 photo = 46MB decoded)

---

## Error Handling

Define domain-specific errors with `LocalizedError`:

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

**Always provide user-friendly error messages with recovery suggestions.**

---

## Development Constraints

- **No premature optimization** beyond what's documented here
- **No complex patterns** unless required (no VIPER, no heavy Coordinators)
- **Portrait orientation only**
- **iOS 17+ minimum** (enables @Observable, SwiftData, modern navigation)
- **Maximum file size: 350 lines** (split components if approaching this limit)