# MindDump API Endpoints

Reference documentation for iOS developers to understand how the app will fetch and send data to the backend.

**Base URL**: `https://api.minddump.app/api/v1`

**Authentication**: JWT Bearer token in Authorization header

---

## Authentication

### Register User
```http
POST /auth/register
```

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response** (201 Created):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "created_at": "2025-12-31T10:00:00Z"
  },
  "access_token": "jwt_token_here",
  "refresh_token": "refresh_token_here",
  "expires_in": 3600
}
```

**Errors**:
- `400` - Validation error (invalid email, weak password)
- `409` - Email already exists

---

### Login
```http
POST /auth/login
```

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response** (200 OK):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  },
  "access_token": "jwt_token_here",
  "refresh_token": "refresh_token_here",
  "expires_in": 3600
}
```

**Errors**:
- `401` - Invalid credentials
- `429` - Too many login attempts

---

### Google OAuth Login
```http
POST /auth/google
```

**Request Body**:
```json
{
  "id_token": "google_id_token_here"
}
```

**Response** (200 OK):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "picture": "https://..."
  },
  "access_token": "jwt_token_here",
  "refresh_token": "refresh_token_here",
  "expires_in": 3600,
  "is_new_user": false
}
```

**Errors**:
- `400` - Invalid Google token
- `401` - Google authentication failed

---

### Refresh Token
```http
POST /auth/refresh
```

**Request Body**:
```json
{
  "refresh_token": "refresh_token_here"
}
```

**Response** (200 OK):
```json
{
  "access_token": "new_jwt_token_here",
  "expires_in": 3600
}
```

**Errors**:
- `401` - Invalid or expired refresh token

---

### Logout
```http
POST /auth/logout
```

**Request Headers**:
```
Authorization: Bearer {access_token}
```

**Response** (204 No Content)

**Errors**:
- `401` - Unauthorized

---

### Get Current User
```http
GET /auth/me
```

**Request Headers**:
```
Authorization: Bearer {access_token}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe",
  "picture": "https://...",
  "created_at": "2025-12-31T10:00:00Z",
  "last_login": "2026-01-06T10:00:00Z"
}
```

**Errors**:
- `401` - Unauthorized

---

## Notes

### List Notes
```http
GET /notes
```

**Query Parameters**:
- `page` (int, default: 1) - Page number
- `limit` (int, default: 20, max: 100) - Items per page
- `status` (string) - Filter by status: "active", "archived", "deleted"
- `priority_min` (int) - Minimum priority level
- `priority_max` (int) - Maximum priority level
- `concept_id` (uuid) - Filter by concept
- `purpose_id` (uuid) - Filter by purpose/intention
- `is_bookmarked` (bool) - Filter bookmarked notes only
- `search` (string) - Search in title and content
- `sort_by` (string, default: "last_update") - Sort field: "creation_date", "last_update", "priority", "title"
- `order` (string, default: "desc") - Sort order: "asc", "desc"

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "user_id": "uuid",
      "title": "Meeting Notes",
      "creation_date": "2025-12-31T10:00:00Z",
      "last_update": "2025-12-31T12:00:00Z",
      "last_open": "2025-12-31T11:30:00Z",
      "original_text": "Discussed project timeline...",
      "processed_data": {
        "summary": "Project timeline discussion",
        "key_points": ["Q1 deadline", "Team expansion"],
        "sentiment": "positive",
        "entities": [
          {
            "text": "Q1",
            "type": "date"
          }
        ]
      },
      "language": "en",
      "priority": 3,
      "status": {
        "id": "uuid",
        "name": "active"
      },
      "word_count": 150,
      "concepts": [
        {
          "id": "uuid",
          "concept_text": "Project Management",
          "normalized_name": "project_management",
          "weight": 0.85
        }
      ],
      "purposes": [
        {
          "id": "uuid",
          "name": "Planear",
          "weight": 8
        }
      ],
      "todos": [
        {
          "id": "uuid",
          "text": "Prepare Q1 presentation",
          "is_completed": false
        }
      ],
      "painting_url": "https://cdn.minddump.app/paintings/uuid.jpg"
    }
  ],
  "pagination": {
    "total": 156,
    "page": 1,
    "limit": 20,
    "pages": 8,
    "has_next": true,
    "has_prev": false
  }
}
```

