# 🔐 Accounts API Documentation

Complete API reference for the **Accounts** module of Jura Modular.

## Overview

The Accounts API handles user authentication, authorization, profile management, and two-factor authentication (2FA) for the Jura Modular system.

### Base URL
```
http://localhost:8000/api/accounts/
```

### Authentication
Most endpoints require JWT authentication:
```http
Authorization: Bearer <access_token>
```

## 🔑 Authentication Endpoints

### Login

Authenticate user and get JWT tokens.

```http
POST /api/accounts/login/
```

**Request Body:**
```json
{
  "username": "string",
  "password": "string",
  "totp_token": "string"  // Optional, required if 2FA enabled
}
```

**Response (200 OK):**
```json
{
  "access": "eyJ0eXAiOiJKV1Q...",
  "refresh": "eyJ0eXAiOiJKV1Q...",
  "user": {
    "id": 1,
    "username": "john.doe",
    "email": "john@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "role": "client",
    "role_display": "Mandant",
    "two_factor_enabled": false,
    "is_verified": true
  },
  "requires_2fa": false
}
```

**Error Responses:**
```json
// 401 Unauthorized - Invalid credentials
{
  "detail": "No active account found with the given credentials"
}

// 400 Bad Request - 2FA required
{
  "error": "2FA token required",
  "requires_2fa": true
}
```

### Token Refresh

Refresh an expired access token.

```http
POST /api/accounts/token/refresh/
```

**Request Body:**
```json
{
  "refresh": "eyJ0eXAiOiJKV1Q..."
}
```

**Response (200 OK):**
```json
{
  "access": "eyJ0eXAiOiJKV1Q..."
}
```

### Token Verification

Verify if a token is valid.

```http
POST /api/accounts/token/verify/
```

**Request Body:**
```json
{
  "token": "eyJ0eXAiOiJKV1Q..."
}
```

**Response (200 OK):**
```json
{}
```

## 👤 User Management

### User Registration

Register a new user account.

```http
POST /api/accounts/register/
```

**Request Body:**
```json
{
  "username": "john.doe",
  "email": "john@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "phone_number": "+49123456789",
  "role": "client",
  "password": "SecurePassword123!",
  "password_confirm": "SecurePassword123!"
}
```

**Response (201 Created):**
```json
{
  "message": "Benutzer erfolgreich registriert.",
  "user": {
    "id": 5,
    "username": "john.doe",
    "email": "john@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "full_name": "John Doe",
    "phone_number": "+49123456789",
    "role": "client",
    "role_display": "Mandant",
    "two_factor_enabled": false,
    "is_verified": false,
    "date_joined": "2025-08-01T19:30:00Z",
    "last_login": null
  }
}
```

**Validation Errors:**
```json
{
  "username": ["A user with that username already exists."],
  "email": ["This field must be unique."],
  "password": ["This password is too common."],
  "password_confirm": ["Passwörter stimmen nicht überein."]
}
```

### Get User Profile

Get current user's profile information.

```http
GET /api/accounts/profile/
```

**Headers:**
```http
Authorization: Bearer <access_token>
```

**Response (200 OK):**
```json
{
  "id": 1,
  "username": "john.doe",
  "email": "john@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "full_name": "John Doe",
  "phone_number": "+49123456789",
  "role": "client",
  "role_display": "Mandant",
  "two_factor_enabled": false,
  "is_verified": true,
  "date_joined": "2025-08-01T19:30:00Z",
  "last_login": "2025-08-01T20:15:00Z"
}
```

### Update User Profile

Update current user's profile information.

```http
PUT /api/accounts/profile/
PATCH /api/accounts/profile/
```

**Request Body (PUT - all fields):**
```json
{
  "email": "newemail@example.com",
  "first_name": "John",
  "last_name": "Smith",
  "phone_number": "+49987654321"
}
```

**Request Body (PATCH - partial update):**
```json
{
  "phone_number": "+49987654321"
}
```

**Response (200 OK):**
```json
{
  "id": 1,
  "username": "john.doe",
  "email": "newemail@example.com",
  "first_name": "John",
  "last_name": "Smith",
  "full_name": "John Smith",
  "phone_number": "+49987654321",
  // ... rest of profile
}
```

### Change Password

Change user's password.

```http
POST /api/accounts/change-password/
```

**Request Body:**
```json
{
  "old_password": "OldPassword123!",
  "new_password": "NewPassword456!",
  "new_password_confirm": "NewPassword456!"
}
```

**Response (200 OK):**
```json
{
  "message": "Passwort erfolgreich geändert."
}
```

**Error Response:**
```json
{
  "old_password": ["Altes Passwort ist falsch."],
  "new_password_confirm": ["Neue Passwörter stimmen nicht überein."]
}
```

## 👥 Client Profile Management

### List Client Profiles

Get list of client profiles (filtered by user role).

