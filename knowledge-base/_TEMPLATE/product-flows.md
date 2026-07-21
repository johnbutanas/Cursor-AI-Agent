# Product Flows

## Purpose

This file documents real, verified user and system flows so the Cursor QA agent can create accurate end-to-end, smoke, happy-path, regression, and exploratory test coverage.

Cursor uses this file to avoid guessing navigation or workflow order.

This file is part of the product-specific knowledge base and should live at:

```text
knowledge-base/<QASE_PROJECT>/product-flows.md
```

---

## Cursor Agent Usage

Use this file during:

- requirements analysis
- acceptance criteria parsing
- test case generation
- edge case generation
- exploratory testing
- test case review
- test execution
- bug reporting
- session reporting

Cursor should use only entries in the **Active Product Flows** section as authoritative product flow knowledge.

Do not treat examples, drafts, or placeholders as real product flows.

---

## How This File Changes QA Behavior

When a feature is tested, Cursor should:

1. Find matching flows by `Flow ID`, `Feature ID`, screen, route, actor, or business process.
2. Use documented steps for happy-path and smoke test scenarios.
3. Cite relevant `FLOW-xx` IDs in requirements analysis, test cases, and regression reports.
4. Avoid inventing navigation that conflicts with active flows.
5. Use related business rules to generate positive and negative validation cases.
6. Use related feature-map IDs to identify regression scope.
7. Use related known defects to add regression and exploratory probes.
8. Flag a conflict if new requirements contradict an active flow.
9. Recommend adding newly verified flows during session closeout.

---

## Flow Status Definitions

| Status | Meaning | Cursor QA Behavior |
|--------|---------|--------------------|
| Active | Verified current product flow | Use as authoritative navigation and expected workflow |
| Draft | Proposed or partially confirmed flow | Use cautiously and mark confidence as Medium |
| Needs Verification | Believed true but not recently verified | Use as guidance, but recommend confirmation |
| Deprecated | Old flow no longer valid | Ignore unless testing legacy behavior |
| Conflict | Flow conflicts with newer requirement or implementation | Flag and ask for clarification |
| Blocked | Flow exists but cannot currently be executed due to environment/data issue | Mark related tests blocked when applicable |

---

## Flow ID Convention

Use stable IDs.

Recommended format:

```text
FLOW-[AREA]-[NNN]
```

Examples:

```text
FLOW-AUTH-001
FLOW-COMPANY-001
FLOW-JOBPOST-001
FLOW-INVOICE-001
FLOW-PROPOSAL-001
```

If your project already uses simple IDs like `FLOW-01`, keep them to avoid broken references.

Never reuse an ID for a different flow.

---

## Active Product Flows

Add real verified product flows here.

Each flow should be atomic enough to become one or more E2E test cases.

---

### FLOW-[AREA]-[NNN] — [Flow Name]

| Field | Value |
|------|-------|
| Status | Draft |
| Actor | _User role/persona_ |
| Entry Point | _URL, screen, menu path, button, API trigger, or batch trigger_ |
| Feature ID | _FEAT-xx or Unmapped_ |
| Related Rules | _BR-xx or N/A_ |
| Related Known Defects | _Jira keys or N/A_ |
| Data Preconditions | _Required data, account state, feature flag, permissions_ |
| Environment Preconditions | _External service, config, batch schedule, browser/device if relevant_ |
| Exit / Success State | _Observable successful outcome_ |
| Last Verified | _YYYY-MM-DD_ |

#### Steps

1. _Step 1 using exact UI label or system event_
2. _Step 2_
3. _Step 3_

#### Alternate Paths

- _Optional alternate route or branch, if verified_

#### Failure / Recovery Expectations

- _Expected behavior when required validation, API, or dependency fails_

#### QA Notes

- _Regression, exploratory, edge-case, or automation notes_

---

## Flow Writing Standards

Each flow must be:

- real — verified from app, code, Jira, product docs, or stakeholder confirmation
- ordered — steps are in execution sequence
- role-aware — actor and permissions are clear
- observable — exit/success state is measurable
- testable — QA can convert it into steps and expected results
- traceable — related feature IDs and business rules are linked where known
- scoped — one flow should not contain multiple unrelated business processes

