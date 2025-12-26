# Installation Guide

This guide provides comprehensive instructions for installing and configuring the Employee Leave Tracker system.

## Table of Contents

- [System Requirements](#system-requirements)
- [Installation Methods](#installation-methods)
- [Configuration](#configuration)
- [Database Setup](#database-setup)
- [First Time Setup](#first-time-setup)
- [Troubleshooting](#troubleshooting)

## System Requirements

### Server Requirements

- **Operating System**: Linux (Ubuntu 20.04+), Windows Server 2019+, or macOS 10.15+
- **Web Server**: Apache 2.4+ or Nginx 1.18+
- **Database**: PostgreSQL 12+ or MySQL 8.0+
- **Runtime**: Node.js 16+ or Python 3.8+
- **Memory**: 4GB RAM minimum, 8GB recommended
- **Storage**: 10GB minimum free space

### Client Requirements

- **Browser**: Chrome 90+, Firefox 88+, Safari 14+, or Edge 90+
- **JavaScript**: Enabled
- **Cookies**: Enabled

## Installation Methods

### Method 1: Docker Installation (Recommended)

Docker installation is the quickest way to get started.

```bash
# Clone the repository
git clone https://github.com/your-org/employee-leave-tracker.git
cd employee-leave-tracker

# Build and run with Docker Compose
docker-compose up -d

# Access the application
# Navigate to http://localhost:3000
```

### Method 2: Manual Installation

#### Step 1: Clone the Repository

```bash
git clone https://github.com/your-org/employee-leave-tracker.git
cd employee-leave-tracker
```

#### Step 2: Install Dependencies

**For Node.js version:**
```bash
npm install
```

**For Python version:**
```bash
pip install -r requirements.txt
```

#### Step 3: Environment Configuration

Create a `.env` file in the root directory:

```env
# Application Settings
APP_NAME=Employee Leave Tracker
APP_ENV=production
APP_PORT=3000
APP_URL=http://localhost:3000

# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=leave_tracker
DB_USER=your_username
DB_PASSWORD=your_secure_password

# Email Configuration
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@domain.com
SMTP_PASSWORD=your_email_password
EMAIL_FROM=noreply@domain.com

# Authentication
JWT_SECRET=your_very_secure_secret_key
SESSION_SECRET=your_session_secret
TOKEN_EXPIRY=24h

# Feature Flags
ENABLE_EMAIL_NOTIFICATIONS=true
ENABLE_SMS_NOTIFICATIONS=false
ENABLE_CALENDAR_SYNC=true
```

#### Step 4: Database Setup

**PostgreSQL:**
```bash
# Create database
createdb leave_tracker

# Run migrations
npm run migrate
# or
python manage.py migrate
```

**MySQL:**
```bash
# Create database
mysql -u root -p
CREATE DATABASE leave_tracker CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
exit;

# Run migrations
npm run migrate
```

#### Step 5: Initialize the Application

```bash
# Seed initial data
npm run seed

# Create admin user
npm run create-admin

# or for Python
python manage.py createsuperuser
```

#### Step 6: Start the Application

**Development Mode:**
```bash
npm run dev
# or
python manage.py runserver
```

**Production Mode:**
```bash
npm run build
npm start
# or
gunicorn app:app --bind 0.0.0.0:8000
```

### Method 3: Cloud Deployment

#### Heroku

```bash
# Login to Heroku
heroku login

# Create new app
heroku create employee-leave-tracker

# Add database
heroku addons:create heroku-postgresql:hobby-dev

# Set environment variables
heroku config:set JWT_SECRET=your_secret
heroku config:set SESSION_SECRET=your_session_secret

# Deploy
git push heroku main

# Run migrations
heroku run npm run migrate
```

#### AWS

For detailed AWS deployment instructions, refer to AWS documentation for deploying Node.js/Python applications on:
- AWS Elastic Beanstalk
- AWS ECS (Elastic Container Service)
- AWS EC2 with Auto Scaling

#### Azure

For detailed Azure deployment instructions, refer to Azure documentation for deploying Node.js/Python applications on:
- Azure App Service
- Azure Container Instances
- Azure Kubernetes Service (AKS)

## Configuration

### Email Configuration

Configure SMTP settings for email notifications:

1. Update `.env` with your SMTP credentials
2. Test email configuration:
   ```bash
   npm run test-email
   ```

### Calendar Integration

To enable calendar synchronization:

1. Set up OAuth credentials for Google Calendar or Outlook
2. Update `.env` with OAuth client ID and secret
3. Enable calendar sync in admin settings

### Authentication Methods

The system supports multiple authentication methods:

- **Local Authentication**: Username and password
- **LDAP/Active Directory**: Enterprise directory integration
- **OAuth2.0**: Google, Microsoft, GitHub
- **SAML**: Enterprise SSO integration

Configure your preferred method in `config/auth.js` or `config/auth.py`

## First Time Setup

### 1. Access the Admin Panel

Navigate to: `http://localhost:3000/admin`

Default credentials:
- **Username**: admin
- **Password**: admin123

> ⚠️ **Important**: Change the default password immediately after first login!

### 2. Configure Leave Types

1. Go to **Settings** > **Leave Types**
2. Add or modify leave types:
   - Vacation/Annual Leave
   - Sick Leave
   - Personal Leave
   - Maternity/Paternity Leave
   - Unpaid Leave

### 3. Set Leave Policies

1. Go to **Settings** > **Leave Policies**
2. Configure:
   - Annual leave allocation
   - Leave accrual rules
   - Carryover policies
   - Approval workflows

### 4. Import Users

**CSV Import:**
1. Prepare CSV file with format: `email,first_name,last_name,department,role`
2. Go to **Users** > **Import**
3. Upload CSV file
4. Map columns and import

**Manual Addition:**
1. Go to **Users** > **Add User**
2. Fill in user details
3. Assign role and department
4. Save

### 5. Configure Approval Workflows

1. Go to **Settings** > **Workflows**
2. Define approval chains:
   - Direct manager approval
   - Department head approval
   - HR approval
3. Set conditions for each workflow

## Troubleshooting

### Common Issues

#### Issue: Application won't start

**Solution:**
```bash
# Check if port is already in use
lsof -i :3000

# Kill the process or use a different port
export APP_PORT=3001
```

#### Issue: Database connection fails

**Solution:**
1. Verify database is running:
   ```bash
   # PostgreSQL
   sudo systemctl status postgresql
   
   # MySQL
   sudo systemctl status mysql
   ```
2. Check credentials in `.env`
3. Verify network connectivity

#### Issue: Email notifications not working

**Solution:**
1. Test SMTP configuration:
   ```bash
   npm run test-email
   ```
2. Check firewall rules for SMTP ports
3. Verify SMTP credentials
4. Check spam folder

#### Issue: Users can't log in

**Solution:**
1. Verify user exists in database
2. Reset password:
   ```bash
   npm run reset-password user@example.com
   ```
3. Check authentication configuration
4. Review error logs

### Logs

Access application logs:

```bash
# View real-time logs
tail -f logs/application.log

# View error logs
tail -f logs/error.log

# Search logs
grep "ERROR" logs/application.log
```

### Health Check

Verify system health:

```bash
# Check application status
curl http://localhost:3000/health

# Check database connection
npm run check-db

# Run system diagnostics
npm run diagnostics
```

## Upgrade Guide

### Backup Before Upgrade

```bash
# Backup database
pg_dump leave_tracker > backup_$(date +%Y%m%d).sql

# Backup application files
tar -czf app_backup_$(date +%Y%m%d).tar.gz .
```

### Upgrade Steps

```bash
# Pull latest code
git pull origin main

# Update dependencies
npm install

# Run migrations
npm run migrate

# Restart application
npm restart
```

## Security Considerations

1. **Change Default Passwords**: Update all default credentials
2. **Enable HTTPS**: Use SSL/TLS certificates
3. **Configure Firewall**: Restrict access to necessary ports
4. **Regular Updates**: Keep dependencies up to date
5. **Backup Strategy**: Implement regular automated backups
6. **Access Control**: Use principle of least privilege
7. **Audit Logs**: Enable and monitor audit logs

## Support

If you encounter issues not covered in this guide:

1. Check the FAQ section in the [USER_GUIDE.md](./USER_GUIDE.md)
2. Search existing [GitHub Issues](https://github.com/your-org/employee-leave-tracker/issues)
3. Contact support: support@employeeleavetracker.com

---

**Next Steps**: After installation, read the [USER_GUIDE.md](./USER_GUIDE.md) to learn how to use the system.
