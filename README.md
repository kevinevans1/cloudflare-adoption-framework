# Cloudflare Adoption Framework

An independent, community-maintained framework for planning, deploying,
governing, and operating Cloudflare at organizational scale — following the
phase-based "cloud adoption framework" pattern common across the cloud
industry, adapted to how Cloudflare's platform actually works: a
specialized, developer-first network, security, and application platform
rather than a general-purpose IaaS hyperscaler.

**Read it here:** https://kevinevans1.github.io/cloudflare-adoption-framework/

> [!IMPORTANT]
> This is an independent project, not affiliated with or endorsed by
> Cloudflare, Inc. See [`NOTICE.md`](NOTICE.md) for the full disclaimer.

## What this is

Seven phases, built around Cloudflare's actual product surface (Zero
Trust/SASE, the developer platform, network services, and application
security) instead of virtual machines and subscriptions:

1. **Strategy** — why Cloudflare, and where it fits next to (or instead of)
   a hyperscaler
2. **Plan** — inventory, plan-tier selection, skills readiness
3. **Foundation** — account/org structure, identity, DNS, network, and IaC
   foundations (the "landing zone" equivalent)
4. **Adopt** — migrating DNS and traffic, rolling out edge security, Zero
   Trust adoption, building on the developer platform
5. **Govern** — guardrails, cost governance, compliance mapping, change
   management
6. **Secure** — shared responsibility, edge security baseline, Zero Trust
   architecture, data protection, incident response
7. **Manage** — observability, FinOps, performance and reliability
   operations, continuous improvement

See also its companion project, [**Cloudflare Well-Architected**](https://github.com/kevinevans1/cloudflare-well-architected),
which provides pillar-level design guidance for individual workloads.

## Running the site locally

```bash
pip install -r requirements.txt
mkdocs serve
```

## Contributing

Corrections and additions are welcome. Every factual claim (a limit, a
price, a specific capability) must link to the current, canonical page on
`developers.cloudflare.com` or `cloudflare.com` — see [`NOTICE.md`](NOTICE.md)
for why.
