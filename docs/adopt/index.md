--8<-- "_snippets/disclaimer.md"

# Adopt

With a [Foundation](../foundation/index.md) in place — account structure decided, identity locked down, DNS and (if relevant) network connectivity established, IaC and naming conventions ready — the Adopt phase is where Cloudflare adoption produces value. It splits into two tracks most organizations run in parallel, not sequentially:

1. **Migrating existing traffic and security onto Cloudflare** — moving DNS, putting existing sites behind Cloudflare's CDN/proxy, and rolling out edge security (WAF, rate limiting, bot management) and Zero Trust network access for workloads and users that already exist elsewhere.
2. **Building new things on the developer platform** — using Workers, Pages, R2, D1, KV, Durable Objects, and Queues to build new applications natively on Cloudflare's edge, with CI/CD wired in from day one.

These tracks carry different risk and pacing. Migrating a production website's DNS or turning on a new WAF managed ruleset can take down real traffic if done carelessly — every page in the migration track below is built around staged, reversible rollout. Building a new Worker-based service has a much smaller blast radius by default (a new codebase, not existing production traffic), so the developer-platform track is more about good early architectural choices than careful staging.

## Migration track

| Page | Covers |
|---|---|
| [Migrating DNS](migrating-dns.md) | Auditing existing records, grey-cloud-first rollout, nameserver cutover vs. CNAME setup, rollback planning |
| [Onboarding a Site](onboarding-a-site.md) | Putting a site behind Cloudflare's proxy: TLS mode selection, caching, origin protection, health checks |
| [Rolling Out Edge Security](rolling-out-security.md) | Staged WAF, rate limiting, and bot management rollout — log-only before block, zone by zone |
| [Zero Trust Adoption Path](zero-trust-adoption.md) | Replacing VPN with Access, the Cloudflare One Client (formerly WARP), and Tunnel in a phased, pilot-group-first rollout |

## Developer platform track

| Page | Covers |
|---|---|
| [Building on the Developer Platform](developer-platform.md) | Workers vs. Pages, and choosing between R2, KV, D1, and Durable Objects for storage |
| [CI/CD & Infrastructure Delivery](cicd-and-delivery.md) | Wrangler, Workers Builds/GitHub integration, environments, and pushing Terraform-managed config through a pipeline |

## How the tracks relate

Most real adoption programs touch both tracks at once: a team migrates its marketing site's DNS and turns on the WAF (migration track) while standing up a new Worker for a redirect service or new API (developer-platform track). Sequence within each track roughly as listed above — DNS before security rollout, a pilot group before org-wide Zero Trust, Workers/storage decisions before CI/CD automation — but there's no requirement to finish one track before starting the other.

Once workloads are live on Cloudflare, move to [Govern](../govern/index.md) to establish policy guardrails and cost controls, and to [Secure](../secure/index.md) to formalize the security architecture beyond the initial rollout.

## Further reading

- [Cloudflare Developer Docs home](https://developers.cloudflare.com/)
- [Cloudflare Learning Paths](https://developers.cloudflare.com/learning-paths/)
