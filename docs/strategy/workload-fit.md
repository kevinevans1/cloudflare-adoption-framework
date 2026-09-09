--8<-- "_snippets/disclaimer.md"

# Assessing Workload Fit

Not every workload belongs on Cloudflare, and not every Cloudflare-suitable workload belongs on the developer platform specifically. This page gives you a practical, repeatable framework for assessing an individual workload — use it during Strategy for the first few candidates, and again during Plan/Adopt whenever a new team wants to bring something onto the platform.

Run this as a short structured conversation with the workload owner, not a solo desk exercise — you need answers about the application that only the owning team has (traffic patterns, state requirements, compliance constraints).

## Step 1: What kind of fit are you assessing?

There are two separate questions, and conflating them causes bad decisions:

1. **Is this workload a fit for Cloudflare's network/security layer** (CDN, WAF, DDoS, Zero Trust) in front of wherever it currently runs? — This is true for almost everything with an HTTP(S) surface, and the bar for "yes" is low.
2. **Is this workload a fit for Cloudflare's developer platform** (Workers/Pages/R2/D1/KV/Durable Objects/Queues) as primary compute? — This is a much narrower question, and the bar for "yes" is meaningfully higher. See [Positioning Cloudflare vs. Hyperscaler Cloud](positioning.md) for the underlying trade-offs.

## Workload characteristics → recommendation

| Characteristic | Recommendation |
|---|---|
| Public-facing HTTP(S) application, any backend | Proxy through Cloudflare (CDN/WAF/DDoS) — near-universal fit |
| High cache-hit-ratio content (static assets, largely-static pages) | Strong CDN fit; evaluate [Pages](https://developers.cloudflare.com/pages/) if also rebuilding the frontend |
| Stateless or lightly-stateful API, latency-sensitive, global user base | Strong developer platform fit — evaluate [Workers](https://developers.cloudflare.com/workers/) as primary compute |
| Needs a full OS, custom runtime, or long-lived local disk | Keep on hyperscaler/on-prem; Cloudflare in front only |
| Heavy relational workload with complex transactions/joins | Keep on hyperscaler-managed RDBMS; evaluate [D1](https://developers.cloudflare.com/d1/) only for access patterns it's designed for, not as a lift-and-shift target |
| Needs coordinated in-memory state per entity (e.g., a game session, a collaborative doc) | Evaluate [Durable Objects](https://developers.cloudflare.com/durable-objects/) specifically — this is close to its designed use case |
| Simple object/blob storage, especially if egress cost is a pain point today | Strong [R2](https://developers.cloudflare.com/r2/) fit |
| Background job processing, decoupled from a request/response cycle | Evaluate [Queues](https://developers.cloudflare.com/queues/) alongside Workers |
| GPU training or large-scale ML workloads | Hyperscaler — not a Cloudflare developer platform use case |
| Internal tool needing VPN-style access today | Strong [Zero Trust](../adopt/zero-trust-adoption.md) (Access/Tunnel) fit regardless of where the app itself runs |
| Regulatory/contractual requirement naming a specific cloud/region | Keep compute on the named platform; Cloudflare still fits in front |
| High-value target for credential stuffing, scraping, or bot abuse (login, checkout, inventory, ticketing) | Prioritize [Bot Management](https://developers.cloudflare.com/bots/) and [API Shield](https://developers.cloudflare.com/api-shield/) regardless of compute location |
| API with a published or discoverable schema, third-party consumers | [API Shield](https://developers.cloudflare.com/api-shield/) fit — schema validation, discovery |
| Loads third-party scripts on customer-facing pages (analytics, payment, chat widgets) | [Client-Side Security](https://developers.cloudflare.com/client-side-security/) (formerly Page Shield) fit |

## Step 2: Ask these questions per workload

- **What's the state model?** Fully stateless, session-scoped, or requires strongly consistent shared state? This is usually the single biggest determinant of developer-platform fit.
- **What's the traffic shape?** Global and latency-sensitive favors the edge; predictable and regional makes the case weaker (though the security layer still applies).
- **What's the compliance/data-residency constraint, if any?** This can rule out the developer platform even when the technical fit is otherwise strong — verify current data residency capabilities against [Cloudflare's documentation](https://developers.cloudflare.com/data-localization/) rather than assuming.
- **What's the team's runway to learn a new programming model?** Workers' isolate-based model is not a container or VM mental model — factor in ramp-up time honestly, don't just evaluate the technology.
- **Is this a net-new build or a migration of something existing?** New builds have much more freedom to fit the platform's model; migrations need to weigh re-architecture cost against the benefit.

## Decisions to make

- **Don't force a workload onto the developer platform because "Cloudflare is strategic."** A poor technical fit produces a bad first impression that damages the broader adoption effort. Use the security/network layer instead and revisit later.
- **Do proxy almost everything through Cloudflare's network/security layer** — the bar for that decision is low and the value (DDoS, WAF, performance) is close to universal for public HTTP(S) surfaces.
- **Treat the first developer-platform workload as a deliberate pilot** (see [Building on the Developer Platform](../adopt/developer-platform.md)) — pick something genuinely well-suited, not the most politically visible project, so the pilot succeeds on technical merits.

## Further reading

- [Positioning Cloudflare vs. Hyperscaler Cloud](positioning.md)
- [Workers overview](https://developers.cloudflare.com/workers/)
- [Durable Objects overview](https://developers.cloudflare.com/durable-objects/)
- [D1 overview](https://developers.cloudflare.com/d1/)
- [R2 overview](https://developers.cloudflare.com/r2/)
- [Data localization](https://developers.cloudflare.com/data-localization/)
- [Bot Management overview](https://developers.cloudflare.com/bots/)
- [API Shield overview](https://developers.cloudflare.com/api-shield/)
