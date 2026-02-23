# Configuration Fix Guide — FlowFit Brazil Metadata Misconfiguration

---

## Fields to Correct

| Field | Incorrect Value | Correct Value | Reason |
|---|---|---|---|
| `transaction_type` | `ecommerce` | `recurring` | This is a subscription, not an ad-hoc purchase |
| `initiator` | `cardholder` | `merchant` | FlowFit initiates the charge — cardholder is not present |
| `stored_credential_usage` | `first` | `subsequent` | A prior consent/initial charge already exists on file |

These values must be consistent across all three layers:
1. What FlowFit sends in their API payload
2. What Yuno normalizes in the enrichment layer
3. What gets submitted to the acquirer

---

## Where the Fix Likely Lives

### Most Likely: Yuno's Side

Evidence pointing to a Yuno-side configuration change:

- FlowFit is a subscription business — merchants in this model typically rely on the orchestration platform to handle the stored credential framework
- All three fields changed simultaneously on a specific date — points to a single config change, not a merchant code deployment
- The timing gap: GlobalPay upgraded March 10, metadata changed March 11 — suggests a reactive config adjustment was made with incorrect values

### How to Confirm

**Check Yuno's audit trail first:**
- Was there any change to FlowFit's account configuration, payment profile, or recurring settings on March 11?
- Does Yuno have a default override or enrichment rule for GlobalPay in Brazil that was modified around that date?

**If nothing found, check with FlowFit:**
- Did their team deploy any changes to the Yuno integration on March 11?

---

## Diagnostic Steps

### Step 1 — Pull Raw Transaction Logs
Compare a transaction from **March 10** (before) vs **March 12** (after).
Look for the exact payload submitted to GlobalPay and identify which layer introduced the wrong values.

### Step 2 — Check Yuno's Change Log
Review any configuration modifications on FlowFit's account between March 10–11.

### Step 3 — Ask FlowFit
If nothing found internally, ask FlowFit whether they deployed any integration changes on or around March 11.

---

## Communication Approach

| If the change is on Yuno's side | If the change is on FlowFit's side |
|---|---|
| Own it clearly, fix it fast, explain what happened | Guide them to revert + validate their payload |
| Offer a retroactive retry on impacted transactions | Collaborate on the retry together |
| Internal postmortem to prevent recurrence | Provide documentation on correct field values |

> **Recommendation:** Check Yuno's audit trail before reaching out to FlowFit. If the config change is internal, own it proactively.

---

## After the Fix

1. Monitor authorization rates in real time for Brazil — expected recovery toward ~84%
2. Execute retry campaign for March 11–15 code 57 declines
3. Keep LatinAcquire as primary for Brazil until GlobalPay performance is confirmed restored
4. Document the incident and update runbooks for recurring transaction metadata validation
