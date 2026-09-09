# Why Cloudflare

Cloudflare is a global network that sits between users and the applications they connect to — and, increasingly, a platform you build applications directly on top of. Both are accurate, and the tension between them is why "why Cloudflare" deserves a dedicated strategy conversation, not a one-line answer.

## What Cloudflare actually is

At its core, Cloudflare operates an [anycast](https://developers.cloudflare.com/fundamentals/concepts/how-cloudflare-works/) global network: the same IP address is announced from many locations, so a request routes to the nearest point of presence rather than one fixed origin. Cloudflare's [network page](https://www.cloudflare.com/network/) is the canonical source for footprint numbers — as of this writing, 300+ cities across 100+ countries, with most of the world's Internet-connected population within double-digit milliseconds of a location. Treat those figures as a moving target; check the live page rather than trusting any number restated elsewhere, including here.

What makes Cloudflare distinct from "a CDN" is that the same network and the same dashboard/API surface span four categories that are normally separate vendors:

| Category | What it covers | Representative products |
|---|---|---|
| **Performance / CDN** | Caching, content delivery, traffic acceleration | CDN, [Load Balancing](https://developers.cloudflare.com/load-balancing/), [Argo Smart Routing](https://developers.cloudflare.com/argo-smart-routing/) |
| **Security** | Perimeter and application-layer defense | [WAF](https://developers.cloudflare.com/waf/), [DDoS Protection](https://developers.cloudflare.com/ddos-protection/), [Bot Management](https://developers.cloudflare.com/bots/), [API Shield](https://developers.cloudflare.com/api-shield/), [Client-Side Security](https://developers.cloudflare.com/client-side-security/) (formerly Page Shield) |
| **Zero Trust / network** | Replacing or supplementing VPN and perimeter network security | [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/) — Access, Gateway, Tunnel, [Magic Transit](https://developers.cloudflare.com/magic-transit/), [Cloudflare WAN](https://developers.cloudflare.com/cloudflare-wan/) (formerly Magic WAN) |
| **Developer platform** | Compute, storage, and data at the edge | [Workers](https://developers.cloudflare.com/workers/), [Pages](https://developers.cloudflare.com/pages/), [R2](https://developers.cloudflare.com/r2/), [D1](https://developers.cloudflare.com/d1/), [KV](https://developers.cloudflare.com/kv/), [Durable Objects](https://developers.cloudflare.com/durable-objects/), [Queues](https://developers.cloudflare.com/queues/) |

The unifying idea — Cloudflare calls this a "connectivity cloud" — is that DNS, WAF policy, Zero Trust rules, and Worker deployments all live under the same account/zone model, configurable through the same [dashboard](https://developers.cloudflare.com/fundamentals/setup/account-setup/) and [API](https://developers.cloudflare.com/api/), with a shared analytics and logging surface ([GraphQL Analytics API](https://developers.cloudflare.com/analytics/graphql-api/), [Logpush](https://developers.cloudflare.com/logs/logpush/)). That's the practical argument for Cloudflare as a platform rather than stitching together a CDN vendor, a WAF vendor, a VPN/ZTNA vendor, and an edge compute vendor.

## Problems it solves

Organizations typically reach for Cloudflare for one or more of these reasons:

- **Reduce attack surface.** DDoS mitigation and WAF sit in front of the application, handling volumetric and application-layer attacks at the edge instead of the origin having to survive them.
- **Improve global performance without re-architecting the origin.** Caching, smart routing, and TLS termination at the edge improve latency for distributed users even when the origin doesn't change at all.
- **Replace a hardware-centric perimeter with a cloud-delivered one.** [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/) — the Zero Trust/SASE product family — moves access control, traffic filtering, and network connectivity into the same platform doing CDN and security, instead of running a separate VPN concentrator and SASE vendor.
- **Build and run applications close to users without managing servers or regions.** [Workers](https://developers.cloudflare.com/workers/) and its storage primitives (R2, D1, KV, Durable Objects) let a team ship globally distributed compute without provisioning VMs, managing autoscaling groups, or picking regions.
- **Consolidate vendors.** Many organizations arrive already running a separate CDN, WAF, DDoS scrubbing service, and VPN — the case for Cloudflare is partly "one bill, one dashboard, one team's expertise instead of four."

## Where it fits

There are two structurally different ways Cloudflare shows up in an architecture, and most organizations end up doing both:

1. **In front of an origin, wherever that origin lives.** The most common pattern: your application runs on AWS, Azure, GCP, on-prem, or a mix, and Cloudflare proxies traffic to it — CDN, WAF, DDoS protection, and (optionally) Zero Trust access in front of infrastructure you don't otherwise change. Same-day integration for most workloads: you're changing DNS and edge configuration, not application code.
2. **As the compute and data platform itself**, via Workers, Pages, R2, D1, KV, Durable Objects, and Queues, for workloads that fit the model well — see [Positioning Cloudflare vs. Hyperscaler Cloud](positioning.md) for where that trade-off is and isn't favorable.

Most adoptions start with pattern 1 — lower-risk, immediate security/performance value — and expand into pattern 2 as teams get comfortable and identify workloads (APIs, edge logic, static-plus-dynamic web apps) that fit well.

## Design considerations

- **Don't treat Cloudflare as "just a CDN" during strategy discussions** — that framing under-sells the security and Zero Trust value and leads teams to skip the [Foundation](../foundation/index.md) work a broader adoption needs.
- **Identify your primary driver early** (security consolidation, performance, Zero Trust/VPN replacement, or developer platform) — it changes which phase of this framework you should prioritize and which stakeholders need to be in the room during Plan.
- **Validate network and product claims against live Cloudflare sources before quoting them externally** (in an RFP response, a board deck, a compliance questionnaire) — the platform evolves quickly and stale numbers undermine credibility.

## Further reading

- [How Cloudflare works · Fundamentals](https://developers.cloudflare.com/fundamentals/concepts/how-cloudflare-works/)
- [Cloudflare global network](https://www.cloudflare.com/network/)
- [Cloudflare One overview](https://developers.cloudflare.com/cloudflare-one/)
- [Workers overview](https://developers.cloudflare.com/workers/)
- [Cloudflare Reference Architecture](https://developers.cloudflare.com/reference-architecture/)