Avoid vague steps such as:

```text
User completes the form.
System processes the data.
User checks the result.
```

Prefer executable steps such as:

```text
User selects `派遣` and `紹介` in the Basic Contract field.
User clicks the Save button.
System displays the Company Details screen.
The Basic Contract row displays `派遣、紹介`.
```

---

## Field Guidance

### Status

Use one of:

```text
Active
Draft
Needs Verification
Deprecated
Conflict
Blocked
```

### Actor

Specify the persona or role.

Examples:

```text
Guest user
Authenticated user
Admin user
Agent
Applicant
Billing user
Department manager
Batch process
External API client
```

### Entry Point

Use the exact starting point.

Examples:

```text
/login
Company List > Register button
Proposal Status List > Status dropdown
Job Posting Details > Edit button
invoice-output-batch scheduled job
POST /api/companies
```

### Feature ID

Reference `feature-map.md` when possible.

Examples:

```text
FEAT-COMPANY-001
FEAT-AUTH-001
FEAT-INVOICE-001
```

Use `Unmapped` if the feature has not been added to `feature-map.md`.

### Related Rules

Reference `business-rules.md`.

Examples:

```text
BR-COMPANY-001
BR-AUTH-002
```

Use `N/A` if no rule exists.

### Related Known Defects

Reference `known-defects.md`.

Examples:

```text
PROJ-321
BUG-1042
```

Use `N/A` if none.

### Data Preconditions

List required test data or system state, not real credentials.

Examples:

```text
A company record exists.
User has Admin role.
At least 21 records exist to verify pagination.
Feature flag `basic-contract` is enabled.
```

### Environment Preconditions

List dependency or environment needs.

Examples:

```text
SMTP sandbox is enabled.
Payment provider test mode is available.
Browser viewport is 1366x768.
Batch job is enabled in staging.
```

### Exit / Success State

State how QA knows the flow completed.

Examples:

```text
The saved company appears in the Company List.
The details screen displays the selected Basic Contract values.
The generated invoice PDF downloads successfully.
The status history row is added with today's date.
```

---

## Flow-to-Test Expectations

For each `Active` flow, Cursor should generate or verify:

| Test Type | Required Coverage |
|-----------|-------------------|
| Smoke | The flow can be accessed and completed at basic level |
| Happy Path | Primary actor completes the flow successfully |
| Negative | Required validation or blocked action is enforced |
| Edge | Boundary/state cases related to the flow |
| Permission | Unauthorized role cannot perform restricted actions |
| Integration | Dependencies are slow/down/invalid where applicable |
| DB/API | Saved or returned data is correct when persistence/API is involved |
| Regression | Related `Used By` features from `feature-map.md` still work |

---

## Flow Conflict Handling

If a new requirement, implementation, or app behavior contradicts an active flow:

1. Do not silently choose one source.
2. Flag the contradiction in requirements analysis or review output.
3. Cite the affected `Flow ID`.
4. Mark related test cases as conditional or blocked if expected behavior is unclear.
5. Recommend updating this file after clarification.

Example:

```text
Conflict: FLOW-COMPANY-001 says Company Registration redirects to Company Details after save, but the new Jira requirement says it should return to Company List. Confirm expected navigation before finalizing test cases.
```

---

## Missing Flow Handling

If Cursor discovers a real flow that is not documented:

1. Use the discovered flow in the current session if supported by app/code/Jira evidence.
2. Mark it as a discovered flow.
3. Recommend adding it to this file during session reporting.
4. Do not treat the missing flow as a product defect by itself.

Example:

```text
Discovered flow: Proposal Status can be updated directly from the list via the pinned Status dropdown.
Recommendation: Add this as FLOW-PROPOSAL-STATUS-001.
```

---

## Regression and Dependency Use

When a flow references a `Feature ID`, Cursor should check `feature-map.md` for:

- `Depends On`
- `Used By`
- `Shared Components`
- `External Services`
- `APIs / Jobs`
- `Data Stores / Tables`
- `Reports / Exports / Dashboards`

Use these to expand regression coverage.

---

## Automation Guidance

