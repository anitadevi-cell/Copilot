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

---

# Detailed Functional Requirements

## Expense Flow Overview

**Create → Submitted → Manager Approved → Finance Approved → Reimbursed**

Possible deviations:
- **Sent Back** (by Manager or Finance)
- **Rejected** (by Manager or Finance)

## Dashboard
The Dashboard is designed to give users a quick and clear view of their expense activities along with the overall status of reports in the system.

### 1) Expense Cards
Show separate clickable cards for the below highlights for:
- **Employees**: Self
- **Admin**: Self & Company
- **Finance Admin / Finance User**: Self & Companies

Requirements:
- At the top of the dashboard, users see a set of summary (**count**) cards representing the current status of expense reports.
- Cards are **clickable** and take users directly to the relevant **filtered list**.
- Display count considering all statuses **except** `Delete`.

Cards (counts):
1. Total Expense Reports
2. Draft Expense Reports
3. Sent Back Expense Reports
4. Expense Reports – Pending Manager Approval
5. Expense Reports – Pending Finance Approval
6. Reimbursement Pending / Completed

### 2) Flow Charts
Display overall expenses of **Self / Company / Sub-Companies**.

Charts for **Daily / Monthly / Yearly** company expenses:

#### i) Expense Type Summary
- Travel
- Non-Travel (General)
- Recurring (Monthly)

For each: show
- **Count**
- **Finance Approved total Amount (including reimbursed amount)**
- Tooltip: show **Finance Approved** and **Reimbursed** amounts separately

#### ii) Policy Violation Expense Report Summary
- Count & Finance Approved total Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

#### iii) Category-wise Expense Report Summary
- Category & Finance Approved Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

#### iv) Department-wise Expense Report Summary (Custom)
- Name & Finance Approved Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

#### v) Designation-wise Expense Report Summary (Custom)
- Name & Finance Approved Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

#### vi) Project-wise Expense Report Summary (Custom)
- Project Name & Code & Finance Approved Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

#### vii) Cost Centre-wise Expense Report Summary (Custom)
- Cost Centre Name & Code & Finance Approved Amount (including reimbursed amount)
- Tooltip: Finance Approved & Reimbursed amounts separately

## Trips
Trips are used to group and manage all travel-related expenses under a single journey, ensuring better organization, visibility, and accurate reporting.

### Workflow
1. Under Trips section:
   - User can see all the trips he/she has booked on EMTDesk (Online/Offline).
2. While creating a Travel Expense Report:
   - User must select a **past-dated** trip if it is mandatory.
   - Show only **Booked** & **Completed** trips.
   - Once selected, fetch trip details and show it under report as **Company Paid** by default:
     - Trip ID
     - Booking ID
     - Product Name
     - Amount
     - Payment Mode – Company Paid
3. If no trip is available, allow creating an offline trip:
   - Trip Name
   - Trip Start Date (allow **past dates only**)
   - Trip End Date (allow **past dates only**)
   - Purpose of Travel

## Expense Inbox
Expense Inbox is the entry point of the expense journey where users can quickly capture and store expenses without needing to complete full details or submit immediately. It acts as a temporary holding area until expenses are organized into reports.

### Key Capabilities
- Multi-channel expense capture:
  - WhatsApp
  - Mobile App
  - Web Portal
- OCR-based automatic data extraction
- Multi-receipt upload with auto-splitting

### 1) Expense Capture Workflow (WhatsApp / App / Web)
- User uploads one or multiple receipts
- System:
  - Splits multiple receipts
  - Runs OCR on each receipt
  - Creates individual expense entries

### 2) Auto-captured fields
- Expense Category
- Expense Date
- Merchant Name
- Amount with currency
  - If OCR captures international currency, system converts amount into **INR** and shows both selected currency and INR value.
- Split Expense Amount
  - If user clicks **Split Expense**:
    - Allow selecting employees of the associated company only
    - Allow dividing amount by percentage or flat
    - Tooltip: *The total amount will be reimbursed to you. Split is for allocation and reporting purposes only.*
- Auto-mark Expense Type:
  - Travel Expense
  - Non-Travel (General) Expense
  - Recurring (Monthly) Expense
- Description

### 3) Expenses View Options
- User can view all expenses in Expense Inbox (manual or OCR).
- Show tabs:
  - Unreported
  - Reported
  - Submitted

Expense list fields:
- Expense ID
- Expense Category Name
- Report ID (if attached/submitted)
- Amount
- Merchant
- Expense Type (Travel / Non-Travel / Recurring)
- Policy Violated (Yes/No) with tooltip

### 4) Unreported tab behavior
- All captured expenses are saved in **Unreported** tab.
- User can:
  - Edit details
  - Add missing fields
  - Delete expense

