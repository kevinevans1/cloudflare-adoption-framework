# Scenario: Agency & Multi-Tenant

Web agencies, MSPs, and SaaS platforms that provision a Cloudflare zone per customer face a different Foundation problem than a single-product company: account structure isn't a one-time decision, it's a repeatable pattern that has to work for customer 5 and customer 500 alike. Get this wrong and every new customer onboarding is a bespoke, error-prone exercise.

## Key Cloudflare capabilities

| Capability | What it's for | Reference |
|---|---|---|
| [Cloudflare Tenant](https://developers.cloudflare.com/tenant/) | A special account type for Channel and Alliance partners that contains and manages many customer accounts, provisioned via the Tenant API or dashboard | [Tenant overview](https://developers.cloudflare.com/tenant/) |
| [Tenant API](https://developers.cloudflare.com/tenant/get-started/) | Programmatic creation and management of customer accounts and user access, for provisioning at scale | [Get started with Tenant](https://developers.cloudflare.com/tenant/get-started/) |
| [Cloudflare for SaaS](https://developers.cloudflare.com/cloudflare-for-platforms/cloudflare-for-saas/) | Lets a SaaS provider extend Cloudflare's edge (custom hostnames, per-customer TLS) to its own end customers without provisioning a full account per customer | [Cloudflare for SaaS](https://developers.cloudflare.com/cloudflare-for-platforms/cloudflare-for-saas/) |
| [Workers for Platforms](https://developers.cloudflare.com/cloudflare-for-platforms/workers-for-platforms/) | Lets a platform run isolated Worker code on behalf of many customers/tenants | [Workers for Platforms](https://developers.cloudflare.com/cloudflare-for-platforms/workers-for-platforms/) |
| [Terraform provider](https://developers.cloudflare.com/terraform/) | Templates a standard zone/security configuration and applies it consistently to every new customer account | [Terraform provider](https://developers.cloudflare.com/terraform/) |

## Design considerations

**Decide early: Tenant (separate customer accounts) vs. Cloudflare for SaaS (one account, many custom hostnames) vs. Workers for Platforms (one account, isolated per-tenant compute).** These solve related but different problems, and picking the wrong one is expensive to unwind:

- **Tenant** fits an agency or MSP model where each customer effectively needs their own Cloudflare account — full zone-level control, potentially their own billing relationship, and isolation between customers who may not trust each other.
- **Cloudflare for SaaS** fits a SaaS platform whose end customers bring their own domain to point at the platform's application — the platform stays on one account, and Cloudflare handles per-customer hostname and certificate provisioning underneath it.
- **Workers for Platforms** fits a platform that runs customer-supplied or customer-specific *code*, not just customer domains — each tenant's Worker is isolated from the others' by design.

Many multi-tenant platforms end up using more than one of these together (Cloudflare for SaaS for the hostname/TLS layer, Workers for Platforms for tenant-specific logic).

**Template the standard configuration once, apply it as code, not by hand per customer.** Whatever baseline WAF rules, cache settings, and security posture every customer zone should have belongs in [Terraform](../foundation/infrastructure-as-code.md) as a reusable module, not recreated by a human clicking through the dashboard for the fiftieth time. This is also what makes [Policy & Guardrails](../govern/policy-and-guardrails.md) enforceable at this scale — a per-customer exception is a deliberate override of the module, not silent drift.

**Plan account structure for the isolation boundary you actually need, not the maximum.** A compromised customer's zone shouldn't be able to affect another customer's zone or your own management account — verify this is true for whichever pattern (Tenant, Cloudflare for SaaS, Workers for Platforms) you choose, rather than assuming isolation as a given.

**Billing and support relationship shape the decision as much as technical isolation.** If customers need their own direct Cloudflare billing/support relationship, Tenant is usually the fit. If Cloudflare is invisible infrastructure behind your product, Cloudflare for SaaS or Workers for Platforms keeps that abstraction intact.

**Naming and tagging conventions matter more here than anywhere else in this framework.** With potentially hundreds of near-identical customer zones, [Naming & Tagging Conventions](../foundation/naming-and-tagging.md) is the difference between "which zone is customer #340's staging environment" being a one-second lookup or a support incident.

## Decisions to make

| Question | If yes → | If no → |
|---|---|---|
| Do customers need their own direct Cloudflare account/billing relationship? | Evaluate Tenant | Cloudflare for SaaS or Workers for Platforms |
| Are customers bringing their own domain to point at your application? | Evaluate Cloudflare for SaaS | Tenant (if separate accounts) or standard zone setup |
| Do you need to run customer-specific or customer-supplied code in isolation? | Evaluate Workers for Platforms | Standard Worker deployment likely sufficient |
| Will you provision more than a handful of customer zones? | Template configuration in Terraform from day one | A manual process may be acceptable short-term |

## Checklist

- [ ] Chosen the multi-tenant pattern (Tenant / Cloudflare for SaaS / Workers for Platforms) deliberately, not by default
- [ ] Standard per-customer configuration templated in Terraform, not applied by hand
- [ ] Isolation boundary between customers explicitly verified for the chosen pattern
- [ ] Naming/tagging convention established before customer count grows past what's memorable
- [ ] Onboarding a new customer is a repeatable, mostly-automated process, not a bespoke project each time

## Further reading

- [Cloudflare Tenant overview](https://developers.cloudflare.com/tenant/)
- [Get started with Tenant](https://developers.cloudflare.com/tenant/get-started/)
- [Cloudflare for SaaS](https://developers.cloudflare.com/cloudflare-for-platforms/cloudflare-for-saas/)
- [Workers for Platforms](https://developers.cloudflare.com/cloudflare-for-platforms/workers-for-platforms/)
- [Cloudflare Terraform provider](https://developers.cloudflare.com/terraform/)
