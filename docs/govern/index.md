--8<-- "_snippets/disclaimer.md"

# Govern

Governance is the phase where a Cloudflare footprint stops being "whatever the first team set up" and becomes something an organization can reason about. It matters most exactly when it's least visible on day one: the moment a second team, a second zone, or a second account joins the first one.

## Why this phase exists

Cloudflare's per-zone and per-account model favors autonomy over consistency. Each zone has its own WAF rules, TLS mode, cache settings, and DNS records; each account has its own members, API tokens, and billing relationship. Nothing forces two zones — let alone two accounts — to converge on the same security baseline or spending pattern. Left unchecked, that flexibility produces three failure modes:

| Failure mode | What it looks like in practice |
|---|---|
| **Configuration drift** | One zone has the OWASP Core Ruleset in Block mode, a sibling zone still has it in Log-only from initial onboarding. One team enabled Bot Fight Mode, another never did. |
| **Inconsistent security posture** | Some origins only accept traffic from Cloudflare IPs (or better, [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/)); others are still directly reachable because nobody enforced it as a standard. |
| **Uncontrolled spend** | A Workers-heavy team ships a change that multiplies request volume, or an R2 bucket accumulates storage and Class A operations, and finance finds out from the invoice instead of from a dashboard. |

These aren't Cloudflare-specific risks — every multi-team cloud footprint has the same drift/sprawl/cost problems. What's Cloudflare-specific is *where* the levers are:

- Terraform-managed zone and account resources
- [Rulesets](https://developers.cloudflare.com/ruleset-engine/) — the mechanism for both security and consistency
- [Notifications](https://developers.cloudflare.com/notifications/) — the mechanism for both cost and security alerting
- [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) — the mechanism for both change tracking and compliance evidence

## What this phase covers

- **[Policy & Guardrails](policy-and-guardrails.md)** — baseline rules that should apply everywhere, and how to enforce them with Terraform instead of tribal knowledge.
- **[Cost Governance](cost-governance.md)** — the usage-based billing components that actually move, and how to get ahead of them with alerts and chargeback design.
- **[Compliance Mapping](compliance-mapping.md)** — connecting real regulatory requirements (data residency, PCI DSS scope, GDPR-style obligations) to actual Cloudflare capabilities, without overclaiming.
- **[Change Management](change-management.md)** — how security and network changes should move from proposal to production without becoming either reckless or frozen.

## How Govern relates to Secure

Govern and [Secure](../secure/index.md) overlap deliberately. Secure covers the *technical* security architecture — which layers exist, what each does, how Zero Trust is composed. Govern covers the *organizational* control plane around it — who can change it, how consistently it's applied, and how spend and compliance posture are tracked over time. A well-secured zone that drifts out of policy a month after launch is still a governance failure.

## Who should own this phase

In most organizations this isn't one person. A practical split:

- **Platform/network team** — owns the Terraform modules and baseline rulesets referenced in [Policy & Guardrails](policy-and-guardrails.md).
- **Security team** — owns exceptions review and the change approval workflow in [Change Management](change-management.md).
- **Finance/FinOps partner** — owns the alerting thresholds and chargeback model in [Cost Governance](cost-governance.md).
- **Compliance/legal** — owns final sign-off on any claim mapped in [Compliance Mapping](compliance-mapping.md); Cloudflare capabilities inform that work but don't substitute for it.

## Further reading

- [Cloudflare Ruleset Engine](https://developers.cloudflare.com/ruleset-engine/)
- [Cloudflare Notifications](https://developers.cloudflare.com/notifications/)
- [Cloudflare Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)
- [Cloudflare Terraform provider](https://developers.cloudflare.com/terraform/)
- [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/)
