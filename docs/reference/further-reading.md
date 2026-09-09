# Further Reading & Sources

--8<-- "_snippets/disclaimer.md"

A curated set of official Cloudflare resources for going deeper than any single page here. Every substantive claim elsewhere in this framework should trace back to something in this list or a page it links to — start here to verify a claim or go beyond what a given page covers.

## Core documentation

| Resource | Best for |
|---|---|
| [developers.cloudflare.com](https://developers.cloudflare.com) | The canonical source for every product's current capabilities, limits, and configuration — the primary source this entire framework is built from |
| [Cloudflare changelog](https://developers.cloudflare.com/changelog/) | Tracking what changed, product by product, since you last checked — see [Continuous Improvement](../manage/continuous-improvement.md) |
| [Cloudflare API documentation](https://developers.cloudflare.com/api/) | The full REST API reference underlying the dashboard, Terraform provider, and Wrangler |
| [Cloudflare Learning Paths](https://developers.cloudflare.com/learning-paths/) | Structured, task-oriented walkthroughs for specific goals (e.g., securing internet traffic, clientless access) |

## Solution and architecture depth

| Resource | Best for |
|---|---|
| [Reference Architecture library](https://developers.cloudflare.com/reference-architecture/) | Solution-level architectures, diagrams, design guides, and implementation guides — see [Related Frameworks](related-frameworks.md) for how this framework points into it |
| [Cloudflare Well-Architected](https://github.com/kevinevans1/cloudflare-well-architected) | Workload-level pillar guidance (Reliability, Security, Cost Optimization, Operational Excellence, Performance Efficiency) — the companion to this framework |
| [architectingoncloudflare.com](https://architectingoncloudflare.com) | Independent, third-party depth on the developer platform (Workers, Pages, R2, D1) — not affiliated with Cloudflare |

## Learning and general education

| Resource | Best for |
|---|---|
| [Cloudflare Learning Center](https://www.cloudflare.com/learning/) | Plain-language explainers on Internet/networking/security concepts (DDoS, CDN, DNS, SSL/TLS, Zero Trust) — useful for bringing non-specialist stakeholders up to speed |
| [Cloudflare Radar](https://radar.cloudflare.com) | Global Internet traffic, attack, and technology trend data drawn from Cloudflare's network — useful context when making a business case in [Business Justification](../strategy/business-justification.md) or investigating an incident in [Incident Response & Status Monitoring](../secure/incident-response.md) |
| [The Cloudflare Blog](https://blog.cloudflare.com) | Product launch announcements and architecture/engineering deep dives directly from Cloudflare's own teams — a good source for understanding *why* a product works the way it does, though treat it as commentary rather than a substitute for the reference docs on current behavior |

## Community and support

| Resource | Best for |
|---|---|
| [Cloudflare Community forum](https://community.cloudflare.com) | Peer troubleshooting, shared configuration patterns, and direct engagement with Cloudflare staff on specific issues |
| [Cloudflare Developers Discord](https://discord.com/invite/cloudflaredev) | Real-time discussion with other developers building on the Cloudflare platform, particularly Workers and the rest of the developer platform |
| [Cloudflare Status](https://www.cloudflarestatus.com) | Current and historical platform incident status — the starting point during any suspected Cloudflare-side issue, referenced in [Incident Response & Status Monitoring](../secure/incident-response.md) |

## How to use this list

- Start with [developers.cloudflare.com](https://developers.cloudflare.com) for anything specific and current — plan limits, pricing, exact configuration steps — since this framework deliberately avoids restating numbers that change over time.
- Use the Reference Architecture library and Cloudflare Well-Architected once you've moved from "should we adopt this" (this framework's job) to "how exactly should we build this" (their job).
- Use the Learning Center and Radar for framing and stakeholder communication, not as a technical reference for configuration decisions.
- If you find a broken link or a claim in this framework that no longer matches current Cloudflare documentation, that's exactly the kind of drift this list exists to catch — file it against the framework's repository.
