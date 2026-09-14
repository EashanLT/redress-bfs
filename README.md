# Redress — Card Dispute & Fraud Resolution

A single-page web app modeling a **BFS card-dispute / fraud-resolution
case-filing flow** for a fictional bank (Meridian Card Services), plus a
**TestMu AI Kane CLI assurance pipeline** that tests it against the live
deployed app and produces coverage and evidence.

The whole flow lives on one scrolling page: sections unlock progressively
as prior ones are completed, and a sticky sidebar shows the case's
computed priority and provisional credit live.

## Repository layout

```
redress-bfs/
├── index.html                       # the app (served by GitHub Pages)
├── requirements/
│   └── redress-prd.md               # the PRD — source of truth for the tests
├── tests/
│   └── *_test.md                    # Kane assurance tests
└── .github/workflows/
    └── assurance.yml                # CI: runs the suite, reports coverage + evidence
```

## Live app

Already deployed via GitHub Pages at:
```
https://eashanlt.github.io/redress-bfs/
```
This is the URL every test in `tests/` points at.

## What the app does

Five sections, unlocking in order as each is completed:

1. **Verify** — card last 4 digits + billing ZIP.
2. **Select transaction** — pick the disputed charge from recent activity
   (only transactions from the last 90 days are selectable).
3. **Categorize** — pick a dispute reason; reason-specific fields (delivery
   date, cancellation reference, secondary identity verification) appear
   only when relevant.
4. **Describe** — a free-text account of what happened.
5. **Review & submit** — see the computed priority and provisional credit,
   then submit to receive a case reference.

## Business rules (acceptance criteria)

| ID | Rule |
|----|------|
| AC-1 | Provisional credit only for fraud disputes filed within 60 days of the transaction date. |
| AC-2 | Disputes over $500 require secondary verification (last 4 of SSN) before continuing. |
| AC-3 | Goods Not Received requires an expected delivery date already in the past. |
| AC-4 | Cancelled Subscription requires a non-empty cancellation confirmation number. |
| AC-5 | Case priority is High if amount > $1,000 or reason is fraud; otherwise Standard. |
| AC-6 | Provisional credit equals the disputed amount, capped at $1,000. |
| AC-7 | A case reference is issued only after full validation at submission. |
| AC-8 | Only transactions from the last 90 days are selectable for a dispute. |

## Test suite

`tests/` holds a small, deliberately focused suite — two Kane-designed
tests, hand-verified and fully hardcoded (no `{{variables}}`), each with
real `@verifies` citations back into the `.context/` assurance graph:

- **`web-timely-fraud-over-1-000-caps-provisional-credit-at-1-000_test.md`**
  — Vertex Electronics, $1,299.00, filed within 60 days: requires secondary
  SSN verification, High priority, provisional credit capped at $1,000.
- **`web-fraud-filed-after-60-days-and-within-90-days-opens_test.md`**
  — Summit Hardware, $610.00, filed 61–90 days after the transaction:
  requires secondary SSN verification, still High priority (fraud reason),
  but **no** provisional credit since it's outside the 60-day credit
  window.

Together these two exercise the core money-outcome logic (AC-1, AC-2,
AC-5, AC-6, AC-7) end to end, including case-reference issuance. This is a
starting set, not full coverage of every AC — extend it the same way these
were produced (see below) as you need more.

## The assurance pipeline — extending this suite

If you want to add more tests later, here's the loop:

```bash
kane-cli context ingest requirements/redress-prd.md   # snapshot the PRD (already done)
kane-cli context extract                              # AI proposes use-cases
kane-cli context review                                # you approve/reject
kane-cli design tests --use-case <use-case-id>         # generate scenarios + tests
```
`design tests` writes to `.testmuai/tests/` at the **repo root** (not under
`tests/`) — move/hand-verify any new file into `tests/` the same way the
two here were done: replace `{{variables}}` with real values matching the
mock transactions in `index.html`, and double-check any transaction over
$500 has an explicit step entering the SSN verification field (Kane's
auto-generated steps sometimes miss this).

## Continuous integration

`.github/workflows/assurance.yml` runs the suite on demand:

- Triggered manually from the **Actions** tab (`workflow_dispatch`).
- Installs Node + Chrome + Kane CLI, authenticates from repo secrets, and
  batch-runs every `*_test.md` in `tests/` with `kane-cli testrun run` as a
  single execution.
- Produces a coverage report, validates the sealed evidence pack with
  `kane-cli evidence validate --profile L1`, posts the coverage panel into
  the workflow run's summary page, and uploads evidence + coverage as
  artifacts.

### Required repository secrets

Set these under **Settings → Secrets and variables → Actions**:

| Secret | Value |
|--------|-------|
| `KANE_USERNAME` | Your TestMu AI username |
| `KANE_ACCESS_KEY` | Your TestMu AI access key (dashboard → Credentials) |

## Running locally

```bash
cd tests
kane-cli testrun run --headless      # runs both tests as one execution
kane-cli cover                       # coverage report (run from repo root instead — .context/ lives there)
```

To run just one test:
```bash
kane-cli testmd run tests/web-timely-fraud-over-1-000-caps-provisional-credit-at-1-000_test.md
```

To view a sealed evidence pack:
```bash
kane-cli evidence serve tests/.testmuai/evidence/<execution_id>.evidence
```

## Testability

Every interactive element and error carries a stable `data-testid`
attribute (for example `card-last4`, `txn-t2`, `reason-fraud`,
`secondary-ssn4`, `submit-case`, `case-reference`).

## Purpose

This repository is a demonstration test target and pipeline for an
AI-assisted quality engineering workflow, adapted for the BFS sector. It is
not a real bank or dispute-resolution product; verification, transactions,
and case handling are all mocked, and no data is stored or transmitted.
