--8<-- "_snippets/disclaimer.md"

# Choosing a Plan Tier

Cloudflare sells plans along two largely independent axes: the **zone/website plan** (Free, Pro, Business, Enterprise), which governs CDN, WAF, and performance features per domain; and the separate **Zero Trust plan**, which governs Access, Gateway, and the rest of Cloudflare One, typically priced per seat. Most organizations mix tiers — for example, Business-tier zones for customer-facing production domains and Free-tier zones for internal or low-risk properties — rather than picking one tier for the whole estate.

Plan features, limits, and pricing change frequently — don't treat any feature list as permanent. Verify current tier contents at [cloudflare.com/plans](https://www.cloudflare.com/plans/) and [cloudflare.com/plans/zero-trust-services](https://www.cloudflare.com/plans/zero-trust-services/) before deciding, and re-verify before renewal.

## Zone plan tiers: how to think about the decision

| Tier | Generally right for | Verify before committing |
|---|---|---|
| **Free** | Personal projects, low-risk internal tools, early evaluation of the platform | Whether it covers your minimum WAF/analytics needs — current feature list at [cloudflare.com/plans](https://www.cloudflare.com/plans/) |
| **Pro** | Small business or single-application production sites needing baseline WAF and image/performance optimization beyond Free | Rule limits, analytics retention window, and whether it supports the specific WAF managed rulesets you need |
| **Business** | Production customer-facing sites needing stronger WAF customization, faster support SLAs, and features like partial (CNAME) DNS setup | Whether partial setup, custom WAF rule volume, and support SLA meet your requirements |
| **Enterprise** | Multi-domain organizations, regulated industries, or anyone needing a dedicated SLA, account team, or advanced controls (custom WAF at scale, advanced Bot Management, dedicated certificates, multi-account tooling) | Everything — Enterprise is sales-led and contract-specific; get the current feature/SLA breakdown directly from your account team and cross-check against public docs |

## Zero Trust plan tiers: how to think about the decision

Cloudflare's Zero Trust family (Access, Gateway, Tunnel, and the broader Cloudflare One suite) is priced separately from zone plans, generally per user/seat. Tiers are commonly structured around a free tier for small teams, a self-serve paid tier, and an Enterprise tier with expanded DLP, CASB, browser isolation, and log-retention capabilities. Confirm current tier names, seat limits, and feature boundaries at [cloudflare.com/plans/zero-trust-services](https://www.cloudflare.com/plans/zero-trust-services/) and the [Cloudflare One documentation](https://developers.cloudflare.com/cloudflare-one/) — this is one of the faster-moving parts of Cloudflare's packaging.

## Enterprise-only considerations

Enterprise tiers (both zone and Zero Trust) typically unlock things that matter specifically at organizational scale rather than per-domain:

- **Dedicated SLAs and support** — guaranteed response times and an assigned account team, materially different from self-serve support queues.
- **Multi-account / partner tooling** — relevant for an agency, MSP, or large enterprise managing many customer or business-unit zones under one umbrella; self-serve tiers aren't built for managing dozens or hundreds of independently-owned zones.
- **Advanced security controls at scale** — expanded Bot Management granularity, custom WAF rule volume, and dedicated egress IPs for Zero Trust are commonly Enterprise-gated.
- **Contractual terms** — data processing agreements, custom uptime commitments, and procurement-friendly billing that self-serve tiers don't offer.

## Design considerations

- **A single-domain startup rarely needs Enterprise.** The multi-account/partner tooling and dedicated account team that justify Enterprise pricing are wasted on an organization with one or two zones and no compliance mandate requiring a dedicated SLA.
- **An agency or MSP managing many customer zones needs Enterprise (or at least Business) specifically for the multi-account/partner tooling**, independent of whether any single customer's traffic volume would otherwise justify it — the operational tooling is the point, not the WAF feature list.
- **Don't assume zone tier and Zero Trust tier need to match.** It's common and reasonable to run Business-tier zones with a lower Zero Trust tier (Free, or the self-serve paid tier — confirm its current name, as this has changed at least once) if your Zero Trust use case (e.g., replacing VPN for a small internal team) doesn't need Enterprise-only DLP/CASB features.
- **Re-evaluate at renewal, not just at initial adoption.** Cloudflare's packaging changes; a feature that justified Enterprise last year may now be available at a lower tier, or vice versa.

## Decision checklist

- [ ] Listed every domain and assigned it a target zone tier based on actual risk/traffic profile, not a blanket default
- [ ] Confirmed current feature boundaries at cloudflare.com/plans for each tier under consideration
- [ ] Separately assessed Zero Trust seat count and required DLP/CASB/browser-isolation features against current Zero Trust tiers
- [ ] Determined whether multi-account/partner tooling is a requirement (agency/MSP/large multi-BU org) independent of traffic volume
- [ ] Identified whether any compliance or contractual requirement forces Enterprise regardless of feature need (dedicated SLA, DPA terms)
- [ ] Established a renewal-time process to re-verify tier fit against current Cloudflare packaging

## Further reading

- [Cloudflare plans](https://www.cloudflare.com/plans/)
- [Zero Trust / SASE plans](https://www.cloudflare.com/plans/zero-trust-services/)
- [Cloudflare One overview](https://developers.cloudflare.com/cloudflare-one/)
- [DNS zone setups (partial setup plan requirements)](https://developers.cloudflare.com/dns/zone-setups/partial-setup/)
- [Accounts & Organizations](../foundation/accounts-and-organizations.md)
