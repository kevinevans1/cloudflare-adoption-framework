--8<-- "_snippets/disclaimer.md"

# Business Justification

Getting budget and buy-in for a Cloudflare adoption requires a business case, not just an architecture diagram. This page gives you a framework for building that case — categories of value and how to reason about each — not fabricated numbers to plug into a slide. Every organization's baseline differs; fill this framework in with *your* numbers, sourced from your own vendor contracts, incident history, and cloud bills.

## Cost avoidance

This is usually the easiest category to quantify because it maps to line items you already have:

- **Egress and bandwidth costs.** If traffic flows through a hyperscaler's CDN or directly out of compute/storage services, compare that against Cloudflare's bandwidth model for cached and proxied traffic. Use current, workload-specific numbers from your cloud bill and [Cloudflare's pricing pages](https://www.cloudflare.com/plans/) — not a generic "Cloudflare is cheaper" multiplier. The delta depends on your traffic mix, cache hit ratio, and existing egress pricing tier.
- **Vendor consolidation.** List every vendor a Cloudflare adoption could let you retire or downsize: standalone CDN, separate WAF/DDoS scrubbing service, VPN concentrator or ZTNA/SASE vendor, bot-detection point solution. Sum current contract value, not list price — this is often the largest line in the case.
- **DDoS and incident response cost avoidance.** Estimate the cost of a realistic incident: engineering hours during an active attack, revenue impact of downtime for customer-facing systems, and any DDoS scrubbing contract being replaced. This is a probabilistic argument — pair it with actual incident history if you have it.

## Risk reduction

Harder to put a single dollar figure on, but material to a board or auditor:

- **Reduced blast radius from application-layer attacks** — WAF and Bot Management sit in front of every proxied application, instead of each team implementing (or failing to implement) its own protections.
- **Reduced dependency on a single perimeter model.** Moving VPN-based access to identity-aware Zero Trust access ([Access](https://developers.cloudflare.com/cloudflare-one/policies/access/)) reduces the risk of a compromised credential having broad network-level access.
- **Compliance posture.** Centralizing logging (Logpush, GraphQL Analytics), access control, and audit trails ([Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/review-audit-logs/)) is easier to demonstrate to an auditor than many inconsistent per-application setups.

Frame this category as "cost of a bad outcome × probability reduction," even qualitatively — that's the language risk and audit committees respond to.

## Performance and reliability outcomes

- **Latency improvement for distributed users**, measurable before/after via real user monitoring or synthetic testing once caching and smart routing are in place.
- **Uptime during attack or traffic spike scenarios** — DDoS protection and rate limiting reduce the chance that a spike (malicious or legitimate, e.g. a marketing campaign) takes down the origin.
- **Reduced origin load**, which can translate into infrastructure cost reduction (smaller fleet, lower database load) if cache hit ratios are meaningful for the workload.

## Developer velocity

- **Time to ship globally distributed features** using the developer platform (Workers/Pages/R2/D1) instead of provisioning multi-region infrastructure on a hyperscaler.
- **Reduced operational burden** — no patching, autoscaling configuration, or region failover design for workloads that run on Workers.
- **Faster security iteration** — WAF custom rules and rate limiting can be deployed in minutes centrally, versus a change request against infrastructure each application team owns individually.

This category is real but the hardest to quantify up front; track it retrospectively (time-to-ship for the first few features built on the platform) to build the case for expansion.

## A framework for articulating ROI

1. **Baseline first.** Before proposing Cloudflare, document current spend (CDN, WAF, DDoS, VPN/SASE vendors), incident history (frequency, cost, duration), and performance metrics (P50/P95 latency by region if available).
2. **Map each Cloudflare capability to a baseline line item it affects.** Not every capability needs a dollar figure — some belong in the risk-reduction narrative instead.
3. **Separate "avoided cost" from "new capability."** Replacing an existing WAF vendor is avoided cost; Zero Trust replacing a VPN you didn't have before is new capability with its own justification (usually security posture, not cost).
4. **Pilot before committing to the full case.** A single high-value zone or one Zero Trust use case (e.g., replacing VPN for a single application) generates real before/after data that's far more persuasive than a projection.
5. **Revisit the case in [Manage → Cost Optimization](../manage/cost-optimization.md)** once live — the business case shouldn't be a one-time document, it should be validated against actual usage and iterated.

## Decisions to make

| If your primary driver is... | Lead the business case with |
|---|---|
| Security incidents / attack exposure | Risk reduction + cost avoidance from incident response |
| High CDN/egress/WAF vendor spend | Direct cost avoidance and vendor consolidation |
| VPN replacement or remote workforce security | Risk reduction narrative (Zero Trust) |
| Global product performance | Performance outcomes, validated with a pilot |
| Engineering speed for new products | Developer velocity, validated retrospectively |

## Further reading

- [Cloudflare plans](https://www.cloudflare.com/plans/)
- [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/review-audit-logs/)
- [GraphQL Analytics API](https://developers.cloudflare.com/analytics/graphql-api/)
- [Cost Governance](../govern/cost-governance.md)
- [Cost Optimization (FinOps)](../manage/cost-optimization.md)
