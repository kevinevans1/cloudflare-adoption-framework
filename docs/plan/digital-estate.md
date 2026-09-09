--8<-- "_snippets/disclaimer.md"

# Digital Estate & Onboarding Inventory

You can't plan a migration around infrastructure you haven't catalogued. Before any zone touches Cloudflare, inventory what you actually have — domains, DNS records, existing CDN/WAF vendors, origin infrastructure, and the applications that depend on all of it. It's unglamorous work, and the single best predictor of whether a migration goes smoothly or turns into a string of surprise outages.

Unlike a hyperscaler migration, where "digital estate" usually means VMs, databases, and applications, a Cloudflare onboarding inventory centers on **domains and traffic flow**: what resolves what, what proxies what, and what depends on IP addresses or DNS behavior staying exactly as they are today.

## What to inventory

| Category | What to capture | Why it matters |
|---|---|---|
| **Domains / zones** | Every domain and subdomain in use, including ones "nobody remembers" (marketing microsites, legacy redirects, internal tools) | Each becomes a [zone](https://developers.cloudflare.com/fundamentals/concepts/accounts-and-zones/) in Cloudflare; missed domains mean gaps in coverage |
| **DNS records** | Full zone file export from the current authoritative DNS provider — A/AAAA, CNAME, MX, TXT (especially SPF/DKIM/DMARC), NS, SRV, CAA | Cloudflare's DNS import needs a complete, accurate source; missing MX or SPF records breaks mail flow immediately on cutover |
| **Current CDN/WAF/DDoS vendor** | What's in front of each domain today, what features are actively used (custom WAF rules, rate limiting, specific cache rules) | Every active rule needs an equivalent on Cloudflare before cutover, or functionality regresses |
| **Origin infrastructure** | IP addresses, hosting provider, TLS certificate details, any IP allow-listing on the origin firewall | Origins often allow-list only their current CDN's IP ranges — this must be updated to allow Cloudflare's ranges before cutover or the origin becomes unreachable |
| **Applications and dependencies** | Which application teams own which domains, any hardcoded IPs or CDN-vendor-specific behavior in application code | Determines migration order and who needs to be in the room for each wave |
| **TLS/certificate model** | Where certificates are issued and managed today, whether client-certificate (mTLS) requirements exist | Determines SSL/TLS mode and certificate strategy on Cloudflare |
| **Email routing** | Whether email is hosted on the domain being migrated | MX records must be preserved exactly; Cloudflare doesn't host email, so this is purely a DNS-preservation concern |

## How to build the catalog

1. **Export existing zone files** from every current DNS provider — this is your ground truth, not a re-creation from memory.
2. **Cross-reference against certificate transparency logs and subdomain enumeration** for domains that might not be in anyone's spreadsheet (a common source of "shadow" subdomains).
3. **Interview each application team** that owns a domain about anything vendor-specific in their current CDN/WAF configuration — rate limits, custom rules, redirect logic — that needs to be replicated.
4. **Document current origin firewall rules** related to CDN IP allow-listing specifically, since this is the most common cause of a "site went down after migration" incident.
5. **Tag each domain with a target wave** once the inventory is complete — this feeds directly into [Migration Planning](migration-planning.md).

## Design considerations

- **Treat DNS record export as a hard prerequisite, not a nice-to-have.** [Cloudflare's DNS](https://developers.cloudflare.com/dns/) can import a zone file directly, but only if you have a complete and accurate one — a partial export silently drops records.
- **Decide full setup vs. partial (CNAME) setup per zone during this phase**, not during cutover. A [full setup](https://developers.cloudflare.com/dns/zone-setups/) makes Cloudflare the authoritative DNS provider; a [partial/CNAME setup](https://developers.cloudflare.com/dns/zone-setups/partial-setup/) keeps your existing provider authoritative and only proxies specific hostnames through Cloudflare — useful when you can't move DNS hosting but still want the security/performance benefits. Partial setup has its own [plan-tier requirements](https://developers.cloudflare.com/dns/zone-setups/partial-setup/) — verify current tier eligibility before committing.
- **Inventory scale drives account structure**, not the other way around — a handful of domains under one team looks very different from hundreds of zones across many business units, and that should feed directly into [Accounts & Organizations](../foundation/accounts-and-organizations.md).

## Checklist

- [ ] Complete zone file export for every domain, from the actual authoritative provider
- [ ] Subdomain/shadow-IT sweep completed (certificate transparency logs, internal asset inventories)
- [ ] Current CDN/WAF/DDoS vendor features documented per domain
- [ ] Origin IP allow-list dependencies identified and a plan to update them to Cloudflare's ranges
- [ ] Email (MX/SPF/DKIM/DMARC) records explicitly verified for domains that host mail
- [ ] Full vs. partial setup decision made per zone
- [ ] Application owner identified and interviewed for every domain

## Further reading

- [Accounts, zones, and profiles · Fundamentals](https://developers.cloudflare.com/fundamentals/concepts/accounts-and-zones/)
- [DNS zone setups](https://developers.cloudflare.com/dns/zone-setups/)
- [CNAME setup (Partial)](https://developers.cloudflare.com/dns/zone-setups/partial-setup/)
- [Cloudflare Fundamentals](https://developers.cloudflare.com/fundamentals/)
- [Cloudflare DNS overview](https://developers.cloudflare.com/dns/)