---

### Create Note
```http
POST /notes
```

**Request Body**:
```json
{
  "title": "New Note",
  "original_text": "Note content goes here",
  "language": "en",
  "priority": 1,
  "status_id": "uuid",
  "concept_ids": ["uuid1", "uuid2"],
  "purpose_ids": [
    {
      "purpose_id": "uuid",
      "weight": 5
    }
  ]
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "title": "New Note",
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z",
  "last_open": null,
  "original_text": "Note content goes here",
  "processed_data": null,
  "language": "en",
  "priority": 1,
  "status": {
    "id": "uuid",
    "name": "active"
  },
  "word_count": 4,
  "concepts": [],
  "purposes": [],
  "todos": []
}
```

**Errors**:
- `400` - Validation error
- `401` - Unauthorized

**Note**: Backend will asynchronously process the note to extract concepts, purposes, and todos.

---

### Create Note from Voice
```http
POST /notes/voice
```

**Request Body** (multipart/form-data):
```
audio: <audio file> (m4a, wav, mp3)
language: "en" (optional)
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "title": "Voice Note",
  "creation_date": "2025-12-31T10:00:00Z",
  "original_text": "Transcribed text from audio...",
  "word_count": 45,
  "transcription_confidence": 0.95
}
```

**Errors**:
- `400` - Invalid audio file
- `413` - Audio file too large (max 25MB)
- `401` - Unauthorized

---

### Get Note
```http
GET /notes/{id}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "title": "Meeting Notes",
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T12:00:00Z",
  "last_open": "2025-12-31T11:30:00Z",
  "original_text": "Discussed project timeline...",
  "processed_data": {
    "summary": "Project timeline discussion",
    "key_points": ["Q1 deadline", "Team expansion"],
    "sentiment": "positive",
    "entities": [
      {
        "text": "Q1",
        "type": "date"
      }
    ]
  },
  "language": "en",
  "priority": 3,
  "status": {
    "id": "uuid",
    "name": "active"
  },
  "word_count": 150,
  "concepts": [
    {
      "id": "uuid",
      "concept_text": "Project Management",
      "normalized_name": "project_management",
      "weight": 0.85
    }
  ],
  "purposes": [
    {
      "id": "uuid",
      "name": "Planear",
      "description": "Planning and strategy notes",
      "weight": 8
    }
  ],
  "todos": [
    {
      "id": "uuid",
      "text": "Prepare Q1 presentation",
      "is_completed": false,
      "created_at": "2025-12-31T10:00:00Z"
    }
  ],
  "painting_url": "https://cdn.minddump.app/paintings/uuid.jpg",
  "embedding": [0.123, 0.456, ...] // Vector embedding for semantic search
}
```

**Errors**:
- `404` - Note not found
- `401` - Unauthorized

---

### Update Note
```http
PUT /notes/{id}
```

**Request Body**:
```json
{
  "title": "Updated Title",
  "original_text": "Updated content",
  "priority": 2,
  "status_id": "uuid",
  "painting_url": "https://..."
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "title": "Updated Title",
  "original_text": "Updated content",
  "last_update": "2025-12-31T13:00:00Z",
  "priority": 2,
  "status": {
    "id": "uuid",
    "name": "active"
  }
}
```

**Errors**:
- `404` - Note not found
- `400` - Validation error
- `401` - Unauthorized

**Note**: Backend will re-process the note if content changes.

---

### Bookmark Note (Toggle Priority)
```http
POST /notes/{id}/bookmark
```

**Request Body**:
```json
{
  "is_bookmarked": true
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "priority": 1,
  "is_bookmarked": true,
  "last_update": "2025-12-31T13:00:00Z"
}
```

**Errors**:
- `404` - Note not found
- `401` - Unauthorized

---

### Prioritize Note
```http
POST /notes/{id}/prioritize
```

**Request Body**:
```json
{
  "action": "increase",
  "value": 1
}
```

Actions:
- `"increase"` - Increment priority by value (default: 1)
- `"decrease"` - Decrement priority by value (default: 1)
- `"set"` - Set priority to specific value

