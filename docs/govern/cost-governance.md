--8<-- "_snippets/disclaimer.md"

# Cost Governance

Cloudflare pricing mixes flat-rate plan tiers (CDN/security/network) with pure usage-based billing (the developer platform: Workers, R2, D1, Queues, and friends). Governance here isn't about predicting an exact bill — it's making sure nobody is surprised by one, and that spend maps back to the team that generated it.

## Key Cloudflare capabilities

| Capability | Governance role |
|---|---|
| [Cloudflare plans](https://www.cloudflare.com/plans/) | The zone-level plan tier (Free/Pro/Business/Enterprise, verify current names and inclusions at time of purchase) drives which security and performance features are available at all — a cost decision and a capability decision at once. |
| [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/) | Usage-based on requests and CPU time; the dimension most likely to spike from a code change, not a traffic change. |
| [R2 pricing](https://developers.cloudflare.com/r2/pricing/) | Usage-based on stored data plus Class A (writes/lists) and Class B (reads) operations — R2's headline feature is no egress fees, but storage and operations still bill. |
| [Notifications](https://developers.cloudflare.com/notifications/) | Configurable alerts on usage and billing-relevant events, delivered via email, webhooks, or PagerDuty depending on plan. |
| [Billable usage dashboard](https://developers.cloudflare.com/billing/manage/billable-usage/) | Daily visibility into usage-based costs per product, before they become a line item (Pay-as-you-go accounts only — not available on Enterprise contracts). |
| [Terraform provider](https://developers.cloudflare.com/terraform/) | Lets you gate expensive resource types (e.g., which teams' accounts can provision Workers for Platforms, or set R2 lifecycle rules) behind reviewed code rather than ad hoc dashboard provisioning. |

## Design considerations

### Where usage-based spend actually moves

Not every Cloudflare product bills the same way — watch the ones that don't most closely:

- **Workers** — billed on request count and CPU time. A recursive bug, cache-bypass regression, or bot storm hitting a Worker route can multiply cost in ways a static CDN zone never would. Check current dimensions and allowances on the [Workers pricing page](https://developers.cloudflare.com/workers/platform/pricing/) rather than assuming last quarter's numbers still hold.
- **R2** — storage plus Class A/B operations. A workload that lists objects frequently (Class A) can cost more from operations than from bytes stored. See [R2 pricing](https://developers.cloudflare.com/r2/pricing/).
- **Bandwidth-metered products** — most CDN/WAF traffic on paid plans isn't billed per-GB, but some products (e.g., [Cloudflare Stream](https://developers.cloudflare.com/stream/), [Images](https://developers.cloudflare.com/images/), [Spectrum](https://developers.cloudflare.com/spectrum/)) have their own usage-based pricing distinct from the zone plan. Confirm current billing dimensions per product on [cloudflare.com/plans](https://www.cloudflare.com/plans/) — don't assume "we're on a Business plan" covers everything.
- **Zero Trust / Cloudflare One** — typically billed per-seat rather than per-request; a spend driver that scales with headcount and device count, not traffic. See the SASE/Zero Trust pricing section on [cloudflare.com/plans](https://www.cloudflare.com/plans/).

Don't hardcode specific dollar figures, request counts, or storage prices into internal runbooks — Cloudflare has changed pricing structures before (including a 2026 restructuring of the pricing page itself into Compute & Storage / Network & CDN / SASE pricing pillars). Link to the live pricing page and re-check it at renewal and budget-planning time.

### Setting up usage and billing alerts

[Notifications](https://developers.cloudflare.com/notifications/) supports alerting on a range of account and zone events, but the exact catalog of billing/usage-specific alert types changes over time. Check the [notification types reference](https://developers.cloudflare.com/notifications/notification-available/) for what's currently offered rather than assuming a specific alert exists. At minimum, wire up:

- An alert path (email at minimum; webhook/PagerDuty for teams that already route on-call through those) reachable by whoever owns the account relationship, not just the engineer who set it up.
- A recurring manual check of the dashboard's usage view for products that don't yet have a built-in threshold alert.

### Chargeback in a multi-team account

Cloudflare's account/zone model doesn't have first-class cost-center tagging equivalent to a hyperscaler's resource tags. Practical approaches, in increasing order of rigor:

| Approach | Trade-off |
|---|---|
| One Cloudflare account per team/business unit | Cleanest cost attribution; more accounts to govern consistently (leans on [Policy & Guardrails](policy-and-guardrails.md) to avoid drift between them). |
| Single account, zones/Workers namespaced by team in naming convention | Easier to govern centrally; requires manual reconciliation of usage data back to teams since billing isn't natively split by zone in every product. |
| Single account with [Workers for Platforms](https://developers.cloudflare.com/cloudflare-for-platforms/workers-for-platforms/) for multi-tenant Workers | Good fit when one platform team runs Workers on behalf of many internal "customers" — check current metering/attribution capabilities for this product specifically. |

Whichever model is chosen, decide it during [Foundation](../foundation/accounts-and-organizations.md), not after ten teams have already onboarded — restructuring accounts later is disruptive.

## Decisions to make

- [ ] Is the account structure one-account-per-team, or single-account-with-conventions? (See [Accounts & Organizations](../foundation/accounts-and-organizations.md).)
- [ ] Who receives usage/billing notifications, and through what channel?
- [ ] What's the review cadence for usage trending (monthly is typical for usage-based products like Workers/R2)?
- [ ] How is spend attributed back to teams for chargeback or showback reporting?
- [ ] What's the escalation path if a usage spike is detected — who can throttle or disable a runaway Worker route?
- [ ] Is plan-tier selection (Free/Pro/Business/Enterprise per zone) itself reviewed periodically, or set once and forgotten?

## Checklist

- [ ] Usage/billing notifications configured for every account with production traffic
- [ ] Plan tier per zone documented and justified (not just "whatever it was set to originally")
- [ ] Usage-based products (Workers, R2, Stream, Images, etc.) identified and their pricing pages bookmarked, not their numbers memorized
- [ ] Chargeback/showback model decided and consistent with the account structure
- [ ] Monthly (at minimum) usage review against forecast

## Further reading

- [Cloudflare plans and pricing](https://www.cloudflare.com/plans/)
- [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- [R2 pricing](https://developers.cloudflare.com/r2/pricing/)
- [Cloudflare Notifications](https://developers.cloudflare.com/notifications/)
- [Notification types reference](https://developers.cloudflare.com/notifications/notification-available/)
- [How Cloudflare billing works](https://developers.cloudflare.com/billing/understand/how-billing-works/)
- [Monitor billable usage](https://developers.cloudflare.com/billing/manage/billable-usage/)
- [Workers for Platforms](https://developers.cloudflare.com/cloudflare-for-platforms/workers-for-platforms/)

See also: [Choosing a Plan Tier](../plan/choosing-a-plan.md) for the upstream planning decision, and [Cost Optimization (FinOps)](../manage/cost-optimization.md) for ongoing operational cost management.
