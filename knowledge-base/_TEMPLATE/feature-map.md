# Feature Map

## Purpose

This file describes product feature relationships so the Cursor QA agent can reason about **blast radius**, **regression scope**, and **integration risk**.

Use this file to answer:

- What does this feature depend on?
- What downstream features may break if this feature changes?
- What shared modules, APIs, jobs, reports, or services are involved?
- Which external services need timeout/down/error testing?
- Which areas require regression coverage?

This file is part of the product-specific knowledge base and should live at:

```text
knowledge-base/<QASE_PROJECT>/feature-map.md
```

---

## Cursor Agent Usage

Cursor QA rules should use this file during:

- requirements analysis
- test case generation
- edge case generation
- regression planning
- exploratory testing
- test case review
- severity classification
- bug reporting
- session reporting

Use only entries in the **Active Feature Map** table as authoritative product dependency knowledge.

Do not treat examples, drafts, or deprecated rows as real product dependencies.

---

## How This File Affects QA Behavior

When a feature is tested or changed, Cursor should:

1. Find the feature in the **Active Feature Map**.
2. Include all `Depends On` items as integration setup and failure-risk areas.
3. Include all `Used By` items as downstream regression-risk areas.
4. Include all `Shared Components` as cross-feature regression-risk areas.
5. Include all `External Services` in slow/down/timeout/error scenarios.
6. Include all `Data Stores / Tables` in DB validation and data-integrity checks.
7. Include all `Reports / Exports / Dashboards` in display and aggregation regression checks.
8. Include all `Known Risk Notes` in edge-case and exploratory testing.
9. If a dependency is missing or unclear, flag it as an ambiguity.

---

## Feature Status Definitions

| Status | Meaning | Agent Behavior |
|--------|---------|----------------|
| Active | Current product feature or dependency | Use for regression and dependency analysis |
| Draft | Proposed or partially confirmed dependency | Mention as possible risk, but mark confidence as Medium |
| Deprecated | Old feature or dependency no longer valid | Ignore unless testing legacy behavior |
| Conflict | Relationship conflicts with newer requirements or code | Flag in analysis and ask for clarification |
| Needs Verification | Believed true but not recently confirmed | Use cautiously and recommend confirmation |

---

## Feature ID Convention

Use stable IDs.

Recommended format:

```text
FEAT-[AREA]-[NNN]
```

Examples:

```text
FEAT-AUTH-001
FEAT-COMPANY-001
FEAT-JOBPOST-001
FEAT-INVOICE-001
FEAT-PROPOSAL-001
```

If your project already uses simple IDs like `FEAT-AUTH`, keep them to avoid broken references.

Never reuse an ID for a different feature.

---

## Active Feature Map

Add real product feature relationships here. Cursor treats rows with `Status = Active` as authoritative.

| Feature ID | Status | Feature Name | Owner / Module | Depends On | Used By | Shared Components | External Services | APIs / Jobs | Data Stores / Tables | Reports / Exports / Dashboards | Known Risk Notes | Last Verified |
|------------|--------|--------------|----------------|------------|---------|-------------------|-------------------|-------------|----------------------|-------------------------------|------------------|---------------|
| _Add feature ID_ | Draft | _Feature name_ | _Frontend / Backend / Batch / Module_ | _Upstream features, APIs, services_ | _Downstream features_ | _Reusable UI/API/shared modules_ | _3rd-party or internal external service_ | _Endpoints, jobs, queues_ | _Tables/entities_ | _Reports, exports, dashboards_ | _Regression or integration risks_ | _YYYY-MM-DD_ |

---

## Column Guidance

### Feature ID

Stable reference used by other knowledge-base files and QA reports.

Examples:

```text
FEAT-COMPANY-001
FEAT-JOBPOST-001
FEAT-INVOICE-001
```

### Status

Use one of:

```text
Active
Draft
Deprecated
Conflict
Needs Verification
```

### Feature Name

Human-readable feature or module name.

Examples:

```text
Company Management
Job Posting List
Proposal Status Dashboard
Combined Invoice Output
```

### Owner / Module

Where the feature primarily lives.

Examples:

```text
Frontend
Backend
Batch
Billing
Recruitment
Master Data
Shared UI
```

### Depends On

Upstream features, services, APIs, tables, or configuration required for this feature to work.

Examples:

```text
Authentication, Department Master, Company Master API
```

### Used By

Downstream features that may break if this feature changes.

Examples:

```text
Job Posting Registration, Proposal Status List, Invoice Output
```

### Shared Components

Reusable components or utilities that increase regression scope.

Examples:

```text
Status dropdown, pagination component, date formatter, file uploader
```

### External Services

Third-party or separately deployed services.

Examples:

```text
SendGrid, Stripe, AWS S3, OCR service, barcode service
```

If none:

```text
—
```

### APIs / Jobs

Relevant endpoints, background jobs, queue consumers, or batch processes.

Examples:

```text
GET /api/companies
POST /api/job-postings
invoice-output-batch
notification-sync-job
```

### Data Stores / Tables

Relevant DB tables, entities, or search indexes.

Examples:

```text
companies, job_postings, proposal_statuses, invoices
```

### Reports / Exports / Dashboards

Outputs that may be affected.

Examples:

