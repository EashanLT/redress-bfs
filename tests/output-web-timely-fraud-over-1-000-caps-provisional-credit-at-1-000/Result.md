---
test: ../web-timely-fraud-over-1-000-caps-provisional-credit-at-1-000_test.md
status: passed
started: 2026-09-14T13:07:01.355Z
duration_s: 542
session_id: 62740bb6-8207-423c-8670-463bb604959d
---

# Web: Timely fraud over $1,000 caps provisional credit at $1,000 — Result

## Step 1 ✓ passed (67.8s)
md5: d08d982573826d03db1a9c88aa14abbb
Open https://eashanlt.github.io/redress-bfs/ in a browser and confirm the single-page dispute flow shows the Verify, Select transaction, Categorize, Describe, and Review & submit sections, then assert all five sections are present on the same page.

## Step 2 ✓ passed (62.1s)
md5: 8b61e30b5cba9bf762f02161707a5452
In the Verify section, enter card last-4 `4821` and billing ZIP `94107` and continue, then assert the Select transaction section unlocks.

## Step 3 ✓ passed (75.1s)
md5: cb4c7713ba710d8788a4e23030cf1bbe
In Select transaction, choose the recent-activity row for "Vertex Electronics" ($1,299.00) and continue, then assert exactly one transaction is selected and the Categorize section unlocks.

## Step 4 ✓ passed (78.8s)
md5: 92bcfb751158fe968ae8193272b85767
In Categorize, choose Unauthorized / Fraud, leave the required SSN field empty, and attempt to continue, then assert only Unauthorized / Fraud is selected, progression remains blocked at Categorize, and a section-level error is shown.

## Step 5 ✓ passed (58.2s)
md5: 945c5beb830c5f3241d77c81af99eaef
Still in Categorize, enter `5678` into the secondary verification field and continue, then assert the Describe section opens.

## Step 6 ✓ passed (50.2s)
md5: 79becfdc4df32bfe0e450600fa6c22e3
In Describe, enter "I did not authorize this charge on my card." and continue, then assert Review & submit opens.

## Step 7 ✓ passed (66.9s)
md5: 0340c0b5913db0d287df4bb8e4be8e04
On Review & submit, store the displayed case priority as review_priority and the displayed provisional credit as review_credit, and confirm no case reference is shown yet; then assert the priority reads High and the provisional credit reads $1,000.

## Step 8 ✓ passed (63.4s)
md5: e774634ea163cc4a1f75cf8d401481fa
Submit the dispute, then assert a case reference appears in `CASE-` plus 6 digits and 2 letters and the confirmation screen shows the same priority as review_priority and the same provisional credit as review_credit.
