# Examples and Usage Templates

Practical examples and templates for common use cases in the Employee Leave Tracker system.

## Table of Contents

- [Common Use Cases](#common-use-cases)
- [Leave Request Examples](#leave-request-examples)
- [Email Templates](#email-templates)
- [CSV Import Templates](#csv-import-templates)
- [Report Examples](#report-examples)
- [Integration Examples](#integration-examples)

## Common Use Cases

### Use Case 1: Requesting Annual Leave

**Scenario**: Employee wants to take a week off for vacation

**Steps**:
1. Check available leave balance
2. Check team calendar for conflicts
3. Submit leave request with details
4. Wait for approval
5. Receive confirmation

**Example Request**:
```json
{
  "leaveTypeId": "annual-leave-uuid",
  "startDate": "2025-03-10",
  "endDate": "2025-03-14",
  "duration": 5,
  "reason": "Family vacation",
  "isHalfDay": false
}
```

### Use Case 2: Emergency Sick Leave

**Scenario**: Employee falls ill and needs immediate time off

**Steps**:
1. Notify manager immediately (phone/SMS)
2. Submit retroactive leave request when able
3. Upload medical certificate if required
4. Manager approves based on notification

**Example Request**:
```json
{
  "leaveTypeId": "sick-leave-uuid",
  "startDate": "2025-01-20",
  "endDate": "2025-01-22",
  "duration": 3,
  "reason": "Flu - medical certificate attached",
  "isHalfDay": false,
  "isRetroactive": true
}
```

### Use Case 3: Half-Day Medical Appointment

**Scenario**: Employee has a doctor's appointment in the afternoon

**Steps**:
1. Submit half-day leave request
2. Specify morning or afternoon
3. Get approval from manager
4. Mark calendar

**Example Request**:
```json
{
  "leaveTypeId": "sick-leave-uuid",
  "startDate": "2025-02-05",
  "endDate": "2025-02-05",
  "duration": 0.5,
  "reason": "Medical appointment",
  "isHalfDay": true,
  "halfDayPeriod": "afternoon"
}
```

### Use Case 4: Manager Approving Team Leave

**Scenario**: Manager reviews and approves a team member's leave request

**Steps**:
1. Review pending approvals in dashboard
2. Check team calendar for coverage
3. Review employee's leave balance
4. Approve or reject with comments
5. System sends notification to employee

**Example Approval**:
```json
{
  "requestId": "leave-request-uuid",
  "action": "approve",
  "comments": "Approved. Have a great vacation! Jane will cover your duties."
}
```

### Use Case 5: HR Adjusting Leave Balance

**Scenario**: HR needs to add bonus leave days for an employee

**Steps**:
1. Navigate to user's leave balance
2. Select leave type to adjust
3. Enter adjustment amount and reason
4. Set effective date
5. Confirm adjustment

**Example Adjustment**:
```json
{
  "userId": "employee-uuid",
  "leaveTypeId": "annual-leave-uuid",
  "adjustment": 5,
  "reason": "5-year service bonus",
  "effectiveDate": "2025-01-01"
}
```

## Leave Request Examples

### Example 1: Single Day Leave

```json
{
  "leaveTypeId": "personal-leave-uuid",
  "startDate": "2025-02-15",
  "endDate": "2025-02-15",
  "duration": 1,
  "reason": "Personal matters",
  "isHalfDay": false
}
```

### Example 2: Extended Leave (2 weeks)

```json
{
  "leaveTypeId": "annual-leave-uuid",
  "startDate": "2025-06-01",
  "endDate": "2025-06-12",
  "duration": 10,
  "reason": "Family trip abroad",
  "isHalfDay": false,
  "requiresSpecialApproval": true
}
```

### Example 3: Maternity Leave

```json
{
  "leaveTypeId": "maternity-leave-uuid",
  "startDate": "2025-03-01",
  "endDate": "2025-06-15",
  "duration": 106,
  "reason": "Maternity leave - due date March 15, 2025",
  "isHalfDay": false,
  "attachments": ["medical-certificate.pdf"]
}
```

### Example 4: Multiple Half Days

**Monday Morning**:
```json
{
  "leaveTypeId": "personal-leave-uuid",
  "startDate": "2025-02-10",
  "endDate": "2025-02-10",
  "duration": 0.5,
  "reason": "Personal appointment",
  "isHalfDay": true,
  "halfDayPeriod": "morning"
}
```

**Wednesday Afternoon**:
```json
{
  "leaveTypeId": "personal-leave-uuid",
  "startDate": "2025-02-12",
  "endDate": "2025-02-12",
  "duration": 0.5,
  "reason": "Family commitment",
  "isHalfDay": true,
  "halfDayPeriod": "afternoon"
}
```

### Example 5: Compassionate Leave

```json
{
  "leaveTypeId": "compassionate-leave-uuid",
  "startDate": "2025-01-25",
  "endDate": "2025-01-29",
  "duration": 5,
  "reason": "Bereavement - grandmother",
  "isHalfDay": false,
  "requiresDocumentation": true
}
```

## Email Templates

### Template 1: Leave Request Confirmation

```
Subject: Leave Request Submitted - [Employee Name]

Dear [Employee Name],

Your leave request has been submitted successfully and is now pending approval.

Leave Details:
- Type: [Leave Type]
- Period: [Start Date] to [End Date]
- Duration: [Number] days
- Status: Pending Approval

Your request will be reviewed by [Manager Name]. You will receive a notification 
once a decision has been made.

Current Leave Balance:
- [Leave Type]: [Available] days remaining

If you need to modify or cancel this request, please visit your dashboard.

Best regards,
Employee Leave Tracker System
```

### Template 2: Leave Approval Notification

```
Subject: Leave Request Approved - [Start Date] to [End Date]

Dear [Employee Name],

Good news! Your leave request has been approved.

Leave Details:
- Type: [Leave Type]
- Period: [Start Date] to [End Date]
- Duration: [Number] days
- Approved by: [Manager Name]
- Approved on: [Approval Date]

Manager's Comments:
[Comments]

Updated Leave Balance:
- [Leave Type]: [Remaining] days available

Please ensure proper handover of your responsibilities before your leave begins.

Have a great time!

Best regards,
Employee Leave Tracker System
```

### Template 3: Leave Rejection Notification

```
Subject: Leave Request Update - [Employee Name]

Dear [Employee Name],

Your leave request for [Start Date] to [End Date] has been reviewed.

Unfortunately, your request could not be approved at this time.

Reason:
[Rejection Reason]

We encourage you to discuss alternative dates with [Manager Name].

If you have any questions or concerns, please reach out to your manager or HR.

Best regards,
Employee Leave Tracker System
```

### Template 4: Leave Balance Low Warning

```
Subject: Leave Balance Running Low

Dear [Employee Name],

This is a friendly reminder that your [Leave Type] balance is running low.

Current Balance:
- Available: [Number] days
- Used this year: [Number] days
- Pending requests: [Number] days

We encourage you to plan your remaining leave for the year. Remember that 
unused leave may expire at the end of the year (check policy for details).

If you have any questions about your leave balance, please contact HR.

Best regards,
Employee Leave Tracker System
```

### Template 5: Pending Approval Reminder (For Managers)

```
Subject: Leave Requests Awaiting Your Approval ([Number] pending)

Dear [Manager Name],

You have [Number] leave request(s) pending your approval:

1. [Employee Name] - [Leave Type]
   Period: [Start Date] to [End Date] ([Number] days)
   Submitted: [Date]

2. [Employee Name] - [Leave Type]
   Period: [Start Date] to [End Date] ([Number] days)
   Submitted: [Date]

Please review and process these requests at your earliest convenience.

[View Pending Approvals Button]

Best regards,
Employee Leave Tracker System
```

## CSV Import Templates

### User Import Template

**Filename**: `users_import.csv`

```csv
email,first_name,last_name,employee_id,department,manager_email,hire_date,role,annual_leave,sick_leave,personal_leave
john.doe@company.com,John,Doe,EMP001,Engineering,jane.smith@company.com,2020-01-15,employee,20,12,5
jane.smith@company.com,Jane,Smith,EMP002,Engineering,,2018-06-01,manager,25,12,5
bob.jones@company.com,Bob,Jones,EMP003,Marketing,sarah.wilson@company.com,2021-03-10,employee,20,12,5
sarah.wilson@company.com,Sarah,Wilson,EMP004,Marketing,,2019-09-20,manager,25,12,5
```

**Field Descriptions**:
- `email`: Work email (required, unique)
- `first_name`: First name (required)
- `last_name`: Last name (required)
- `employee_id`: Employee ID (required, unique)
- `department`: Department name (required)
- `manager_email`: Manager's email (optional for managers)
- `hire_date`: Start date (YYYY-MM-DD)
- `role`: employee, manager, hr_admin, or super_admin
- `annual_leave`: Annual leave allocation
- `sick_leave`: Sick leave allocation
- `personal_leave`: Personal leave allocation

### Leave Balance Adjustment Template

**Filename**: `balance_adjustments.csv`

```csv
employee_email,leave_type,adjustment,reason,effective_date
john.doe@company.com,Annual Leave,5,5-year service bonus,2025-01-01
jane.smith@company.com,Annual Leave,3,Extra days for project completion,2025-01-15
bob.jones@company.com,Sick Leave,-2,Retroactive correction,2025-01-01
```

### Holiday Calendar Template

**Filename**: `holidays_2025.csv`

```csv
date,name,type,departments
2025-01-01,New Year's Day,public,All
2025-07-04,Independence Day,public,All
2025-12-25,Christmas Day,public,All
2025-03-17,St. Patrick's Day,regional,"Sales,Marketing"
2025-11-28,Thanksgiving,public,All
```

## Report Examples

### Example 1: Leave Utilization Report

**Parameters**:
- Date Range: 2025-01-01 to 2025-12-31
- Department: Engineering
- Format: Excel

**Sample Output**:

| Employee      | Annual Allocated | Annual Used | Utilization % | Sick Used | Personal Used |
|---------------|------------------|-------------|---------------|-----------|---------------|
| John Doe      | 20               | 15          | 75%           | 3         | 2             |
| Jane Smith    | 25               | 18          | 72%           | 1         | 1             |
| Bob Johnson   | 20               | 10          | 50%           | 5         | 3             |
| **Department**| **65**           | **43**      | **66%**       | **9**     | **6**         |

### Example 2: Leave Balance Summary

**Parameters**:
- As of Date: 2025-06-30
- Include: All Departments

**Sample Output**:

| Department  | Employees | Total Allocated | Total Used | Total Pending | Total Available |
|-------------|-----------|-----------------|------------|---------------|-----------------|
| Engineering | 50        | 1000            | 450        | 75            | 475             |
| Marketing   | 30        | 600             | 300        | 50            | 250             |
| Sales       | 40        | 800             | 520        | 60            | 220             |
| HR          | 10        | 200             | 80         | 10            | 110             |
| **Total**   | **130**   | **2600**        | **1350**   | **195**       | **1055**        |

### Example 3: Team Calendar Report

**Parameters**:
- Month: March 2025
- Team: Engineering - Team A

**Sample Output**:

```
Week of March 3-7, 2025
├── Monday 3rd: John Doe (Annual), Sarah Chen (Personal AM)
├── Tuesday 4th: John Doe (Annual)
├── Wednesday 5th: John Doe (Annual), Bob Smith (Sick)
├── Thursday 6th: John Doe (Annual), Bob Smith (Sick)
└── Friday 7th: John Doe (Annual), Alice Wong (Personal PM)

Week of March 10-14, 2025
├── Monday 10th: Available (Full team)
├── Tuesday 11th: Available (Full team)
├── Wednesday 12th: Jane Wilson (Annual)
├── Thursday 13th: Jane Wilson (Annual), Mike Lee (Sick)
└── Friday 14th: Jane Wilson (Annual)
```

## Integration Examples

### Example 1: Calendar Sync (Google Calendar)

**Setup Steps**:
1. Go to Settings > Integrations
2. Click "Connect Google Calendar"
3. Authorize access
4. Select calendar for leave sync
5. Enable automatic sync

**What Gets Synced**:
- Approved leave requests → Calendar events
- Event title: "[Leave Type] - Out of Office"
- All-day events for full days
- Timed events for half days

### Example 2: Slack Integration

**Notification Example**:
```
Employee Leave Tracker [Bot] 2:30 PM

📅 Leave Request Approved

John Doe's leave request has been approved:
• Type: Annual Leave
• Dates: March 10-14, 2025 (5 days)
• Approved by: Jane Smith

[View Details]
```

**Setup**:
1. Add Employee Leave Tracker app to Slack
2. Connect to your account
3. Choose notification preferences
4. Select channels for team notifications

### Example 3: Email Integration

**Outlook Rules Example**:

Create a rule to automatically categorize leave notification emails:
```
From: noreply@employeeleavetracker.com
Subject contains: "Leave Request"
→ Categorize as: "HR - Leave"
→ Move to folder: "Leave Notifications"
```

### Example 4: API Integration with HRIS

**Sync User Data**:
```javascript
// Daily sync script
const syncUsers = async () => {
  // Get users from HRIS
  const hrisUsers = await hrisAPI.getUsers();
  
  // Update in Leave Tracker
  for (const user of hrisUsers) {
    await leaveTrackerAPI.updateUser(user.id, {
      firstName: user.firstName,
      lastName: user.lastName,
      department: user.department,
      manager: user.managerId,
      status: user.isActive ? 'active' : 'inactive'
    });
  }
};

// Run daily at midnight
schedule('0 0 * * *', syncUsers);
```

### Example 5: Custom Dashboard Widget

**HTML Embed Code**:
```html
<!-- Leave Balance Widget -->
<iframe 
  src="https://app.employeeleavetracker.com/widgets/balance?token=YOUR_TOKEN" 
  width="300" 
  height="200" 
  frameborder="0">
</iframe>
```

**Displays**:
- Current leave balances
- Upcoming leave
- Quick request button

## Testing Examples

### Test Scenario 1: Happy Path Leave Request

```gherkin
Feature: Submit Leave Request
  
  Scenario: Employee submits a valid leave request
    Given I am logged in as an employee
    And I have 20 days of annual leave available
    When I submit a leave request for 5 days
    Then the request should be created successfully
    And the status should be "Pending"
    And my manager should receive a notification
    And my pending balance should be reduced by 5 days
```

### Test Scenario 2: Insufficient Balance

```gherkin
Feature: Leave Request Validation
  
  Scenario: Employee tries to request more leave than available
    Given I am logged in as an employee
    And I have 5 days of annual leave available
    When I try to submit a leave request for 10 days
    Then I should see an error message "Insufficient leave balance"
    And the request should not be created
    And my balance should remain unchanged
```

### Test Scenario 3: Manager Approval Workflow

```gherkin
Feature: Approve Leave Request
  
  Scenario: Manager approves team member's leave
    Given I am logged in as a manager
    And there is a pending leave request from my team member
    When I approve the request with comments
    Then the request status should change to "Approved"
    And the employee should receive an approval notification
    And the leave should appear on the team calendar
    And the employee's available balance should be reduced
```

---

**Need More Examples?**
- Contact Support: support@employeeleavetracker.com
- Check the [USER_GUIDE.md](./USER_GUIDE.md) for detailed instructions
- See [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) for API examples
