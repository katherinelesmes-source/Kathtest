# TAM Challenge Submission — FlowFit Brazil Payment Incident

---

## Section 1 — Incident Analysis & Root Cause

### Summary

| Metric | Value |
|---|---|
| Merchant | FlowFit (subscription business) |
| Region affected | Brazil (36% of total volume) |
| Period | March 11–15 |
| Authorization rate before | 84.2% |
| Authorization rate after | 67.1% |
| Decline (pp) | −17.1pp |

### Root Cause

On March 11, a misconfiguration caused FlowFit's recurring subscription transactions to be submitted to card issuers as first-time, cardholder-initiated purchases — incorrectly flagging three interdependent fields (`transaction_type`, `initiator`, and `stored_credential_usage`) in a way that implied no prior payment consent existed on file. This misconfiguration became critical because GlobalPay completed a compliance infrastructure upgrade in Brazil on March 10, which began strictly enforcing the EMVCo Stored Credential Framework — meaning transactions that were previously accepted despite loose metadata were now being actively rejected. The evidence is unambiguous: decline code 57 ("Transaction not permitted to cardholder") surged from under 10% to 48.9% of all declines, all major Brazilian issuers were affected equally — ruling out any issuer-specific or fraud-related cause — and LatinAcquire, which has not yet enforced the same compliance rules, continued authorizing at 79.2% versus GlobalPay's 66.8%. The impact is isolated to Brazil because GlobalPay's upgrade was scoped to their Brazilian infrastructure, leaving other regions unaffected. The decline began precisely on March 11 — one day after the GlobalPay upgrade — because that is when the misconfigured metadata first collided with the new, stricter validation rules, converting a latent configuration error into an active authorization failure.

### Metadata Misconfiguration Detail

| Field | Incorrect Value (from March 11) | Correct Value |
|---|---|---|
| `transaction_type` | `ecommerce` | `recurring` |
| `initiator` | `cardholder` | `merchant` |
| `stored_credential_usage` | `first` | `subsequent` |

---

## Section 2 — Revenue Impact

| Metric | Value |
|---|---|
| Authorization rate decline | 84.2% → 67.1% **(−17.1pp)** |
| Brazil share of total volume | 36% |
| Blended portfolio impact | **~6.2pp on total GMV** |
| Estimated loss per $1M Brazil GMV | **~$171K** |

> Every additional day without a fix compounds the loss. March 11–15 already represents 5 days of degraded performance.

---

## Section 3 — Prioritized Actions by Revenue Recovery Speed

### #1 — Shift Routing to LatinAcquire
*(Do now — impact in minutes)*

Redirects live Brazil recurring traffic away from GlobalPay to LatinAcquire, which is already authorizing at 79.2% vs 66.8%. No code change required — routing rule only.

**Business impact:** Recovers ~12.4pp of authorization rate immediately on all new transactions.
> Protects approximately **$124K per $1M of Brazil GMV** with zero delay.

---

### #2 — Revert Metadata Configuration
*(24–48h — restores full performance)*

Corrects the three misconfigured fields, restoring transactions to their correct recurring/MIT classification across all acquirers including GlobalPay.

**Business impact:** Expected to recover authorization rate back toward ~84%.
> Estimated recovery of **~$171K per $1M of Brazil GMV** vs current degraded state.

---

### #3 — Retry March 11–15 Code 57 Declines
*(48–72h — recovers lost revenue)*

Re-submits declined transactions from the impact window using corrected metadata. Code 57 declines are retryable. Typical recovery rate on misconfiguration-driven declines is 40–60%.

> Priority: retry most recent declines first — subscriber churn risk increases with time since failed charge.

---

### Actions Summary

| Priority | Action | Timeline | Revenue Impact |
|---|---|---|---|
| #1 | Route to LatinAcquire | Minutes | ~$124K protected per $1M Brazil GMV |
| #2 | Revert metadata config | 24–48h | ~$171K recovered per $1M Brazil GMV |
| #3 | Retry code 57 declines | 48–72h | 40–60% of lost transactions recovered |

---

## Section 4 — Client Communication

### Email — Suitable for CFO

**Subject:** Brazil Payment Performance — Update & Recovery Plan

Hi [Name],

I want to give you a direct update on the payment performance issue we identified in Brazil, and more importantly, what we are doing to recover revenue as quickly as possible.

Between March 11–15, authorization rates in Brazil dropped from 84% to 67%. We have identified the exact cause — a configuration error in how transactions were being submitted to your payment processor — and the fix is clearly defined and already underway.

To be clear: this was not a fraud issue, not a problem with your customers' cards, and not a sign of broader business risk. It was a technical misconfiguration that we have isolated and are correcting.

Here is what is happening right now:

- We have already rerouted your Brazil transactions to a better-performing processor, which is recovering authorization rates immediately.
- The underlying configuration is being corrected to restore full performance within 48 hours.
- Once fixed, we will reprocess the declined transactions from the affected period to recover as much lost revenue as possible.

Brazil represents 36% of your volume, so restoring performance here is our highest priority. We will keep you updated at every step and share a full summary once the fix is confirmed stable.

Please don't hesitate to reach out directly if you have questions.

Best regards,
[Your name]

---

### Key Message for Live Presentation

**"We are already recovering your revenue — action started before this meeting."**

Before we sat down today, we had already rerouted your Brazil transactions to a secondary processor that is performing 12 points better than your primary. That means every subscription charge running through Brazil right now is being authorized at a higher rate than it was yesterday. No code change, no waiting for a deployment — a routing decision we made on your behalf the moment we identified the issue.

The configuration fix that resolves the root cause is underway and will be confirmed within 48 hours. Once that is in place, your authorization rates return to where they were before March 11 — around 84%.

And here is what matters most for your financials: the transactions that were declined between March 11 and today are not written off. We are preparing a targeted retry campaign for those transactions using the corrected configuration. Based on the type of declines we saw, we expect to recover between 40 and 60 percent of those failed charges.

So when you leave this meeting, the situation is this — your payments are already performing better than when you walked in, a permanent fix is 48 hours away, and a portion of the revenue from the affected period is coming back.