**Response** (200 OK):
```json
{
  "id": "uuid",
  "priority": 4,
  "previous_priority": 3,
  "last_update": "2025-12-31T13:00:00Z"
}
```

**Errors**:
- `404` - Note not found
- `400` - Invalid action or value
- `401` - Unauthorized

---

### Archive Note
```http
POST /notes/{id}/archive
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "status": {
    "id": "uuid",
    "name": "archived"
  },
  "last_update": "2025-12-31T13:00:00Z"
}
```

**Errors**:
- `404` - Note not found
- `401` - Unauthorized

---

### Unarchive Note
```http
POST /notes/{id}/unarchive
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "status": {
    "id": "uuid",
    "name": "active"
  },
  "last_update": "2025-12-31T13:00:00Z"
}
```

---

### Delete Note
```http
DELETE /notes/{id}
```

**Response** (204 No Content)

**Errors**:
- `404` - Note not found
- `401` - Unauthorized

**Note**: This is a soft delete. The note status is set to "deleted" but remains in the database.

---

### Batch Delete Notes
```http
POST /notes/batch/delete
```

**Request Body**:
```json
{
  "note_ids": ["uuid1", "uuid2", "uuid3"]
}
```

**Response** (200 OK):
```json
{
  "deleted_count": 3,
  "deleted_ids": ["uuid1", "uuid2", "uuid3"]
}
```

---

### Export Notes
```http
GET /notes/export
```

**Query Parameters**:
- `format` (string, required) - Export format: "json", "markdown", "txt"
- `concept_id` (uuid, optional) - Export notes from specific concept
- `purpose_id` (uuid, optional) - Export notes with specific purpose

**Response** (200 OK):
- Content-Type: `application/json` or `text/markdown` or `text/plain`
- Content-Disposition: `attachment; filename="minddump_notes_2026-01-06.{format}"`

**Errors**:
- `400` - Invalid format
- `401` - Unauthorized

---

### Search Notes
```http
GET /notes/search
```

**Query Parameters**:
- `q` (string, required) - Search query
- `page` (int, default: 1)
- `limit` (int, default: 20)
- `search_mode` (string, default: "text") - Search mode: "text", "semantic"
- `concept_id` (uuid, optional) - Filter by concept
- `purpose_id` (uuid, optional) - Filter by purpose

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "title": "Meeting Notes",
      "original_text": "Discussed project timeline...",
      "highlighted_text": "Discussed <mark>project</mark> timeline...",
      "score": 0.95,
      "matched_concepts": ["Project Management"],
      "matched_purposes": ["Planear"]
    }
  ],
  "pagination": {
    "total": 23,
    "page": 1,
    "limit": 20
  },
  "query": "project",
  "search_mode": "text"
}
```

---

### Get Note Processing Status
```http
GET /notes/{id}/processing
```

**Response** (200 OK):
```json
{
  "note_id": "uuid",
  "is_processing": false,
  "processing_started_at": "2025-12-31T10:00:00Z",
  "processing_completed_at": "2025-12-31T10:00:15Z",
  "processing_steps": {
    "transcription": "completed",
    "concept_extraction": "completed",
    "purpose_classification": "completed",
    "todo_extraction": "completed",
    "embedding_generation": "completed"
  }
}
```

---

## Concepts

### List User Concepts
```http
GET /concepts
```

**Query Parameters**:
- `page` (int, default: 1)
- `limit` (int, default: 50, max: 200)
- `min_weight` (float) - Minimum concept weight
- `search` (string) - Search in concept text
- `sort_by` (string, default: "weight") - Sort by: "weight", "notes_count", "creation_date"
- `order` (string, default: "desc") - Sort order

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "user_id": "uuid",
      "concept_text": "Project Management",
      "normalized_name": "project_management",
      "weight": 0.85,
      "creation_date": "2025-12-31T10:00:00Z",
      "last_update": "2025-12-31T10:00:00Z",
      "notes_count": 12,
      "painting_url": "https://cdn.minddump.app/paintings/uuid.jpg"
    }
  ],
  "pagination": {
    "total": 45,
    "page": 1,
    "limit": 50,
    "pages": 1
  }
}
```

---

