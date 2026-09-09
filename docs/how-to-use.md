--8<-- "_snippets/disclaimer.md"

# How to Use This Framework

This framework is organized as a lifecycle, but you don't have to read it like a book. How you use it depends on where your organization actually is with Cloudflare today.

## If you're starting from zero (greenfield adoption)

If Cloudflare isn't in your environment yet, or you're re-platforming a fragmented, ad-hoc deployment into something deliberate, read the phases roughly in order:

1. **[Strategy](strategy/index.md)** — get alignment on why Cloudflare, for what, and build the business case before you touch a dashboard.
2. **[Plan](plan/index.md)** — inventory what you're onboarding, pick a plan tier, and assess whether your team has the skills it needs.
3. **[Foundation](foundation/index.md)** — do this *before* onboarding production traffic. Account structure, identity, DNS, and IaC decisions are expensive to change later — treat this like landing zone design on a hyperscaler.
4. **[Adopt](adopt/index.md)** — the actual migration: DNS cutover, security rollout, Zero Trust adoption, developer platform builds.
5. **[Govern](govern/index.md)**, **[Secure](secure/index.md)**, and **[Manage](manage/index.md)** — stand these up as you adopt, not after. By the time your first zone is live, you should already have baseline WAF policy, a change-management process, and observability in place. These three run continuously and in parallel for the life of the estate.

Sequential doesn't mean rigid — most organizations run Plan and early Foundation work in parallel, and Adopt happens in waves rather than as a single cutover (see [Migration Planning](plan/migration-planning.md)).

## If you're already on Cloudflare (reference use)

If Cloudflare is already in production and you need guidance on one problem — tightening account governance, designing a Zero Trust rollout, building a cost-governance process — skip straight to the relevant phase and sub-page. Each detail page stands alone: it states the decision it helps you make, links to the Cloudflare product docs you need, and cross-links to adjacent pages only where directly relevant. You don't need to have read Strategy to get value from [Cost Governance](govern/cost-governance.md).

A few common "I already have Cloudflare, I need X" entry points:

| Situation | Start here |
|---|---|
| Zones were onboarded ad hoc, no consistent account structure | [Accounts & Organizations](foundation/accounts-and-organizations.md) |
| WAF rules exist but nobody agrees on how they get changed | [Policy & Guardrails](govern/policy-and-guardrails.md) |
| Replacing a corporate VPN or evaluating SASE | [Zero Trust Adoption Path](adopt/zero-trust-adoption.md) |
| Cloudflare spend is growing and finance is asking questions | [Cost Governance](govern/cost-governance.md) and [Cost Optimization (FinOps)](manage/cost-optimization.md) |
| Considering Workers/Pages/R2/D1 as primary compute, not just CDN | [Building on the Developer Platform](adopt/developer-platform.md) |
| Need to explain Cloudflare's security responsibilities vs. yours | [Shared Responsibility Model](secure/shared-responsibility.md) |

## How this relates to Cloudflare Well-Architected

This framework and its companion, [**Cloudflare Well-Architected**](https://github.com/kevinevans1/cloudflare-well-architected), split the problem into two levels:

- **This framework (Adoption Framework)** operates at the **organization/estate** level: how do we structure accounts, govern change, roll out security baselines, and run the whole Cloudflare footprint over time.
- **Cloudflare Well-Architected** operates at the **workload** level: given a specific application or service you're building or migrating, how do you design it well on Cloudflare — covering reliability, security, cost, operational excellence, and performance for *that workload specifically*.

Use this framework to get the organization ready and keep it governed. Use Well-Architected when a team sits down to design or review one particular application on top of that foundation.

## Contributing and fact-checking standard

This project draws exclusively on Cloudflare's public documentation, written by a Cloudflare employee in a personal capacity — see [`NOTICE.md`](https://github.com/kevinevans1/cloudflare-adoption-framework/blob/main/NOTICE.md) for the full disclaimer and authorship statement.

The standard for any contribution:

- **Every specific number — a price, a quota, a limit — must link to the current canonical Cloudflare page**, not be stated as a bare fact. Cloudflare's plans and limits change faster than a community project can track, so the link is the source of truth, not the prose.
- **Cite a public source or don't include the claim.** No internal, confidential, or non-public information about Cloudflare's roadmap or product internals belongs here, ever.
- **If Cloudflare's own docs and this framework disagree, Cloudflare's docs win.** Open an issue or PR here so the discrepancy gets fixed.
- Prefer plain, direct, developer-first language — this should read like a solutions architect explaining trade-offs to a peer, not marketing copy.

Found something stale, wrong, or a dead link? Open an issue or pull request against the [GitHub repository](https://github.com/kevinevans1/cloudflare-adoption-framework).

## Further reading

- [NOTICE.md — authorship, disclaimer, and fact-checking standard](https://github.com/kevinevans1/cloudflare-adoption-framework/blob/main/NOTICE.md)
- [Cloudflare Well-Architected (companion repo)](https://github.com/kevinevans1/cloudflare-well-architected)
- [Cloudflare Reference Architecture](https://developers.cloudflare.com/reference-architecture/)
