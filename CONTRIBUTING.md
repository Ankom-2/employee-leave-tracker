# Contributing Guidelines

Thank you for your interest in contributing to the Employee Leave Tracker project! This document provides guidelines and instructions for contributing.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Testing Requirements](#testing-requirements)
- [Pull Request Process](#pull-request-process)
- [Issue Guidelines](#issue-guidelines)

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive experience for everyone. We pledge to:

- Be respectful and professional
- Welcome diverse perspectives and experiences
- Accept constructive criticism gracefully
- Focus on what is best for the community
- Show empathy towards other community members

### Unacceptable Behavior

- Harassment, discrimination, or offensive comments
- Personal attacks or trolling
- Publishing others' private information
- Any conduct that could reasonably be considered inappropriate

### Enforcement

Violations of the code of conduct should be reported to the project maintainers. All complaints will be reviewed and investigated promptly and fairly.

## Getting Started

### Prerequisites

Before contributing, ensure you have:

- Git installed and configured
- Node.js 16+ or Python 3.8+ (depending on your contribution area)
- A GitHub account
- Familiarity with the project structure

### Fork and Clone

1. Fork the repository on GitHub
2. Clone your fork locally:
   ```bash
   git clone https://github.com/your-username/employee-leave-tracker.git
   cd employee-leave-tracker
   ```
3. Add the upstream repository:
   ```bash
   git remote add upstream https://github.com/original-org/employee-leave-tracker.git
   ```

## Development Setup

### Backend Setup (Node.js)

```bash
# Install dependencies
npm install

# Copy environment file
cp .env.example .env

# Edit .env with your local settings
nano .env

# Set up database
npm run db:setup

# Start development server
npm run dev
```

### Backend Setup (Python)

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp .env.example .env

# Run migrations
python manage.py migrate

# Start development server
python manage.py runserver
```

### Frontend Setup

```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
npm install

# Start development server
npm start
```

### Database Setup

```bash
# PostgreSQL
createdb leave_tracker_dev

# Run migrations
npm run migrate  # or python manage.py migrate

# Seed test data
npm run seed  # or python manage.py seed
```

## How to Contribute

### Types of Contributions

We welcome various types of contributions:

1. **Bug Fixes**: Fix identified bugs
2. **Features**: Implement new features
3. **Documentation**: Improve or add documentation
4. **Tests**: Add or improve test coverage
5. **Performance**: Optimize existing code
6. **UI/UX**: Enhance user interface and experience
7. **Refactoring**: Improve code quality without changing functionality

### Finding Issues to Work On

- Browse [Issues](https://github.com/org/employee-leave-tracker/issues)
- Look for labels:
  - `good first issue`: Great for newcomers
  - `help wanted`: Issues where we need help
  - `bug`: Bug fixes needed
  - `enhancement`: New feature requests
  - `documentation`: Documentation improvements

### Claiming an Issue

1. Comment on the issue expressing your interest
2. Wait for a maintainer to assign you
3. Ask questions if anything is unclear
4. Start working once assigned

## Coding Standards

### General Principles

- **KISS**: Keep It Simple, Stupid
- **DRY**: Don't Repeat Yourself
- **YAGNI**: You Aren't Gonna Need It
- **SOLID**: Follow SOLID principles for OOP

### JavaScript/TypeScript Style

```javascript
// Use ES6+ features
const getUserName = (user) => `${user.firstName} ${user.lastName}`;

// Destructuring
const { firstName, lastName, email } = user;

// Arrow functions
const calculateLeaveDays = (startDate, endDate) => {
  // Implementation
};

// Async/await for promises
async function fetchLeaveRequests() {
  try {
    const response = await api.get('/leave-requests');
    return response.data;
  } catch (error) {
    handleError(error);
  }
}

// Constants in UPPER_CASE
const MAX_LEAVE_DAYS = 30;
const DEFAULT_PAGE_SIZE = 20;
```

### Python Style (PEP 8)

```python
# Follow PEP 8 naming conventions
class LeaveRequest:
    def __init__(self, user_id, leave_type_id):
        self.user_id = user_id
        self.leave_type_id = leave_type_id
    
    def calculate_duration(self, start_date, end_date):
        """Calculate leave duration excluding weekends and holidays."""
        # Implementation
        pass

# Use type hints
def get_leave_balance(user_id: str, leave_type_id: str) -> dict:
    """Get leave balance for a user and leave type."""
    # Implementation
    return {}

# Constants in UPPER_CASE
MAX_LEAVE_DAYS = 30
DEFAULT_PAGE_SIZE = 20
```

### Code Comments

```javascript
// Good comments explain WHY, not WHAT
// Bad: Increment counter
counter++;

// Good: Increment counter to track retry attempts for rate limiting
counter++;

/**
 * Calculate leave duration excluding weekends and holidays
 * 
 * @param {Date} startDate - Leave start date
 * @param {Date} endDate - Leave end date
 * @param {Array} holidays - List of holiday dates
 * @returns {number} Number of working days
 */
function calculateLeaveDuration(startDate, endDate, holidays) {
  // Implementation
}
```

### Naming Conventions

**Variables and Functions**:
- camelCase for JavaScript: `getUserBalance`, `isApproved`
- snake_case for Python: `get_user_balance`, `is_approved`
- Use descriptive names: `leaveBalance` not `lb`

**Classes**:
- PascalCase: `LeaveRequest`, `ApprovalWorkflow`

**Constants**:
- UPPER_SNAKE_CASE: `MAX_LEAVE_DAYS`, `API_BASE_URL`

**Files**:
- kebab-case for files: `leave-request.js`, `approval-service.py`

### Error Handling

```javascript
// Always handle errors appropriately
try {
  const result = await someAsyncOperation();
  return result;
} catch (error) {
  logger.error('Operation failed', { error, context });
  throw new CustomError('User-friendly message', error);
}
```

## Testing Requirements

### Test Coverage

- Minimum 80% code coverage for new code
- All bug fixes must include a regression test
- All new features must include tests

### Writing Tests

**Unit Tests**:
```javascript
// Jest example
describe('LeaveBalanceService', () => {
  describe('calculateAvailableBalance', () => {
    it('should correctly calculate available balance', () => {
      const balance = calculateAvailableBalance(20, 5, 3);
      expect(balance).toBe(12);
    });
    
    it('should return 0 when balance is negative', () => {
      const balance = calculateAvailableBalance(5, 10, 2);
      expect(balance).toBe(0);
    });
  });
});
```

**Integration Tests**:
```javascript
describe('Leave Request API', () => {
  it('should create a leave request', async () => {
    const response = await request(app)
      .post('/api/leave-requests')
      .send({
        leaveTypeId: 'annual-uuid',
        startDate: '2025-03-10',
        endDate: '2025-03-14',
        duration: 5,
        reason: 'Vacation'
      })
      .expect(201);
    
    expect(response.body.success).toBe(true);
    expect(response.body.data.status).toBe('pending');
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test file
npm test leave-request.test.js

# Run in watch mode
npm test -- --watch
```

### Test Best Practices

1. **Test Behavior, Not Implementation**: Focus on what the code does, not how
2. **One Assertion Per Test**: Keep tests focused and clear
3. **Use Descriptive Test Names**: `it('should reject request when balance is insufficient')`
4. **Arrange-Act-Assert**: Structure tests clearly
5. **Mock External Dependencies**: Use mocks for APIs, databases, etc.
6. **Test Edge Cases**: Include boundary conditions and error scenarios

## Pull Request Process

### Before Submitting

1. **Update from upstream**:
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Run linters**:
   ```bash
   npm run lint
   # Fix any issues
   npm run lint:fix
   ```

3. **Run tests**:
   ```bash
   npm test
   npm run test:coverage
   ```

4. **Update documentation**: If your changes affect user-facing features

5. **Check commit messages**: Follow conventional commits format

### Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples**:
```
feat(leave-request): add half-day leave support

Implements the ability to request half-day leave (morning or afternoon).
Includes UI updates and API changes.

Closes #123

---

fix(balance): correct carryover calculation

Fixed bug where carryover days were not properly added to new year balance.

Fixes #456

---

docs(api): update authentication section

Added examples for JWT token refresh flow.
```

### Creating a Pull Request

1. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

2. **Open a PR** on GitHub with:
   - Clear title following conventional commits format
   - Detailed description of changes
   - Reference to related issues
   - Screenshots for UI changes
   - List of breaking changes (if any)

3. **PR Template**:
   ```markdown
   ## Description
   Brief description of changes
   
   ## Type of Change
   - [ ] Bug fix
   - [ ] New feature
   - [ ] Breaking change
   - [ ] Documentation update
   
   ## Related Issues
   Closes #123
   
   ## Testing
   - [ ] Unit tests added/updated
   - [ ] Integration tests added/updated
   - [ ] Manual testing completed
   
   ## Screenshots (if applicable)
   
   ## Checklist
   - [ ] Code follows project style guidelines
   - [ ] Self-review completed
   - [ ] Comments added for complex code
   - [ ] Documentation updated
   - [ ] No new warnings generated
   - [ ] Tests pass locally
   ```

### Review Process

1. **Automated Checks**: CI/CD pipeline will run
2. **Code Review**: Maintainers will review your code
3. **Feedback**: Address any comments or requested changes
4. **Approval**: Once approved, a maintainer will merge

### After Merge

1. **Delete your branch**:
   ```bash
   git branch -d feature/your-feature-name
   git push origin --delete feature/your-feature-name
   ```

2. **Update your fork**:
   ```bash
   git checkout main
   git pull upstream main
   git push origin main
   ```

## Issue Guidelines

### Creating an Issue

**Bug Report**:
```markdown
## Bug Description
Clear description of the bug

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. See error

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS: [e.g., Windows 10]
- Browser: [e.g., Chrome 96]
- Version: [e.g., 1.2.0]

## Screenshots
If applicable

## Additional Context
Any other relevant information
```

**Feature Request**:
```markdown
## Feature Description
Clear description of the proposed feature

## Problem It Solves
What problem does this feature address?

## Proposed Solution
How should this feature work?

## Alternatives Considered
Other solutions you've thought about

## Additional Context
Any other relevant information
```

### Issue Labels

- `bug`: Something isn't working
- `enhancement`: New feature or request
- `documentation`: Documentation improvements
- `good first issue`: Good for newcomers
- `help wanted`: Extra attention needed
- `question`: Further information requested
- `wontfix`: This will not be worked on
- `duplicate`: This issue already exists
- `invalid`: This doesn't seem right

## Development Workflow

### Branch Naming

- Feature: `feature/add-half-day-support`
- Bug fix: `fix/balance-calculation`
- Documentation: `docs/update-api-guide`
- Hotfix: `hotfix/security-patch`

### Development Process

1. Create a feature branch from `main`
2. Make your changes
3. Write/update tests
4. Update documentation
5. Commit with conventional commit messages
6. Push and create a pull request
7. Address review comments
8. Merge after approval

## Questions?

- **General Questions**: Open a [Discussion](https://github.com/org/employee-leave-tracker/discussions)
- **Bug Reports**: Create an [Issue](https://github.com/org/employee-leave-tracker/issues)
- **Security Issues**: Email security@employeeleavetracker.com
- **Other**: Contact maintainers directly

## Recognition

Contributors are recognized in:
- README.md contributors section
- Release notes
- Project documentation

Thank you for contributing! 🎉

---

**Additional Resources**:
- [User Guide](./USER_GUIDE.md)
- [API Documentation](./API_DOCUMENTATION.md)
- [System Design](./SYSTEM_DESIGN.md)
