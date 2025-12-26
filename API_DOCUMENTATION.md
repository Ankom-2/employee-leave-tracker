# API Documentation

Complete API reference for the Employee Leave Tracker system.

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [API Endpoints](#api-endpoints)
- [Request/Response Formats](#requestresponse-formats)
- [Error Codes](#error-codes)
- [Rate Limiting](#rate-limiting)
- [Webhooks](#webhooks)
- [Code Examples](#code-examples)

## Overview

### Base URL

```
Production: https://api.employeeleavetracker.com/v1
Staging: https://staging-api.employeeleavetracker.com/v1
Development: http://localhost:3000/api/v1
```

### API Version

Current version: **v1**

### Content Type

All requests and responses use JSON:
```
Content-Type: application/json
```

### Date Format

All dates follow ISO 8601 format:
```
Date: YYYY-MM-DD
DateTime: YYYY-MM-DDTHH:mm:ss.sssZ
```

## Authentication

### Login

Obtain access and refresh tokens.

**Endpoint**: `POST /auth/login`

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
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 900,
    "user": {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "employee"
    }
  }
}
```

### Refresh Token

Get a new access token using refresh token.

**Endpoint**: `POST /auth/refresh`

**Request Body**:
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 900
  }
}
```

### Logout

Invalidate current session.

**Endpoint**: `POST /auth/logout`

**Headers**: `Authorization: Bearer <accessToken>`

**Response** (200 OK):
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

### Using Access Tokens

Include the access token in the Authorization header for all authenticated requests:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

## API Endpoints

### Leave Requests

#### List Leave Requests

Get a list of leave requests (filtered based on user role).

**Endpoint**: `GET /leave-requests`

**Query Parameters**:
- `status` (optional): Filter by status (pending, approved, rejected, cancelled)
- `startDate` (optional): Filter by start date (YYYY-MM-DD)
- `endDate` (optional): Filter by end date (YYYY-MM-DD)
- `leaveType` (optional): Filter by leave type ID
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 20, max: 100)

**Example Request**:
```
GET /leave-requests?status=pending&page=1&limit=10
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "requests": [
      {
        "id": "123e4567-e89b-12d3-a456-426614174000",
        "userId": "user-uuid",
        "user": {
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@example.com"
        },
        "leaveType": {
          "id": "type-uuid",
          "name": "Annual Leave",
          "code": "ANNUAL"
        },
        "startDate": "2025-01-15",
        "endDate": "2025-01-19",
        "duration": 5,
        "reason": "Family vacation",
        "status": "pending",
        "submittedAt": "2025-01-01T10:30:00Z",
        "approvedBy": null,
        "approvedAt": null
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 45,
      "totalPages": 5
    }
  }
}
```

#### Create Leave Request

Submit a new leave request.

**Endpoint**: `POST /leave-requests`

**Request Body**:
```json
{
  "leaveTypeId": "type-uuid",
  "startDate": "2025-02-10",
  "endDate": "2025-02-14",
  "duration": 5,
  "reason": "Personal matters",
  "isHalfDay": false,
  "halfDayPeriod": null
}
```

**Half-Day Request**:
```json
{
  "leaveTypeId": "type-uuid",
  "startDate": "2025-02-10",
  "endDate": "2025-02-10",
  "duration": 0.5,
  "reason": "Medical appointment",
  "isHalfDay": true,
  "halfDayPeriod": "morning"
}
```

**Response** (201 Created):
```json
{
  "success": true,
  "data": {
    "id": "new-request-uuid",
    "userId": "user-uuid",
    "leaveTypeId": "type-uuid",
    "startDate": "2025-02-10",
    "endDate": "2025-02-14",
    "duration": 5,
    "status": "pending",
    "submittedAt": "2025-01-20T14:30:00Z"
  },
  "message": "Leave request submitted successfully"
}
```

#### Get Leave Request

Get details of a specific leave request.

**Endpoint**: `GET /leave-requests/:id`

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "request-uuid",
    "userId": "user-uuid",
    "user": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@example.com",
      "department": "Engineering"
    },
    "leaveType": {
      "id": "type-uuid",
      "name": "Annual Leave",
      "code": "ANNUAL"
    },
    "startDate": "2025-02-10",
    "endDate": "2025-02-14",
    "duration": 5,
    "reason": "Personal matters",
    "status": "approved",
    "submittedAt": "2025-01-20T14:30:00Z",
    "approvedBy": {
      "id": "manager-uuid",
      "firstName": "Jane",
      "lastName": "Smith"
    },
    "approvedAt": "2025-01-21T09:15:00Z",
    "approvalComments": "Approved - have a good time",
    "attachments": [
      {
        "id": "attachment-uuid",
        "filename": "document.pdf",
        "fileSize": 245678,
        "uploadedAt": "2025-01-20T14:32:00Z"
      }
    ]
  }
}
```

#### Update Leave Request

Update a pending leave request.

**Endpoint**: `PUT /leave-requests/:id`

**Request Body**:
```json
{
  "startDate": "2025-02-11",
  "endDate": "2025-02-15",
  "duration": 5,
  "reason": "Updated reason"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "request-uuid",
    "startDate": "2025-02-11",
    "endDate": "2025-02-15",
    "updatedAt": "2025-01-21T10:00:00Z"
  },
  "message": "Leave request updated successfully"
}
```

#### Cancel Leave Request

Cancel a pending or approved leave request.

**Endpoint**: `DELETE /leave-requests/:id`

**Request Body**:
```json
{
  "reason": "Plans changed"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "message": "Leave request cancelled successfully"
}
```

#### Approve Leave Request

Approve a pending leave request (Manager/HR only).

**Endpoint**: `POST /leave-requests/:id/approve`

**Request Body**:
```json
{
  "comments": "Approved - enjoy your time off"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "request-uuid",
    "status": "approved",
    "approvedBy": "manager-uuid",
    "approvedAt": "2025-01-21T11:30:00Z"
  },
  "message": "Leave request approved successfully"
}
```

#### Reject Leave Request

Reject a pending leave request (Manager/HR only).

**Endpoint**: `POST /leave-requests/:id/reject`

**Request Body**:
```json
{
  "reason": "Insufficient team coverage during this period"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "request-uuid",
    "status": "rejected",
    "rejectedBy": "manager-uuid",
    "rejectedAt": "2025-01-21T11:30:00Z",
    "rejectionReason": "Insufficient team coverage during this period"
  },
  "message": "Leave request rejected"
}
```

### Leave Balances

#### Get User Leave Balances

Get current leave balances for authenticated user.

**Endpoint**: `GET /leave-balances`

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "year": 2025,
    "balances": [
      {
        "leaveType": {
          "id": "annual-uuid",
          "name": "Annual Leave",
          "code": "ANNUAL"
        },
        "totalAllocated": 20,
        "used": 8,
        "pending": 3,
        "available": 9,
        "carriedOver": 2
      },
      {
        "leaveType": {
          "id": "sick-uuid",
          "name": "Sick Leave",
          "code": "SICK"
        },
        "totalAllocated": 12,
        "used": 2,
        "pending": 0,
        "available": 10,
        "carriedOver": 0
      }
    ]
  }
}
```

