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

**Important**: Never use "Folder" terminology. Use "Concept" instead for semantic groupings.

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

- **SwiftData** for notes, concepts, purposes, and structured data (currently)
- **API Backend** for production (PostgreSQL - pending implementation)
- **UserDefaults/@AppStorage** for simple preferences (theme, sort order)
- **Keychain** for API tokens and sensitive credentials (NEVER UserDefaults)
- **File System** for large attachments (audio, images)
- **NSCache** for temporary data (rendered markdown, thumbnails)

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

---

## Terminology Standards

**ALWAYS use these terms consistently:**

| Correct Term | NEVER Use | Context |
|--------------|-----------|---------|
| **Concepto** / **Concept** | Folder, Category, Tag | Semantic groupings extracted from notes |
| **Intención** / **Purpose** | Goal, Type, Category | Cognitive intention (Acción, Aprender, etc.) |
| **To-do** | Task, Action Item | Actionable items extracted from notes |
| **Nota** / **Note** | Document, Entry, Item | Main content unit |
| **Pintura** / **Painting** | Image, Picture, Photo | Impressionist-style backgrounds |
| **Procesamiento** / **Processing** | Analysis, Parsing | AI/NLP extraction pipeline |

---

## Backend Integration (Pending)

### Current State: Mock Data
- All data lives in SwiftData locally
- No backend connection
- `ProcessedData` is always `null`
- No automatic concept/purpose/todo extraction

### Future State: API Backend
When backend is ready:
1. Migrate `NoteRepository`, `ConceptRepository`, etc. to API calls
2. Keep SwiftData for offline-first caching
3. Implement sync logic (optimistic updates)
4. Handle processing status polling
5. Manage auth tokens in Keychain

### Repository Pattern for Migration
```swift
protocol NoteRepository {
    func fetchNotes() async throws -> [Note]
    func createNote(_ note: Note) async throws -> Note
    // ...
}

// Current: SwiftData
class SwiftDataNoteRepository: NoteRepository { ... }

// Future: API + SwiftData cache
class APIBacnoteRepository: NoteRepository {
    private let apiClient: APIClient
    private let localStore: ModelContext

    func fetchNotes() async throws -> [Note] {
        // Try API first, fallback to local, sync in background
    }
}
```

---

## Feature Flags

Use compile-time flags for unfinished features:

```swift
struct FeatureFlags {
    static let enableConceptsView = false
    static let enablePurposesView = false
    static let enableTodosView = false
    static let enableVoiceInput = false
    static let enableSearch = false
    static let enableBackend = false // Switch when API ready
}
```

---

## Processing Pipeline (Backend Only)

When a note is created/edited, backend will:

1. **Transcription** (if voice input)
2. **Concept Extraction** → Updates `Note.concepts`
3. **Purpose Classification** → Updates `Note.purposes`
4. **Todo Extraction** → Creates `Todo` entities
5. **Embedding Generation** → For semantic search
6. **Summary Generation** → Updates `ProcessedData.summary`

Frontend should:
- Poll `/notes/{id}/processing` for status
- Show loading state while `is_processing == true`
- Refresh note when processing completes

---

## AI Services Architecture

### Voice Transcription
```swift
protocol TranscriptionService {
    func transcribe(audio: Data, language: String) async throws -> String
}

// MVP: On-device (Apple Speech Framework)
class OnDeviceTranscriptionService: TranscriptionService { ... }

// Fallback: Cloud (Whisper API)
class CloudTranscriptionService: TranscriptionService { ... }
```

### Concept Extraction (Backend Only)
- Uses NLP/LLM to extract key concepts
- Returns weighted list of concepts
- Creates new `KeyConcept` if doesn't exist
- Updates concept weights based on usage

### Purpose Classification (Backend Only)
- Classifies note into 1+ predefined purposes
- Returns purpose with confidence weight
- System-defined purposes (not user-editable)

### Todo Extraction (Backend Only)
- Detects actionable items in text
- Creates `Todo` entities linked to note
- User can mark as completed (doesn't edit note)

---

## Testing Strategy

### Unit Tests
- Test ViewModels (business logic)
- Test Services (CRUD operations)
- Test Mappers (DTO ↔ Model conversion)

### Integration Tests
- Test Repository implementations
- Test API client with mock responses
- Test sync logic

### UI Tests
- Critical user flows only
- Note creation → view → edit
- Auth flow
- Search

---

## Performance Budgets

### Memory
- **Note List**: Max 150MB for 1000 notes
- **Image Cache**: Max 50MB
- **Audio Recording**: Stream, never load full file

### Network
- **API Response Time**: < 500ms for list endpoints
- **Note Creation**: < 200ms (async processing)
- **Image Upload**: < 2s for 5MB

### Rendering
- **List Scroll**: 60fps maintained
- **Note Detail**: < 100ms to render
- **Search Results**: < 300ms to display

---

## Security Guidelines

### Authentication
- Store JWT tokens in Keychain ONLY
- Refresh tokens before expiry (proactive)
- Clear tokens on logout
- Never log tokens or sensitive data

### Data Protection
- Enable Data Protection entitlement
- Mark sensitive files as protected
- Use HTTPS for all network calls
- Validate all API responses

### Voice/Audio
- Request microphone permission with clear explanation
- Delete audio files after transcription
- Never store raw audio permanently
- Inform user about on-device vs cloud processing

---

## Accessibility Requirements

### VoiceOver Support
- All interactive elements have labels
- Images have meaningful descriptions
- Navigation is logical and predictable

### Dynamic Type
- All text respects user font size preferences
- Layouts adapt to larger text
- Minimum tap target: 44x44pt

### Color Contrast
- All text meets WCAG AA standards
- Don't rely on color alone for information
- Support dark mode

---

## Localization Preparation

### String Management
```swift
// Use NSLocalizedString for all user-facing text
Text(NSLocalizedString("notes.list.title", comment: "Notes list screen title"))

// NOT
Text("Notas") // ❌ Hardcoded
```

### Supported Languages (Future)
- English (en)
- Spanish (es)
- Portuguese (pt)

### Date/Number Formatting
```swift
// Always use locale-aware formatters
let formatter = DateFormatter()
formatter.dateStyle = .medium
formatter.timeStyle = .short
// formatter auto-adapts to user locale
```

---

## Git Workflow

### Branch Naming
- `feature/concept-list-view`
- `fix/note-delete-crash`
- `refactor/repository-layer`
- `docs/update-guidelines`

### Commit Messages
```
feat: add Concepts list view with filtering
fix: resolve crash when deleting note
refactor: extract note card into component
docs: update API endpoints documentation
```

### PR Requirements
- Link to issue/task
- Screenshots for UI changes
- Update documentation if needed
- No merge without review

---

**Last Updated**: 2026-01-06