### Get Concept
```http
GET /concepts/{id}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "concept_text": "Project Management",
  "normalized_name": "project_management",
  "weight": 0.85,
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z",
  "painting_url": "https://cdn.minddump.app/paintings/uuid.jpg",
  "related_notes": [
    {
      "id": "uuid",
      "title": "Q1 Planning",
      "creation_date": "2025-12-31T10:00:00Z",
      "weight": 0.92
    }
  ],
  "related_concepts": [
    {
      "id": "uuid",
      "concept_text": "Team Management",
      "similarity": 0.78
    }
  ]
}
```

**Errors**:
- `404` - Concept not found
- `401` - Unauthorized

---

### Update Concept Painting
```http
PUT /concepts/{id}/painting
```

**Request Body**:
```json
{
  "painting_url": "https://...",
  "painting_source": "manual"
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "painting_url": "https://...",
  "last_update": "2025-12-31T13:00:00Z"
}
```

---

### Generate Concept Painting
```http
POST /concepts/{id}/painting/generate
```

**Request Body**:
```json
{
  "style": "impressionist",
  "prompt_override": "A serene landscape representing productivity" // optional
}
```

**Response** (202 Accepted):
```json
{
  "job_id": "uuid",
  "status": "processing",
  "estimated_time_seconds": 30
}
```

---

### Get Concept Painting Generation Status
```http
GET /concepts/{id}/painting/status/{job_id}
```

**Response** (200 OK):
```json
{
  "job_id": "uuid",
  "status": "completed",
  "painting_url": "https://cdn.minddump.app/paintings/uuid.jpg",
  "completed_at": "2025-12-31T10:01:00Z"
}
```

Status values: `"processing"`, `"completed"`, `"failed"`

---

## Purposes (Intenciones)

### List Purposes
```http
GET /purposes
```

**Query Parameters**:
- `page` (int, default: 1)
- `limit` (int, default: 20)

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "name": "Acción",
      "description": "Notes about tasks and actions to take",
      "notes_count": 45
    },
    {
      "id": "uuid",
      "name": "Aprender",
      "description": "Learning and educational notes",
      "notes_count": 38
    },
    {
      "id": "uuid",
      "name": "Planear",
      "description": "Planning and strategy notes",
      "notes_count": 29
    },
    {
      "id": "uuid",
      "name": "Crear",
      "description": "Creative and brainstorming notes",
      "notes_count": 22
    },
    {
      "id": "uuid",
      "name": "Reflexionar",
      "description": "Reflective and introspective notes",
      "notes_count": 18
    }
  ],
  "pagination": {
    "total": 5,
    "page": 1,
    "limit": 20
  }
}
```

**Note**: Purposes are system-defined and cannot be created/modified by users.

---

### Get Purpose
```http
GET /purposes/{id}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "name": "Planear",
  "description": "Planning and strategy notes",
  "creation_date": "2025-01-01T00:00:00Z",
  "notes_count": 29,
  "top_concepts": [
    {
      "id": "uuid",
      "concept_text": "Project Management",
      "notes_count": 12
    }
  ],
  "recent_notes": [
    {
      "id": "uuid",
      "title": "Q1 Planning",
      "creation_date": "2025-12-31T10:00:00Z"
    }
  ]
}
```

---

### Get Notes by Purpose
```http
GET /purposes/{id}/notes
```

**Query Parameters**:
- `page` (int, default: 1)
- `limit` (int, default: 20)
- `sort_by` (string, default: "weight") - Sort by: "weight", "creation_date", "last_update"

**Response** (200 OK):
```json
{
  "purpose": {
    "id": "uuid",
    "name": "Planear"
  },
  "items": [
    {
      "id": "uuid",
      "title": "Q1 Planning",
      "original_text": "...",
      "weight": 8,
      "creation_date": "2025-12-31T10:00:00Z"
    }
  ],
  "pagination": {
    "total": 29,
    "page": 1,
    "limit": 20
  }
}
```

---

## To-dos

### List To-dos
```http
GET /todos
```

**Query Parameters**:
- `page` (int, default: 1)
- `limit` (int, default: 50)
- `is_completed` (bool, optional) - Filter by completion status
- `concept_id` (uuid, optional) - Filter by concept
- `purpose_id` (uuid, optional) - Filter by purpose
- `sort_by` (string, default: "creation_date") - Sort by: "creation_date", "note_priority"

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "note_id": "uuid",
      "text": "Prepare Q1 presentation",
      "is_completed": false,
      "created_at": "2025-12-31T10:00:00Z",
      "completed_at": null,
      "note": {
        "id": "uuid",
        "title": "Meeting Notes",
        "priority": 3
      },
      "concepts": [
        {
          "id": "uuid",
          "concept_text": "Project Management"
        }
      ]
    }
  ],
  "pagination": {
    "total": 34,
    "page": 1,
    "limit": 50
  },
  "stats": {
    "total": 34,
    "completed": 12,
    "pending": 22
  }
}
```

