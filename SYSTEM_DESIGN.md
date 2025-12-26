# System Design Documentation

Technical architecture and design documentation for the Employee Leave Tracker system.

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [System Components](#system-components)
- [Data Models](#data-models)
- [Database Schema](#database-schema)
- [API Design](#api-design)
- [Security Architecture](#security-architecture)
- [Integration Points](#integration-points)
- [Scalability and Performance](#scalability-and-performance)
- [Monitoring and Logging](#monitoring-and-logging)

## Architecture Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Web Browser  │  │ Mobile App   │  │ API Clients  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Load Balancer (Nginx)                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    Application Layer                         │
│  ┌──────────────────────────────────────────────────────┐   │
│  │            Web Application Servers                   │   │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │   │
│  │  │  Server 1  │  │  Server 2  │  │  Server N  │     │   │
│  │  └────────────┘  └────────────┘  └────────────┘     │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Business Logic Layer                    │   │
│  │  • Authentication & Authorization                    │   │
│  │  • Leave Request Processing                          │   │
│  │  • Approval Workflow Engine                          │   │
│  │  • Balance Calculation                               │   │
│  │  • Notification Service                              │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                        Data Layer                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Primary    │  │   Read       │  │    Cache     │      │
│  │   Database   │─▶│   Replica    │  │    (Redis)   │      │
│  │ (PostgreSQL) │  │ (PostgreSQL) │  └──────────────┘      │
│  └──────────────┘  └──────────────┘                         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   External Services                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Email       │  │  Calendar    │  │   Storage    │      │
│  │  Service     │  │  Integration │  │   (S3/Blob)  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Principles

1. **Separation of Concerns**: Clear boundaries between presentation, business logic, and data layers
2. **Scalability**: Horizontal scaling capability for application servers
3. **Reliability**: Database replication and failover mechanisms
4. **Security**: Defense in depth with multiple security layers
5. **Maintainability**: Modular design for easy updates and maintenance
6. **Performance**: Caching strategies and optimized queries

## System Components

### 1. Frontend Application

**Technology Stack**:
- Framework: React.js / Vue.js / Angular
- State Management: Redux / Vuex / NgRx
- UI Library: Material-UI / Ant Design / Bootstrap
- Build Tool: Webpack / Vite
- Package Manager: npm / yarn

**Key Features**:
- Single Page Application (SPA)
- Responsive design for mobile and desktop
- Progressive Web App (PWA) capabilities
- Offline support for viewing data
- Real-time updates via WebSockets

**Component Structure**:
```
src/
├── components/
│   ├── common/          # Reusable components
│   ├── dashboard/       # Dashboard components
│   ├── leave-request/   # Leave request forms
│   ├── calendar/        # Calendar views
│   └── admin/           # Admin panels
├── services/
│   ├── api.js           # API client
│   ├── auth.js          # Authentication
│   └── websocket.js     # Real-time updates
├── store/               # State management
├── utils/               # Utility functions
└── App.js               # Main application
```

### 2. Backend Application

**Technology Stack**:
- Runtime: Node.js / Python (Django/Flask) / Java (Spring Boot)
- Framework: Express.js / FastAPI / Spring MVC
- ORM: Sequelize / SQLAlchemy / Hibernate
- Authentication: JWT / Passport.js / OAuth2
- API: RESTful / GraphQL

**Service Architecture**:
```
backend/
├── controllers/         # Request handlers
│   ├── auth.js
│   ├── leave.js
│   ├── user.js
│   └── admin.js
├── services/            # Business logic
│   ├── leaveService.js
│   ├── approvalService.js
│   ├── balanceService.js
│   └── notificationService.js
├── models/              # Data models
│   ├── User.js
│   ├── LeaveRequest.js
│   ├── LeaveBalance.js
│   └── LeaveType.js
├── middleware/          # Custom middleware
│   ├── auth.js
│   ├── validation.js
│   └── errorHandler.js
├── routes/              # API routes
├── config/              # Configuration
└── utils/               # Utilities
```

### 3. Database

**Primary Database**: PostgreSQL
- ACID compliance for data integrity
- Advanced indexing capabilities
- JSON support for flexible data
- Robust backup and recovery

**Caching Layer**: Redis
- Session storage
- Frequently accessed data
- Rate limiting
- Real-time notifications queue

### 4. Background Services

**Job Queue**: Bull (Redis-based) / Celery (Python)
- Email notifications
- Report generation
- Data cleanup
- Balance calculations
- Scheduled tasks

**Scheduler**: Cron jobs / Node-cron
- Daily balance accruals
- Reminder notifications
- Report generation
- Data archival

## Data Models

### Core Entities

#### 1. User
```
User
├── id (UUID)
├── email (String, unique)
├── password_hash (String)
├── first_name (String)
├── last_name (String)
├── employee_id (String, unique)
├── department_id (Foreign Key)
├── manager_id (Foreign Key)
├── role (Enum: employee, manager, hr_admin, super_admin)
├── status (Enum: active, inactive)
├── hire_date (Date)
├── created_at (Timestamp)
└── updated_at (Timestamp)
```

#### 2. LeaveRequest
```
LeaveRequest
├── id (UUID)
├── user_id (Foreign Key)
├── leave_type_id (Foreign Key)
├── start_date (Date)
├── end_date (Date)
├── duration (Decimal) # Total days
├── reason (Text)
├── status (Enum: pending, approved, rejected, cancelled)
├── submitted_at (Timestamp)
├── approved_by (Foreign Key, nullable)
├── approved_at (Timestamp, nullable)
├── rejection_reason (Text, nullable)
├── cancellation_reason (Text, nullable)
├── created_at (Timestamp)
└── updated_at (Timestamp)
```

#### 3. LeaveBalance
```
LeaveBalance
├── id (UUID)
├── user_id (Foreign Key)
├── leave_type_id (Foreign Key)
├── year (Integer)
├── total_allocated (Decimal)
├── used (Decimal)
├── pending (Decimal)
├── available (Decimal) # Calculated: total - used - pending
├── carried_over (Decimal)
└── updated_at (Timestamp)
```

#### 4. LeaveType
```
LeaveType
├── id (UUID)
├── name (String)
├── code (String, unique)
├── description (Text)
├── annual_allocation (Decimal)
├── requires_documentation (Boolean)
├── allow_half_day (Boolean)
├── carryover_enabled (Boolean)
├── max_carryover_days (Decimal)
├── accrual_type (Enum: immediate, monthly, annual)
├── is_active (Boolean)
├── created_at (Timestamp)
└── updated_at (Timestamp)
```

#### 5. ApprovalWorkflow
```
ApprovalWorkflow
├── id (UUID)
├── request_id (Foreign Key)
├── step_number (Integer)
├── approver_id (Foreign Key)
├── status (Enum: pending, approved, rejected)
├── comments (Text)
├── acted_at (Timestamp, nullable)
└── created_at (Timestamp)
```

#### 6. Department
```
Department
├── id (UUID)
├── name (String)
├── code (String, unique)
├── manager_id (Foreign Key, nullable)
├── parent_department_id (Foreign Key, nullable)
├── created_at (Timestamp)
└── updated_at (Timestamp)
```

#### 7. Holiday
```
Holiday
├── id (UUID)
├── name (String)
├── date (Date)
├── type (Enum: public, regional, optional)
├── applicable_departments (Array)
├── year (Integer)
├── created_at (Timestamp)
└── updated_at (Timestamp)
```

#### 8. Attachment
```
Attachment
├── id (UUID)
├── request_id (Foreign Key)
├── filename (String)
├── file_path (String)
├── file_size (Integer)
├── mime_type (String)
├── uploaded_by (Foreign Key)
└── uploaded_at (Timestamp)
```

#### 9. Notification
```
Notification
├── id (UUID)
├── user_id (Foreign Key)
├── type (String)
├── title (String)
├── message (Text)
├── related_entity_type (String)
├── related_entity_id (UUID)
├── is_read (Boolean)
├── created_at (Timestamp)
└── read_at (Timestamp, nullable)
```

#### 10. AuditLog
```
AuditLog
├── id (UUID)
├── user_id (Foreign Key)
├── action (String)
├── entity_type (String)
├── entity_id (UUID)
├── old_values (JSON)
├── new_values (JSON)
├── ip_address (String)
├── user_agent (String)
└── created_at (Timestamp)
```

## Database Schema

### Entity Relationship Diagram

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   User      │────────▶│ LeaveRequest │◀────────│ LeaveType   │
│             │         │              │         │             │
│ - id        │         │ - id         │         │ - id        │
│ - email     │         │ - user_id    │         │ - name      │
│ - name      │         │ - type_id    │         │ - code      │
│ - dept_id   │         │ - start_date │         │ - allocation│
│ - manager_id│         │ - end_date   │         └─────────────┘
│ - role      │         │ - status     │
└─────────────┘         └──────────────┘
      │                        │
      │                        │
      ▼                        ▼
┌─────────────┐         ┌──────────────┐
│LeaveBalance │         │ApprovalFlow  │
│             │         │              │
│ - user_id   │         │ - request_id │
│ - type_id   │         │ - approver_id│
│ - allocated │         │ - status     │
│ - used      │         │ - step       │
│ - available │         └──────────────┘
└─────────────┘

┌─────────────┐         ┌──────────────┐
│ Department  │         │   Holiday    │
│             │         │              │
│ - id        │         │ - id         │
│ - name      │         │ - name       │
│ - manager   │         │ - date       │
└─────────────┘         │ - type       │
                        └──────────────┘
```

### Indexes

**Critical Indexes**:
```sql
-- User indexes
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_user_employee_id ON users(employee_id);
CREATE INDEX idx_user_department ON users(department_id);

-- Leave request indexes
CREATE INDEX idx_leave_user ON leave_requests(user_id);
CREATE INDEX idx_leave_status ON leave_requests(status);
CREATE INDEX idx_leave_dates ON leave_requests(start_date, end_date);
CREATE INDEX idx_leave_submitted ON leave_requests(submitted_at);

-- Leave balance indexes
CREATE INDEX idx_balance_user_type ON leave_balances(user_id, leave_type_id);
CREATE INDEX idx_balance_year ON leave_balances(year);

-- Approval workflow indexes
CREATE INDEX idx_approval_request ON approval_workflows(request_id);
CREATE INDEX idx_approval_approver ON approval_workflows(approver_id, status);

-- Notification indexes
CREATE INDEX idx_notif_user_unread ON notifications(user_id, is_read);
CREATE INDEX idx_notif_created ON notifications(created_at);
```

## API Design

### RESTful API Endpoints

#### Authentication
```
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh
POST   /api/auth/forgot-password
POST   /api/auth/reset-password
GET    /api/auth/profile
PUT    /api/auth/profile
```

#### Leave Requests
```
GET    /api/leave-requests              # List all (filtered by role)
POST   /api/leave-requests              # Create new request
GET    /api/leave-requests/:id          # Get specific request
PUT    /api/leave-requests/:id          # Update request
DELETE /api/leave-requests/:id          # Cancel request
POST   /api/leave-requests/:id/approve  # Approve request
POST   /api/leave-requests/:id/reject   # Reject request
```

#### Leave Balances
```
GET    /api/leave-balances              # Get user's balances
GET    /api/leave-balances/:userId      # Get specific user (admin)
POST   /api/leave-balances/adjust       # Adjust balance (admin)
GET    /api/leave-balances/history      # Balance transaction history
```

#### Users
```
GET    /api/users                       # List users (admin)
POST   /api/users                       # Create user (admin)
GET    /api/users/:id                   # Get user details
PUT    /api/users/:id                   # Update user
DELETE /api/users/:id                   # Deactivate user
GET    /api/users/:id/team              # Get team members (manager)
```

#### Leave Types
```
GET    /api/leave-types                 # List all leave types
GET    /api/leave-types/:id             # Get specific type
POST   /api/leave-types                 # Create type (admin)
PUT    /api/leave-types/:id             # Update type (admin)
DELETE /api/leave-types/:id             # Delete type (admin)
```

#### Reports
```
GET    /api/reports/leave-utilization
GET    /api/reports/balance-summary
GET    /api/reports/team-calendar
GET    /api/reports/trends
POST   /api/reports/custom
```

#### Calendar
```
GET    /api/calendar                    # Get calendar events
GET    /api/calendar/team               # Team calendar (manager)
GET    /api/calendar/department         # Department calendar (admin)
```

#### Notifications
```
GET    /api/notifications               # Get user notifications
PUT    /api/notifications/:id/read      # Mark as read
PUT    /api/notifications/read-all      # Mark all as read
DELETE /api/notifications/:id           # Delete notification
```

### API Response Format

**Success Response**:
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Operation successful",
  "timestamp": "2025-12-26T10:30:00Z"
}
```

**Error Response**:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "start_date",
        "message": "Start date must be in the future"
      }
    ]
  },
  "timestamp": "2025-12-26T10:30:00Z"
}
```

### Authentication

**JWT Token Structure**:
```json
{
  "userId": "uuid",
  "email": "user@example.com",
  "role": "employee",
  "iat": 1703592000,
  "exp": 1703678400
}
```

**Authorization Header**:
```
Authorization: Bearer <jwt_token>
```

## Security Architecture

### Authentication & Authorization

1. **JWT-based Authentication**
   - Access tokens (short-lived, 15 minutes)
   - Refresh tokens (long-lived, 7 days)
   - Secure HTTP-only cookies for tokens

2. **Role-Based Access Control (RBAC)**
   - Employee: Basic leave operations
   - Manager: Team management and approvals
   - HR Admin: User and policy management
   - Super Admin: System configuration

3. **Multi-Factor Authentication (Optional)**
   - TOTP-based (Google Authenticator, Authy)
   - SMS-based
   - Email-based

### Data Security

1. **Encryption**
   - Data in transit: TLS 1.3
   - Data at rest: Database encryption
   - Password hashing: bcrypt (cost factor 12)

2. **Input Validation**
   - Server-side validation for all inputs
   - Parameterized queries (SQL injection prevention)
   - XSS protection with content sanitization

3. **API Security**
   - Rate limiting (100 requests/minute per user)
   - CORS configuration
   - API key management for integrations
   - Request signing for sensitive operations

4. **Session Management**
   - Secure session cookies
   - Session timeout (30 minutes inactive)
   - Concurrent session limits
   - Session invalidation on logout

### Compliance

- **GDPR**: Data privacy controls, right to deletion
- **SOC 2**: Security and availability controls
- **ISO 27001**: Information security management
- **Data Retention**: Configurable retention policies

## Integration Points

### Email Service
- **Provider**: SendGrid / AWS SES / Mailgun
- **Purpose**: Transactional emails and notifications
- **Templates**: Leave request confirmations, approvals, reminders

### Calendar Integration
- **Google Calendar API**: Sync approved leave to calendar
- **Microsoft Outlook API**: Sync for Office 365 users
- **CalDAV**: Standard calendar protocol support

### Directory Services
- **LDAP/Active Directory**: Enterprise user authentication
- **Azure AD**: Cloud directory integration
- **SAML 2.0**: Single Sign-On integration

### File Storage
- **AWS S3**: Document storage (attachments)
- **Azure Blob Storage**: Alternative cloud storage
- **Local Storage**: On-premise file storage

### Monitoring & Analytics
- **Application Performance**: New Relic / Datadog
- **Error Tracking**: Sentry / Rollbar
- **Analytics**: Google Analytics / Mixpanel
- **Logging**: ELK Stack / CloudWatch

## Scalability and Performance

### Horizontal Scaling
- Load balancer distributes traffic across multiple app servers
- Stateless application design
- Session data in Redis (shared across servers)

### Database Optimization
- Connection pooling
- Read replicas for reporting queries
- Query optimization and indexing
- Database sharding (for very large deployments)

### Caching Strategy
- **L1 Cache**: Application-level (in-memory)
- **L2 Cache**: Redis (shared cache)
- **CDN**: Static assets
- **Cache Invalidation**: Event-based invalidation

### Performance Targets
- **API Response Time**: < 200ms (p95)
- **Page Load Time**: < 2 seconds
- **Database Query Time**: < 50ms (p95)
- **Concurrent Users**: 10,000+
- **Uptime**: 99.9%

## Monitoring and Logging

### Application Logs
```
{
  "timestamp": "2025-12-26T10:30:00Z",
  "level": "INFO",
  "service": "leave-service",
  "action": "leave_request_created",
  "userId": "uuid",
  "requestId": "uuid",
  "metadata": { }
}
```

### Metrics
- Request rate and latency
- Error rates
- Database performance
- Cache hit/miss ratio
- Queue depth
- User activity

### Alerts
- High error rate (> 1%)
- Slow response time (> 500ms p95)
- Database connection issues
- High memory/CPU usage
- Failed background jobs
- Security events

### Health Checks
```
GET /health
Response:
{
  "status": "healthy",
  "checks": {
    "database": "healthy",
    "redis": "healthy",
    "email": "healthy"
  },
  "uptime": 86400,
  "version": "1.0.0"
}
```

---

**For API Documentation**: See [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)  
**For Deployment**: See [INSTALLATION.md](./INSTALLATION.md)
