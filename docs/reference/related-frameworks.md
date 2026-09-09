# Related Frameworks

--8<-- "_snippets/disclaimer.md"

This framework doesn't stand alone. It sits at one altitude — the organizational, phase-based adoption lifecycle — and points outward to other resources for depth it doesn't try to replace. This page explains how the pieces fit together.

## Cloudflare's official Reference Architecture library

[developers.cloudflare.com/reference-architecture](https://developers.cloudflare.com/reference-architecture/) is Cloudflare's own, authoritative library of solution-level architecture guidance. It's organized into a small number of content types, each answering a different question:

| Content type | Question it answers |
|---|---|
| Reference Architectures | "What's the recommended overall shape for this category of solution?" (e.g., CDN, SASE, Magic Transit) |
| Reference Architecture Diagrams | "What does a specific pattern look like end to end?" — narrower, visual, scenario-specific |
| Design Guides | "What are the best practices and trade-offs to consider when designing this?" |
| Implementation Guides | "What are the concrete steps to deploy this?" |

**This framework is a complementary, higher-level layer, not a substitute.** This framework walks an organization through the lifecycle of *adopting Cloudflare as a platform* — strategy, planning, landing zone, rollout, governance, security posture, and ongoing operations. The Reference Architecture library gives you the actual solution-level design once you know which problem you're solving. Most detail pages here link out to Reference Architecture pages at the point where "how do I structure this specific thing" replaces "what should I be deciding at this stage of adoption."

Trust the Reference Architecture library over this framework wherever the two might disagree — it's Cloudflare's own, product-team-maintained source of truth, while this framework is an independent, community-maintained synthesis of it.

## Cloudflare Well-Architected (companion project)

[cloudflare-well-architected](https://github.com/kevinevans1/cloudflare-well-architected) is a separate, companion project covering **workload-level pillars** — Reliability, Security, Cost Optimization, Operational Excellence, and Performance Efficiency. Like any well-architected framework, it's a set of design principles and review questions you apply to a specific workload, regardless of lifecycle phase.

The relationship between the two projects is one of altitude, not overlap:

- **This framework (Cloudflare Adoption Framework)** answers "how does our organization roll Cloudflare out, govern it, and operate it over time?"
- **Cloudflare Well-Architected** answers "is *this specific workload* built well on Cloudflare, judged against the five pillars?"

Pages in this framework's [Manage](../manage/index.md) phase — particularly [Reliability Operations](../manage/reliability-operations.md) — link into the Well-Architected repo's Reliability pillar for the deeper, workload-specific version of guidance this framework only covers at the operating-model level. Expect more of those cross-links to appear as both projects mature.

## architectingoncloudflare.com

[architectingoncloudflare.com](https://architectingoncloudflare.com) is an independent, third-party resource focused on Cloudflare's developer platform (Workers, Pages, R2, D1, and related compute/storage products). Its own disclaimer states it is **not produced by, affiliated with, or endorsed by Cloudflare, Inc.** — it represents its author's independent analysis based on publicly available documentation and hands-on experience with the platform, the same posture this framework takes toward Cloudflare.

It's referenced here as a useful independent resource for developer-platform depth, not as an authority on Cloudflare's product behavior. Treat it like any well-regarded independent technical resource: good for architecture patterns and lessons learned, but verify anything load-bearing against [developers.cloudflare.com](https://developers.cloudflare.com) before relying on it.

## How the pieces fit together

```
Strategy → Plan → Foundation → Adopt → Govern / Secure / Manage   (this framework)
                                              │
                                              ▼
                              Cloudflare Reference Architecture library
                         (solution-level: Reference Architectures, Diagrams,
                              Design Guides, Implementation Guides)
                                              │
                                              ▼
                              Cloudflare Well-Architected (companion repo)
                         (workload-level: Reliability, Security, Cost,
                          Operational Excellence, Performance Efficiency)

              architectingoncloudflare.com — independent, developer-platform-focused,
                        useful alongside all of the above, authoritative for none of it
```

This framework follows the general phase-based adoption-lifecycle pattern common across the cloud industry — inventory and plan, establish a foundation, roll out, then govern and operate on an ongoing basis — adapted to how Cloudflare's platform actually works, not general-purpose IaaS concepts like virtual machines or provisioned capacity.

## Further reading

- [Cloudflare Reference Architecture library](https://developers.cloudflare.com/reference-architecture/)
- [How to use the Reference Architecture library](https://developers.cloudflare.com/reference-architecture/how-to-use/)
- [Cloudflare Well-Architected](https://github.com/kevinevans1/cloudflare-well-architected)
- [architectingoncloudflare.com](https://architectingoncloudflare.com)
