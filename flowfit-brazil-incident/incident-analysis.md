# Payment Incident Analysis — FlowFit Brazil

**Period:** March 11–15 | **Severity:** Critical

---

## Root Cause

A metadata misconfiguration introduced on March 11 is misrepresenting FlowFit's subscription transactions to issuers.

Three fields changed simultaneously:

| Field | Correct Value | Incorrect Value (from March 11) |
|---|---|---|
| `transaction_type` | `recurring` | `ecommerce` |
| `initiator` | `merchant` | `cardholder` |
| `stored_credential_usage` | `subsequent` | `first` |

This combination tells Brazilian issuers that a **returning subscriber is initiating a brand-new payment** — with no prior consent on file. Under the EMVCo Stored Credential Framework, issuers are entitled to refuse this.

GlobalPay's Brazil infrastructure upgrade on **March 10** began enforcing stricter compliance validation, triggering the spike. This explains:

- Why the drop starts precisely on March 11
- Why **decline code 57 ("Transaction not permitted to cardholder") jumped from <10% → 48.9%** of declines
- Why **all issuers are affected equally** — it's a systemic framing issue, not issuer-specific behavior
- Why **LatinAcquire (79.2%) outperforms GlobalPay (66.8%)** — LatinAcquire has not yet enforced the same compliance rules

---

## Revenue Impact

| Metric | Value |
|---|---|
| Auth rate decline | 84.2% → 67.1% **(−17.1pp)** |
| Brazil share of volume | 36% |
| Blended portfolio impact | **~6.2pp on total GMV** |
| Estimated recovery if fixed | **~$171K per $1M of Brazil GMV** |

> Every additional day without a fix compounds the loss. March 11–15 already represents 5 days of degraded performance.

---

## Immediate Actions (Next 24–48 Hours)

**1. Revert metadata — highest priority**
Restore the three fields to their correct values for all recurring Brazil transactions.

**2. Shift routing to LatinAcquire**
While GlobalPay is misconfigured or during any validation period, route Brazil recurring transactions to LatinAcquire. Current 12.4pp performance gap makes this a material revenue decision.

**3. Retry March 11–15 declines with corrected metadata**
Code 57 declines caused by misconfiguration are retryable. A targeted retry campaign with corrected flags can recover a portion of the lost revenue from the impact window.

---

## Medium-Term Recommendations

- **Audit recurring metadata across all regions** — if this misconfiguration was deployed globally, other markets may be affected
- **Add metadata validation at the transaction layer** — recurring transactions with `initiator: cardholder` or `stored_credential_usage: first` should trigger an alert before submission
- **Align with GlobalPay on compliance requirements** — understand the full scope of their March 10 upgrade to anticipate further rule enforcement

---

## Stakeholder Talking Points

> *"FlowFit experienced a 17-point authorization rate decline in Brazil between March 11–15, driven by a metadata configuration error that misrepresented recurring subscription charges to card issuers. This was surfaced by a compliance enforcement upgrade at our primary acquirer. The fix is well-defined: reverting three transaction fields restores correct framing. We are also shifting volume to a secondary acquirer while the fix is validated and pursuing a retry of impacted transactions to recover revenue from the affected window."*

---

**Bottom line:** This is not an issuer problem, a fraud problem, or a product problem. It is a misconfiguration with a precise, fast-executable fix.
