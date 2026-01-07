# Product Requirements Document (PRD)

## MindDump — Cognitive Notes App

**Versión:** 0.2 (Scope redefinido)
**Plataforma:** iOS (Swift)
**Dispositivo objetivo:** iPhone

---

## 1. Overview

### 1.1 Propósito

MindDump es una aplicación móvil de notas diseñada para **pensar**, no solo para escribir. Su objetivo es ayudar al usuario a capturar ideas rápidamente y permitir que el sistema procese esas notas para **extraer estructura cognitiva**: conceptos, intenciones y pendientes (to‑do’s), que evolucionan con el tiempo.

La app funciona como un **segundo cerebro**, donde el valor no está únicamente en cada nota individual, sino en las relaciones que emergen entre ellas.

---

### 1.2 Propuesta de Valor

* Captura rápida de ideas (texto y voz)
* Procesamiento automático de notas para:

  * conceptos personales
  * intenciones cognitivas
  * to‑do’s
* Organización emergente sin fricción manual
* Priorización implícita mediante bookmarks
* Asociación visual mediante imágenes/pinturas

---

### 1.3 Usuario Objetivo

Personas que:

* piensan escribiendo o hablando
* generan ideas dispersas a lo largo del día
* quieren claridad, no carpetas manuales
* valoran estructura emergente más que organización rígida

---

## 2. User Journey (Alto Nivel)

1. **Onboarding / Tutorial**
   Introduce el valor de la app y cómo el sistema ayuda a pensar mejor.

   > *El contenido del tutorial falta por definirse en esta versión del PRD.*

2. **Autenticación**
   Inicio de sesión mediante:

   * Google
   * Email / método tradicional

3. **Vista Principal — Todas las Notas**
   Acceso al sistema completo de notas y navegación a vistas derivadas.

4. **Exploración Cognitiva**
   Navegación entre:

   * Conceptos
   * Intenciones
   * To‑do’s

---

## 3. Entidades Principales del Sistema

### 3.1 Nota

La **nota** es la unidad central del sistema.

* Se representa como un **JSON estructurado**
* Puede contener:

  * texto
  * bloques (futuro)
  * metadatos
* Es **editable**
* Es la **fuente única de verdad**

Cada vez que una nota:

* se crea
* se edita
* se elimina
* se marca o desmarca como bookmark

→ el sistema vuelve a ejecutar el pipeline de procesamiento que se hara en el backend.

---

### 3.2 Concepto

* Entidad **generada automáticamente** por el sistema
* Representa agrupaciones semánticas personales
* Por el momento no es editable por el usuario en esta versión
* Puede:

  * agrupar múltiples notas
  * evolucionar con el tiempo

> El comportamiento multi‑idioma de los conceptos queda **fuera de definición** en este PRD.

---

### 3.3 Intención

* Categoría cognitiva **predefinida**
* Set finito controlado por el sistema
* Cada nota tiene **exactamente una intención**

Ejemplos:

* Acción
* Aprender
* Planear
* Crear
* Reflexionar

Las intenciones se usan principalmente para:

* lógica backend
* vistas de utilidad
* flujos futuros

De momomento solo es una columna mas en la base de datos.

---

### 3.4 To‑do

* Derivado automáticamente del contenido de una nota
* No existe de forma independiente
* Vive y muere con la nota

Características:

* Referencia siempre a su nota origen
* Editable solo indirectamente (editando la nota)

---

## 4. Funcionalidades (Scope Actual)

### 4.1 Autenticación

* Login obligatorio después del tutorial
* Métodos:

  * Google
  * Email

---

### 4.2 Gestión de Notas

* Crear nota:

  * texto
  * voz (dictado)
* Editar nota
* Eliminar nota
* Marcar / desmarcar bookmark

**Bookmark**:

* Se presenta como un favorito simple
* Internamente incrementa el peso semántico de la nota
* No se explica al usuario como funcionalidad avanzada

---

### 4.3 Procesamiento Automático

Cada nota es procesada para extraer:

* intención
* conceptos relacionados
* to‑do’s
* embedding semántico

El procesamiento es:

* reactivo
* eventual (no realtime)

---

### 4.4 Vistas Principales

#### 4.4.1 Vista — Todas las Notas

* Lista cronológica de notas
* Acciones:

  * abrir nota
  * bookmark
  * eliminar

---

