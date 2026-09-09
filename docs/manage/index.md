# Manage

--8<-- "_snippets/disclaimer.md"

Manage is the continuous-operations phase of this framework. [Govern](../govern/index.md) sets policies and guardrails; [Secure](../secure/index.md) defines security architecture and controls. Manage covers the day-to-day work of running Cloudflare well once it's live: watching platform behavior, controlling cost, sustaining performance, and keeping services available.

Govern, Secure, and Manage run in parallel, not in sequence. A workload that completes [Adopt](../adopt/index.md) doesn't "finish" Manage — it's the operating rhythm that continues for as long as the workload runs on Cloudflare. It also feeds back into earlier phases: an observability finding can trigger a Govern policy change, a cost anomaly can reopen a Plan-phase sizing decision, and a reliability gap can send a team back to Foundation to reconsider network topology.

## Why Manage is its own phase

Cloudflare's operating model differs from a general-purpose IaaS platform in a way that changes what "operations" means:

- There are no servers, VM fleets, or clusters to patch. Operational effort shifts almost entirely to **configuration correctness, traffic behavior, and cost/usage visibility** rather than infrastructure lifecycle management.
- Most cost and performance levers (cache hit ratio, routing, placement) are **configuration decisions**, not capacity-planning decisions — which means Manage is largely about continuously tuning configuration against observed traffic, not provisioning more resources.
- Billing is usage-based across many independent meters (requests, CPU time, storage, operations, bandwidth in specific products) rather than a small number of instance types — so cost visibility depends on genuinely understanding what each product measures.

## What's in this phase

| Page | Covers |
|---|---|
| [Observability & Analytics](observability.md) | Zone Analytics, the GraphQL Analytics API, Logpush, Log Explorer, Workers Observability, real-time logs, and Tail Workers |
| [Cost Optimization (FinOps)](cost-optimization.md) | How caching and R2's pricing model affect origin cost, right-sizing Workers usage, and monitoring against plan limits |
| [Performance Operations](performance-operations.md) | Argo Smart Routing, Tiered Cache, Cache Rules, Smart Placement, cache hit ratio as a KPI, and image optimization |
| [Reliability Operations](reliability-operations.md) | Load Balancing, health checks, failover patterns, and stateful-workload consistency considerations |
| [Continuous Improvement](continuous-improvement.md) | Review cadence, tracking the Cloudflare changelog, and handling deprecations |

## Who owns Manage

Unlike Foundation or Adopt, which are often led by a platform/networking team during a defined project window, Manage is typically a shared, ongoing responsibility:

| Function | Typical Manage responsibilities |
|---|---|
| Platform/network team | Cache configuration, routing, Load Balancing, DNS health |
| Application/developer teams | Workers Observability, Worker-level cost and performance tuning, Smart Placement decisions |
| Security/SOC | Log Explorer and Logpush-fed SIEM alerting (see also [Secure](../secure/index.md)) |
| FinOps / finance partner | Usage-against-plan-limit monitoring, cost anomaly review |
| Architecture/platform lead | Quarterly config review, changelog triage, deprecation migration planning |

## Further reading

- [Cloudflare Analytics](https://developers.cloudflare.com/analytics/) — the umbrella for zone-, account-, and product-level analytics
- [Cloudflare changelog](https://developers.cloudflare.com/changelog/) — the single feed for tracking platform changes relevant to every page in this phase
- [Cloudflare Reference Architecture library](https://developers.cloudflare.com/reference-architecture/) — solution-level depth that complements the operating guidance in this phase