#### Get Specific User Balance

Get leave balances for a specific user (Manager/HR only).

**Endpoint**: `GET /leave-balances/:userId`

**Query Parameters**:
- `year` (optional): Specific year (default: current year)

**Response**: Same format as user balances

#### Adjust Leave Balance

Manually adjust a user's leave balance (HR only).

**Endpoint**: `POST /leave-balances/adjust`

**Request Body**:
```json
{
  "userId": "user-uuid",
  "leaveTypeId": "type-uuid",
  "adjustment": 5,
  "reason": "Bonus days for tenure",
  "effectiveDate": "2025-01-01"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "userId": "user-uuid",
    "leaveTypeId": "type-uuid",
    "newBalance": 25,
    "adjustment": 5
  },
  "message": "Balance adjusted successfully"
}
```

#### Get Balance History

Get transaction history for leave balance.

**Endpoint**: `GET /leave-balances/history`

**Query Parameters**:
- `leaveTypeId` (optional): Filter by leave type
- `year` (optional): Filter by year

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": "transaction-uuid",
        "type": "accrual",
        "amount": 1.67,
        "balance": 20,
        "description": "Monthly accrual",
        "date": "2025-01-01T00:00:00Z"
      },
      {
        "id": "transaction-uuid",
        "type": "deduction",
        "amount": -5,
        "balance": 15,
        "description": "Leave taken (Jan 15-19)",
        "date": "2025-01-15T00:00:00Z"
      }
    ]
  }
}
```

### Users

#### Get Current User Profile

Get profile of authenticated user.

**Endpoint**: `GET /users/me`

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "email": "john.doe@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "employeeId": "EMP001",
    "department": {
      "id": "dept-uuid",
      "name": "Engineering"
    },
    "manager": {
      "id": "manager-uuid",
      "firstName": "Jane",
      "lastName": "Smith"
    },
    "role": "employee",
    "hireDate": "2020-01-15",
    "status": "active"
  }
}
```