```text
Proposal Status Dashboard, Invoice PDF, CSV export, search results list
```

### Known Risk Notes

Short QA-focused notes.

Examples:

```text
Status value changes affect dashboard counters and list filters.
Date formatting is shared across list, detail, and export.
```

### Last Verified

Date this relationship was last confirmed.

Use:

```text
YYYY-MM-DD
```

---

## Regression Scope Rules

When testing a feature, Cursor should generate regression coverage based on these relationships.

| Relationship | Regression Action |
|-------------|-------------------|
| Depends On | Verify upstream dependency works and failure is handled |
| Used By | Verify downstream feature still works after change |
| Shared Components | Verify other features using the component are not broken |
| External Services | Test slow, down, timeout, invalid response, and retry behavior |
| APIs / Jobs | Verify request/response, job selection, retries, logs, and rollback |
| Data Stores / Tables | Verify persistence, history, soft delete, uniqueness, and audit behavior |
| Reports / Exports / Dashboards | Verify totals, filters, formatting, and displayed values |

---

## Integration Failure Scenarios

For every external service or internal dependency, Cursor should consider:

- service unavailable
- slow response
- timeout
- invalid response shape
- 4xx error
- 5xx error
- partial success
- duplicate request
- retry behavior
- fallback behavior
- user-facing error message
- logging and alerting
- rollback or compensation logic
- stale cache after recovery

---

## Blast Radius Assessment

Use this guide when classifying risk.

| Blast Radius | Definition | QA Action |
|--------------|------------|-----------|
| Wide | Feature is used by many modules or critical flows | Expand regression scope and prioritize P1/P2 cases |
| Moderate | Feature affects one or more important downstream areas | Add targeted regression coverage |
| Narrow | Feature affects isolated or low-traffic area | Keep regression focused |
| Unknown | Dependency relationship is unclear | Flag ambiguity and inspect code or ask for confirmation |

---

## Dependency Conflict Handling

If repository code, requirements, or tickets contradict this file:

1. Do not silently choose one source.
2. Flag the mismatch in requirements analysis or review output.
3. Cite the affected `Feature ID`.
4. Mark related regression coverage as conditional.
5. Recommend updating this file after clarification.

Example:

```text
Conflict: feature-map says FEAT-COMPANY-001 is used by Proposal Status List, but current implementation no longer references Company data. Confirm whether the feature-map entry should be deprecated.
```

---

## Missing Dependency Handling

If Cursor discovers a dependency that is not documented here:

1. Use it for the current session if supported by code or requirements.
2. Mark it as a discovered dependency.
3. Recommend adding it to the **Active Feature Map** during session reporting.
4. Do not automatically treat undocumented dependency behavior as a confirmed business rule.

Example:

```text
Discovered dependency: Job Posting List uses Department Master labels for filtering.
Recommendation: Add Department Master to `Depends On` for FEAT-JOBPOST-001.
```

---

## Example Feature Relationships

The examples below show the expected writing style only.

Do **not** treat these as active product dependencies unless they are copied into the **Active Feature Map** table with `Status = Active`.

```markdown
| Feature ID | Status | Feature Name | Owner / Module | Depends On | Used By | Shared Components | External Services | APIs / Jobs | Data Stores / Tables | Reports / Exports / Dashboards | Known Risk Notes | Last Verified |
|------------|--------|--------------|----------------|------------|---------|-------------------|-------------------|-------------|----------------------|-------------------------------|------------------|---------------|
| FEAT-AUTH-001 | Active | Authentication and Sessions | Backend / Frontend | Identity provider | All authenticated modules | Route guard, session timeout handler | Auth provider | POST /auth/login, POST /auth/logout | users, sessions | Login audit report | Auth changes have wide regression impact across all authenticated pages. | 2026-07-08 |
| FEAT-COMPANY-001 | Active | Company Management | Recruitment / Master Data | Authentication, Department Master | Job Posting Registration, Proposal Status, Invoice Output | Form layout, detail screen component, pagination | — | GET /companies, POST /companies, PATCH /companies/:id | companies, company_contracts | Company CSV export | Company field changes often affect list, detail, registration, edit, and export screens. | 2026-07-08 |
| FEAT-INVOICE-001 | Active | Combined Invoice Output | Billing | Customer Master, Payment Method Rules, Sales Records | Delivery Note Output, Billing Reports | PDF renderer, barcode renderer | Barcode/OCR provider | invoice-output-batch | invoices, delivery_notes, customers | Invoice PDF, Delivery Note PDF | Payment method and billed amount rules affect PDF lower-half rendering. | 2026-07-08 |
```

---

## Maintenance Rules

When updating this file:

- add only confirmed or strongly evidenced relationships
- keep examples outside the active table
- keep each feature entry scoped and readable
- use stable feature IDs
- avoid duplicate feature IDs
- mark old relationships as `Deprecated` instead of deleting when history matters
- mark unresolved contradictions as `Conflict`
- include `Last Verified`
- update related test cases when a feature dependency changes
- update this file during session closeout when a new dependency is confirmed

---

## Change Log

| Date | Change | Source |
|------|--------|--------|
| _YYYY-MM-DD_ | _Describe added/updated/deprecated feature relationship_ | _Jira / session / code inspection / stakeholder_ |
