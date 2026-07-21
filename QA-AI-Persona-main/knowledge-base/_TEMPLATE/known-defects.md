# Known Defects and Historical Weak Spots

## Purpose

This file records confirmed product defects, intermittent issues, fixed defects that require regression checks, and historical weak spots.

Cursor QA uses this file for two major purposes:

1. **Probe harder** in areas that have broken before.
2. **Avoid duplicate bug reports** by matching new observations against known symptoms.

This file is part of the product-specific knowledge base and should live at:

```text
knowledge-base/<QASE_PROJECT>/known-defects.md
```

---

## Cursor Agent Usage

Use this file during:

- requirements analysis
- edge case generation
- exploratory testing
- test execution
- severity classification
- bug reporting
- test case review
- session reporting

Cursor should use only entries in the **Known Defect Registry** table as active defect knowledge.

Do not treat examples, notes, drafts, or placeholders as real defects.

---

## How This File Changes QA Behavior

When testing a feature, Cursor should:

1. Search this file for defects in the same `Area`, `Feature ID`, `Flow ID`, API, or screen.
2. Generate extra exploratory and edge cases for `Open`, `Intermittent`, and `Needs Retest` entries.
3. Add regression checks for `Fixed` entries.
4. Before filing a new Jira bug, compare the new failure to existing `Symptom Signature`, `Failure Condition`, and `Deduplication Rule`.
5. If the new failure matches an existing defect, reference the existing `Ref` instead of creating a duplicate.
6. If a similar issue does not fully match, create a new defect and mention the related known defect.
7. During session closeout, add newly confirmed defects to this file.

---

## Status Definitions

| Status | Meaning | Cursor QA Behavior |
|--------|---------|--------------------|
| Open | Confirmed defect still unresolved | Probe actively; deduplicate matching failures |
| Intermittent | Confirmed issue occurs inconsistently | Probe repeatedly; capture trace/logs; deduplicate if symptom matches |
| Fixed | Defect was fixed | Add regression verification to confirm the fix still holds |
| Needs Retest | Fix is available but QA has not verified it yet | Prioritize retest and update status after verification |
| Reopened | Previously fixed issue reproduced again | Escalate as regression; link original Jira key |
| Won't Fix | Product/team accepted the behavior | Do not file duplicate unless behavior changed or scope expanded |
| Duplicate | Defect is tracked under another reference | Link to canonical `Ref` |
| Environment | Known environment-only issue | Mark execution as blocked when encountered; do not file product bug |
| Obsolete | No longer relevant due to removed feature or redesign | Ignore unless testing legacy behavior |

---

## Severity / Priority Guidance

Use the latest classification from Jira if available.

If not available, Cursor should classify using:

```text
10-severity-classification.mdc
```

Guidance:

| Known Defect Type | Typical QA Handling |
|-------------------|---------------------|
| Security, auth, tenant, PII | High-priority probing and deduplication |
| Data loss, corruption, wrong save | High-priority regression and DB validation |
| Intermittent API failure | Repeat execution, collect network logs and traces |
| UI-only fixed defect | Add focused regression check |
| Environment-only issue | Mark blocked; do not create product bug |
| Won't Fix | Document as known issue; do not fail unless acceptance changed |

---

## Known Defect Registry

Add real known defects here.

Cursor treats rows in this table as product memory.

| Ref | Status | Severity | Priority | Area | Feature ID / Flow ID | Symptom Signature | Failure Condition / Trigger | Expected Handling | Deduplication Rule | Regression Probe | Evidence / Link | Last Seen | Notes for Cursor |
|-----|--------|----------|----------|------|----------------------|-------------------|-----------------------------|-------------------|--------------------|------------------|-----------------|-----------|------------------|
| _Add Jira key_ | Open | _Critical/High/Medium/Low_ | _High/Medium/Low_ | _Feature/screen/API_ | _FEAT-xx / FLOW-xx_ | _Short symptom used for matching_ | _Condition where it appears_ | _What QA should do when seen_ | _When to treat as same defect_ | _Retest or edge case to add_ | _Jira/evidence path_ | _YYYY-MM-DD_ | _Agent-specific note_ |