---

### Get To-do
```http
GET /todos/{id}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "note_id": "uuid",
  "text": "Prepare Q1 presentation",
  "is_completed": false,
  "created_at": "2025-12-31T10:00:00Z",
  "completed_at": null,
  "note": {
    "id": "uuid",
    "title": "Meeting Notes",
    "original_text": "Discussed project timeline...",
    "priority": 3
  }
}
```

---

### Toggle To-do Completion
```http
POST /todos/{id}/toggle
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "is_completed": true,
  "completed_at": "2026-01-06T14:00:00Z"
}
```

**Note**: Completing a to-do does NOT modify the source note. To-dos are read-only projections.

---

### Get To-do Stats
```http
GET /todos/stats
```

**Response** (200 OK):
```json
{
  "total": 34,
  "completed": 12,
  "pending": 22,
  "completion_rate": 0.35,
  "by_purpose": [
    {
      "purpose": "Acción",
      "count": 18,
      "completed": 5
    }
  ],
  "by_concept": [
    {
      "concept": "Project Management",
      "count": 8,
      "completed": 3
    }
  ]
}
```

---

## Paintings / Images

### Upload Painting
```http
POST /paintings/upload
```

**Request Body** (multipart/form-data):
```
image: <image file> (jpg, png, max 5MB)
entity_type: "note" or "concept"
entity_id: "uuid"
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "url": "https://cdn.minddump.app/paintings/uuid.jpg",
  "entity_type": "note",
  "entity_id": "uuid",
  "uploaded_at": "2026-01-06T14:00:00Z"
}
```

**Errors**:
- `400` - Invalid image or entity
- `413` - Image too large
- `401` - Unauthorized

---

### Generate Painting for Note
```http
POST /notes/{id}/painting/generate
```

**Request Body**:
```json
{
  "style": "impressionist",
  "prompt_override": "A serene landscape" // optional
}
```

**Response** (202 Accepted):
```json
{
  "job_id": "uuid",
  "status": "processing",
  "estimated_time_seconds": 30
}
```

---

### Get Available Painting Styles
```http
GET /paintings/styles
```

**Response** (200 OK):
```json
{
  "styles": [
    {
      "id": "impressionist",
      "name": "Impressionist",
      "description": "Soft, dreamy landscapes inspired by Monet and Van Gogh",
      "preview_url": "https://..."
    },
    {
      "id": "abstract",
      "name": "Abstract",
      "description": "Bold colors and geometric shapes",
      "preview_url": "https://..."
    }
  ]
}
```

---

## User Settings

### Get User Settings
```http
GET /users/me/settings
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "language": "en",
  "auto_structure_note": true,
  "painting_style_preference": "impressionist",
  "auto_generate_paintings": true,
  "theme": "light",
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z"
}
```

**Errors**:
- `404` - Settings not found
- `401` - Unauthorized

---

### Create User Settings
```http
POST /users/me/settings
```

**Request Body**:
```json
{
  "language": "en",
  "auto_structure_note": true,
  "painting_style_preference": "impressionist",
  "auto_generate_paintings": true
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "language": "en",
  "auto_structure_note": true,
  "painting_style_preference": "impressionist",
  "auto_generate_paintings": true,
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z"
}
```

**Errors**:
- `409` - Settings already exist
- `400` - Validation error
- `401` - Unauthorized

---

### Update User Settings
```http
PUT /users/me/settings
```