```http
GET /api/accounts/clients/
```

**Query Parameters:**
- `page` (int): Page number for pagination
- `page_size` (int): Number of items per page (default: 10)
- `search` (string): Search in names and email
- `user_type` (string): Filter by `private` or `company`
- `is_active_client` (boolean): Filter by active status

**Response (200 OK):**
```json
{
  "count": 25,
  "next": "http://localhost:8000/api/accounts/clients/?page=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "user": {
        "id": 5,
        "username": "client1",
        "email": "client1@example.com",
        "first_name": "Max",
        "last_name": "Mustermann",
        "full_name": "Max Mustermann",
        "role": "client",
        "role_display": "Mandant"
      },
      "user_type": "private",
      "phone_number": "+49123456789",
      "address": "Musterstraße 123, 12345 Musterstadt",
      "birth_date": "1980-05-15",
      "avatar": null,
      "avatar_url": "/static/images/default_avatar.png",
      "company_name": null,
      "tax_id": null,
      "case_notes": "Wichtiger Mandant - bevorzugte Behandlung",
      "preferred_contact_method": "email",
      "document_upload_permission": true,
      "is_active_client": true,
      "contact_info": {
        "phone_number": "+49123456789",
        "email": "client1@example.com",
        "address": "Musterstraße 123, 12345 Musterstadt",
        "preferred_method": "E-Mail"
      },
      "created_at": "2025-08-01T19:30:00Z",
      "updated_at": "2025-08-01T20:15:00Z"
    }
  ]
}
```

### Get Client Profile

Get specific client profile by ID.

```http
GET /api/accounts/clients/{id}/
```

**Response (200 OK):**
```json
{
  "id": 1,
  "user": {
    "id": 5,
    "username": "client1",
    "email": "client1@example.com",
    "first_name": "Max",
    "last_name": "Mustermann",
    "full_name": "Max Mustermann",
    "role": "client",
    "role_display": "Mandant"
  },
  "user_type": "company",
  "phone_number": "+49123456789",
  "address": "Firmenstraße 456, 54321 Businesstown",
  "birth_date": null,
  "avatar": "/media/avatars/client1.jpg",
  "avatar_url": "/media/avatars/client1.jpg",
  "company_name": "Mustermann GmbH",
  "tax_id": "DE123456789",
  "case_notes": "Großkunde - spezielle Konditionen",
  "preferred_contact_method": "phone",
  "document_upload_permission": true,
  "is_active_client": true,
  "contact_info": {
    "phone_number": "+49123456789",
    "email": "client1@example.com",
    "address": "Firmenstraße 456, 54321 Businesstown",
    "preferred_method": "Telefon"
  },
  "created_at": "2025-08-01T19:30:00Z",
  "updated_at": "2025-08-01T20:15:00Z"
}
```

### Create Client Profile

Create a new client profile.

```http
POST /api/accounts/clients/
```

**Request Body:**
```json
{
  "user_type": "company",
  "phone_number": "+49123456789",
  "address": "Neue Straße 789, 67890 Neustadt",
  "company_name": "Neue Firma GmbH",
  "tax_id": "DE987654321",
  "preferred_contact_method": "email",
  "document_upload_permission": true,
  "is_active_client": true
}
```

**Response (201 Created):**
```json
{
  "id": 10,
  "user": {
    // Current user data
  },
  "user_type": "company",
  "company_name": "Neue Firma GmbH",
  // ... rest of profile data
}
```

### Update Client Profile

Update an existing client profile.

```http
PUT /api/accounts/clients/{id}/
PATCH /api/accounts/clients/{id}/
```

**Request Body (PATCH example):**
```json
{
  "preferred_contact_method": "phone",
  "case_notes": "Updated notes for this client"
}
```

**Response (200 OK):**
```json
{
  "id": 1,
  // Updated profile data
}
```

### Activate Client

Activate a client profile.

```http
POST /api/accounts/clients/{id}/activate/
```

**Response (200 OK):**
```json
{
  "message": "Klientenprofil aktiviert."
}
```

### Deactivate Client

Deactivate a client profile.

```http
POST /api/accounts/clients/{id}/deactivate/
```

**Response (200 OK):**
```json
{
  "message": "Klientenprofil deaktiviert."
}
```

## 🔐 Two-Factor Authentication (2FA)

### Get 2FA Status

Check current 2FA status and devices.

```http
GET /api/accounts/2fa/status/
```

**Response (200 OK):**
```json
{
  "enabled": true,
  "devices": [
    {
      "id": 1,
      "name": "iPhone 14",
      "confirmed": true,
      "qr_code": null,
      "created_at": "2025-08-01T19:30:00Z"
    }
  ],
  "settings": {
    "id": 1,
    "recovery_email": "recovery@example.com",
    "is_backup_used": false,
    "backup_tokens_count": 8,
    "last_used": "2025-08-01T20:15:00Z",
    "created_at": "2025-08-01T19:30:00Z"
  }
}
```