---

## Column Guidance

### Ref

Use the canonical Jira key or internal defect ID.

Examples:

```text
PROJ-321
BUG-1042
KNOWN-AUTH-001
```

If the issue is a duplicate, use the duplicate issue key and mention the canonical key in `Notes for Cursor`.

### Status

Use one of:

```text
Open
Intermittent
Fixed
Needs Retest
Reopened
Won't Fix
Duplicate
Environment
Obsolete
```

### Severity

Use one of:

```text
Critical
High
Medium
Low
Trivial
N/A
```

Use `N/A` for environment-only issues, test-data issues, or non-product defects.

### Priority

Use one of:

```text
High
Medium
Low
N/A
```

Use `N/A` when the issue is not a product defect.

### Area

Use the affected product area, screen, API, batch job, report, or module.

Examples:

```text
Company Registration
Proposal Status List
Job Posting Search
Invoice PDF Output
POST /api/companies
notification-sync-job
```

### Feature ID / Flow ID

Reference IDs from other knowledge-base files when available.

Examples:

```text
FEAT-COMPANY-001
FLOW-COMPANY-REG-001
FEAT-INVOICE-001 / FLOW-INVOICE-OUTPUT-001
```

If no ID exists yet, write:

```text
Unmapped
```

and recommend adding the feature/flow during session closeout.

### Symptom Signature

Write the shortest reliable symptom used for deduplication.

Good:

```text
Basic Contract row is blank after saving selections
Search pagination resets to page 1 after applying status filter
Duplicate SendGrid email request sent for one sharing action
```

Bad:

```text
Bug happens
Screen broken
Does not work
```

### Failure Condition / Trigger

Describe when the issue appears.

Examples:

```text
Occurs when user selects multiple Basic Contract values and opens Company Details.
Occurs after deleting a document from page 2 of the document list.
Occurs intermittently when network latency is above 2 seconds.
```

### Expected Handling

Tell Cursor what to do when the symptom appears.

Examples:

```text
If reproduced, reference this defect instead of filing a duplicate.
If reproduced after fix, classify as regression and reopen.
If seen only in staging with SMTP disabled, mark related tests as blocked.
```

### Deduplication Rule

Define when a new observation should be considered the same defect.

Examples:

```text
Same defect if Company Details omits any selected Basic Contract values after successful save.
Same defect if duplicate email requests are sent for a single Share action.
Not same defect if the email is sent once but the recipient is wrong.
```

### Regression Probe

Define the test Cursor should add or run.

Examples:

```text
Register company with 派遣 and 紹介 selected, then verify details display 派遣、紹介.
Delete a record from page 2 and verify list refreshes without manual reload.
Submit upload at 8 MB, 9 MB, 10 MB, and 10 MB + 1 byte.
```

### Evidence / Link

Use:

- Jira link or key
- Qase TC ID
- session artifact path
- screenshot path
- trace path
- network log path

Avoid sensitive data.

### Last Seen

Use:

```text
YYYY-MM-DD
```

Update this when the defect is reproduced, verified fixed, or reopened.

### Notes for Cursor

Add concise agent guidance.

Examples:

```text
Generate targeted edge cases near file size boundary.
Check network log for duplicate POST requests.
Treat as environment blocker if SMTP is disabled in staging.
Do not file duplicate; comment on PROJ-203 with new evidence.
```

---

## Defect Matching Rules

Before filing a new bug, Cursor should compare the new issue against this file.

Match using:

- same screen or API
- same user action
- same symptom
- same error message
- same network/API status
- same data impact
- same affected role
- same feature/flow ID
- same known trigger

### Strong Match

If symptom and trigger match:

```text
Do not create a new Jira bug.
Reference the existing Ref.
Attach or note new evidence if available.
```

### Partial Match

If symptom is similar but scope differs:

```text
Create a new defect if behavior is materially different.
Mention the related known defect in the report.
```

### No Match

If no similar defect exists:

```text
File a new bug if Actual Result and Expected Result are clear.
Add it to this file during session closeout after Jira key is available.
```