**Request Body**:
```json
{
  "language": "es",
  "auto_structure_note": false,
  "painting_style_preference": "abstract",
  "auto_generate_paintings": false,
  "theme": "dark"
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "language": "es",
  "auto_structure_note": false,
  "painting_style_preference": "abstract",
  "auto_generate_paintings": false,
  "theme": "dark",
  "last_update": "2025-12-31T13:00:00Z"
}
```

**Errors**:
- `404` - Settings not found
- `400` - Validation error
- `401` - Unauthorized

---

### Delete User Settings
```http
DELETE /users/me/settings
```

**Response** (204 No Content)

**Errors**:
- `404` - Settings not found
- `401` - Unauthorized

---

## Stats & Analytics

### Get User Stats
```http
GET /users/me/stats
```

**Response** (200 OK):
```json
{
  "total_notes": 156,
  "total_concepts": 45,
  "total_todos": 34,
  "completed_todos": 12,
  "total_words": 12450,
  "notes_this_week": 8,
  "notes_this_month": 34,
  "most_used_concepts": [
    {
      "id": "uuid",
      "concept_text": "Project Management",
      "notes_count": 12
    }
  ],
  "most_used_purposes": [
    {
      "id": "uuid",
      "name": "Acción",
      "notes_count": 45
    }
  ],
  "activity_by_day": [
    {
      "date": "2026-01-06",
      "notes_created": 3,
      "notes_updated": 5
    }
  ]
}
```

---

### Get Concept Stats
```http
GET /concepts/{id}/stats
```

**Response** (200 OK):
```json
{
  "concept_id": "uuid",
  "concept_text": "Project Management",
  "notes_count": 12,
  "total_words": 3450,
  "avg_priority": 2.5,
  "notes_created_this_week": 2,
  "related_purposes": [
    {
      "purpose": "Planear",
      "count": 8
    }
  ],
  "growth_trend": "increasing"
}
```

---

## Error Response Format

All errors follow this format:

```json
{
  "error": {
    "code": "NOTE_NOT_FOUND",
    "message": "Note with ID {id} not found",
    "details": {
      "note_id": "uuid-here"
    },
    "timestamp": "2025-12-31T10:30:00Z"
  }
}
```

### Common Error Codes

- `VALIDATION_ERROR` - Invalid request data
- `UNAUTHORIZED` - Missing or invalid authentication token
- `FORBIDDEN` - Valid token but insufficient permissions
- `NOT_FOUND` - Resource not found
- `CONFLICT` - Resource already exists or constraint violation
- `INTERNAL_ERROR` - Server error
- `RATE_LIMIT_EXCEEDED` - Too many requests
- `PROCESSING_ERROR` - Error during async processing
- `STORAGE_LIMIT_EXCEEDED` - User storage quota exceeded

---

## Rate Limiting

- **Authenticated requests**: 100 requests per minute
- **Authentication endpoints**: 10 requests per minute
- **Upload endpoints**: 20 requests per hour
- **AI generation endpoints**: 10 requests per hour

Rate limit headers:
- `X-RateLimit-Limit`: Request limit
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Timestamp when limit resets

**429 Too Many Requests** response when limit exceeded.

---

## Webhooks (Future)

### Register Webhook
```http
POST /webhooks
```

**Request Body**:
```json
{
  "url": "https://yourapp.com/webhook",
  "events": ["note.created", "note.processed", "concept.created"]
}
```

**Available events**:
- `note.created`
- `note.updated`
- `note.deleted`
- `note.processed`
- `concept.created`
- `concept.updated`
- `todo.created`
- `todo.completed`

---

## Pagination

All list endpoints support pagination with these query parameters:
- `page` (int, default: 1) - Page number (1-indexed)
- `limit` (int) - Items per page (default varies by endpoint)

Response format includes:
```json
{
  "items": [...],
  "pagination": {
    "total": 156,
    "page": 1,
    "limit": 20,
    "pages": 8,
    "has_next": true,
    "has_prev": false
  }
}
```

---

## Sorting & Filtering

Most list endpoints support:
- `sort_by` - Field to sort by
- `order` - Sort order: "asc" or "desc"
- Endpoint-specific filters (see individual endpoint documentation)

---

## Versioning

The API version is included in the base URL: `/api/v1`

Breaking changes will result in a new version (`/api/v2`), while backward-compatible changes will be added to the current version.

---

**Last Updated**: 2026-01-06
