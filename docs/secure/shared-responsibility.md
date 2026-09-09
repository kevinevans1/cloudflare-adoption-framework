--8<-- "_snippets/disclaimer.md"

# Shared Responsibility Model

Cloudflare sits in a different architectural position than a hyperscaler: it's a reverse proxy, DNS authority, and network edge in front of infrastructure you may still run entirely elsewhere. That position changes the shape of the shared responsibility model — Cloudflare secures its own network and the services it operates by default; you're responsible for configuring the protections that apply to *your* traffic, *your* origin, and *your* policies on top of that network. Proxying traffic through Cloudflare does not automatically make an application secure.

## The core split

| Cloudflare's responsibility (by default) | Your responsibility (must be configured) |
|---|---|
| Physical and network security of Cloudflare's global edge infrastructure | Choosing and enforcing the [TLS mode](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) appropriate to your origin (Full/Full Strict, not Flexible, for anything sensitive) |
| Absorbing volumetric network-layer (L3/L4) DDoS traffic across the Anycast network — see [DDoS Protection](https://developers.cloudflare.com/ddos-protection/) | Enabling and tuning [WAF managed and custom rulesets](edge-security-baseline.md) for your specific applications |
| TLS termination options at the edge (including free/managed certificates) | Deciding whether the origin itself is hardened — [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/), firewalling the origin to only accept Cloudflare IP ranges, patching origin software |
| Availability and integrity of the Cloudflare platform itself (see [Cloudflare Status](https://www.cloudflarestatus.com/)) | Configuring [Access policies](https://developers.cloudflare.com/cloudflare-one/policies/access/) to control *who* can reach an internal application through Zero Trust |
| Maintaining and updating [managed ruleset](https://developers.cloudflare.com/waf/managed-rules/) rule content as threats evolve | Selecting the *mode* (Log/Challenge/Block) and scope those managed rulesets run in, and writing custom rules for app-specific logic |
| Underlying encryption-at-rest mechanics for platform storage services (e.g., [R2's automatic encryption](https://developers.cloudflare.com/r2/reference/data-security/)) | Application-layer secrets and credential handling — e.g., using [Workers secrets](https://developers.cloudflare.com/workers/configuration/secrets/) rather than hardcoding credentials in source |
| Providing [DNSSEC](https://developers.cloudflare.com/dns/dnssec/) signing infrastructure once enabled | Actually enabling DNSSEC on the zone and completing the DS record handoff at the registrar |
| Providing the [Rulesets engine](https://developers.cloudflare.com/ruleset-engine/), [Bot Management](https://developers.cloudflare.com/bots/), [Rate Limiting](https://developers.cloudflare.com/waf/rate-limiting-rules/), [API Shield](https://developers.cloudflare.com/api-shield/), and [Zero Trust](https://developers.cloudflare.com/cloudflare-one/) as available capabilities | Actually deploying and tuning each of those capabilities against real traffic — an unconfigured feature provides no protection |
| Publishing its own compliance/security posture via the [Trust Hub](https://www.cloudflare.com/trust-hub/) | Validating whether that posture satisfies *your* specific regulatory obligations — see [Compliance Mapping](../govern/compliance-mapping.md) |

This table is intentionally conservative: where a capability exists but requires explicit configuration to take effect (which is true of nearly everything in the "your responsibility" column), it's listed as customer-owned even if Cloudflare provides the underlying mechanism.

## Design considerations

### "Proxied" is not "protected"

Turning on the orange cloud (proxying) gets you network-layer DDoS absorption and TLS termination options by default. It does **not** get you WAF rules in an enforcing mode, Bot Management tuned to your traffic, or an origin that rejects direct (non-Cloudflare) connections — those are separate, deliberate configuration steps covered in [Edge Security Baseline](edge-security-baseline.md).

### The origin is still your problem

Cloudflare cannot protect an origin server that's directly reachable by attackers who bypass the proxy entirely (e.g., because the origin's real IP leaked in DNS history, an SPF record, or a misconfigured subdomain). Origin hardening — [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/), IP allowlisting to Cloudflare's published ranges, and rotating origin IPs if they've leaked — is entirely a customer responsibility, covered further in [Data Protection](data-protection.md).

### Zero Trust flips the model slightly

For [Zero Trust](zero-trust-architecture.md) (Access, Gateway, the Cloudflare One Client/WARP), Cloudflare provides the enforcement plane, but *every* policy decision — who can reach what, what device posture is required, what traffic categories Gateway blocks — is customer-authored. There is no sensible Zero Trust default; an unconfigured Access application with no policy typically fails closed, but that's a starting state to build from, not a security control by itself.

## Decisions to make

- [ ] Has the origin been hardened to reject any connection that didn't come through Cloudflare?
- [ ] Is the TLS mode Full Strict wherever the origin supports it?
- [ ] Are the WAF managed rulesets not just enabled, but in an enforcing mode after a log-only soak period (see [Change Management](../govern/change-management.md))?
- [ ] Does every internal application behind Zero Trust Access have an explicit policy, reviewed for correctness — not just "default deny and hope"?
- [ ] Has someone with compliance authority actually reviewed the [Trust Hub](https://www.cloudflare.com/trust-hub/) content relevant to your regulatory obligations, rather than assuming Cloudflare's certifications transfer automatically?

## Checklist

- [ ] TLS mode set to Full or Full Strict for all production zones handling sensitive data
- [ ] Origin firewalled to Cloudflare IP ranges and/or Authenticated Origin Pulls enabled
- [ ] WAF managed rulesets enabled and enforcing (not left in default/log-only state indefinitely)
- [ ] DNSSEC enabled where the registrar supports it
- [ ] Every Zero Trust Access application has an explicit, reviewed policy
- [ ] Compliance-relevant claims cross-checked against the current Trust Hub, not assumed

## Further reading

- [Cloudflare DDoS Protection](https://developers.cloudflare.com/ddos-protection/)
- [SSL/TLS encryption modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/)
- [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/)
- [WAF managed rules](https://developers.cloudflare.com/waf/managed-rules/)
- [R2 data security (encryption at rest)](https://developers.cloudflare.com/r2/reference/data-security/)
- [DNSSEC](https://developers.cloudflare.com/dns/dnssec/)
- [Cloudflare Status](https://www.cloudflarestatus.com/)
- [Cloudflare Trust Hub](https://www.cloudflare.com/trust-hub/)

See also: [Edge Security Baseline](edge-security-baseline.md) for the layered controls that turn these responsibilities into a working configuration, and [Compliance Mapping](../govern/compliance-mapping.md) for how this maps to regulatory obligations.