#### Update User Profile

Update authenticated user's profile.

**Endpoint**: `PUT /users/me`

**Request Body**:
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1234567890",
  "preferences": {
    "emailNotifications": true,
    "smsNotifications": false
  }
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890",
    "updatedAt": "2025-01-21T15:00:00Z"
  },
  "message": "Profile updated successfully"
}
```

#### List Users

List all users (HR/Admin only).

**Endpoint**: `GET /users`

**Query Parameters**:
- `department` (optional): Filter by department ID
- `role` (optional): Filter by role
- `status` (optional): Filter by status (active, inactive)
- `search` (optional): Search by name or email
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "users": [
      {
        "id": "user-uuid",
        "email": "john.doe@example.com",
        "firstName": "John",
        "lastName": "Doe",
        "department": "Engineering",
        "role": "employee",
        "status": "active"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 150,
      "totalPages": 8
    }
  }
}
```

#### Create User

Create a new user (HR/Admin only).

**Endpoint**: `POST /users`

**Request Body**:
```json
{
  "email": "new.user@example.com",
  "firstName": "New",
  "lastName": "User",
  "employeeId": "EMP123",
  "departmentId": "dept-uuid",
  "managerId": "manager-uuid",
  "role": "employee",
  "hireDate": "2025-02-01",
  "leaveEntitlements": {
    "annual": 20,
    "sick": 12,
    "personal": 5
  }
}
```

**Response** (201 Created):
```json
{
  "success": true,
  "data": {
    "id": "new-user-uuid",
    "email": "new.user@example.com",
    "temporaryPassword": "TempPass123!",
    "status": "active"
  },
  "message": "User created successfully. Temporary password sent via email."
}
```

### Leave Types

#### List Leave Types

Get all available leave types.

**Endpoint**: `GET /leave-types`

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "leaveTypes": [
      {
        "id": "annual-uuid",
        "name": "Annual Leave",
        "code": "ANNUAL",
        "description": "Paid vacation time",
        "annualAllocation": 20,
        "requiresDocumentation": false,
        "allowHalfDay": true,
        "carryoverEnabled": true,
        "maxCarryoverDays": 5,
        "isActive": true
      },
      {
        "id": "sick-uuid",
        "name": "Sick Leave",
        "code": "SICK",
        "description": "Leave for illness or medical appointments",
        "annualAllocation": 12,
        "requiresDocumentation": true,
        "allowHalfDay": true,
        "carryoverEnabled": false,
        "maxCarryoverDays": 0,
        "isActive": true
      }
    ]
  }
}
```

#### Create Leave Type

Create a new leave type (Admin only).

**Endpoint**: `POST /leave-types`

**Request Body**:
```json
{
  "name": "Study Leave",
  "code": "STUDY",
  "description": "Leave for educational purposes",
  "annualAllocation": 10,
  "requiresDocumentation": true,
  "allowHalfDay": true,
  "carryoverEnabled": false,
  "accrualType": "annual"
}
```

**Response** (201 Created):
```json
{
  "success": true,
  "data": {
    "id": "study-uuid",
    "name": "Study Leave",
    "code": "STUDY",
    "isActive": true
  },
  "message": "Leave type created successfully"
}
```

### Calendar

#### Get Calendar Events

Get leave events for calendar view.

**Endpoint**: `GET /calendar`

**Query Parameters**:
- `startDate` (required): Start date (YYYY-MM-DD)
- `endDate` (required): End date (YYYY-MM-DD)
- `view` (optional): Calendar view (user, team, department)

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "events": [
      {
        "id": "request-uuid",
        "title": "John Doe - Annual Leave",
        "start": "2025-02-10",
        "end": "2025-02-14",
        "type": "annual",
        "status": "approved",
        "user": {
          "id": "user-uuid",
          "name": "John Doe"
        }
      }
    ]
  }
}
```

