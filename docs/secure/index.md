--8<-- "_snippets/disclaimer.md"

# Secure

Security on Cloudflare isn't a gate you pass through once during onboarding — it's a phase that runs continuously, in parallel with every other phase in this framework. A zone that passed a security review at [Adopt](../adopt/rolling-out-security.md) time can be actively misconfigured six months later: a new route got added, a managed ruleset shipped a new detection that needs tuning, or a Zero Trust policy never got updated when the app inventory changed.

## Why Secure runs in parallel, not in sequence

Most of this framework's phases have a natural before/after relationship: you plan before you adopt, you adopt before you optimize. Security doesn't fit that shape cleanly, because:

- New zones, Workers, and Zero Trust applications get added continuously during [Adopt](../adopt/index.md) — each one needs the same security baseline as the first one, not a one-time review.
- Cloudflare ships new managed ruleset versions and detection capabilities on its own schedule, independent of your release cycle.
- Threats change. A bot-mitigation posture tuned for last year's scraping patterns isn't automatically adequate for this year's.
- [Govern](../govern/index.md) depends on Secure staying current — a compliance mapping or cost-governance baseline built on a stale security posture is itself stale.

Treat this section as the reference architecture to configure once and *re-verify* continuously, not a checklist to close out.

## What this phase covers

- **[Shared Responsibility Model](shared-responsibility.md)** — where Cloudflare's default protections end and your configuration responsibility begins.
- **[Edge Security Baseline](edge-security-baseline.md)** — the layered stack (DDoS, WAF, Bot Management, Rate Limiting, API Shield, Client-Side Security) that sits in front of public-facing traffic.
- **[Zero Trust Security Architecture](zero-trust-architecture.md)** — Access, Gateway, Tunnel, and the Cloudflare One Client (formerly WARP) composed into a SASE model for private/internal access and outbound traffic control.
- **[Data Protection](data-protection.md)** — encryption in transit and at rest, origin hardening, and secrets handling.
- **[Incident Response & Status Monitoring](incident-response.md)** — what to do when something is actively going wrong, and how to learn from it afterward.

## Scope: four security domains

| Domain | What it covers | Where it's addressed |
|---|---|---|
| Network | DDoS absorption, network-layer filtering, Magic Transit/WAN-style protections | [Edge Security Baseline](edge-security-baseline.md) |
| Application | WAF, Bot Management, Rate Limiting, API Shield, Client-Side Security | [Edge Security Baseline](edge-security-baseline.md) |
| Identity & access | Zero Trust Access policies, device posture, Gateway filtering | [Zero Trust Security Architecture](zero-trust-architecture.md) |
| Data | TLS/mTLS, encryption at rest, DLP, secrets management | [Data Protection](data-protection.md) |

## How to use this section

Read [Shared Responsibility Model](shared-responsibility.md) first — it sets the frame for everything else: nothing here is automatic just because traffic is proxied through Cloudflare. From there, [Edge Security Baseline](edge-security-baseline.md) and [Zero Trust Security Architecture](zero-trust-architecture.md) cover the two major traffic patterns — public-facing apps and private/internal access — side by side, since most estates need both.

## Further reading

- [Cloudflare security center](https://developers.cloudflare.com/security-center/)
- [Cloudflare Zero Trust / Cloudflare One documentation](https://developers.cloudflare.com/cloudflare-one/)
- [Cloudflare Trust Hub](https://www.cloudflare.com/trust-hub/)

See also: [Govern](../govern/index.md) for the organizational controls around this technical architecture, and [Rolling Out Edge Security](../adopt/rolling-out-security.md) for the initial onboarding sequence.