#### 4.4.2 Vista — Nota Individual

* Contenido completo de la nota
* Pintura de fondo (estética)
* Acceso a:

  * conceptos asociados
  * intención
  * to‑do’s derivados

---

#### 4.4.3 Vista — Conceptos

* Lista de conceptos generados
* Cada concepto muestra:

  * imagen de fondo
  * notas asociadas

---

#### 4.4.4 Vista — Intenciones

* Lista fija de intenciones
* Al seleccionar una:

  * se muestran las notas correspondientes

---

#### 4.4.5 Vista — To‑do’s

* Lista de pendientes activos
* Cada to‑do:

  * referencia su nota origen

---

### 4.5 Pinturas / Imágenes

* Pueden asociarse a:

  * notas
  * conceptos
* Se pueden:

  * generar automáticamente
  * seleccionar manualmente
* Función puramente estética

---

### 4.6 Settings

Opciones disponibles:

* Idioma (afecta procesamiento futuro)
* Preferencias visuales (pinturas)

---

### 4.7 Widgets y Quick Actions

* Widgets para:

  * crear nota rápida
* Acciones rápidas:

  * grabación de voz

Toda entrada por widget se trata como **una nota normal**.

---

## 5. Requerimientos Técnicos

### 5.1 Plataforma

* Swift
* iOS
* Mobile only

---

### 5.2 Arquitectura

* Frontend desacoplado del backend
* Contratos de datos claros:

  * Note JSON
  * Concept JSON
  * To‑do projection
  * Intention enum

---

### 5.3 Persistencia

* Backend con almacenamiento de:

  * notas
  * resultados derivados
* Auth obligatoria

---

## 6. Fuera de Alcance (Explícito)

* Edición manual de conceptos
* Fusión / renombrado de conceptos
* Definición multi‑idioma semántica
* Colaboración
* Sync multi‑dispositivo avanzado
* AI avanzada explicable al usuario

---

## 7. Futuro (No implementado)

* Control total del usuario sobre conceptos
* Re‑procesamiento manual
* Edición rica de notas (bloques, headers, estilos)
* To‑do management avanzado
* Inteligencia multi‑idioma

---

## 8. Principios de Diseño del Producto

* La nota es la verdad
* Todo lo demás es derivado
* El sistema piensa, el usuario decide
* Simplicidad en UI, complejidad en backend
* Evolución progresiva, no magia inmediata

---

*Este PRD define el sistema mínimo coherente para construir MindDump sin contradicciones ni deuda conceptual temprana.*

---

## 9. Especificaciones Técnicas de Implementación

### 9.1 Frontend (iOS - Swift)

**Arquitectura:**
- MVVM con @Observable (iOS 17+)
- Feature-based folder structure
- SwiftData para persistencia local (transición a API backend)
- NavigationStack con typed routes

**Frameworks clave:**
- SwiftUI (UI declarativa)
- SwiftData (persistencia local)
- Speech Framework (dictado on-device)
- Combine (reactive programming)

**Dependencias externas:**
- Ninguna por defecto (opcionalmente WhisperKit para mejor transcripción)

---

### 9.2 Backend (Pendiente)

**Stack recomendado:**
- Python (FastAPI) o Node.js (Express/NestJS)
- PostgreSQL (base de datos principal)
- Redis (cache y rate limiting)
- Vector DB (Pinecone/Qdrant para semantic search)
- Object Storage (S3/CloudFlare R2 para pinturas)

**Servicios de AI:**
- OpenAI GPT-4 (extracción de conceptos, clasificación de intenciones)
- OpenAI Embeddings (semantic search)
- Whisper API (transcripción de voz - fallback cloud)
- DALL-E o Stable Diffusion (generación de pinturas)

**Infraestructura:**
- Serverless functions para procesamiento asíncrono
- Message Queue (RabbitMQ/SQS) para pipeline de procesamiento
- CDN para servir pinturas

---

### 9.3 Pipeline de Procesamiento (Backend)

**Flujo cuando se crea/edita una nota:**

