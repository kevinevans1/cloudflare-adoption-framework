# Cost Optimization (FinOps)

--8<-- "_snippets/disclaimer.md"

FinOps on Cloudflare differs from FinOps on a general-purpose IaaS platform: there's very little to "right-size" in the traditional sense — no instance families, no reserved capacity, no idle VMs to shut down. Cost instead moves with **configuration decisions that affect traffic volume reaching origin and compute** (cache hit ratio, routing efficiency, Worker logic) and with **usage against independent, product-specific meters** (requests, CPU time, storage, operations, bandwidth). It's mostly a configuration and monitoring discipline, not a procurement one — which is why it belongs in Manage rather than a one-time Plan-phase exercise. See [Cost Governance](../govern/cost-governance.md) for the policy side (budgets, approval thresholds, chargeback); this page covers the operational side — what to actually tune and watch.

## Key Cloudflare capabilities

| Capability | Cost lever | Docs |
|---|---|---|
| Cache Rules / Tiered Cache / Argo Smart Routing | Higher cache hit ratio and fewer, better-routed origin connections directly reduce origin egress and origin compute cost | [Performance Operations](performance-operations.md) |
| R2 | Object storage with no egress fee, which changes the cost math for read-heavy or public-asset workloads compared to egress-billed cloud storage | [R2 pricing](https://developers.cloudflare.com/r2/pricing/) |
| Workers pricing (Standard usage model) | Billed on requests and CPU time; understanding what actually consumes CPU time is the main lever for controlling Worker cost | [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/) |
| GraphQL Analytics API | Programmatic usage data for building internal cost/usage dashboards ahead of the monthly invoice | [GraphQL Analytics API](https://developers.cloudflare.com/analytics/graphql-api/) |
| Plan tier limits | Every plan has specific quotas and included usage; usage past those quotas is billed or throttled depending on the product | [Billing overview](https://developers.cloudflare.com/billing/) |

## Caching's effect on origin cost

Every request Cloudflare serves from cache is a request your origin never sees. This is the single biggest cost lever most organizations have, and it compounds three ways:

1. **Origin egress** — cloud providers and traditional hosting typically charge for data leaving their network; a cache miss triggers that egress, a cache hit doesn't.
2. **Origin compute** — dynamic origins (an app server, a database-backed API) pay in CPU and database load for every miss; hit ratio directly caps that load.
3. **Origin capacity planning** — a higher, more predictable hit ratio lets you provision origin capacity for a smaller, steadier baseline instead of full potential peak traffic.

Improving hit ratio is covered in detail in [Performance Operations](performance-operations.md) — Cache Rules, Tiered Cache, and Argo Smart Routing are the primary levers.

## R2 and the egress-fee question

R2 is priced with **no per-gigabyte charge for egress bandwidth** — data transfer out is free. Pricing dimensions instead are storage (by class), Class A operations (writes/mutations), and Class B operations (reads), with an additional retrieval fee only for the Infrequent Access storage class. Confirm this against the [current R2 pricing page](https://developers.cloudflare.com/r2/pricing/) rather than treating it as permanent — it's why R2 is frequently evaluated specifically for public asset delivery, backup/archive, and any workload where a traditional cloud object store's egress fees dominate the bill.

That doesn't mean R2 has no data-transfer cost decisions — Class A/B operation counts scale with request volume. A workload with very high read volume against small objects should still model operation cost, not assume "no egress" means "no variable cost."

## Right-sizing Workers usage

Workers on the Standard usage model bill on **requests plus CPU time**, not wall-clock time spent waiting on I/O (a `fetch()` call, a KV read). The practical cost lever is reducing CPU-bound work per request, not shortening end-to-end request time:

- Avoid unnecessary parsing, serialization, or cryptographic work on the hot path when it can be cached, memoized, or pushed to build time.
- Watch for logic that runs on every request but only needs to run occasionally (feature-flag evaluation, config fetches) — cache it in KV or at the edge instead of recomputing it.
- Smart Placement (see [Performance Operations](performance-operations.md)) is a performance feature, but it also reduces the number of expensive round trips a Worker makes to a distant backend, which can reduce CPU time spent waiting/orchestrating multiple subrequests.
- Confirm current Workers pricing terminology and numbers directly against the [Workers pricing page](https://developers.cloudflare.com/workers/platform/pricing/) — legacy "Bundled" and "Unbound" usage models have been superseded by a single Standard usage model, and some accounts may still be on a legacy model without realizing it.

## Design considerations

- **Don't buy Enterprise features you're not using.** Enterprise unlocks capabilities (custom rulesets at scale, dedicated support, specific analytics retention) that are genuinely valuable for some organizations and pure overhead for others — validate the need against [Choosing a Plan Tier](../plan/choosing-a-plan.md) decisions on a recurring basis, not just at initial purchase.
- **Usage against plan limits should be monitored continuously, not discovered on the invoice.** Build a habit (dashboard, GraphQL Analytics query, or alert) around whatever meters matter most for your workload — requests, storage, operations — before they become a surprise.
- **Cost and performance tuning are the same activity here.** Unlike traditional infrastructure, where cost optimization can mean accepting worse performance for lower spend, on Cloudflare the two are usually aligned — a higher cache hit ratio is both cheaper and faster.
- **Attribute cost by account/zone structure early.** If Foundation-phase [Accounts & Organizations](../foundation/accounts-and-organizations.md) decisions didn't separate workloads cleanly, retrofitting cost attribution later is much harder.

## Decisions to make

| Decision | Options | Notes |
|---|---|---|
| How is usage tracked against plan limits? | Manual dashboard review / scheduled GraphQL Analytics export / third-party FinOps tool | Should match the review cadence set in [Continuous Improvement](continuous-improvement.md) |
| Is R2 evaluated for egress-heavy workloads currently on other storage? | Migrate / keep as-is / hybrid | Model actual operation counts, not just egress savings, before committing |
| What's the process for catching a Worker cost regression? | Alerting on CPU-time trend / manual review only | A regression is often a code change, not a traffic change — tie this to CI/CD review in [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md) |
| Who owns plan-tier and add-on purchase decisions? | Central platform team / distributed to workload teams | Should align with the ownership model in [Cost Governance](../govern/cost-governance.md) |

## Checklist

- [ ] Cache hit ratio tracked as a recurring KPI, not just checked when origin cost spikes
- [ ] R2 evaluated (or re-evaluated) for any workload currently paying cloud egress fees for static or infrequently-changed assets
- [ ] Workers CPU-time trends reviewed per Worker, not only in aggregate
- [ ] A named owner monitors usage against plan-tier limits before they're exceeded
- [ ] Enterprise/add-on features reviewed at least annually against actual usage
- [ ] Cost attribution mapped to account/zone structure so spend can be traced to a workload or team

## Further reading

- [Workers pricing](https://developers.cloudflare.com/workers/platform/pricing/)
- [R2 pricing](https://developers.cloudflare.com/r2/pricing/)
- [GraphQL Analytics API](https://developers.cloudflare.com/analytics/graphql-api/)
- [Cloudflare Billing overview](https://developers.cloudflare.com/billing/)
- [Change domain plan](https://developers.cloudflare.com/fundamentals/subscriptions-and-billing/change-plan/)
- [Cache Rules](https://developers.cloudflare.com/cache/how-to/cache-rules/)
