--8<-- "_snippets/disclaimer.md"

# Positioning Cloudflare vs. Hyperscaler Cloud

Cloudflare is best understood as a **specialized hyperscaler**: hyperscale in reach, specialized in purpose. Its network operates at the same scale and reach as AWS, Azure, or GCP's global infrastructure — but instead of general-purpose compute and storage, it's purpose-built for network, security, and developer-platform workloads. Comparing it feature-for-feature against a general-purpose IaaS provider and concluding it's "missing" managed relational databases at scale, GPU training instances, or a general-purpose VM fleet is the wrong frame — that's not the problem it's built to solve. The positioning question isn't "Cloudflare or a hyperscaler" — it's "which parts of my architecture belong on which platform."

A concrete expression of this: Cloudflare doesn't ask you to pick a region. Every location in the network runs the full product stack — the same security, routing, and compute logic everywhere — an approach Cloudflare's own engineering blog calls ["Region: Earth."](https://blog.cloudflare.com/best-place-region-earth-inference/) There's no us-east-1 to choose or availability zone to plan around; your code and policy run everywhere by default. That's the strength this framework leans on throughout: global reach without regional configuration.

## Three realistic patterns

### 1. Cloudflare in front of a hyperscaler-hosted application (most common)

The overwhelming majority of Cloudflare deployments look like this: application, database, and business logic run on AWS/Azure/GCP (or on-prem), and Cloudflare sits in front as a reverse proxy — CDN, WAF, DDoS protection, bot management, TLS termination. Nothing about the origin changes. It's low-risk, fast to adopt (see [Onboarding a Site](../adopt/onboarding-a-site.md)), and delivers immediate value: better global performance via caching and [Argo Smart Routing](https://developers.cloudflare.com/argo-smart-routing/), attack absorption before it reaches the origin, and one place to manage edge security policy across every application regardless of cloud.

This pattern is also where **Zero Trust** most often enters: [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/) can replace a VPN into a hyperscaler VPC with identity-aware access (Access), DNS/HTTP filtering (Gateway), and outbound-only connectivity (Tunnel) — without touching workloads inside that VPC.

### 2. Cloudflare's developer platform as primary compute

For workloads that fit the model, [Workers](https://developers.cloudflare.com/workers/), [Pages](https://developers.cloudflare.com/pages/), [R2](https://developers.cloudflare.com/r2/), [D1](https://developers.cloudflare.com/d1/), [KV](https://developers.cloudflare.com/kv/), [Durable Objects](https://developers.cloudflare.com/durable-objects/), and [Queues](https://developers.cloudflare.com/queues/) are a genuine alternative to running compute on a hyperscaler — not a CDN in front of one. Strong fit for:

- Stateless or lightly-stateful API logic
- Request routing and transformation
- Static-plus-dynamic web apps
- Workloads where global low latency matters more than deep integration with an existing cloud-native stack

It's a materially different programming model — V8 isolates, not containers or VMs; no long-running background processes without Durable Objects; object storage and edge-native databases rather than a full RDBMS. This is an adoption decision for the engineering team, not just an infrastructure swap.

### 3. Hybrid — the realistic steady state

Most mature Cloudflare estates end up here deliberately: hyperscaler cloud for the workloads that need it, Cloudflare's developer platform for workloads that fit it well, and Cloudflare's network/security layer in front of everything. Durable Objects and D1 might back a new edge-native feature while the transactional system of record stays on a hyperscaler's managed database. This isn't a compromise — for most organizations, it's the correct end state.

## Where a hyperscaler is still the right tool

Be honest about this in strategy conversations, or the eventual pushback from engineering will undermine the whole initiative's credibility:

- **Long-running, stateful compute** that needs full OS access, arbitrary runtimes, or persistent local disk beyond what an isolate or container-based edge platform provides.
- **Large-scale relational databases** with complex transactional workloads, heavy joins, or deep investment in a specific RDBMS engine — D1 and other edge-native stores are strong for specific access patterns, not a drop-in replacement for a large operational Postgres/MySQL/SQL Server estate.
- **GPU-based training at scale** and other workloads that need specialized, large-scale accelerator hardware and the surrounding MLOps tooling a hyperscaler provides.
- **Deep, existing integration with a hyperscaler's proprietary managed services** (a large investment in a specific cloud's data warehouse, ML platform, or enterprise directory integration) where re-platforming cost outweighs the benefit.
- **Regulatory or contractual requirements** that specify a named cloud provider or region/residency model a hyperscaler already satisfies.

## Where Cloudflare's platform is a strong fit

- **Global, low-latency APIs** where the majority of value comes from being close to the user, not from heavyweight backend processing.
- **Edge logic**: request routing, header manipulation, A/B testing, authentication checks, and transformation that today runs as a reverse-proxy layer or an API gateway.
- **Static-plus-dynamic web applications** (Pages plus Workers/Functions) where the origin's job is mostly serving content and light dynamic logic.
- **The security and Zero Trust layer, full stop** — this is Cloudflare's core competency regardless of where compute lives, and it's rarely the right call to run WAF, DDoS protection, or ZTNA anywhere else once Cloudflare is already in the picture.

## Decisions to make

| Question | If yes, lean toward |
|---|---|
| Does the workload need a full OS, arbitrary runtime, or long-lived local state? | Hyperscaler |
| Is the primary value proposition proximity to a global user base? | Cloudflare developer platform |
| Does it depend heavily on a specific hyperscaler's managed data/ML services? | Hyperscaler (Cloudflare in front) |
| Is the goal edge security/Zero Trust regardless of where compute lives? | Cloudflare, always |
| Is this a new, greenfield API or service with modest state needs? | Evaluate Cloudflare developer platform seriously |
| Does compliance or contract mandate a specific cloud/region? | Hyperscaler (Cloudflare in front) |

## Further reading

- [Positioning workload fit checklist](workload-fit.md)
- [Workers overview](https://developers.cloudflare.com/workers/)
- [D1 overview](https://developers.cloudflare.com/d1/)
- [Durable Objects overview](https://developers.cloudflare.com/durable-objects/)
- [Cloudflare Reference Architecture](https://developers.cloudflare.com/reference-architecture/)
- [Cloudflare One overview](https://developers.cloudflare.com/cloudflare-one/)
