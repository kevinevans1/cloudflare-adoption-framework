# Migration Planning

Migrating to Cloudflare is rarely a single cutover — it's a series of smaller, independently reversible changes, each validated before the next happens. Organizations that have a bad time treat "move to Cloudflare" as one event: flip DNS for every domain at once, turn on every WAF managed ruleset in block mode simultaneously, and hope nothing breaks. Organizations that have a good time break it into waves, sequence low-risk changes before high-risk ones, and keep a rollback path open at every step.

This page assumes you've already completed the inventory in [Digital Estate & Onboarding Inventory](digital-estate.md) — you need to know what you're migrating before you can sequence it.

## Wave planning for multiple zones or domains

Group domains into waves based on risk and complexity, not alphabetical order or organizational politics:

| Wave characteristic | Example | Sequencing guidance |
|---|---|---|
| Low traffic, low business criticality, simple DNS | Internal tools, marketing microsites, staging environments | Migrate first — validates the process with low blast radius |
| Moderate traffic, standard DNS, no exotic origin dependencies | Most production application domains | Migrate in the middle waves, after the process is proven |
| High traffic, business-critical, complex DNS (partial setups, many subdomains, email hosted) | Primary customer-facing domain, e-commerce checkout | Migrate last, with the most validation and the most stakeholders in the room |
| Anything with hardcoded IP dependencies, custom TLS/mTLS requirements, or exotic origin firewall rules | Legacy systems, B2B integrations with IP allow-listing on the far end | Treat as its own wave regardless of traffic volume — these need bespoke validation |

Within each wave, cut over one or a handful of domains, hold for a validation window (hours to days depending on traffic pattern and criticality), then proceed to the next batch.

## Low-risk-first sequencing

Two specific sequencing patterns matter more than any other piece of migration advice on this page:

- **DNS-only ("grey cloud") before proxying.** Add the zone to Cloudflare and import DNS records with proxying *disabled* (grey cloud, not orange) first. This validates that DNS resolves correctly and nothing is missing from the import, without changing how traffic flows. Enable the proxy (orange cloud) only once DNS is confirmed correct — that isolates DNS import errors from proxy/WAF behavior changes, so when something breaks you know which layer caused it.
- **Log mode before block mode for WAF and rate limiting.** Every new [WAF](https://developers.cloudflare.com/waf/) managed ruleset, custom rule, or [rate limiting rule](https://developers.cloudflare.com/waf/rate-limiting-rules/) should run in log-only mode first, against real production traffic, before switching to block/challenge. Review the log output for false positives against your application's legitimate traffic patterns before flipping to enforcement. Skipping this step is the single most common cause of a security rollout causing a self-inflicted outage.

## Rollback strategy

- **Keep the previous DNS provider's zone file and TTLs documented** so a revert is a known, tested action, not an improvisation under pressure.
- **Lower TTLs on records before migration**, not during an incident — a low TTL going into a cutover means a rollback propagates fast; a high TTL discovered mid-incident means you're stuck for however long it was set.
- **For proxied zones, know how to quickly disable the proxy (revert to grey cloud) per-record** as an immediate mitigation if proxying introduces unexpected behavior, without needing a full DNS rollback.
- **For WAF/security rollouts, keep the previous vendor's equivalent control active in parallel during the validation window** where feasible, rather than removing it the moment Cloudflare's control goes live — redundancy during transition is cheaper than an exposure gap.
- **Define an explicit rollback owner and decision criteria per wave** before the wave starts — "who decides to roll back, and based on what signal" should never be improvised live.

## Stakeholder communication

- **Notify application owners before their domain's wave**, not after — they need to know when to watch for anomalies and who to contact.
- **Give customer support and status-page owners a heads-up before any customer-facing domain's cutover**, even a low-risk one — an unrelated issue during a migration window is much easier to triage if support already knows a change happened.
- **Set explicit validation windows and communicate them** — "we'll consider this wave stable after 48 hours of normal traffic" gives everyone a shared understanding of when to stop watching closely and when it's safe to proceed.
- **Keep a running migration log** (what changed, when, by whom, validation result) — this becomes the record you need if something breaks days later and the cause isn't obvious.

## Checklist

- [ ] Domains grouped into waves by risk/complexity, not convenience
- [ ] DNS TTLs lowered ahead of each wave's cutover window
- [ ] Each wave starts DNS-only (grey cloud) before enabling proxying
- [ ] Every new WAF/rate-limiting rule runs in log mode before block mode, validated against real traffic
- [ ] Rollback owner and decision criteria defined per wave, before the wave starts
- [ ] Application owners, support, and status-page owners notified ahead of their wave
- [ ] Migration log maintained throughout

## Further reading

- [Digital Estate & Onboarding Inventory](digital-estate.md)
- [DNS zone setups](https://developers.cloudflare.com/dns/zone-setups/)
- [WAF overview](https://developers.cloudflare.com/waf/)
- [Rate limiting rules](https://developers.cloudflare.com/waf/rate-limiting-rules/)
- [Onboarding a Site (CDN/Proxy)](../adopt/onboarding-a-site.md)
- [Migrating DNS](../adopt/migrating-dns.md)
