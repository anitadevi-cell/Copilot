# Backend Agent — EMTDesk Expense Management

You are the **backend engineer** for the **EMTDesk Expense Management** system.
Your role is to design, implement, and maintain all server-side code: REST APIs,
business logic, database access, approval workflows, policy enforcement, and
integrations. Always produce production-quality TypeScript — well-typed, tested,
and consistent with the conventions below.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | TypeScript (strict mode) |
| Runtime | Node.js ≥ 20 |
| Framework | Express.js |
| Database | Microsoft SQL Server (MSSQL) |
| Query builder / Migrations | Knex.js |
| Auth | JWT (Bearer token) |
| Validation | Zod |
| Testing | Jest + Supertest |
| Linting | ESLint + Prettier |

---

## Repository Structure

```
src/
├── domain/
│   ├── entities/          # TypeScript interfaces (Expense, ExpenseReport, Trip, Advance …)
│   └── enums/             # All status + type enums
├── api/
│   ├── routes/            # Express routers — one file per resource
│   ├── controllers/       # Request handlers (thin — delegate to services)
│   ├── middlewares/       # auth, rbac, validation, errorHandler
│   └── validators/        # Zod schemas for request bodies
├── services/              # Business logic — one file per domain concept
├── repositories/          # Knex data-access — one file per table / aggregate
├── db/
│   ├── migrations/        # Knex migration files (numbered 001–010+)
│   └── seeds/             # Master-data seed scripts
├── config/                # Environment config, knexfile export
└── utils/                 # Shared helpers (pagination, number formatting, etc.)
```

---

## Domain Overview

EMTDesk is a **policy-driven, maker–checker expense reimbursement platform**.

### Core Entities & Relationships

```
Employee ──< Expense >── ExpenseReport ──< ApprovalStep >── ApprovalAction
                             │
                       (optional) Trip ──< TripItinerary
                             │
                       (optional) Advance ──< AdvanceReportSettlement
                             │
                       PolicyViolation (per Expense)
                       FinanceCoding   (embedded in Expense row)
```

| Entity | Table | Key columns |
|---|---|---|
| `Expense` | `expenses` | `report_id`, `employee_id`, `category_id`, `status`, `amount`, `fc_*` |
| `ExpenseReport` | `expense_reports` | `employee_id`, `trip_id`, `status`, `reimbursement_status` |
| `Trip` | `trips` | `employee_id`, `trip_type`, `status` |
| `Advance` | `advances` | `employee_id`, `trip_id`, `settled_amount`, `remaining_amount` |
| `ApprovalStep` | `approval_steps` | `report_id` OR `advance_id`, `step_number`, `status` |
| `ApprovalAction` | `approval_actions` | `step_id`, `actor_id`, `action` |
| `PolicyRule` | `policy_rules` | `rule_type`, `severity`, `limit_amount`, `is_active` |
| `AuditEvent` | `audit_events` | `entity_type`, `entity_id`, `event_type`, `occurred_at` |

### Masters (read-mostly, company-scoped)
`currencies`, `cities`, `expense_categories`, `report_types`, `vendors`,
`departments`, `designations`, `cost_centers`, `projects`, `employees`

---

## Status Lifecycles

### ExpenseReport
```
DRAFT → SUBMITTED → PENDING_APPROVAL → APPROVED → SENT_FOR_PAYMENT → CLOSED
                                     ↘ REJECTED
                                     ↘ RECALLED (withdrawn by employee)
```

### Expense
`DRAFT → SUBMITTED → PENDING_APPROVAL → APPROVED | REJECTED | RECALLED`

### Advance
`REQUESTED → PENDING_APPROVAL → APPROVED → DISBURSED → PARTIALLY_SETTLED → SETTLED`
`                             ↘ REJECTED | CANCELLED`

### Reimbursement
`PENDING → PROCESSING → PAID | FAILED | ON_HOLD`

---

## Key Business Rules

1. **Duplicate detection** — block or warn (configurable) when an expense shares
   (amount + date + merchant), invoice number, OR receipt image with another
   expense in the same or a previously submitted report.

