---
assurance:
  id: t-1
  base: sha256:dc96d361113c3f537cb48ef235782b82ef20cb22ad7a80fb16f34d611a0ab301
---
# Web: Timely fraud over $1,000 caps provisional credit at $1,000

> Prove an eligible Unauthorized / Fraud dispute dated within 60 days and above $1,000 shows High priority, requires SSN before continuing past Categorize, caps provisional credit at $1,000, and can be submitted successfully.

## Step 1 @verifies ac-15

Open https://eashanlt.github.io/redress-bfs/ in a browser and confirm the single-page dispute flow shows the Verify, Select transaction, Categorize, Describe, and Review & submit sections, then assert all five sections are present on the same page.

## Step 2 @verifies ac-8, ac-9, ac-16

In the Verify section, enter card last-4 `4821` and billing ZIP `94107` and continue, then assert the Select transaction section unlocks.

## Step 3 @verifies ac-10, ac-16

In Select transaction, choose the recent-activity row for "Vertex Electronics" ($1,299.00) and continue, then assert exactly one transaction is selected and the Categorize section unlocks.

## Step 4 @verifies ac-13, ac-21, ac-22

In Categorize, choose Unauthorized / Fraud, leave the required SSN field empty, and attempt to continue, then assert only Unauthorized / Fraud is selected, progression remains blocked at Categorize, and a section-level error is shown.

## Step 5 @verifies ac-25, ac-16

Still in Categorize, enter `5678` into the secondary verification field and continue, then assert the Describe section opens.

## Step 6 @verifies ac-14, ac-16

In Describe, enter "I did not authorize this charge on my card." and continue, then assert Review & submit opens.

## Step 7 @verifies ac-2, ac-4, ac-5, ac-23, ac-24

On Review & submit, store the displayed case priority as review_priority and the displayed provisional credit as review_credit, and confirm no case reference is shown yet; then assert the priority reads High and the provisional credit reads $1,000.

## Step 8 @verifies ac-1, ac-3, ac-6, ac-7

Submit the dispute, then assert a case reference appears in `CASE-` plus 6 digits and 2 letters and the confirmation screen shows the same priority as review_priority and the same provisional credit as review_credit.
