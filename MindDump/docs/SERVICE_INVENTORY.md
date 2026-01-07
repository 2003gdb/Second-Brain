# Service Inventory

**Purpose:** Track all services and repositories to identify duplication and refactoring opportunities.

After implementing a new service or repository, document it here with a concise description.

---

| Service/Repository | Location | Purpose |
|-------------------|----------|---------|
| **Services** | | |
| `NoteService` | `Core/Services/` | CRUD de notas en SwiftData (fetch, create, update, delete, priority) |
| `ConceptService` | `Core/Services/` | Gestión de conceptos y organización jerárquica |
| `TranscriptionService` | `Core/Services/` | Servicio de transcripción de voz (estructura, sin Speech Framework) |
| `SampleDataService` | `Core/Services/` | Carga de datos de ejemplo en primera ejecución |
| **Repositories** | | |
| `NoteRepository` | `Core/Repositories/` | Abstracción de datos de notas (actualmente SwiftData, listo para migrar a API) |
| `ConceptRepository` | `Core/Repositories/` | Abstracción de datos de conceptos |
| `PurposeRepository` | `Core/Repositories/` | Abstracción de datos de intenciones/purposes |
| `TodoRepository` | `Core/Repositories/` | Abstracción de datos de to-dos |
| `PaintingService` | `Core/Services/` | Generación y gestión de pinturas/imágenes |
| `AuthService` | `Core/Services/` | Autenticación y gestión de sesión |
| `SearchService` | `Core/Services/` | Búsqueda de notas (text y semantic search) |
| `ExportService` | `Core/Services/` | Exportación de notas en múltiples formatos |
| `ProcessingService` | `Core/Services/` | Procesamiento cognitivo de notas (conceptos, purposes, todos) |
| `EmbeddingService` | `Core/Services/` | Generación de embeddings semánticos para notas |
| `StatsService` | `Core/Services/` | Estadísticas y analytics de usuario |

---

## Notes

- **When to add**: After completing implementation of any new Service or Repository
- **Location**: All services and repositories live in `Core/Services/` or `Core/Repositories/` (business logic layer)
- **How to describe**: One concise sentence explaining what it does (not how)
- **Why track**: Spot duplication early and identify refactoring opportunities