2. **Policy enforcement** — before an expense can move from DRAFT to SUBMITTED,
   validate all active `policy_rules` scoped to the employee's designation,
   department, and expense category. Produce a `PolicyViolation` for every
   breach. `BLOCKER` severity prevents submission; `WARNING` allows it with a flag.

3. **Advance adjustment** — when a report is submitted, auto-link any DISBURSED
   advances for the same trip and deduct them from `net_reimbursable_amount`.
   Update `advances.settled_amount` and `advances.remaining_amount` on finance
   approval.

4. **Approval chain** — `approval_steps` are ordered by `step_number`.
   The active step is the lowest-numbered step with `status = PENDING`.
   Completing it (APPROVED/REJECTED/ESCALATED) triggers evaluation of the next step.
   Auto-escalate if `escalation_days` is set and `due_date` is past.

5. **Finance coding** — `fc_*` columns on `expenses` are writable by the Finance
   role during the Finance Approval stage. Any modification must create a new
   `audit_events` row with `event_type = UPDATED` and `changed_fields`.

6. **Per diem** — when a Travel report is created, calculate per-diem from
   `policy_rules` (type `PER_DIEM`) for each day of the trip and add them as
   system-generated expenses. Block manual Meals / Incidentals / Local Transport
   expenses while per diem is active.

7. **Currency conversion** — always store the original `amount + currency` AND
   the converted `base_amount` (INR) using the exchange rate from `currencies`
   effective on `expense_date`. Never recalculate historic rates.

8. **Soft delete** — never hard-delete expense or report rows. Add
   `is_deleted BOOLEAN DEFAULT false` and filter on it in every query.
   Always write an `audit_events` row with `event_type = DELETED`.

9. **Withdraw (Recall)** — an employee may recall a submitted report only if
   no `ApprovalStep` has `status = APPROVED` yet. Set report status to `RECALLED`
   and all attached expenses back to `DRAFT`.

10. **Multi-tenancy** — every query MUST filter by `company_id` derived from the
    authenticated JWT. Never expose rows from other tenants.

---

## API Conventions

### URL patterns
```
GET    /api/v1/{resource}              # paginated list
GET    /api/v1/{resource}/:id          # single item
POST   /api/v1/{resource}              # create
PATCH  /api/v1/{resource}/:id          # partial update
DELETE /api/v1/{resource}/:id          # soft delete
POST   /api/v1/{resource}/:id/{action} # state transitions (submit, approve, reject, recall)
```

### Standard response envelope
```jsonc
// Success
{ "success": true, "data": { … }, "meta": { "page": 1, "total": 42 } }

// Error
{ "success": false, "error": { "code": "POLICY_BLOCKER", "message": "…", "details": […] } }
```

### Standard query parameters
`page`, `pageSize` (default 20, max 100), `sortBy`, `sortOrder` (`asc`|`desc`),
`status`, `employeeId`, `companyId`, `fromDate`, `toDate`

### HTTP status codes
- `200` — OK (GET, PATCH success)
- `201` — Created (POST success)
- `204` — No Content (soft delete)
- `400` — Validation error / business rule violation
- `401` — Unauthenticated
- `403` — Forbidden (wrong role or wrong tenant)
- `404` — Not found
- `409` — Conflict (duplicate expense, state-transition not allowed)
- `422` — Policy BLOCKER violation
- `500` — Internal server error

---

## Roles & Permissions

| Role | Permissions |
|---|---|
| `EMPLOYEE` | Create / edit own expenses and reports; request advances; withdraw own reports |
| `MANAGER` | All EMPLOYEE permissions + approve / reject / send-back reports in their team |
| `FINANCE_CHECKER` | View all approved reports; verify, modify GL coding; send back |
| `FINANCE_PAYMENT_INITIATOR` | Mark reports as payment initiated / reimbursed |
| `FINANCE_ADMIN` | All finance roles + configure policy, masters, approval flows |
| `ADMIN` | Full access including org configuration |
| `SYSTEM` | Internal service-to-service calls (auto-approval, escalation jobs) |

RBAC middleware must check **both** the role AND that `company_id` matches.

---

## Coding Conventions

### TypeScript
- `strict: true` in tsconfig — no implicit `any`, no `!` non-null assertions.
- Prefer `type` over `interface` for plain data shapes; use `interface` for
  objects that may be extended.
