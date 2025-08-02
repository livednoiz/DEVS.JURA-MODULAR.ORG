# 📚 API Reference

Complete API documentation for **Jura Modular** RESTful web services.

## 🎯 API Overview

### Base Information

| Attribute | Value |
|-----------|--------|
| **Base URL** | `https://api.jura-modular.org` (Production)<br>`http://127.0.0.1:8000/api` (Development) |
| **Version** | `v1` |
| **Format** | JSON |
| **Authentication** | JWT Bearer Token |
| **Rate Limiting** | 1000 requests/hour per user |

### Quick Start

```bash
# Get access token
curl -X POST http://127.0.0.1:8000/api/auth/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "user", "password": "pass"}'

# Use token for authenticated requests
curl -X GET http://127.0.0.1:8000/api/accounts/profile/ \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## 🔐 Authentication

### JWT Token Authentication

All API endpoints require authentication using JWT tokens in the Authorization header.

#### Login

```http
POST /api/auth/login/
```

**Request:**
```json
{
  "username": "string",
  "password": "string"
}
```

**Response (200):**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "user": {
    "id": 1,
    "username": "user",
    "email": "user@example.com",
    "role": "client"
  }
}
```

#### Token Refresh

```http
POST /api/auth/token/refresh/
```

**Request:**
```json
{
  "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

**Response (200):**
```json
{
  "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

#### Logout

```http
POST /api/auth/logout/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
}
```

**Response (200):**
```json
{
  "message": "Successfully logged out"
}
```

## 👥 Accounts Management

### User Registration

```http
POST /api/accounts/register/
```

**Request:**
```json
{
  "username": "string",
  "email": "user@example.com",
  "password": "string",
  "password_confirm": "string",
  "first_name": "string",
  "last_name": "string",
  "phone_number": "+49123456789",
  "role": "client"
}
```

**Response (201):**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "user": {
    "id": 1,
    "username": "string",
    "email": "user@example.com",
    "first_name": "string",
    "last_name": "string",
    "role": "client",
    "is_active": true,
    "date_joined": "2025-01-01T10:00:00Z"
  }
}
```

**Validation Errors (400):**
```json
{
  "username": ["User with this username already exists."],
  "email": ["Enter a valid email address."],
  "password": ["Passwords do not match."]
}
```

### User Profile

#### Get Profile

```http
GET /api/accounts/profile/
Authorization: Bearer TOKEN
```

**Response (200):**
```json
{
  "id": 1,
  "username": "user",
  "email": "user@example.com",
  "first_name": "Max",
  "last_name": "Mustermann",
  "phone_number": "+49123456789",
  "role": "client",
  "two_factor_enabled": false,
  "is_active": true,
  "date_joined": "2025-01-01T10:00:00Z",
  "last_login": "2025-01-15T09:30:00Z"
}
```

#### Update Profile

```http
PATCH /api/accounts/profile/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "first_name": "Updated",
  "last_name": "Name",
  "phone_number": "+49987654321"
}
```

**Response (200):**
```json
{
  "id": 1,
  "username": "user",
  "email": "user@example.com",
  "first_name": "Updated",
  "last_name": "Name",
  "phone_number": "+49987654321",
  "role": "client",
  "two_factor_enabled": false,
  "is_active": true
}
```

### Client Profile Management

#### Create Client Profile

```http
POST /api/accounts/client-profile/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "user_type": "private",
  "phone_number": "+49123456789",
  "address": "Musterstraße 123",
  "city": "München",
  "postal_code": "80331",
  "country": "DE",
  "date_of_birth": "1990-01-01",
  "company_name": null,
  "tax_number": null
}
```

**Response (201):**
```json
{
  "id": 1,
  "user": 1,
  "user_type": "private",
  "phone_number": "+49123456789",
  "address": "Musterstraße 123",
  "city": "München",
  "postal_code": "80331",
  "country": "DE",
  "date_of_birth": "1990-01-01",
  "company_name": null,
  "tax_number": null,
  "created_at": "2025-01-15T10:00:00Z",
  "updated_at": "2025-01-15T10:00:00Z"
}
```

#### Get Client Profile

```http
GET /api/accounts/client-profile/
Authorization: Bearer TOKEN
```

**Response (200):**
```json
{
  "id": 1,
  "user": {
    "id": 1,
    "username": "client",
    "email": "client@example.com",
    "first_name": "Max",
    "last_name": "Mustermann"
  },
  "user_type": "private",
  "phone_number": "+49123456789",
  "address": "Musterstraße 123",
  "city": "München",
  "postal_code": "80331",
  "country": "DE",
  "date_of_birth": "1990-01-01"
}
```

## 🔒 Two-Factor Authentication

### Setup 2FA

```http
POST /api/accounts/2fa/setup/
Authorization: Bearer TOKEN
```

**Response (200):**
```json
{
  "qr_code": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
  "secret_key": "JBSWY3DPEHPK3PXP",
  "backup_tokens": [
    "backup0001", "backup0002", "backup0003", 
    "backup0004", "backup0005", "backup0006",
    "backup0007", "backup0008", "backup0009", "backup0010"
  ],
  "recovery_email": "recovery.user@example.com"
}
```

### Verify 2FA Setup

```http
POST /api/accounts/2fa/verify-setup/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "token": "123456"
}
```

**Response (200):**
```json
{
  "message": "Two-factor authentication enabled successfully",
  "backup_tokens": [
    "backup0001", "backup0002", "backup0003"
  ]
}
```

### 2FA Login

```http
POST /api/accounts/2fa/verify/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "token": "123456"
}
```

**Response (200):**
```json
{
  "message": "Two-factor authentication verified",
  "tokens": {
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
    "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
  }
}
```

### Disable 2FA

```http
POST /api/accounts/2fa/disable/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "password": "current_password"
}
```

**Response (200):**
```json
{
  "message": "Two-factor authentication disabled successfully"
}
```

### Recovery with Backup Token

```http
POST /api/accounts/2fa/recover/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "backup_token": "backup0001"
}
```

**Response (200):**
```json
{
  "message": "Recovery successful",
  "tokens": {
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
    "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."
  },
  "remaining_tokens": 9
}
```

## 👨‍⚖️ User Management (Admin)

### List Users

```http
GET /api/accounts/users/
Authorization: Bearer TOKEN
```

**Query Parameters:**
- `role`: Filter by user role (`admin`, `lawyer`, `assistant`, `client`, `property_manager`)
- `is_active`: Filter by active status (`true`, `false`)
- `search`: Search in username, email, first_name, last_name
- `ordering`: Sort by field (`username`, `email`, `date_joined`, `-date_joined`)
- `page`: Page number for pagination
- `page_size`: Items per page (max 100)

**Response (200):**
```json
{
  "count": 150,
  "next": "http://127.0.0.1:8000/api/accounts/users/?page=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "username": "user1",
      "email": "user1@example.com",
      "first_name": "Max",
      "last_name": "Mustermann",
      "role": "client",
      "is_active": true,
      "date_joined": "2025-01-01T10:00:00Z",
      "last_login": "2025-01-15T09:30:00Z",
      "two_factor_enabled": false
    }
  ]
}
```

### Get User Details

```http
GET /api/accounts/users/{id}/
Authorization: Bearer TOKEN
```

**Response (200):**
```json
{
  "id": 1,
  "username": "user1",
  "email": "user1@example.com",
  "first_name": "Max",
  "last_name": "Mustermann",
  "phone_number": "+49123456789",
  "role": "client",
  "is_active": true,
  "is_staff": false,
  "date_joined": "2025-01-01T10:00:00Z",
  "last_login": "2025-01-15T09:30:00Z",
  "two_factor_enabled": false,
  "client_profile": {
    "user_type": "private",
    "address": "Musterstraße 123",
    "city": "München",
    "postal_code": "80331"
  }
}
```

### Create User (Admin)

```http
POST /api/accounts/users/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "TempPassword123!",
  "first_name": "New",
  "last_name": "User",
  "role": "lawyer",
  "phone_number": "+49123456789",
  "is_active": true
}
```

**Response (201):**
```json
{
  "id": 2,
  "username": "newuser",
  "email": "newuser@example.com",
  "first_name": "New",
  "last_name": "User",
  "role": "lawyer",
  "is_active": true,
  "date_joined": "2025-01-15T10:00:00Z"
}
```

### Update User (Admin)

```http
PATCH /api/accounts/users/{id}/
Authorization: Bearer TOKEN
```

**Request:**
```json
{
  "is_active": false,
  "role": "assistant"
}
```

**Response (200):**
```json
{
  "id": 1,
  "username": "user1",
  "email": "user1@example.com",
  "first_name": "Max",
  "last_name": "Mustermann",
  "role": "assistant",
  "is_active": false
}
```

### Delete User

```http
DELETE /api/accounts/users/{id}/
Authorization: Bearer TOKEN
```

**Response (204):** No content

## 🔍 Search & Filtering

### Advanced User Search

```http
GET /api/accounts/users/search/
Authorization: Bearer TOKEN
```

**Query Parameters:**
- `q`: Full-text search query
- `role`: Role filter
- `has_2fa`: Filter users with 2FA enabled
- `created_after`: ISO date string
- `created_before`: ISO date string

**Example:**
```http
GET /api/accounts/users/search/?q=max&role=client&has_2fa=true&created_after=2025-01-01
```

**Response (200):**
```json
{
  "count": 5,
  "results": [
    {
      "id": 1,
      "username": "maxmuster",
      "email": "max@example.com",
      "first_name": "Max",
      "last_name": "Mustermann",
      "role": "client",
      "two_factor_enabled": true,
      "relevance_score": 0.95
    }
  ]
}
```

## 📊 Analytics & Reports

### User Statistics

```http
GET /api/accounts/statistics/
Authorization: Bearer TOKEN
```

**Response (200):**
```json
{
  "total_users": 150,
  "active_users": 142,
  "users_by_role": {
    "admin": 2,
    "lawyer": 15,
    "assistant": 8,
    "client": 120,
    "property_manager": 5
  },
  "two_factor_adoption": {
    "enabled": 85,
    "disabled": 65,
    "percentage": 56.7
  },
  "registration_trends": {
    "last_30_days": 12,
    "last_7_days": 3,
    "today": 1
  }
}
```

### User Activity Report

```http
GET /api/accounts/activity-report/
Authorization: Bearer TOKEN
```

**Query Parameters:**
- `date_from`: Start date (YYYY-MM-DD)
- `date_to`: End date (YYYY-MM-DD)
- `user_id`: Specific user ID
- `activity_type`: Filter by activity type

**Response (200):**
```json
{
  "period": {
    "from": "2025-01-01",
    "to": "2025-01-15"
  },
  "activities": [
    {
      "user_id": 1,
      "username": "user1",
      "activity_type": "login",
      "timestamp": "2025-01-15T09:30:00Z",
      "ip_address": "192.168.1.100",
      "user_agent": "Mozilla/5.0..."
    }
  ],
  "summary": {
    "total_activities": 450,
    "unique_users": 85,
    "most_active_user": "user1",
    "peak_activity_hour": 14
  }
}
```

## 🚨 Error Handling

### Standard Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The provided data is invalid",
    "details": {
      "field_name": ["Specific error message"]
    },
    "timestamp": "2025-01-15T10:00:00Z",
    "request_id": "req_abc123"
  }
}
```

