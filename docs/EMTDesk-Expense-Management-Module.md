# EMTDesk – Expense Management Module (Overview)

## Purpose
The **EMTDesk Expense Management Module** provides a unified, **policy-driven** platform that enables employees to **create, submit, track, and get reimbursed** for **Travel and Non-Travel** expenses while ensuring:

- **Policy compliance**
- **Approval controls** (maker–checker)
- **Financial accuracy**
- **Multi-level approvals**
- **GST handling**
- **Detailed analytics and reporting**

## Key Concepts
- **Maker–Checker workflow**: Expense items and reports are created by employees (makers) and reviewed/approved by managers/finance (checkers) to enforce controls and reduce errors/fraud.
- **Travel vs Non-Travel**: Expenses may be associated with trips (travel) or created independently (non-travel).
- **Reimbursement lifecycle**: Capture → Compile → Submit → Approve → Finance verify → Reimburse.

## Modules / Key Features

### 1) Dashboard
The **Dashboard** provides a quick overview of expense-related activities, giving users a snapshot of:
- Draft expenses waiting in the inbox
- Reports pending submission
- Reports in approval / finance queue
- Reimbursement status
- Alerts for policy violations or missing details

### 2) Trips
The **Trips** feature allows users to link travel-related expenses to a specific journey, enabling:
- Better organization of travel spend
- Accurate tracking by trip
- Easier reporting and auditing

### 3) Expense Inbox
The **Expense Inbox** is the starting point of the entire expense flow. It allows users to:
- Capture and store expenses as they occur
- Upload/attach proofs (if applicable)
- Keep expenses in draft until ready
- Avoid submitting each expense immediately

### 4) Expense Reports
**Expense Reports** let users compile multiple expenses into a single submission for approval.
Instead of submitting each expense individually, users can:
- Group expenses by trip, month, department, or purpose
- Submit once for review
- Track status across the full approval chain

### 5) Advances
The **Advances** feature allows users to request money **in advance** before incurring expenses, applicable to:
- **Travel advances**
- **Non-travel advances**

The module should support linking advances to:
- Trips or purposes
- Final reports for adjustment/settlement

### 6) Expense Policy
The **Expense Policy** defines the rules and guidelines that control how expenses are created, submitted, and reimbursed, such as:
- Category limits (amount/daily caps)
- Allowed/blocked merchants or expense types
- Required fields (GSTIN, receipts above threshold, etc.)
- Submission timelines / cutoff dates
- Exceptions and escalation handling

### 7) Approval Flow
The **Approval Flow** defines how expense reports move through stages before final reimbursement.
It supports:
- Configurable stages and roles
- **Multi-level approvals** (e.g., L1 manager → L2 manager → finance)
- Maker–checker controls
- Reject / send-back / approve actions

### 8) My Approvals (Managers)
**My Approvals** is designed for managers responsible for reviewing and approving expense reports.
Capabilities include:
- View pending reports with summaries
- Drill down into report details and receipts
- Approve, reject, or send back for correction
- See policy flags and violations

### 9) Finance Approvals (Finance Queue)
The **Finance Queue** is used by the finance team to process reports after manager approval, covering:
- Verification of receipts and GST details
- Compliance checks and coding
- Final reimbursement processing
- Tracking reimbursement status and completion

### 10) Configurations Panel
The **Configurations Panel** supports admin/finance configuration of master data such as:
- Expense Categories
- Finance Codes
- Tax/GST settings
- Approval routing rules
- Policy rule sets

### 11) Reports / Analytics
**Reports & Analytics** provide visibility into how expenses are incurred across the organization, including:
- Spend by category, cost center, employee, location
- Trip cost analytics
- Policy violation trends
- Reimbursement cycle time and bottlenecks
- GST reporting readiness

## Suggested Next Additions (Optional)
If you want, we can extend this document with:
- User roles & permissions (Employee, Manager, Finance, Admin)
- End-to-end workflow diagrams
- Data model (entities like Expense, Report, Trip, Advance, Policy, ApprovalStep)
- Status definitions and state transitions
- Compliance/GST rules and examples
- API endpoints and UI screen list