```
1. Usuario crea/edita nota
   ↓
2. Frontend guarda en SwiftData (offline-first)
   ↓
3. Frontend envía a backend API
   ↓
4. Backend guarda nota en PostgreSQL
   ↓
5. Backend encola job de procesamiento
   ↓
6. Worker procesa en background:
   a. Extracción de conceptos (GPT-4)
   b. Clasificación de intención (GPT-4)
   c. Extracción de to-dos (GPT-4)
   d. Generación de embedding (OpenAI Embeddings)
   e. Generación de resumen (GPT-4)
   ↓
7. Worker actualiza nota con datos procesados
   ↓
8. Frontend recibe notificación (polling o webhook)
   ↓
9. Frontend sincroniza y actualiza UI
```

**Tiempo estimado de procesamiento:** 5-15 segundos por nota

---

### 9.4 Base de Datos (Schema Principal)

**Tablas PostgreSQL:**

```sql
-- Usuarios
CREATE TABLE users (
    id UUID PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255), -- NULL si Google OAuth
    name VARCHAR(255),
    picture_url TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    last_login TIMESTAMP
);

-- Notas
CREATE TABLE notes (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    title VARCHAR(500),
    original_text TEXT NOT NULL,
    processed_data JSONB, -- summary, key_points, sentiment, entities
    language VARCHAR(10) DEFAULT 'en',
    priority INTEGER DEFAULT 0,
    word_count INTEGER,
    status_id UUID REFERENCES statuses(id),
    embedding VECTOR(1536), -- Para semantic search
    painting_url TEXT,
    creation_date TIMESTAMP DEFAULT NOW(),
    last_update TIMESTAMP DEFAULT NOW(),
    last_open TIMESTAMP
);

-- Conceptos (generados automáticamente)
CREATE TABLE key_concepts (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    concept_text VARCHAR(255) NOT NULL,
    normalized_name VARCHAR(255) NOT NULL,
    weight FLOAT DEFAULT 0.0,
    painting_url TEXT,
    creation_date TIMESTAMP DEFAULT NOW(),
    last_update TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, normalized_name)
);

-- Relación Notes ↔ Concepts (many-to-many)
CREATE TABLE note_concepts (
    note_id UUID REFERENCES notes(id) ON DELETE CASCADE,
    concept_id UUID REFERENCES key_concepts(id) ON DELETE CASCADE,
    weight FLOAT DEFAULT 0.0,
    PRIMARY KEY (note_id, concept_id)
);

-- Intenciones (catálogo predefinido)
CREATE TABLE purposes (
    id UUID PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL, -- "Acción", "Aprender", etc.
    description TEXT
);

-- Relación Notes ↔ Purposes (many-to-many con peso)
CREATE TABLE note_purposes (
    id UUID PRIMARY KEY,
    note_id UUID REFERENCES notes(id) ON DELETE CASCADE,
    purpose_id UUID REFERENCES purposes(id),
    weight INTEGER DEFAULT 0
);

-- To-dos (extraídos de notas)
CREATE TABLE todos (
    id UUID PRIMARY KEY,
    note_id UUID REFERENCES notes(id) ON DELETE CASCADE,
    text VARCHAR(500) NOT NULL,
    is_completed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    completed_at TIMESTAMP
);

-- Estados de nota
CREATE TABLE statuses (
    id UUID PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL, -- "active", "archived", "deleted"
    description TEXT
);

-- Configuración de usuario
CREATE TABLE user_settings (
    id UUID PRIMARY KEY,
    user_id UUID UNIQUE REFERENCES users(id),
    language VARCHAR(10) DEFAULT 'en',
    auto_structure_note BOOLEAN DEFAULT TRUE,
    painting_style_preference VARCHAR(50) DEFAULT 'impressionist',
    auto_generate_paintings BOOLEAN DEFAULT TRUE,
    theme VARCHAR(20) DEFAULT 'light',
    creation_date TIMESTAMP DEFAULT NOW(),
    last_update TIMESTAMP DEFAULT NOW()
);

-- Pinturas (tracking)
CREATE TABLE paintings (
    id UUID PRIMARY KEY,
    entity_type VARCHAR(20) NOT NULL, -- "note" or "concept"
    entity_id UUID NOT NULL,
    url TEXT NOT NULL,
    source VARCHAR(20) DEFAULT 'generated', -- "generated", "manual", "uploaded"
    style VARCHAR(50),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Índices para performance
CREATE INDEX idx_notes_user_id ON notes(user_id);
CREATE INDEX idx_notes_status_id ON notes(status_id);
CREATE INDEX idx_notes_embedding ON notes USING ivfflat (embedding vector_cosine_ops);
CREATE INDEX idx_key_concepts_user_id ON key_concepts(user_id);
CREATE INDEX idx_todos_note_id ON todos(note_id);
CREATE INDEX idx_todos_is_completed ON todos(is_completed);
```

