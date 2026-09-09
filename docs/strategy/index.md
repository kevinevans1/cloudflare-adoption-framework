--8<-- "_snippets/disclaimer.md"

# Strategy

The Strategy phase decides *why* you're adopting Cloudflare, *for what*, and how you'll justify the investment — before any account gets created or DNS record touched. Skipping it is the single most common cause of Cloudflare deployments that stall at "we turned on the orange cloud for one domain": nobody articulated the problem being solved, so nobody builds momentum to go further.

Because Cloudflare isn't a general-purpose IaaS replacement, strategy work here differs from a hyperscaler migration. You're not deciding "do we move to the cloud" — you're deciding which value propositions (edge security, network performance, Zero Trust, developer platform) apply to your organization, and building a case that's honest about where a hyperscaler is still the right tool.

## Key decisions this phase answers

- What is Cloudflare, concretely, and which of its capabilities are relevant to us?
- Where does Cloudflare fit relative to our existing hyperscaler cloud — in front of it, instead of parts of it, or both?
- What's the business justification: cost, risk, performance, or developer velocity — and can we make that case with real numbers?
- Which specific workloads or initiatives are good candidates for Cloudflare, and which aren't?

## In this section

- [Why Cloudflare](why-cloudflare.md) — what the platform actually is and the problems it solves
- [Positioning Cloudflare vs. Hyperscaler Cloud](positioning.md) — where it fits next to AWS/Azure/GCP, and where it doesn't
- [Business Justification](business-justification.md) — building the ROI case
- [Assessing Workload Fit](workload-fit.md) — a practical checklist for evaluating individual workloads
