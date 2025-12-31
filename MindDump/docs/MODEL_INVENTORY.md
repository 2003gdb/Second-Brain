# Model Inventory

**Purpose:** Track all models to identify duplication and understand data structure relationships.

After implementing a new model, document it here with a concise description.

---

| Model | Location | Purpose |
|-------|----------|---------|
| _None yet_ | `Features/[Name]/Models/` or `Core/Models/` | _Add models as they're created_ |

---

## Notes

- **When to add**: After completing implementation of any Model
- **Location rules**:
  - Feature-specific model (used in 1 feature only) → `Features/[Name]/Models/`
  - Shared model (used by 2+ features) → `Core/Models/`
- **How to describe**: One concise sentence explaining what data it represents (not how)
- **Why track**: Avoid duplicating data structures and understand model relationships across features
