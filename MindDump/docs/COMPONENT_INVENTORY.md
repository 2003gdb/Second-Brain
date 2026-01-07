# Component Inventory

**Purpose:** Track all views and shared components to identify duplication and reuse opportunities.

After implementing a new view or shared component, document it here with a concise description.

---

| Component/View | Location | Purpose |
|----------------|----------|---------|
| **Shared Components** | | |
| `Badge` | `Shared/Components/` | Badge genérico reutilizable con múltiples estilos |
| `CategoryBadge` | `Shared/Components/` | Badge específico para categorías/conceptos con estilo beige |
| `IconButton` | `Shared/Components/` | Botón de icono con variantes de tamaño y estilo (ghost, primary, etc.) |
| `BottomSheet` | `Shared/Components/` | Sheet modal que aparece desde abajo con animación |
| `OverlayMenu` | `Shared/Components/` | Menú overlay genérico con backdrop |
| `FlowLayout` | `Shared/Components/` | Layout fluido que envuelve elementos (usado para badges) |
| `FAB` | `Shared/Components/` | Floating Action Button con estilos primary/secondary |
| `FloatingActionMenu` | `Shared/Components/` | Menú flotante de acciones (transcribir, dictar, escanear, escribir) |
| **Notes Feature** | | |
| `NotesListView` | `Features/Notes/Views/` | Vista principal con lista de notas, header, y FAB |
| `NotesTableViewRepresentable` | `Features/Notes/Views/UIKit/` | Wrapper SwiftUI para UITableView optimizado |
| `NotesTableViewController` | `Features/Notes/Views/UIKit/` | UITableViewController con scroll optimizado |
| `NoteTableViewCell` | `Features/Notes/Views/UIKit/` | UITableViewCell customizada para notas |
| `NoteDetailView` | `Features/Notes/Views/` | Detalle de nota con scroll parallax y painting de fondo |
| `NoteCard` | `Features/Notes/Views/` | Card de nota usado en lista (feature-specific) |
| `BlankNoteEditorView` | `Features/Notes/Views/` | Editor básico de notas (pendiente funcionalidad completa) |
| `NoteActionsMenu` | `Features/Notes/Views/` | Menú de acciones específico de nota |
| `ActionMenu` | `Features/Notes/Views/` | Menú de acciones genérico |
| `ConceptsDrawer` | `Features/Notes/Views/` | Drawer para filtrar por conceptos |
| **Voice Input Feature** | | |
| `DictateSheet` | `Features/VoiceInput/Views/` | Sheet para dictado de voz (UI lista, sin Speech Framework) |
| **Prioritize Feature** | | |
| `PrioritizeView` | `Features/Prioritize/Views/` | Vista de priorización tipo Tinder con swipe cards |
| `SwipeableCard` | `Features/Prioritize/Views/` | Card swipeable para priorizar notas |
| **Settings Feature** | | |
| `SettingsView` | `Features/Settings/Views/` | Vista de configuración (básica) |
| **Concepts Feature** | | |
| `ConceptsListView` | `Features/Concepts/Views/` | Lista de conceptos generados (PENDIENTE) |
| `ConceptDetailView` | `Features/Concepts/Views/` | Detalle de concepto con notas asociadas (PENDIENTE) |
| `ConceptCard` | `Features/Concepts/Views/` | Card de concepto con pintura (PENDIENTE) |
| **Purposes Feature** | | |
| `PurposesListView` | `Features/Purposes/Views/` | Lista de intenciones predefinidas (PENDIENTE) |
| `PurposeDetailView` | `Features/Purposes/Views/` | Detalle de purpose con notas filtradas (PENDIENTE) |
| **Todos Feature** | | |
| `TodosListView` | `Features/Todos/Views/` | Lista de to-dos pendientes (PENDIENTE) |
| `TodoRow` | `Features/Todos/Views/` | Row de to-do con checkbox (PENDIENTE) |
| `TodoDetailSheet` | `Features/Todos/Views/` | Sheet de detalle de to-do (PENDIENTE) |
| **Auth Feature** | | |
| `OnboardingView` | `Features/Auth/Views/` | Onboarding/Tutorial (PENDIENTE) |
| `LoginView` | `Features/Auth/Views/` | Login con email/password (PENDIENTE) |
| `GoogleSignInButton` | `Features/Auth/Views/` | Botón de Google Sign In (PENDIENTE) |
| **Search Feature** | | |
| `SearchBar` | `Features/Search/Views/` | Barra de búsqueda (PENDIENTE) |
| `SearchResultsView` | `Features/Search/Views/` | Vista de resultados de búsqueda (PENDIENTE) |

---

## Notes

- **When to add**: After completing implementation of any View or Shared Component
- **Location examples**: `Features/Notes/Views/`, `Features/Tags/Views/`, `Shared/Components/`
- **How to describe**: One concise sentence explaining what it displays/does (not how)
- **Why track**: Avoid rebuilding existing components and identify reuse opportunities