### 5) Readiness status
System flags missing fields / policy violations:
- **Ready** (all details complete)
- **Action Required** (incomplete / policy violated)
  - Allow edit/update until not attached to a report

### 6) Reported tab behavior
- Expenses attached to a report in `Draft` status appear as **Reported**.
- Should not be allowed to attach again in a new report.
- If removed from existing draft report, mark back to **Unreported** and allow attaching to another report.

### 7) Submitted tab behavior
- Expenses attached to a submitted report appear as **Submitted**.
- Should not be allowed to attach again.

## Expense Reports
Reports are used to group expenses and submit them for approval instead of submitting individual expenses.

### Workflow
1. Create a new report via **+New Report**:
   - Report Type:
     - Travel Report
     - Non-Travel (General) Report
     - Recurring (Monthly) Report
   - Report Name
   - Location – Jio Location:
     - Country, State, City
   - Duration:
     - From Date, To Date
   - Trip (only when Travel Report):
     - Allow selecting past-dated completed trips
     - Mandatory/optional controlled by configuration
     - Show all services booked in the trip as Company Paid and display
   - Custom fields if enabled from masters (Project Code, Department Code, Cost Centre Code, etc.)

2. After report is created:
   - Allow attaching **Unreported** expenses from Expense Inbox
   - Show expenses based on selected report type

3. Add new expense manually via **+Add New Expense**:
   - Upload receipt (OCR reads details) and fill fields automatically:
     - Expense Category
     - Expense Date (Start & End date if enabled)
     - Location (if enabled)
     - Amount with currency (convert to INR if international)
     - Split Expense Amount (same logic + tooltip)
     - Merchant Name
     - Description (free-flow remarks)
     - Add Expense

4. Duplicate Check
- If adding duplicate expense within same report: alert
  - Same Amount + Date + Merchant
  - Same Invoice Number
  - Same Receipt Image
- If adding duplicate expense in a new report but already submitted elsewhere: alert with Report ID
  - Same Amount + Date + Merchant
  - Same Invoice Number
  - Same Receipt Image

5. Advance Amount
- Show advance amount to be adjusted associated with selected trip

6. Reimbursable
- Show total employee-paid amount to reimburse after adjusting advance
- If no advance, show total employee-paid expenses amount

7. Submit
- On final Submit, show consent popup: expenses reviewed and attached correctly
  - Yes: submit for approval
  - No: stay on page

8. Draft
- Option to save report as Draft: “Save & Submit Later”

### Expense Reports View Options
Users can view all expense reports added by the user in Expense Reports section.

List fields:
- Expense Report ID
- Expense Report Name
- Total Reimbursable Amount
- Trip ID (if attached)
- Report Type (Travel / Non-Travel / Recurring)
- Policy Violated (Yes/No) with tooltip

Filters:
- By report type: All / Travel / Non-Travel / Recurring
- By status:
  - Draft
  - Sent Back
  - Awaiting Manager Approval
  - Awaiting Finance Approval
  - Reimbursement Pending
  - Reimbursed
  - Rejected

Additional behaviors:
- Allow edit/update draft reports and expenses until submitted
- Allow Withdraw submitted report until not approved by the first approver

Report detail view:
- Report details: Report Name, Report Type, Duration
- Expense list fields: Expense ID, Category, Amount, Policy Violated
- Expense detail view:
  - Uploaded Receipt
  - Category
  - Date
  - Location
  - Amount with currency (convert to INR if international)
  - Merchant Name
  - Description

## Advances (Advance Request)
The Advances feature allows employees to request funds in advance before incurring expenses.

### Advance Request Types
1. Travel Advance Report
2. Non-Travel Advance Report

### Travel Advance Report – Select Trip
- Advance Report Name
- Advance Request Date (prefill today; read-only)
- Trip selection:
  - Mandatory/optional by configuration
  - Show only Booked trips
  - Show only future trips (not past-dated)
- Estimated Expenses (currency + INR conversion)
- Advance Amount (currency + INR conversion)
- Remarks (free text)
- Total Amount (with currency symbol)

### Non-Travel Advance Report
- Trip not required
- Advance Report Name
- Advance Request Date (prefill today; read-only)
- Location: City-State-Country
- Estimated Expenses (currency + INR conversion)
- Advance Amount (currency + INR conversion)
- Remarks
- Total Amount

### Advance Request View Options
Users can view all advance reports in the Expense Reports section.
- Advance Report ID
- Advance Report Name
- Estimated Expenses
- Advance Amount
- Trip ID (if available)
- Status

### Advance Surrender Request Workflow
Return unutilized advance amount.

