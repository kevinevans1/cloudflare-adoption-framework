--8<-- "_snippets/disclaimer.md"

# DNS Foundation

DNS is where almost every organization's Cloudflare adoption starts, and it's the piece of foundation work most likely to get rushed. That's a mistake: DNS is the trust anchor for everything else you do on Cloudflare. Whether a hostname is proxied determines whether the WAF, bot management, and caching you configure later actually apply to it. How you delegate authority (full nameserver setup vs. partial CNAME setup) determines how much of your DNS estate Cloudflare can see and protect.

Get this right before touching security or performance features, and every later phase builds on a clean, understood record set instead of an inherited pile of unexplained entries.

## Key Cloudflare capabilities

| Capability | What it does | Reference |
|---|---|---|
| Full setup (nameserver delegation) | Cloudflare becomes your authoritative DNS provider; you point your domain's nameservers at Cloudflare | [Full setup](https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/) |
| Partial (CNAME) setup | Keep your existing DNS provider authoritative; CNAME specific hostnames to Cloudflare for proxying | [CNAME setup (Partial)](https://developers.cloudflare.com/dns/zone-setups/partial-setup/) |
| Proxy status (orange/grey cloud) | Per-record toggle for whether traffic routes through Cloudflare's network or resolves directly to your origin | [Proxy status](https://developers.cloudflare.com/load-balancing/understand-basics/proxy-modes/) |
| DNSSEC | Cryptographically signs your zone and publishes a DS record at your registrar to prevent DNS spoofing | [DNSSEC](https://developers.cloudflare.com/dns/dnssec/) |
| DNS record types | A, AAAA, CNAME, MX, TXT, and more, each with different proxy eligibility | [DNS record types](https://developers.cloudflare.com/dns/manage-dns-records/reference/dns-record-types/) |
| TTL | Per-record time-to-live; proxied records default to Auto (effectively short) | [Time to Live (TTL)](https://developers.cloudflare.com/dns/manage-dns-records/reference/ttl/) |

## Design considerations

**Full setup vs. partial (CNAME) setup.** Full setup — delegating your nameservers to Cloudflare — makes it your authoritative DNS provider. It's the default, most capable path: required for DNS-layer DDoS protection and the full feature set. Partial (CNAME) setup keeps an existing provider authoritative and selectively CNAMEs individual hostnames to Cloudflare — useful when a platform team doesn't fully control the corporate DNS provider, or during a phased migration where handing over nameservers isn't yet an option. Real constraints apply, though:

- Only available on Business and Enterprise plans.
- CNAME records can't sit at a zone apex unless your upstream provider supports CNAME flattening.
- DNS-layer DDoS protection doesn't extend to a partial setup the way it does to a full one.

Treat partial setup as a valid transitional or permanently constrained option, not a lesser version of the same thing.

**Orange-cloud (proxied) vs. grey-cloud (DNS-only) is a per-record decision, not a zone-wide one.** Only A, AAAA, and CNAME records can be proxied. Rule of thumb: orange-cloud anything serving HTTP/HTTPS that should get Cloudflare's CDN, WAF, and DDoS protection. Keep non-web-protocol records — mail (MX targets), SSH, FTP, database endpoints, VPN — grey-clouded, since the proxy only understands HTTP(S) and a small set of protocols via Spectrum. Domain-verification CNAMEs for SaaS tools should also stay DNS-only. Getting this wrong causes real outages either way: proxying a non-HTTP service breaks it outright; forgetting to proxy a web-facing record exposes your origin IP directly, bypassing every edge control you've configured.

**DNSSEC protects the zone itself, not the records' contents.** It prevents DNS spoofing and cache-poisoning by having Cloudflare sign your zone and publish a DS record at your registrar. One-time setup, negligible ongoing cost — enable it on any full-setup zone. Gotcha: removing Cloudflare as your DNS provider later requires removing the DS record first, or the domain becomes unresolvable.

**Record hygiene is a discipline, not a one-time cleanup.** The most common DNS foundation failure is inherited cruft: stale CNAMEs pointing at decommissioned SaaS tools, forgotten TXT records from abandoned verification attempts, orphaned A records for dead servers. Every unexplained record is either a security risk (a dangling CNAME invites subdomain takeover) or a migration hazard (nobody wants to delete a record and break something unrelated). Audit the full record set before onboarding a domain — see [Migrating DNS](../adopt/migrating-dns.md) — and require an owner and purpose for every record going forward, per your [naming and tagging](naming-and-tagging.md) discipline.

**TTL matters most right before a change.** Proxied records use an Auto TTL that Cloudflare manages, effectively short by default. For DNS-only records, lower the TTL well before a planned migration or origin change — not during it — so DNS caches worldwide pick up the new value quickly at cutover. Raise it back afterward if stability is a priority.

## Decisions to make

| Decision | Choose full (nameserver) setup when… | Choose partial (CNAME) setup when… |
|---|---|---|
| DNS authority | You can migrate nameservers, or are building fresh | Another team/provider must remain DNS-authoritative |
| DDoS protection scope | You need DNS-layer DDoS protection | Acceptable to rely on proxied-record protection only |
| Plan tier | Any plan | Business or Enterprise only |
| Rollout pace | Comfortable cutting over the whole domain | Testing proxying on select hostnames first |

| Record type | Typical proxy status | Why |
|---|---|---|
| `www`, `app`, apex A/AAAA for web traffic | Orange-clouded | Gets CDN, WAF, DDoS protection |
| `mail`, MX targets | Grey-clouded (must be) | SMTP isn't proxied |
| SaaS domain-verification TXT/CNAME | Grey-clouded | Verification checks expect the real target |
| `vpn`, `ssh`, internal service records | Grey-clouded | Non-HTTP protocol |
| Staging/internal CNAMEs during rollout | Grey-clouded, then flip to orange | Phased security rollout pattern — see [Onboarding a Site](../adopt/onboarding-a-site.md) |

## Checklist

- [ ] Chosen full setup or partial (CNAME) setup deliberately, with the trade-offs above documented
- [ ] Every existing DNS record audited for owner, purpose, and correct proxy status before go-live
- [ ] Non-HTTP records (mail, SSH, VPN, databases) confirmed grey-clouded
- [ ] DNSSEC enabled on full-setup zones, with the DS record published at the registrar
- [ ] TTLs lowered in advance of any planned cutover, then restored afterward
- [ ] Record-naming and ownership convention agreed — see [Naming & Tagging Conventions](naming-and-tagging.md)
- [ ] Rollback plan documented before any nameserver or proxy-status change — see [Migrating DNS](../adopt/migrating-dns.md)

## Further reading

- [Full setup](https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/)
- [CNAME setup (Partial)](https://developers.cloudflare.com/dns/zone-setups/partial-setup/)
- [Convert full setup to partial setup](https://developers.cloudflare.com/dns/zone-setups/conversions/convert-full-to-partial/)
- [Proxy status](https://developers.cloudflare.com/load-balancing/understand-basics/proxy-modes/)
- [DNSSEC](https://developers.cloudflare.com/dns/dnssec/)
- [DNS record types](https://developers.cloudflare.com/dns/manage-dns-records/reference/dns-record-types/)
- [Time to Live (TTL)](https://developers.cloudflare.com/dns/manage-dns-records/reference/ttl/)
