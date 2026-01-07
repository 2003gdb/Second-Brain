# Networking Inventory

**Purpose:** Track all networking components, endpoints, and API clients to avoid duplication and maintain consistency.

After implementing networking code, document it here with a concise description.

---

| Component | Location | Purpose |
|-----------|----------|---------|
| **Core Networking** | | |
| `APIClient` | `Core/Networking/` | Cliente HTTP genérico con async/await usando URLSession |
| `APIEndpoint` | `Core/Networking/` | Enum con todos los endpoints del backend (notes, folders, concepts, auth, settings) |
| `APIError` | `Core/Networking/` | Enum de errores de red con LocalizedError |
| **DTOs (Data Transfer Objects)** | | |
| `NoteDTO` | `Core/Networking/DTOs/` | DTO para notas con estructura de API |
| `PurposeDTO` | `Core/Networking/DTOs/` | DTO para intenciones/purposes |
| `TodoDTO` | `Core/Networking/DTOs/` | DTO para to-dos |
| `PaintingDTO` | `Core/Networking/DTOs/` | DTO para pinturas/imágenes |
| `AuthResponseDTO` | `Core/Networking/DTOs/` | DTO para respuestas de autenticación |
| `UserDTO` | `Core/Networking/DTOs/` | DTO para datos de usuario |
| `StatsDTO` | `Core/Networking/DTOs/` | DTO para estadísticas |
| `SearchResultDTO` | `Core/Networking/DTOs/` | DTO para resultados de búsqueda |
| `ProcessingStatusDTO` | `Core/Networking/DTOs/` | DTO para estado de procesamiento de notas |
| `ConceptDTO` | `Core/Networking/DTOs/` | DTO para conceptos |
| `SettingsDTO` | `Core/Networking/DTOs/` | DTO para configuración de usuario |
| `PaginatedResponse` | `Core/Networking/DTOs/` | DTO genérico para respuestas paginadas |
| **Mappers** | | |
| `NoteMapper` | `Core/Networking/Mappers/` | Convierte NoteDTO ↔ Note model |
| `PurposeMapper` | `Core/Networking/Mappers/` | Convierte PurposeDTO ↔ Purpose model |
| `TodoMapper` | `Core/Networking/Mappers/` | Convierte TodoDTO ↔ Todo model |
| `UserMapper` | `Core/Networking/Mappers/` | Convierte UserDTO ↔ User model |
| `StatsMapper` | `Core/Networking/Mappers/` | Convierte StatsDTO ↔ Stats model |
| `ConceptMapper` | `Core/Networking/Mappers/` | Convierte ConceptDTO ↔ KeyConcept model |

**Nota:** Toda la infraestructura de networking está lista pero no conectada a backend real. Actualmente todos los datos vienen de SwiftData local.

---

## Notes

- **When to add**: After completing implementation of API clients, endpoints, or networking utilities
- **Location**: All networking code lives in `Core/Networking/`
- **How to describe**: One concise sentence explaining what endpoint/client it handles (not how)
- **Why track**: Avoid duplicate endpoints, maintain consistent API patterns, and identify networking layer responsibilities