---

## Regression Rules by Status

| Status | Regression Action |
|--------|-------------------|
| Open | Probe actively; do not duplicate if reproduced |
| Intermittent | Repeat with evidence capture; record reproduction frequency |
| Fixed | Add regression case to verify fix still holds |
| Needs Retest | Execute retest before broader regression if in scope |
| Reopened | Escalate as regression; check release impact |
| Won't Fix | Do not fail current session unless requirement changed |
| Duplicate | Use canonical reference |
| Environment | Mark affected tests blocked with environment reason |
| Obsolete | Ignore unless legacy scope is requested |

---

## Example Known Defects

The examples below show the expected writing style only.

Do **not** treat these as active known defects unless they are copied into the **Known Defect Registry** table with the correct status.

```markdown
| Ref | Status | Severity | Priority | Area | Feature ID / Flow ID | Symptom Signature | Failure Condition / Trigger | Expected Handling | Deduplication Rule | Regression Probe | Evidence / Link | Last Seen | Notes for Cursor |
|-----|--------|----------|----------|------|----------------------|-------------------|-----------------------------|-------------------|--------------------|------------------|-----------------|-----------|------------------|
| PROJ-321 | Intermittent | High | Medium | Document Upload | FEAT-DOCS-001 / FLOW-DOC-UPLOAD-001 | Uploads above 8 MB intermittently time out | Uploading files from 8 MB to 10 MB during slow network | If reproduced, link PROJ-321 and attach trace/network log | Same if timeout occurs before 10 MB limit during upload | Test 8 MB, 9 MB, 10 MB, and 10 MB + 1 byte | Jira PROJ-321 | 2026-07-08 | Add boundary cases around file size and slow network. |
| PROJ-256 | Fixed | Medium | Medium | Document List | FEAT-DOCS-001 | List does not refresh after delete | Deleting a document from the list | Verify fix still holds in regression | Same if deleted row remains visible until manual reload | Delete record and confirm table refreshes automatically | Jira PROJ-256 | 2026-07-08 | Add as fixed-defect regression case. |
```

---

## Session Closeout Update Rules

During session reporting, update this file when:

- a new confirmed Jira bug was filed
- an intermittent known defect was reproduced
- a fixed defect was verified
- a fixed defect regressed
- an environment issue blocked testing repeatedly
- a known defect was closed, reopened, or marked won't fix

### Add New Confirmed Defect

After filing a new Jira bug, add a row:

```markdown
| PROJ-999 | Open | High | Medium | Company Details | FEAT-COMPANY-001 / FLOW-COMPANY-DETAILS-001 | Basic Contract row is blank after saving selections | Multiple Basic Contract values are selected during registration | Reference PROJ-999 if same display omission recurs | Same if details screen omits saved Basic Contract values | Select 派遣 and 紹介, save, verify detail display | Jira PROJ-999 | 2026-07-08 | Include details-screen regression when Company layout changes. |
```

### Update Existing Defect

When a defect is reproduced:

- keep the original `Ref`
- update `Last Seen`
- add evidence path or session note
- do not create duplicate rows

When a fixed defect passes regression:

- keep status as `Fixed`
- update `Last Seen`
- note verification result in `Notes for Cursor`

When a fixed defect fails again:

- change status to `Reopened`
- add the new Jira key or evidence in `Notes for Cursor`

---

## Maintenance Rules

When maintaining this file:

- add only confirmed, useful defect knowledge
- keep examples outside the active registry
- use stable references
- keep symptoms short and searchable
- keep deduplication rules clear
- remove sensitive data from evidence and notes
- do not store credentials, tokens, cookies, or production PII
- do not create duplicate rows for the same issue
- mark obsolete entries instead of deleting if history is useful
- update `Last Seen` when status changes
- prefer Jira keys over informal IDs
- keep `Notes for Cursor` actionable

---

## Change Log

| Date | Change | Source |
|------|--------|--------|
| _YYYY-MM-DD_ | _Describe added/updated/reopened/verified defect_ | _Jira / Qase / session / stakeholder_ |