### HTTP Status Codes

| Code | Name | Description |
|------|------|-------------|
| `200` | OK | Request successful |
| `201` | Created | Resource created |
| `204` | No Content | Successful deletion |
| `400` | Bad Request | Invalid request data |
| `401` | Unauthorized | Authentication required |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource not found |
| `409` | Conflict | Resource conflict |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Server error |

### Common Error Codes

| Code | Description | Resolution |
|------|-------------|------------|
| `AUTHENTICATION_REQUIRED` | No valid authentication provided | Include Bearer token |
| `INVALID_TOKEN` | Token expired or invalid | Refresh token |
| `PERMISSION_DENIED` | Insufficient permissions | Check user role |
| `VALIDATION_ERROR` | Request data validation failed | Fix data format |
| `RATE_LIMIT_EXCEEDED` | Too many requests | Wait before retrying |
| `RESOURCE_NOT_FOUND` | Requested resource doesn't exist | Check resource ID |
| `DUPLICATE_RESOURCE` | Resource already exists | Use unique values |

## 🔄 Rate Limiting

### Rate Limit Headers

All responses include rate limiting information:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1642329600
X-RateLimit-Window: 3600
```

### Rate Limits by Endpoint

| Endpoint Pattern | Limit | Window |
|------------------|-------|--------|
| `/api/auth/login/` | 5 attempts | 15 minutes |
| `/api/accounts/register/` | 3 attempts | 1 hour |
| `/api/accounts/2fa/verify/` | 5 attempts | 15 minutes |
| `/api/accounts/*` | 1000 requests | 1 hour |
| `/api/cases/*` | 500 requests | 1 hour |

## 📖 API Examples

### Complete User Registration Flow

```javascript
// 1. Register new user
const registerResponse = await fetch('/api/accounts/register/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    username: 'newclient',
    email: 'client@example.com',
    password: 'StrongPass123!',
    password_confirm: 'StrongPass123!',
    first_name: 'Max',
    last_name: 'Mustermann',
    role: 'client'
  })
});

const { access_token, user } = await registerResponse.json();

// 2. Setup 2FA
const setup2faResponse = await fetch('/api/accounts/2fa/setup/', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  }
});

const { qr_code, backup_tokens } = await setup2faResponse.json();

// 3. Create client profile
const profileResponse = await fetch('/api/accounts/client-profile/', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    user_type: 'private',
    address: 'Musterstraße 123',
    city: 'München',
    postal_code: '80331',
    country: 'DE',
    date_of_birth: '1990-01-01'
  })
});
```

### Admin User Management

```python
import requests

class JuraModularAPI:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.headers = {
            'Authorization': f'Bearer {token}',
            'Content-Type': 'application/json'
        }
    
    def get_users(self, role=None, page=1, page_size=20):
        """Get paginated list of users."""
        params = {'page': page, 'page_size': page_size}
        if role:
            params['role'] = role
        
        response = requests.get(
            f'{self.base_url}/accounts/users/',
            headers=self.headers,
            params=params
        )
        return response.json()
    
    def create_user(self, user_data):
        """Create new user."""
        response = requests.post(
            f'{self.base_url}/accounts/users/',
            headers=self.headers,
            json=user_data
        )
        return response.json()
    
    def deactivate_user(self, user_id):
        """Deactivate user account."""
        response = requests.patch(
            f'{self.base_url}/accounts/users/{user_id}/',
            headers=self.headers,
            json={'is_active': False}
        )
        return response.json()

# Usage
api = JuraModularAPI('http://127.0.0.1:8000/api', 'your_token_here')

# Get all lawyers
lawyers = api.get_users(role='lawyer')

# Create new assistant
new_user = api.create_user({
    'username': 'assistant1',
    'email': 'assistant@example.com',
    'password': 'TempPass123!',
    'first_name': 'Anna',
    'last_name': 'Schmidt',
    'role': 'assistant'
})
```

---

Diese API-Dokumentation bietet eine vollständige Referenz für alle **Jura Modular** Endpunkte! 📚🚀