Fields:
1. Advance Report ID
2. Advance Report Name
3. Expense Report ID
4. Requested Advance Amount
5. Settled Advance Amount
6. Utilized Advance Amount
7. Trip ID (if available)
8. Status

Surrender:
- On Surrender:
  - Display Utilized Advance Amount
  - Surrender Mode: UPI / Bank transfer / Cash
  - Attach Proof
  - Bank UTR number
  - Remarks
- On Submit: submit to finance directly for approval & settlement
- Finance also can surrender utilized advance amounts for all employees if user does not

## Cards Workflow (Corporate Credit Card)
Company Card module automatically captures card transactions, matches receipts, validates policies, and processes via standard approval and reconciliation flow.

- Allow selecting multiple card expenses:
  - Transaction ID
  - Transaction Date
  - Card Number (last 4 digits)
  - Merchant
  - Auto-tag Expense Category based on expense type
  - Amount with currency symbol
  - Payment Method: Corporate Paid (default)
  - Remarks
- After selection, create expenses automatically and attach to respective expense report
- Show Total Amount with currency symbol

## Mileage Workflow
Mileage Expense can be raised while creating an expense report.

- Travel Category: Local / Outstation
  - For outstation: Departure Date & Return Date with time
- Vehicle Type: Car / Bike/Scooty
- From Location (GPS / geo-fencing)
- To Location (GPS / geo-fencing)
- Distance (KM/Mile): auto-calculated
- Rate per KM/Mile: fetch from policy
- Payment Method: Employee Paid (default)
- Total Amount: calculated automatically; show with currency symbol

## Per Diem Workflow
When user creates travel expense report:
- Calculate per diem configured at policy level based on report start & end date and add day-wise
- If per diem enabled, do not allow adding below categories:
  - Meals (Breakfast/Lunch/Dinner)
  - Incidental Expenses
  - Local Transport (Metro / Auto / Bus)

## My Approvals Workflow
- Show all expense reports pending approval & approved requests
- Approve full report
- Partially approve expenses with remarks
- Send Back report with remarks

### Send Back Feature (Manager)
- Approver enters remarks and submits
- Report goes back to user
- User can modify request (add category, modify expense fields)
- User can resubmit to approver

## Finance Approvals Workflow
### A) Finance Approvals
- Show reports pending & approved for finance approvals
- Approve full report
- Partially approve with remarks
- Send Back with remarks

### B) Reimbursement
- Show reports pending reimbursements & reimbursed
- Allow view & modify: amount, category, GL code, Tax code, HSN code, Cost Centre code, Project code, Merchant code with remarks

### C) Reimbursement View Options (Payment Initiator)
Payment initiator can view Expense & Advance reports in separate tabs.

#### i) Expense Reports tab
Fields:
- Expense Report ID
- Expense Report Name
- Total Reimbursable Amount
- Trip ID (if attached)
- Report Type (Travel / Non-Travel / Recurring)
- Policy Violated (Yes/No) with tooltip

Filters:
- By report type: All / Travel / Non-Travel / Recurring
- By status: Sent Back / Reimbursement Pending / Reimbursed / Rejected

#### ii) Advance Reports tab
- Advance Report ID
- Advance Report Name
- Estimated Expenses
- Advance Amount
- Trip ID (if available)
- Status
- Surrender

### D) Manual reimbursement status upload
If ERP not integrated or no payment response:
- Download excel of finance approved reports pending payment update
- Mark each transaction as Reimbursed in excel
- Enter bank UTR reference number
- Upload to update status from Finance Approved – Payment Initiated to Reimbursed

### Send Back Feature (Finance)
- Finance approver enters remarks and sends back
- User can modify request (add category, modify expense fields)
- Resubmission routing: to original approver or finance directly based on settings

## History / Audit Trail
### Expense Inbox History
Capture each step/action in history.
- When user adds expense via mobile / WhatsApp / web:
  - Capture mode, name, date, time

### Expense Report History
Capture each step/action in history:
- Submission: name, date, time
- Approve/Reject (Manager/Finance): name, date, time, action/status
- Send back (Manager/Finance): name, date, time, action/status
- Payment initiator submits reimbursement: name, date, time, action/status
- Reimbursed via manual upload or ERP: date, time, action/status

## Finance Team Management
- Select finance team members (only users with Finance User role)
- Tag finance user as:
  - Checker
  - Payment Initiator
  - Both
- Assign base location:
  - All or multiple base locations
  - Finance user sees requests only for selected locations

## Policy Mechanism

### Expense Policy
1. Create policy at hierarchy level
2. Select expense category (from all categories)
3. Select location (Country, State, City)
4. Expense amount limit (can vary by location)
5. Receipt required amount limit (can vary by location)
6. Expense count & period limit:
   - Count (numeric)
   - Period: Daily / Weekly / Monthly / Yearly