Mark flows as good automation candidates when they are:

- stable
- high priority
- run frequently
- critical to release
- not heavily dependent on unstable external systems
- possible to assert with clear UI/API/DB outcomes

Recommended automation labels:

```text
Automation Candidate: Yes / No / Later
Suggested Layer: E2E / API / DB / Unit / Hybrid
```

Add this information in `QA Notes` when useful.

---

## Example Product Flows

The examples below show the expected writing style only.

Do **not** treat these as active product flows unless they are copied into the **Active Product Flows** section with `Status = Active`.

### FLOW-AUTH-001 — User Login

| Field | Value |
|------|-------|
| Status | Active |
| Actor | Registered user |
| Entry Point | `/login` |
| Feature ID | FEAT-AUTH-001 |
| Related Rules | BR-AUTH-001 |
| Related Known Defects | N/A |
| Data Preconditions | A registered active user account exists. |
| Environment Preconditions | Authentication service is available. |
| Exit / Success State | User lands on `/dashboard` and their name is shown in the header. |
| Last Verified | 2026-07-08 |

#### Steps

1. User navigates to `/login`.
2. User enters a valid email address in the Email field.
3. User enters a valid password in the Password field.
4. User clicks the Sign In button.
5. System validates credentials.
6. System redirects the user to the dashboard.

#### Alternate Paths

- User is redirected to their originally requested page after login, if applicable.

#### Failure / Recovery Expectations

- Invalid credentials show an authentication error and do not create a session.
- Expired session redirects to login with a session-expired notice.

#### QA Notes

- Good smoke and E2E automation candidate.

---

### FLOW-DOCS-001 — Document Upload

| Field | Value |
|------|-------|
| Status | Active |
| Actor | Authenticated user with Editor role |
| Entry Point | `/documents` > Upload Document button |
| Feature ID | FEAT-DOCS-001 |
| Related Rules | BR-DOCS-001, BR-DOCS-002 |
| Related Known Defects | PROJ-321 |
| Data Preconditions | User has Editor role. |
| Environment Preconditions | File storage service is available. |
| Exit / Success State | Uploaded file is visible in the document list with a Processing complete badge. |
| Last Verified | 2026-07-08 |

#### Steps

1. User navigates to `/documents`.
2. User clicks the Upload Document button.
3. User selects a valid file from the file picker.
4. User clicks the Save button.
5. System validates file type and size.
6. System uploads and stores the file.
7. File appears at the top of the document list.

#### Alternate Paths

- User cancels file selection and no upload occurs.

#### Failure / Recovery Expectations

- Unsupported file types are rejected.
- Files over the size limit are rejected.
- Storage timeout shows a recoverable error without creating a broken file record.

#### QA Notes

- Add boundary tests around file size and slow storage response.

---

## Session Closeout Update Rules

During session reporting, update this file when:

- a new verified user flow was exercised
- a previously draft flow was confirmed
- a flow changed due to a new requirement
- a flow was found to be deprecated
- implementation contradicts documented flow
- a new alternate path was discovered
- a new recovery behavior was confirmed

### Add New Flow

Add a new flow only when the behavior is confirmed by:

- Jira requirement
- acceptance criteria
- app execution
- repository implementation
- stakeholder clarification
- test execution result

### Update Existing Flow

When updating an existing flow:

- keep the same `Flow ID`
- update `Last Verified`
- add source/context in `QA Notes`
- mark `Status = Conflict` if expected behavior is unresolved
- mark `Status = Deprecated` if the flow no longer applies

---

## Maintenance Rules

When maintaining this file:

- add only confirmed or clearly sourced flows
- keep examples outside the active flow section
- keep one flow focused on one business process
- use exact UI labels when known
- use stable flow IDs
- include related business rules and feature IDs
- avoid real credentials or personal data
- update `Last Verified`
- do not delete old flows if history is useful; mark them Deprecated
- record unresolved contradictions as Conflict
- update related test cases when active flows change

---

## Change Log

| Date | Change | Source |
|------|--------|--------|
| _YYYY-MM-DD_ | _Describe added/updated/deprecated flow_ | _Jira / app execution / code inspection / stakeholder_ |
