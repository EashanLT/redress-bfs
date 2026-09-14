---
assurance:
  id: t-3
  base: sha256:90d3694a60a67b3c41191f1da60e5cad1eb8dbb3aaad829a16442f0e9342340a
---
# Web: Fraud filed after 60 days and within 90 days opens without provisional credit

> Prove an Unauthorized / Fraud dispute older than 60 days but still within 90 days can be submitted successfully, remains High priority because of fraud, and shows no provisional credit.

## Step 1 @verifies ac-8, ac-9, ac-16

Open https://eashanlt.github.io/redress-bfs/ in a browser, enter card last-4 `4821` and billing ZIP `94107` in Verify, and continue, then assert the Select transaction section unlocks.

## Step 2 @verifies ac-10, ac-16

In Select transaction, choose the recent-activity row for "Summit Hardware" ($610.00, dated more than 60 but fewer than 90 days ago) and continue, then assert exactly one transaction is selected and the Categorize section unlocks.

## Step 3 @verifies ac-13, ac-16

In Categorize, choose Unauthorized / Fraud. Since this transaction is over $500, enter `5678` into the secondary verification field for the last 4 digits of SSN that appears. Continue, then assert only Unauthorized / Fraud is selected and the Describe section opens.

## Step 4 @verifies ac-14, ac-16

In Describe, enter "I noticed this unauthorized charge too late to receive provisional credit." and continue, then assert Review & submit opens.

## Step 5 @verifies ac-2, ac-4, ac-5, ac-30, ac-29

On Review & submit, store the displayed case priority as review_priority and the displayed provisional credit as review_credit, and confirm no case reference is shown yet; then assert the priority reads High and the provisional credit display shows no provisional credit.

## Step 6 @verifies ac-1, ac-3, ac-6, ac-7

Submit the dispute, then assert a case reference appears in `CASE-` plus 6 digits and 2 letters and the confirmation screen shows the same priority as review_priority and the same provisional credit as review_credit.
