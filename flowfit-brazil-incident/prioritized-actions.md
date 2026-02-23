# Prioritized Actions by Revenue Recovery Speed — FlowFit Brazil

---

## #1 — Shift Routing to LatinAcquire
*(Do now — impact in minutes)*

**What it does:** Redirects live Brazil recurring traffic away from GlobalPay to LatinAcquire, which is already authorizing at 79.2% vs 66.8%.

**Business impact:** Recovers **~12.4pp of authorization rate immediately** on all new transactions. No code change required — routing rule only.

> For every $1M in Brazil GMV remaining in the billing cycle, this protects approximately **$124K in revenue** with zero delay.

---

## #2 — Revert Metadata Configuration
*(24–48h — restores full performance)*

**What it does:** Corrects the three misconfigured fields, restoring transactions to their correct recurring/MIT classification.

| Field | Incorrect Value | Correct Value |
|---|---|---|
| `transaction_type` | `ecommerce` | `recurring` |
| `initiator` | `cardholder` | `merchant` |
| `stored_credential_usage` | `first` | `subsequent` |

**Business impact:** Expected to recover authorization rate back toward **~84%** across all acquirers, including GlobalPay. This is the permanent fix.

> Estimated recovery of **~$171K per $1M of Brazil GMV** vs current degraded state.

---

## #3 — Retry March 11–15 Code 57 Declines
*(48–72h — recovers lost revenue)*

**What it does:** Re-submits declined transactions from the impact window using corrected metadata.

**Business impact:** Code 57 declines are retryable. Typical retry recovery on misconfiguration-driven declines is **40–60%**. For a 5-day window at 17.1pp degraded rate, this represents a material one-time revenue recovery.

> Priority: retry most recent declines first — subscriber churn risk increases with time since failed charge.

---

## Summary

| Priority | Action | Timeline | Revenue Impact |
|---|---|---|---|
| #1 | Route to LatinAcquire | Minutes | ~$124K protected per $1M Brazil GMV |
| #2 | Revert metadata config | 24–48h | ~$171K recovered per $1M Brazil GMV |
| #3 | Retry code 57 declines | 48–72h | 40–60% of lost transactions recovered |

**Sequence matters:** Action #1 stops the bleeding today. Action #2 fixes the root. Action #3 recovers what was already lost.