---

### 9.5 Autenticación y Seguridad

**Métodos de autenticación:**
1. Email/Password (bcrypt para hashing)
2. Google OAuth 2.0

**Tokens:**
- JWT para access tokens (expira en 1 hora)
- Refresh tokens (expira en 30 días, stored en DB)
- Storage: Keychain en iOS, httpOnly cookies en web

**Seguridad:**
- HTTPS obligatorio en producción
- Rate limiting (100 req/min por usuario)
- Input validation en todos los endpoints
- SQL injection protection (prepared statements)
- XSS protection (sanitización de HTML)

---

### 9.6 Búsqueda

**Dos modos:**

**1. Text Search (rápido, exacto)**
- PostgreSQL full-text search
- Indexa `title` y `original_text`
- Soporta operadores AND, OR, NOT
- Response time: < 100ms

**2. Semantic Search (inteligente, conceptual)**
- Vector similarity search con embeddings
- Usa pgvector extension en PostgreSQL
- Encuentra notas conceptualmente similares
- Response time: < 500ms

**Ejemplo:**
```
Query: "ideas de proyectos"

Text Search → Notas que contengan literalmente "ideas" y "proyectos"
Semantic Search → Notas sobre "brainstorming", "planificación", "desarrollo"
```

---

### 9.7 Exportación de Datos

**Formatos soportados:**
- **JSON**: Estructura completa con metadatos
- **Markdown**: Notas en formato legible
- **TXT**: Texto plano simple

**Alcance:**
- Exportar todas las notas
- Exportar notas de un concepto específico
- Exportar notas con una intención específica

**Implementación:**
- Generación server-side
- Descarga directa (no email)
- Límite de tamaño: 50MB por exportación

---

### 9.8 Widgets (iOS)

**Widget de Creación Rápida:**
- Tamaños: small, medium
- Funcionalidad: Tap para crear nota
- Deep link a `BlankNoteEditorView`

**Widget de Última Nota:**
- Tamaño: small
- Muestra título de última nota editada
- Tap para abrir detalle

**Widget de To-dos Pendientes:**
- Tamaño: medium, large
- Lista de to-dos no completados (max 5)
- Tap en to-do marca como completado
- Tap fuera abre TodosListView

---

### 9.9 Quick Actions (iOS)

**Home Screen Quick Actions:**
1. Nueva Nota de Texto
2. Nueva Nota de Voz
3. Ver To-dos
4. Buscar Notas

Implementación: `UIApplicationShortcutItem` en Info.plist

---

### 9.10 Offline-First Strategy

**Sincronización:**
- CRUD local en SwiftData (siempre funciona offline)
- Cola de operaciones pendientes
- Sincronización automática al recuperar conexión
- Conflictos: última modificación gana (timestamp)

**Indicadores UI:**
- Badge en notas no sincronizadas
- Banner de "No hay conexión"
- Estado de sync en settings

---

### 9.11 Límites y Restricciones

**Por usuario:**
- Notas: ilimitadas
- Conceptos generados: ilimitados
- To-dos: ilimitados
- Audio: max 25MB por archivo
- Imágenes: max 5MB por archivo
- Almacenamiento total: 1GB (gratis), 10GB (premium)

**Rate Limits (API):**
- Autenticación: 10 req/min
- CRUD notas: 100 req/min
- Uploads: 20 req/hora
- Generación de pinturas: 10 req/hora

---

### 9.12 Roadmap de Features Post-MVP

**v1.1 - Edición Rica:**
- Markdown support
- Headers, listas, énfasis
- Bloques estructurados

**v1.2 - Colaboración:**
- Compartir notas (read-only links)
- Workspaces compartidos
- Comentarios en notas

**v1.3 - AI Avanzada:**
- Chat con tus notas (Q&A)
- Sugerencias de conceptos relacionados
- Auto-completado inteligente

**v1.4 - Integraciones:**
- Importar desde Notion, Evernote
- Exportar a Google Docs
- API pública para third-party

**v1.5 - Multi-plataforma:**
- macOS app (Catalyst)
- Web app (React)
- Android app (Kotlin)

---

*Última actualización: 2026-01-06*
