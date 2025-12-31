# Second Brain - Esquema Final de Base de Datos

## 📋 Tablas Finales

### **Users** (Supabase Auth)
Tabla manejada por Supabase Authentication

---

### **tm_notes** (Notas del usuario)
```sql
- uuid (PK)
- user_id (FK -> users.uuid)
- title (text)
- creation_date (datetime)
- last_update (datetime)
- last_open (datetime)
- original_text (text)
- processed_data (jsonb)
- language (text)
- priority (int)
- status_id (FK -> tc_status.uuid)
- note_type_id (FK -> tc_note_type.uuid)
- word_count (int)
```

---

### **tm_key_concepts** (Conceptos clave maestros)
```sql
- uuid (PK)
- user_id (FK -> users.uuid)
- concept_text (text)
- normalized_name (text)
- weight (numeric(5,4))
- creation_date (datetime)
- last_update (datetime)
```

---

### **tr_notes_concepts** (Relación Notas-Conceptos)
```sql
- uuid (PK)
- note_id (FK -> tm_notes.uuid)
- concept_id (FK -> tm_key_concepts.uuid)
```

---

### **tm_folders** (Carpetas jerárquicas)
```sql
- uuid (PK)
- user_id (FK -> users.uuid)
- parent_folder_id (FK -> tm_folders.uuid, nullable)
- category_level (int) -- 1, 2, 3, etc.
- concept_id (FK -> tm_key_concepts.uuid, nullable)
- percentage (decimal)
- creation_date (datetime)
- last_update (datetime)
```

---

### **tc_purpose** (Catálogo de Propósitos)
```sql
- uuid (PK)
- name (text) -- "Do", "Plan", "Decide", "Create", etc.
- description (text)
```

---

### **tm_purpose** (Relación Notas-Propósitos)
```sql
- uuid (PK)
- note_id (FK -> tm_notes.uuid)
- purpose_id (FK -> tc_purpose.uuid)
- weight (int)
```

---

### **tc_status** (Catálogo de Estados)
```sql
- uuid (PK)
- name (text) -- "active", "archived", "deleted"
- description (text)
```

---

### **tc_note_type** (Catálogo de Tipos de Nota)
```sql
- uuid (PK)
- name (text) -- "voice", "text", "image", "transcribe_link".
- description (text)
```

---

### **tm_user_settings** (Configuración de Usuario)
```sql
- uuid (PK)
- user_id (FK -> users.uuid, unique)
- language (text) -- "es", "en", etc.
- auto_structure_note (boolean)
- creation_date (datetime)
- last_update (datetime)
```

---

## 🔗 Relaciones Principales

1. **Users → tm_notes** (1:N)
2. **Users → tm_key_concepts** (1:N)
3. **Users → tm_folders** (1:N)
4. **Users → tm_user_settings** (1:1)
5. **tm_notes ↔ tm_key_concepts** (N:M via tr_notes_concepts)
6. **tm_notes ↔ tc_purpose** (N:M via tm_purpose)
7. **tm_notes → tc_status** (N:1)
8. **tm_notes → tc_note_type** (N:1)
9. **tm_folders → tm_folders** (self-referencing para jerarquía)
10. **tm_folders → tm_key_concepts** (N:1, nullable)

---

## 📊 Índices Recomendados

```sql
-- Búsquedas por usuario
CREATE INDEX idx_notes_user_id ON tm_notes(user_id);
CREATE INDEX idx_concepts_user_id ON tm_key_concepts(user_id);
CREATE INDEX idx_folders_user_id ON tm_folders(user_id);

-- Ordenamiento temporal
CREATE INDEX idx_notes_last_update ON tm_notes(last_update DESC);
CREATE INDEX idx_notes_creation_date ON tm_notes(creation_date DESC);

-- Búsqueda de conceptos
CREATE INDEX idx_concepts_normalized ON tm_key_concepts(normalized_name);
CREATE INDEX idx_concepts_weight ON tm_key_concepts(weight DESC);

-- Relaciones many-to-many
CREATE INDEX idx_notes_concepts_note_id ON tr_notes_concepts(note_id);
CREATE INDEX idx_notes_concepts_concept_id ON tr_notes_concepts(concept_id);

-- Jerarquía de folders
CREATE INDEX idx_folders_parent_id ON tm_folders(parent_folder_id);
CREATE INDEX idx_folders_concept_id ON tm_folders(concept_id);

-- Estados y tipos
CREATE INDEX idx_notes_status_id ON tm_notes(status_id);
CREATE INDEX idx_notes_type_id ON tm_notes(note_type_id);
```

---

## 🎯 Notas de Implementación

### Valores por defecto para tc_status:
- active
- archived
- deleted

### Valores por defecto para tc_note_type:
- voice
- text
- mixed

### Valores por defecto para tc_purpose:
1. Do (Actions, tasks, to-dos)
2. Plan (Organization, structure, steps)
3. Decide (Choices, conclusions)
4. Create (Creative ideas, concepts)
5. Learn (Discoveries, study)
6. Communicate (Messages to prepare)
7. Reflect (Questions, introspection)
8. Feel (Emotions, moods)
9. Capture (Observations, experiences)
10. Envision (Goals, vision)

### Row Level Security (RLS) en Supabase:
```sql
-- Ejemplo para tm_notes
ALTER TABLE tm_notes ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can only see their own notes"
ON tm_notes FOR SELECT
USING (auth.uid() = user_id);

CREATE POLICY "Users can only insert their own notes"
ON tm_notes FOR INSERT
WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can only update their own notes"
ON tm_notes FOR UPDATE
USING (auth.uid() = user_id);
```

Aplicar políticas similares para todas las tablas con user_id.