7. Action on violation:
   - Warn (allow submit with warning)
   - Block (prevent submit; show error)
8. Claim count & period limit:
   - Count (numeric)
   - Period: Daily / Weekly / Monthly / Yearly

### Mileage Policy
1. Vehicle type: Car / Bike/Scooty
2. Location (Country, State, City)
3. Mileage rate per KM/Mile
4. Max distance limit in KM/Mile
5. Action: Warn / Block

### Per Diem Rate Policy
1. Travel type: Domestic / International
2. Location (Country, State, City)
3. Per diem type: Per Day / Per Hour
4. Rate in selected country currency

## Approval Policy Mechanism
1. Define category-based approval flow or one approval flow for all categories
   - Regular approvers (same list as Travel)
   - Deviation approvers (same list as Travel)
2. Define category-based finance approval flow
   - Display list of finance users

## General Masters Configuration

### Duplicate Check Configuration
- Enable/Disable
- Action: Warn / Block

### Report Type Masters Configuration
- Travel Report: Enable/Disable; Trip ID mandatory/optional
- Non-Travel (General) Report: Enable/Disable
- Recurring (Monthly) Report: Enable/Disable
- Custom Fields (Project, Department, Designation, Cost Centre, etc.): Enable/Disable; Mandatory/Optional

### Expense Category Masters Configuration
1. Default categories (enable/disable + rename option)

#### a) Travel Expense
- Flight
- Train
- Cab
- Local Transport (Metro / Auto / Bus)
- Hotel
- Meals (Breakfast/Lunch/Dinner)
- Travel Insurance
- Visa Fees
- Baggage Charges
- Airport Transfer
- Incidentals Expenses

#### b) Non-Travel Expense
- Office Supplies
- Printing & Stationery
- Internet / Wi-Fi
- Telephone / Mobile Bills
- Courier / Shipping
- Client Entertainment
- Team Expense / Team Outing
- Gifts & Rewards
- Training & Certification
- Conference / Event Fees
- Consultancy / Professional Fees
- Fuel / Petrol
- Parking Charges
- Toll Charges
- Miscellaneous

#### c) Recurring (Monthly) Expense
- Electricity Bill
- Water Charges
- Internet Subscription
- Mobile Plan
- Software Subscriptions
- Maintenance / Housekeeping

2. Add new expense category:
- Expense Category Name
- Expense Category Code
- Expense Category Type: Travel / Non-Travel / Recurring
  - If recurring:
    - Define month start & end date
    - Default applicable for 30 days
    - Do not allow adding for less than 30 days
- Activation Date

3. Fields configuration per category:
- Expense Date:
  - Start Date & End Date required
  - Mandatory/Optional
- Location (Jio Location): Yes/No; Mandatory/Optional
- Invoice Number required: Yes/No; Mandatory/Optional
- Remarks: Mandatory/Optional

### Mileage Masters Configuration
- Vehicle Type: Car / Bike
- Vehicle Code
- Location
- Mileage Type: Kms / Mile
- Travel Type (multi-select): Local / Outstation
- Activation Date
- Fields:
  - Expense Date (start/end required; mandatory/optional)
  - Invoice number required (yes/no; mandatory/optional)
  - Remarks (mandatory/optional)

### Advance Configuration
1. Advance types:
- Travel advance:
  - Maximum travel advance limit
  - Maximum count limit
- Non-travel advance:
  - Maximum non-travel advance limit
  - Maximum count limit

2. Fields:
- Estimated expense (mandatory/optional)
- Remarks (mandatory/optional)

## Finance Masters Configuration

### GL Code Masters
- GL Code
- GL Name
- Show list of expense categories
- Option to add separate GL for Mileage
- Option to add separate GL for Per Diem
- Option to add separate GL for Advance

### HSN Code Masters
- HSN Code
- HSN Name
- Show list of expense categories
- Option to add separate HSN for Mileage
- Option to add separate HSN for Per Diem
- Option to add separate HSN for Advance

### Merchants Configuration
- Merchant Code
- Merchant Name
- Show list of expense categories
- Option to add separate merchant for Mileage
- Option to add separate merchant for Per Diem

### Tax Codes Masters
- Tax Code
- Tax Name
- Show list of expense categories
- Tax %: 5% / 12% / 18%
- Tax Type: CGST+SGST / IGST

---

## Suggested Next Additions (Optional)
If you want, we can extend this document with:
- User roles & permissions (Employee, Manager, Finance, Admin)
- End-to-end workflow diagrams
- Data model (entities like Expense, Report, Trip, Advance, Policy, ApprovalStep)
- Status definitions and state transitions
- Compliance/GST rules and examples
- API endpoints and UI screen list