### Reports

#### Leave Utilization Report

Generate leave utilization report.

**Endpoint**: `GET /reports/leave-utilization`

**Query Parameters**:
- `startDate` (required): Report start date
- `endDate` (required): Report end date
- `departmentId` (optional): Filter by department
- `format` (optional): Response format (json, csv, pdf)

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "period": {
      "start": "2025-01-01",
      "end": "2025-12-31"
    },
    "summary": {
      "totalEmployees": 100,
      "totalDaysAllocated": 2000,
      "totalDaysUsed": 1200,
      "utilizationRate": 60,
      "averageDaysPerEmployee": 12
    },
    "byLeaveType": [
      {
        "type": "Annual Leave",
        "allocated": 2000,
        "used": 800,
        "utilization": 40
      }
    ],
    "byDepartment": [
      {
        "department": "Engineering",
        "employees": 50,
        "daysUsed": 600,
        "utilization": 60
      }
    ]
  }
}
```

### Notifications

#### Get Notifications

Get user notifications.

**Endpoint**: `GET /notifications`

**Query Parameters**:
- `unreadOnly` (optional): Filter unread notifications (true/false)
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response** (200 OK):
```json
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "notif-uuid",
        "type": "leave_approved",
        "title": "Leave Request Approved",
        "message": "Your leave request from Feb 10-14 has been approved",
        "isRead": false,
        "relatedEntity": {
          "type": "leave_request",
          "id": "request-uuid"
        },
        "createdAt": "2025-01-21T11:30:00Z"
      }
    ],
    "unreadCount": 5
  }
}
```

#### Mark Notification as Read

Mark a notification as read.

**Endpoint**: `PUT /notifications/:id/read`

**Response** (200 OK):
```json
{
  "success": true,
  "message": "Notification marked as read"
}
```

## Request/Response Formats

### Standard Success Response

```json
{
  "success": true,
  "data": { },
  "message": "Optional success message",
  "timestamp": "2025-12-26T10:30:00Z"
}
```

### Pagination Response

```json
{
  "success": true,
  "data": {
    "items": [ ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5,
      "hasNext": true,
      "hasPrevious": false
    }
  }
}
```

## Error Codes

### HTTP Status Codes

- `200 OK`: Successful request
- `201 Created`: Resource created successfully
- `400 Bad Request`: Invalid request data
- `401 Unauthorized`: Authentication required or failed
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `409 Conflict`: Request conflicts with current state
- `422 Unprocessable Entity`: Validation error
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "startDate",
        "message": "Start date must be in the future"
      }
    ]
  },
  "timestamp": "2025-12-26T10:30:00Z"
}
```

### Common Error Codes

- `VALIDATION_ERROR`: Input validation failed
- `AUTHENTICATION_REQUIRED`: No valid authentication token
- `INSUFFICIENT_BALANCE`: Not enough leave balance
- `INVALID_CREDENTIALS`: Login failed
- `TOKEN_EXPIRED`: Access token expired
- `RESOURCE_NOT_FOUND`: Requested resource doesn't exist
- `PERMISSION_DENIED`: Insufficient permissions
- `DUPLICATE_REQUEST`: Request already exists
- `CONFLICT`: Request conflicts with existing data
- `RATE_LIMIT_EXCEEDED`: Too many requests

## Rate Limiting

### Limits

- **Authenticated users**: 100 requests per minute
- **Admin users**: 500 requests per minute
- **Anonymous**: 20 requests per minute (login endpoint)