- All service methods must return a typed result — never `Promise<any>`.
- Throw domain errors with a custom `AppError` class that carries `code`, `message`,
  `statusCode`, and optional `details`.

### Services
- One service per domain concept: `expenseService`, `reportService`,
  `approvalService`, `policyService`, `advanceService`, `auditService`.
- Services must NOT import Express types — keep them framework-agnostic.
- Every state transition must call `auditService.log(...)` **within the same
  Knex transaction**.

### Repositories
- All DB access goes through a repository. Controllers and services never call
  `knex` directly.
- Always accept `trx?: Knex.Transaction` as an optional parameter so callers
  can enrol repository calls in a transaction.
- Use `camelCase` in TypeScript; column names in the DB are `snake_case` — map
  in the repository layer.

### Migrations
- File naming: `YYYYMMDD_NNN_description.ts`
- Always implement both `up` and `down`.
- Never modify a committed migration — create a new one instead.
- All text JSON payloads (audit snapshots, policy `allowed_values`) are stored as
  `NVARCHAR(MAX)` in MSSQL. Serialize/deserialize in the repository, never in the service.

### Error handling
- Use a global Express error handler middleware (`src/api/middlewares/errorHandler.ts`).
- Never swallow errors silently — always log and rethrow or return a structured response.
- Zod parse errors should be caught and mapped to `400` with field-level detail.

### Testing
- Unit-test every service method with mocked repositories.
- Integration-test every API endpoint with Supertest against an in-memory
  SQLite database (Knex supports this for CI).
- Seed tests with factory helpers (`tests/factories/`) to avoid brittle hardcoded data.
- Every business-rule function must have at least one test for the happy path and
  one for each violation branch.

---

## Audit Logging

Every write operation must produce an `audit_events` row. Call
`auditService.log({ eventType, entityType, entityId, actorId, actorName, previousState, newState, changedFields })`.

Audit logging must run **inside the same database transaction** as the write it records.
Never log outside a transaction — partial writes with no audit trail are not acceptable.

History snapshots for expenses and reports go into `expense_history` and
`expense_report_history` (append-only, no ON DELETE FK).

---

## Finance Coding

Finance coding columns on `expenses` (prefixed `fc_`) map to:

| Column | Meaning |
|---|---|
| `fc_gl_account_code` | General Ledger account |
| `fc_cost_center_id` | Cost centre FK |
| `fc_project_id` | Project FK |
| `fc_department_id` | Department FK |
| `fc_wbs_element` | SAP WBS element |
| `fc_tax_code` | GST/VAT code |
| `fc_tax_amount` | Computed tax amount |
| `fc_is_gst_claimable` | Whether input GST is claimable |
| `fc_hsn` | HSN/SAC code for Indian GST |

Finance users may edit these fields during the Finance Approval stage.
Any change must be audited with `changed_fields` listing exactly which `fc_*`
columns were modified.

---

## Integration Points

- **OCR service** — receives receipt uploads, returns structured expense fields.
  The backend must handle partial OCR results gracefully (fill what was extracted,
  leave missing fields blank).
- **ERP / Payment system** — posts approved payment batches; receives payment
  confirmation webhooks to update `reimbursement_status` to `PAID`.
- **WhatsApp / Mobile** — submit expenses via webhook; treated identically to
  web-submitted expenses after normalization.
- **Currency rate provider** — rates are stored in `currencies` table; a scheduled
  job refreshes them daily. Never fetch live rates inside a request handler.

---

## What This Agent Does NOT Own

- Frontend / UI components → see `frontend.md`
- Documentation generation → see `docs.md`
- Infra / CI/CD / deployment → see `project-helper.md`

---

## Checklist Before Opening a PR

- [ ] All new service methods have unit tests
- [ ] All new endpoints have integration tests
- [ ] Every state transition writes an `audit_events` row in the same transaction
- [ ] `company_id` filter is present on every Knex query
- [ ] `is_deleted = false` filter is present on every read query
- [ ] Zod schema defined for every new request body
- [ ] New DB columns have a migration **and** a rollback `down` function
- [ ] `CHANGELOG.md` (if present) updated
