# Business Rules

## Purpose

This file is the product-specific source of truth for what the application **allows, forbids, calculates, displays, or enforces**.

Cursor QA rules use this file as the **bug-vs-intended-behavior oracle**.

If observed behavior contradicts an `Active` rule in this file, treat it as a confirmed defect.
If observed behavior looks unusual but matches an `Active` rule in this file, do not file a bug.
If a new requirement contradicts an `Active` rule, flag the conflict in requirements analysis instead of silently choosing one.

---

## Cursor Agent Usage

Use this file during:

- requirements analysis
- acceptance criteria parsing
- test case generation
- edge case generation
- test case review
- test execution
- severity classification
- bug reporting
- session reporting

Cursor should use only rules in the **Active Business Rules** table below.

Do not treat examples, drafts, notes, or deprecated rules as authoritative product behavior.

---

## Rule Status Definitions

| Status | Meaning | Agent Behavior |
|--------|---------|----------------|
| Active | Approved product rule | Use as authoritative expected behavior |
| Draft | Proposed or unconfirmed rule | Mention as possible requirement, but do not use as defect oracle |
| Deprecated | Old rule no longer valid | Ignore unless checking legacy behavior |
| Conflict | Rule currently conflicts with a newer requirement | Flag in analysis and ask for clarification |
| Needs Verification | Believed true but not recently confirmed | Use with caution and mark confidence as Medium |

---

## Rule ID Convention

Use stable IDs.

Recommended format:

```text
BR-[AREA]-[NNN]
```

Examples:

```text
BR-AUTH-001
BR-COMPANY-001
BR-JOBPOST-001
BR-INVOICE-001
BR-PROPOSAL-001
```

If the project already uses simple IDs like `BR-01`, keep using that format to avoid broken references.

Never reuse an ID after deleting or replacing a rule.

---

## Rule Writing Standards

Each rule must be:

- atomic — one behavior only
- testable — QA can verify it through UI, API, DB, or logs
- observable — expected result is visible or measurable
- enforceable — violation behavior is clear
- scoped — applies to a known feature, flow, role, API, or screen
- sourced — linked to Jira, PRD, AC, design, stakeholder decision, or observed enforcement

Avoid vague rules such as:

```text
The page should load quickly.
The form should work correctly.
The user should see the right message.
```

Prefer measurable rules such as:

```text
Uploaded files larger than 10 MB must be rejected before submission.
The Company Details screen must display selected Basic Contract values as comma-separated Japanese labels.
```

---

## Active Business Rules

Add real product rules here. Cursor treats rows with `Status = Active` as authoritative.

| Rule ID | Status | Rule | Applies To | Enforcement Layer | On Violation / Expected Behavior | Source | Last Verified |
|---------|--------|------|------------|-------------------|----------------------------------|--------|---------------|
| _Add rule ID_ | Draft | _Write one atomic, testable rule_ | _FEAT / FLOW / screen / API_ | UI / API / DB / Batch / Report | _Expected behavior when rule is violated_ | _Jira / AC / PRD / stakeholder / observed_ | _YYYY-MM-DD_ |

---

## Validation and Enforcement Guidance

Use the `Enforcement Layer` column consistently.

| Layer | Meaning | QA Verification |
|-------|---------|-----------------|
| UI | Browser/client-side behavior | Verify field validation, messages, disabled states, display logic |
| API | Backend request handling | Verify status code, response body, auth, validation, and error handling |
| DB | Persistence and data integrity | Verify saved values, constraints, audit fields, soft delete, history |
| Batch | Scheduled/background process | Verify job selection, processing result, logs, retries, rollback |
| Report | Export, PDF, dashboard, or list output | Verify generated output, formatting, filters, totals, visibility |
| Integration | External service or internal service boundary | Verify timeout, failure, retry, fallback, and reconciliation behavior |

When a rule crosses multiple layers, list all relevant layers:

```text
UI / API / DB
```

---

## Rule-to-Test Expectations

For each `Active` rule, Cursor should generate or verify at minimum:

| Test Type | Required Coverage |
|-----------|-------------------|
| Positive | Rule is followed and expected behavior succeeds |
| Negative | Rule is violated and expected enforcement occurs |
| Boundary | Limits are tested where the rule contains a threshold |
| Permission | Role/ownership enforcement is tested where the rule mentions users, departments, tenants, or access |
| API | Backend enforcement is tested when data crosses server boundary |
| DB | Persistence is tested when the rule changes saved data |
| Regression | Related flows from `feature-map.md` are checked |

---

## Conflict Handling

If a session requirement conflicts with an active business rule:

1. Do not silently choose either behavior.
2. Flag the contradiction in the requirements analysis.
3. Cite the affected `Rule ID`.
4. Mark related test cases as blocked or conditional if expected behavior cannot be confirmed.
5. Ask for product clarification.

Example:

```text
Conflict: Jira AC says Basic Contract is mandatory, but BR-COMPANY-002 says it is optional.
Clarification required before finalizing validation test cases.
```

---

## Missing Rule Handling

If behavior is observed but no rule covers it:

- do not classify it as a confirmed business-rule defect
- classify confidence as `Suspected` or `Strong` depending on evidence
- recommend adding a rule if the behavior is important or repeatedly tested

Example:

```text
Observed: System allows duplicate company names.
No active business rule defines whether company names must be unique.
Recommendation: Confirm expected behavior and add a business rule if uniqueness matters.
```

---

## Example Rules

The examples below show the expected writing style only.

Do **not** treat these as active product rules unless they are copied into the **Active Business Rules** table with `Status = Active`.

```markdown
| Rule ID | Status | Rule | Applies To | Enforcement Layer | On Violation / Expected Behavior | Source | Last Verified |
|---------|--------|------|------------|-------------------|----------------------------------|--------|---------------|
| BR-AUTH-001 | Active | Passwords must be at least 8 characters and include one number and one symbol. | FEAT-AUTH / Registration / Password Reset | UI / API | Block submission and show an inline password validation error. | Jira AUTH-123 AC1 | 2026-07-08 |
| BR-DOCS-001 | Active | Only PDF and DOCX files may be uploaded. | FEAT-DOCS / FLOW-DOC-UPLOAD | UI / API | Reject the file and show `Only PDF and DOCX files are allowed`. | PRD Upload Rules | 2026-07-08 |
| BR-DOCS-002 | Active | Uploaded files must be 10 MB or smaller. | FEAT-DOCS / FLOW-DOC-UPLOAD | UI / API | Reject the file and show `File size exceeds the 10 MB limit`. | PRD Upload Rules | 2026-07-08 |
| BR-PERM-001 | Active | Only Admin users may delete an account. | Account Management / Delete Account | UI / API | Hide or disable the Delete Account action for non-admin users and return 403 if forced by API. | Security Requirement SEC-01 | 2026-07-08 |
```

---

## Maintenance Rules

When updating this file:

- add only confirmed product behavior
- keep each rule atomic
- include the source
- include last verified date
- do not overwrite old rules without reason
- mark replaced rules as `Deprecated`
- mark unresolved contradictions as `Conflict`
- keep examples outside the Active Business Rules table
- update affected test cases when an active rule changes

---

## Change Log

| Date | Change | Source |
|------|--------|--------|
| _YYYY-MM-DD_ | _Describe added/updated/deprecated rule_ | _Jira / session / stakeholder_ |