### Setup 2FA

Start 2FA setup process.

```http
POST /api/accounts/2fa/setup/
```

**Request Body:**
```json
{
  "device_name": "iPhone 14 Pro"
}
```

**Response (200 OK):**
```json
{
  "message": "2FA-Setup gestartet.",
  "device": {
    "id": 2,
    "name": "iPhone 14 Pro",
    "confirmed": false,
    "qr_code": "otpauth://totp/JuraModular:john.doe?secret=JBSWY3DPEHPK3PXP&issuer=JuraModular",
    "created_at": "2025-08-01T20:30:00Z"
  },
  "secret_key": "JBSWY3DPEHPK3PXP",
  "qr_code_url": "otpauth://totp/JuraModular:john.doe?secret=JBSWY3DPEHPK3PXP&issuer=JuraModular"
}
```

### Verify 2FA Setup

Verify 2FA setup with TOTP token.

```http
POST /api/accounts/2fa/verify/
```

**Request Body:**
```json
{
  "token": "123456"
}
```

**Response (200 OK):**
```json
{
  "message": "2FA erfolgreich aktiviert.",
  "backup_tokens": [
    "a1b2c3d4",
    "e5f6g7h8",
    "i9j0k1l2",
    "m3n4o5p6",
    "q7r8s9t0",
    "u1v2w3x4",
    "y5z6a7b8",
    "c9d0e1f2",
    "g3h4i5j6",
    "k7l8m9n0"
  ]
}
```

**Error Response:**
```json
{
  "error": "Ungültiger Token."
}
```

### Disable 2FA

Disable two-factor authentication.

```http
POST /api/accounts/2fa/disable/
```

**Response (200 OK):**
```json
{
  "message": "2FA wurde deaktiviert."
}
```

## 📊 Error Codes & Status Codes

### HTTP Status Codes

| Code | Description | Usage |
|------|-------------|--------|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | Successful POST |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Validation errors |
| `401` | Unauthorized | Authentication required |
| `403` | Forbidden | Permission denied |
| `404` | Not Found | Resource doesn't exist |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Server Error | Internal server error |

### Error Response Format

```json
{
  "error": "Error message",
  "details": {
    "field_name": ["Field-specific error message"]
  },
  "code": "ERROR_CODE"
}
```

### Common Error Codes

| Code | Description |
|------|-------------|
| `INVALID_CREDENTIALS` | Login credentials are incorrect |
| `ACCOUNT_DISABLED` | User account is disabled |
| `ACCOUNT_NOT_VERIFIED` | Email verification required |
| `2FA_REQUIRED` | Two-factor authentication required |
| `INVALID_2FA_TOKEN` | 2FA token is invalid or expired |
| `PERMISSION_DENIED` | Insufficient permissions |
| `VALIDATION_ERROR` | Request data validation failed |
| `RATE_LIMIT_EXCEEDED` | Too many requests |

## 🔗 Rate Limiting

API endpoints are rate-limited to prevent abuse:

| Endpoint Pattern | Limit | Window |
|------------------|-------|---------|
| `/api/accounts/login/` | 5 requests | per minute |
| `/api/accounts/register/` | 3 requests | per minute |
| `/api/accounts/2fa/*` | 10 requests | per minute |
| Other endpoints | 100 requests | per minute |

Rate limit headers are included in responses:
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1691781600
```

## 🧪 Testing Examples

### cURL Examples

```bash
# Login
curl -X POST http://localhost:8000/api/accounts/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "admin", "password": "password"}'

# Get profile with token
curl -X GET http://localhost:8000/api/accounts/profile/ \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1Q..."

# Update profile
curl -X PATCH http://localhost:8000/api/accounts/profile/ \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1Q..." \
  -H "Content-Type: application/json" \
  -d '{"phone_number": "+49987654321"}'
```

### JavaScript/Fetch Examples

```javascript
// Login
const response = await fetch('/api/accounts/login/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    username: 'admin',
    password: 'password'
  })
});

const data = await response.json();
const accessToken = data.access;

// Authenticated request
const profileResponse = await fetch('/api/accounts/profile/', {
  headers: {
    'Authorization': `Bearer ${accessToken}`
  }
});

const profile = await profileResponse.json();
```

### Python/Requests Examples

```python
import requests

# Login
login_response = requests.post('http://localhost:8000/api/accounts/login/', 
    json={'username': 'admin', 'password': 'password'})
    
tokens = login_response.json()
access_token = tokens['access']

# Authenticated request
headers = {'Authorization': f'Bearer {access_token}'}
profile_response = requests.get('http://localhost:8000/api/accounts/profile/', 
    headers=headers)
    
profile = profile_response.json()
```

---

This API documentation provides comprehensive coverage of all authentication and user management endpoints in the Jura Modular system. 🔐
