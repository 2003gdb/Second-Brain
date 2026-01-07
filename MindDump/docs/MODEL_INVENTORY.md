# Model Inventory

**Purpose:** Track all models to identify duplication and understand data structure relationships.

After implementing a new model, document it here with a concise description.

---

| Model | Location | Purpose |
|-------|----------|---------|
| `Note` | `Features/Notes/Models/` | Modelo principal de notas con relaciones a conceptos, status, y purposes |
| `KeyConcept` | `Core/Models/` | Conceptos semánticos extraídos (shared entre features) |
| `Purpose` | `Features/Notes/Models/` | Catálogo de intenciones cognitivas (Acción, Aprender, Planear, etc.) |
| `NotePurpose` | `Features/Notes/Models/` | Relación many-to-many entre notas e intenciones con peso |
| `Status` | `Features/Notes/Models/` | Estados de nota (active, archived, deleted) |
| `User` | `Core/Models/` | Modelo de usuario (shared, sin autenticación real aún) |
| `UserSettings` | `Core/Models/` | Configuración de usuario (idioma, auto_structure_note) |
| `Todo` | `Core/Models/` | To-dos extraídos automáticamente de notas |
| `Painting` | `Core/Models/` | Pinturas/imágenes asociadas a notas o conceptos |
| `Stats` | `Core/Models/` | Estadísticas y analytics de usuario |
| `SearchResult` | `Core/Models/` | Resultado de búsqueda de notas |
| `ProcessedData` | `Features/Notes/Models/` | Datos procesados de nota (summary, keyPoints, sentiment, entities) |
| `Entity` | `Features/Notes/Models/` | Entidades extraídas dentro de ProcessedData |

---

## Notes

- **When to add**: After completing implementation of any Model
- **Location rules**:
  - Feature-specific model (used in 1 feature only) → `Features/[Name]/Models/`
  - Shared model (used by 2+ features) → `Core/Models/`
- **How to describe**: One concise sentence explaining what data it represents (not how)
- **Why track**: Avoid duplicating data structures and understand model relationships across features
