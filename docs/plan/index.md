--8<-- "_snippets/disclaimer.md"

# Plan

Once Strategy establishes why you're adopting Cloudflare and for what, the Plan phase turns that into an executable onboarding plan: what you're bringing onto the platform, which plan tier fits, whether the team is ready to operate it, and in what sequence the migration happens. This is where a vague mandate ("get us on Cloudflare") becomes a concrete list of zones, a chosen plan tier, named owners, and a wave-by-wave cutover schedule.

Skipping straight from Strategy to Adopt is how organizations end up mid-migration discovering a zone they forgot to inventory, a plan tier that doesn't support a control they need, or a team that doesn't know how to operate WAF exceptions under pressure. Plan work is cheap; discovering a gap during a production cutover is not.

## Key decisions this phase answers

- What exactly are we onboarding — how many domains, what DNS providers, what origins, what existing CDN/WAF vendors are being replaced?
- Which plan tier — Free, Pro, Business, Enterprise, and separately which Zero Trust tier — actually fits our requirements?
- Does our team have the skills to operate this, and who needs training before go-live?
- What's the migration sequence: which zones or controls go first, in what order, with what rollback plan?

## In this section

- [Digital Estate & Onboarding Inventory](digital-estate.md) — cataloging domains, DNS, origins, and existing vendors
- [Choosing a Plan Tier](choosing-a-plan.md) — matching plan tiers (and Zero Trust tiers) to real requirements
- [Skills & Team Readiness](skills-readiness.md) — roles, training, and RACI for operating Cloudflare
- [Migration Planning](migration-planning.md) — wave sequencing, rollback strategy, and stakeholder communication