### Rate Limit Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1703592120
```

### Rate Limit Exceeded Response

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Please try again later.",
    "retryAfter": 60
  }
}
```

## Webhooks

Configure webhooks to receive real-time notifications of events.

### Webhook Events

- `leave_request.created`
- `leave_request.updated`
- `leave_request.approved`
- `leave_request.rejected`
- `leave_request.cancelled`
- `user.created`
- `user.updated`
- `balance.adjusted`

### Webhook Payload

```json
{
  "event": "leave_request.approved",
  "timestamp": "2025-01-21T11:30:00Z",
  "data": {
    "id": "request-uuid",
    "userId": "user-uuid",
    "status": "approved",
    "approvedBy": "manager-uuid"
  }
}
```

### Webhook Security

- Webhooks include `X-Webhook-Signature` header
- Verify signature using your webhook secret:
  ```
  HMAC-SHA256(payload, webhook_secret)
  ```

## Code Examples

### JavaScript (Node.js)

```javascript
const axios = require('axios');

const api = axios.create({
  baseURL: 'https://api.employeeleavetracker.com/v1',
  headers: {
    'Content-Type': 'application/json'
  }
});

// Login
async function login(email, password) {
  const response = await api.post('/auth/login', {
    email,
    password
  });
  
  const { accessToken } = response.data.data;
  api.defaults.headers.common['Authorization'] = `Bearer ${accessToken}`;
  
  return response.data;
}

// Get leave balance
async function getLeaveBalance() {
  const response = await api.get('/leave-balances');
  return response.data.data;
}

// Create leave request
async function createLeaveRequest(data) {
  const response = await api.post('/leave-requests', data);
  return response.data;
}

// Usage
(async () => {
  await login('user@example.com', 'password');
  const balances = await getLeaveBalance();
  console.log('Leave balances:', balances);
  
  const request = await createLeaveRequest({
    leaveTypeId: 'type-uuid',
    startDate: '2025-02-10',
    endDate: '2025-02-14',
    duration: 5,
    reason: 'Vacation'
  });
  console.log('Request created:', request);
})();
```

### Python

```python
import requests

class LeaveTrackerAPI:
    def __init__(self, base_url):
        self.base_url = base_url
        self.session = requests.Session()
        self.session.headers.update({'Content-Type': 'application/json'})
    
    def login(self, email, password):
        response = self.session.post(
            f'{self.base_url}/auth/login',
            json={'email': email, 'password': password}
        )
        data = response.json()
        token = data['data']['accessToken']
        self.session.headers.update({'Authorization': f'Bearer {token}'})
        return data
    
    def get_leave_balance(self):
        response = self.session.get(f'{self.base_url}/leave-balances')
        return response.json()['data']
    
    def create_leave_request(self, request_data):
        response = self.session.post(
            f'{self.base_url}/leave-requests',
            json=request_data
        )
        return response.json()

# Usage
api = LeaveTrackerAPI('https://api.employeeleavetracker.com/v1')
api.login('user@example.com', 'password')

balances = api.get_leave_balance()
print('Leave balances:', balances)

request = api.create_leave_request({
    'leaveTypeId': 'type-uuid',
    'startDate': '2025-02-10',
    'endDate': '2025-02-14',
    'duration': 5,
    'reason': 'Vacation'
})
print('Request created:', request)
```

### cURL

```bash
# Login
curl -X POST https://api.employeeleavetracker.com/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "password"
  }'

# Get leave balance (with token)
curl -X GET https://api.employeeleavetracker.com/v1/leave-balances \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"

# Create leave request
curl -X POST https://api.employeeleavetracker.com/v1/leave-requests \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "leaveTypeId": "type-uuid",
    "startDate": "2025-02-10",
    "endDate": "2025-02-14",
    "duration": 5,
    "reason": "Vacation"
  }'
```

---

**Need Help?**
- Technical Support: api-support@employeeleavetracker.com
- Documentation: See [SYSTEM_DESIGN.md](./SYSTEM_DESIGN.md)
- User Guide: See [USER_GUIDE.md](./USER_GUIDE.md)
