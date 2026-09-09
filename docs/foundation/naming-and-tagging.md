# Naming & Tagging Conventions

Cloudflare doesn't force much naming structure on you — a zone is a domain, a Worker script gets whatever name you give it at `wrangler deploy` time, an Access application gets a free-text label. That flexibility is exactly why a deliberate convention matters: without one, a growing estate of zones, Workers, rulesets, and Access applications turns into names nobody can parse without asking whoever created them.

This page gives practical, low-ceremony conventions, not an exhaustive taxonomy. The goal: anyone on the team can look at a resource name or tag and know what it is, what environment it's in, and who owns it — without opening a wiki.

## Key Cloudflare capabilities

| Capability | What it does | Reference |
|---|---|---|
| Resource Tagging | Attach key-value tags to zones, custom hostnames, Cloudflare Tunnels, Workers, D1 databases, R2 buckets, KV namespaces, and more, via a dedicated Tagging API | [Resource Tagging overview](https://developers.cloudflare.com/resource-tagging/) |
| Tag-based filtering | Query and scope resources by tag with AND/OR logic and key-only matching | [Manage tags](https://developers.cloudflare.com/resource-tagging/how-to/manage-tags/) |
| Tag-scoped roles | Super Administrator, Workers Admin, or Tag Admin roles are required to create, update, or delete tags | [Resource Tagging: Get started](https://developers.cloudflare.com/resource-tagging/get-started/) |
| Wrangler `name` field | The canonical name for a Worker, set in `wrangler.jsonc`/`wrangler.toml`, which also determines its `*.workers.dev` subdomain and environment suffix | [Wrangler environments](https://developers.cloudflare.com/workers/wrangler/environments/) |

Resource Tagging is a real, purpose-built product, not a workaround. It covers the resource types most foundation and adoption work touches (zones, Workers, R2, D1, KV, Tunnels), stores tags as key-value string pairs, and is available on all plans (dashboard UI currently in beta; the API is the more mature automation path). Two sharp edges to design around:

- A `PUT` replaces *all* tags on a resource rather than merging.
- There's no way to delete a single tag independent of the full set.

Build your tagging automation (ideally in Terraform/Pulumi) around always sending the complete desired tag set.

## Design considerations

**Names are for humans skimming a list; tags are for machines and filtering.** A name should be readable and stable — something recognizable in a dashboard list at 2am during an incident. Tags carry the structured metadata (environment, owner, cost center, classification) automation actually queries. Don't cram metadata into the name: `acme-prod-us-east-team-platform-billing-v2-worker` serves nobody better than `billing-api` plus four tags.

**Keep the naming grammar small and consistent.** `<system>-<component>-<environment>` (e.g., `checkout-api-prod`, `checkout-api-staging`) scales fine for most organizations. Resist adding segments "just in case" — every extra required segment is something a busy engineer gets wrong or skips six months in.

**Environment suffixes on Workers have a real mechanical effect, not just a naming convention.** Defining a Wrangler environment (`[env.staging]`) deploys it as a *separate* Worker named `<top-level-name>-<environment-name>`. Naming convention and Wrangler environment configuration are the same decision — pick the top-level name with the suffix in mind, so `checkout-api` plus a `staging` environment produces `checkout-api-staging`. See [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md).

**Tag for ownership and cost attribution from the first resource, not the fiftieth.** Make two tags mandatory everywhere before settling the rest of your taxonomy: `owner` (a team or distribution list, not an individual who'll leave) and `environment` (`production`, `staging`, `dev`). Layer on cost center, data classification, and compliance scope as governance matures — see [Cost Governance](../govern/cost-governance.md) and [Compliance Mapping](../govern/compliance-mapping.md).

**Enforce tags through the same IaC path you use for everything else.** Since `PUT` replaces the full tag set, define tags alongside the resource in Terraform/Pulumi (see [Infrastructure as Code](infrastructure-as-code.md)) rather than by hand in the dashboard beta UI. A missing required tag becomes a code-review problem, not a runtime surprise.

## Example convention

| Resource type | Naming pattern | Example | Required tags |
|---|---|---|---|
| Zone | Registrable domain (no convention needed — it *is* the domain) | `example.com`, `staging.example-internal.com` | `owner`, `environment` |
| Worker | `<system>-<component>[-<environment>]` | `checkout-api`, `checkout-api-staging` | `owner`, `environment`, `system` |
| R2 bucket | `<system>-<purpose>-<environment>` | `checkout-invoices-prod` | `owner`, `environment` |
| D1 database | `<system>-<purpose>-<environment>` | `checkout-orders-prod` | `owner`, `environment` |
| KV namespace | `<system>-<purpose>-<environment>` | `checkout-sessions-prod` | `owner`, `environment` |
| Cloudflare Tunnel | `<site-or-datacenter>-<purpose>` | `dc-east-app-ingress` | `owner`, `environment` |
| WAF custom ruleset | `<zone-or-scope>-<intent>` | `checkout-api-bot-mitigation` | `owner` |
| Access application | `<system>-<component>` matching the Worker/zone it fronts | `checkout-api-admin` | `owner`, `environment` |

Adapt the segments to your organization's real structure — the point is picking *a* small, documented pattern and applying it everywhere, not matching this table exactly.

## Decisions to make

| Decision | Recommended default |
|---|---|
| Naming grammar | 2–3 segments max (`system-component-environment`) |
| Mandatory tags | `owner`, `environment` on every taggable resource |
| Where tags are set | In IaC alongside the resource definition, not by hand in the dashboard |
| Enforcement | Code review + a CI check that fails on missing required tags, once volume justifies it |
| Individual names as owners | Never — use team names or distribution lists |

## Checklist

- [ ] A documented naming grammar exists and is short enough that people actually follow it
- [ ] `owner` and `environment` tags are mandatory on every zone, Worker, R2 bucket, D1 database, KV namespace, and Tunnel
- [ ] Wrangler environment names were chosen with the resulting `<name>-<environment>` Worker name in mind
- [ ] Tags are set via Terraform/Pulumi, not the Resource Tagging dashboard beta, to avoid `PUT`-replaces-all surprises
- [ ] Naming/tagging convention is documented somewhere new team members actually find it (this repo's [Reference](../reference/glossary.md) section, a README, or a wiki page linked from onboarding)

## Further reading

- [Resource Tagging overview](https://developers.cloudflare.com/resource-tagging/)
- [Resource Tagging: Get started](https://developers.cloudflare.com/resource-tagging/get-started/)
- [Manage tags](https://developers.cloudflare.com/resource-tagging/how-to/manage-tags/)
- [Wrangler environments](https://developers.cloudflare.com/workers/wrangler/environments/)
