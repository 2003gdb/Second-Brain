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
    "email": "user@example.com"
  },
  "access_token": "jwt_token_here",
  "refresh_token": "refresh_token_here"
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
  "refresh_token": "refresh_token_here"
}
```

**Errors**:
- `401` - Invalid credentials

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
  "access_token": "new_jwt_token_here"
}
```

**Errors**:
- `401` - Invalid or expired refresh token

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
        "sentiment": "positive"
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
          "weight": 0.85
        }
      ],
      "purposes": [
        {
          "id": "uuid",
          "name": "work",
          "weight": 8
        }
      ]
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
  "purposes": []
}
```

**Errors**:
- `400` - Validation error
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
    "key_points": ["Q1 deadline", "Team expansion"]
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
      "name": "work",
      "description": "Work-related notes",
      "weight": 8
    }
  ],
  "folder_id": "uuid"
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
  "status_id": "uuid"
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

## Folders

### List Folders
```http
GET /folders
```

**Query Parameters**:
- `parent_id` (uuid, optional) - Filter by parent folder (null for root folders)
- `page` (int, default: 1)
- `limit` (int, default: 20, max: 100)

**Response** (200 OK):
```json
{
  "items": [
    {
      "id": "uuid",
      "user_id": "uuid",
      "parent_folder_id": null,
      "category_level": 1,
      "concept": {
        "id": "uuid",
        "concept_text": "Work",
        "normalized_name": "work"
      },
      "percentage": 0.45,
      "creation_date": "2025-12-31T10:00:00Z",
      "last_update": "2025-12-31T10:00:00Z",
      "children_count": 3
    }
  ],
  "pagination": {
    "total": 12,
    "page": 1,
    "limit": 20,
    "pages": 1
  }
}
```

---

### Get Folder Tree
```http
GET /folders/tree
```

**Response** (200 OK):
```json
{
  "folders": [
    {
      "id": "uuid",
      "parent_folder_id": null,
      "category_level": 1,
      "concept": {
        "id": "uuid",
        "concept_text": "Work"
      },
      "children": [
        {
          "id": "uuid2",
          "parent_folder_id": "uuid",
          "category_level": 2,
          "concept": {
            "id": "uuid3",
            "concept_text": "Projects"
          },
          "children": []
        }
      ]
    }
  ]
}
```

---

### Create Folder
```http
POST /folders
```

**Request Body**:
```json
{
  "parent_folder_id": "uuid",
  "concept_id": "uuid",
  "category_level": 2
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "parent_folder_id": "uuid",
  "category_level": 2,
  "concept": {
    "id": "uuid",
    "concept_text": "Projects"
  },
  "percentage": 0.0,
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z"
}
```

**Errors**:
- `400` - Validation error or circular reference
- `404` - Parent folder or concept not found
- `401` - Unauthorized

---

### Get Folder
```http
GET /folders/{id}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "parent_folder_id": "uuid",
  "category_level": 2,
  "concept": {
    "id": "uuid",
    "concept_text": "Projects",
    "normalized_name": "projects"
  },
  "percentage": 0.35,
  "creation_date": "2025-12-31T10:00:00Z",
  "last_update": "2025-12-31T10:00:00Z",
  "children": [],
  "notes_count": 15
}
```

**Errors**:
- `404` - Folder not found
- `401` - Unauthorized

---

### Update Folder
```http
PUT /folders/{id}
```

**Request Body**:
```json
{
  "parent_folder_id": "uuid",
  "concept_id": "uuid",
  "percentage": 0.5
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "parent_folder_id": "uuid",
  "concept_id": "uuid",
  "percentage": 0.5,
  "last_update": "2025-12-31T13:00:00Z"
}
```

**Errors**:
- `404` - Folder not found
- `400` - Circular reference or validation error
- `401` - Unauthorized

---

### Delete Folder
```http
DELETE /folders/{id}
```

**Response** (204 No Content)

**Errors**:
- `404` - Folder not found
- `400` - Folder has children (must be empty)
- `401` - Unauthorized

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
  "auto_structure_note": true
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "language": "en",
  "auto_structure_note": true,
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
  "auto_structure_note": false
}
```

**Response** (200 OK):
```json
{
  "id": "uuid",
  "user_id": "uuid",
  "language": "es",
  "auto_structure_note": false,
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
      "notes_count": 12
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
  "related_notes": [
    {
      "id": "uuid",
      "title": "Q1 Planning"
    }
  ]
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

---

## Rate Limiting

- **Authenticated requests**: 100 requests per minute
- **Authentication endpoints**: 10 requests per minute

Rate limit headers:
- `X-RateLimit-Limit`: Request limit
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Timestamp when limit resets

**429 Too Many Requests** response when limit exceeded.
