---
test: ../web-fraud-filed-after-60-days-and-within-90-days-opens_test.md
status: passed
started: 2026-09-14T12:59:38.558Z
duration_s: 418
session_id: 822eefb3-d70f-4fb5-9f26-f842d9dfb5a5
---

# Web: Fraud filed after 60 days and within 90 days opens without provisional credit — Result

## Step 1 ✓ passed (74.5s)
md5: efd905da83d9643f67729d018316956b
Open https://eashanlt.github.io/redress-bfs/ in a browser, enter card last-4 `4821` and billing ZIP `94107` in Verify, and continue, then assert the Select transaction section unlocks.

## Step 2 ✓ passed (62.5s)
md5: 14ffa28305934245dcaa754e6ade1aea
In Select transaction, choose the recent-activity row for "Summit Hardware" ($610.00, dated more than 60 but fewer than 90 days ago) and continue, then assert exactly one transaction is selected and the Categorize section unlocks.

## Step 3 ✓ passed (83.5s)
md5: c12a582eedc45cf8a194ca2af42d26c2
In Categorize, choose Unauthorized / Fraud. Since this transaction is over $500, enter `5678` into the secondary verification field for the last 4 digits of SSN that appears. Continue, then assert only Unauthorized / Fraud is selected and the Describe section opens.

## Step 4 ✓ passed (44.9s)
md5: 7b1b708a311cd65b1ba4cca3d629908d
In Describe, enter "I noticed this unauthorized charge too late to receive provisional credit." and continue, then assert Review & submit opens.

## Step 5 ✓ passed (62s)
md5: 91760d6be28cce920955f15cda55e74b
On Review & submit, store the displayed case priority as review_priority and the displayed provisional credit as review_credit, and confirm no case reference is shown yet; then assert the priority reads High and the provisional credit display shows no provisional credit.

## Step 6 ✓ passed (67s)
md5: e774634ea163cc4a1f75cf8d401481fa
Submit the dispute, then assert a case reference appears in `CASE-` plus 6 digits and 2 letters and the confirmation screen shows the same priority as review_priority and the same provisional credit as review_credit.
