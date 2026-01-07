# Feature: Note Creation with Animated Expansion

## Overview

Sistema de creación de notas con sheet modal que se expande animadamente hasta convertirse en la vista de detalle, proporcionando una transición visual fluida y deliciosa.

## User Flow

1. Usuario toca FAB → Abre FloatingActionMenu
2. Usuario toca botón "Handwrite" (pencil)
3. Sheet aparece desde abajo (30% de altura)
4. Usuario escribe título y contenido
5. **Expansión automática** cuando:
   - Contenido supera altura disponible, O
   - Usuario desliza el sheet hacia arriba
6. Sheet se expande animadamente (30% → 100%)
7. Aparece painting background (42%) y card se reposiciona
8. Nota se guarda automáticamente en SwiftData
9. Navegación automática a NoteDetailView
10. Sheet se dismiss, usuario ve la nota creada

## Technical Architecture

### Components

**NoteCreationViewModel.swift**
- Estados: `collapsed` → `expanding` → `expanded` → `navigating`
- Auto-save con debounce (500ms)
- Detección de triggers de expansión
- Gestión de creación en SwiftData

**NoteCreationSheet.swift**
- Sheet modal con animaciones complejas
- Monitoreo de altura de contenido (GeometryReader)
- DragGesture para expansión manual
- Visual transformation matching NoteDetailView

**ContentSizePreferenceKey.swift**
- PreferenceKey para medir altura dinámica del contenido

### Animation Sequence (500ms)

```
T+0ms: Trigger detected
  └─ Estado → .expanding
  └─ Bloquear edición

T+0-350ms: Expansión
  └─ Altura: 30% → 100% (easeInOut)
  └─ Corner radius: 16pt → 0pt (linear)
  └─ Background overlay: 0 → 0.4 → 0 (fade in/out)

T+100ms: Transformación visual (paralela)
  └─ Header aparece (back, bookmark buttons)
  └─ Painting slide down (42% de pantalla)
  └─ Card se posiciona en 42% - 24pt
  └─ Sombra aparece en card

T+350ms: Expanded state alcanzado
  └─ Crear nota en SwiftData
  └─ Apariencia = NoteDetailView exacta

T+450ms: Navegación
  └─ NavigationStack.append(.noteDetail)
  └─ Dismiss sheet
  └─ Usuario ve NoteDetailView sin salto visual
```

### Expansion Triggers

**Automático (Content Overflow)**
```swift
contentHeight > availableHeight - 100
```

**Manual (Drag Gesture)**
```swift
dragOffset < -150 && dragVelocity < -500
```

## Key Features

### Auto-Save
- Debounce de 500ms después de cada cambio
- Actualiza `lastUpdate` automáticamente
- Usa `NoteService` (SwiftData local)

### Visual Matching
- Painting: exactamente 42% de pantalla
- Card position: `UIScreen.main.bounds.height * 0.42 - 24`
- Colores, sombras, corner radius idénticos a NoteDetailView

### Gestures
- **Drag up**: Expandir sheet (threshold: -150pt)
- **Drag down**: Dismiss sheet (threshold: +100pt)
- Cancelación de expansión: tap fuera durante animación

## Delete Functionality

**Swipe Actions**
- Swipe **left**: Eliminar nota
- Confirmation alert con título de nota
- Eliminación via `NoteService.deleteNote()`
- Background negro con ícono beige

**Swipe right**: Toggle bookmark (ya existente)

## Integration Points

### NotesListView
```swift
@State private var showNoteCreationSheet = false

FloatingActionMenu(
    onHandwrite: { showNoteCreationSheet = true }
)

.overlay {
    if showNoteCreationSheet {
        NoteCreationSheet(
            isPresented: $showNoteCreationSheet,
            onNoteCreated: { noteId in
                // Reload y navegar
            }
        )
    }
}
```

### NotesTableViewController
```swift
// Swipe left → Delete con confirmación
func tableView(_ tableView: UITableView,
               trailingSwipeActionsConfigurationForRowAt indexPath: IndexPath)
    -> UISwipeActionsConfiguration?
```

## Edge Cases

| Caso | Comportamiento |
|------|----------------|
| Contenido vacío | No crea nota, muestra error |
| Tap fuera durante expansión | Cancela animación, vuelve a collapsed |
| Drag down en cualquier momento | Dismiss sheet |
| App backgrounded | Auto-save inmediato |
| Typing rápido | Debounce previene expansiones erráticas |

## Future Enhancements

- [ ] Soporte para imágenes/adjuntos
- [ ] Formateo de texto (bold, italic, lists)
- [ ] Templates de notas
- [ ] Compartir antes de guardar
- [ ] Modo oscuro optimizado
- [ ] Transiciones más complejas (hero animations)

## Files Modified/Created

**Created:**
- `Features/Notes/ViewModels/NoteCreationViewModel.swift`
- `Features/Notes/Views/NoteCreationSheet.swift`
- `Shared/Utilities/ContentSizePreferenceKey.swift`

**Modified:**
- `Features/Notes/Views/NotesListView.swift`
- `Features/Notes/Views/UIKit/NotesTableViewController.swift`

## Testing Checklist

- [ ] Sheet aparece al tocar botón Handwrite
- [ ] Expansión automática al escribir mucho texto
- [ ] Expansión manual con drag gesture up
- [ ] Dismiss con drag gesture down
- [ ] Animación suave sin glitches
- [ ] Navegación correcta a NoteDetailView
- [ ] Auto-save funciona correctamente
- [ ] Delete con swipe muestra confirmación
- [ ] Delete elimina nota correctamente
- [ ] Bookmark toggle funciona (swipe right)

---

**Versión:** 1.0
**Fecha:** 2026-01-06
**Estado:** ✅ Implementado y funcionando
