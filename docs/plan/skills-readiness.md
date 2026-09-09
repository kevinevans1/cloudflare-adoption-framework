# Skills & Team Readiness

Cloudflare adoption touches at least four distinct skill sets that, in most organizations, live in different teams and rarely talk day-to-day: network/DNS administration, security/WAF operations, application development, and IT/endpoint administration for Zero Trust. Adoption stalls when a group is assumed to "just pick it up" without dedicated time to learn the platform, or when nobody's assigned ownership of a capability at all.

Assess readiness by role before go-live, not during an incident — the worst time to discover nobody knows how to write a WAF exception is while a false positive is blocking real customer traffic.

## Roles and what they need to know

| Role | Needs to understand | Primary resources |
|---|---|---|
| **Network/DNS administrator** | Zone/account structure, DNS record management, full vs. partial setup, SSL/TLS modes | [DNS docs](https://developers.cloudflare.com/dns/), [Fundamentals](https://developers.cloudflare.com/fundamentals/) |
| **Security/WAF operator** | Managed rulesets vs. custom rules, rate limiting, Bot Management, log-mode-before-block-mode discipline, incident response workflow | [WAF docs](https://developers.cloudflare.com/waf/), [Bot Management](https://developers.cloudflare.com/bots/), [DDoS Protection](https://developers.cloudflare.com/ddos-protection/) |
| **Developer platform engineer** | Workers/Pages deployment model, Wrangler CLI, bindings to R2/D1/KV/Durable Objects/Queues, local dev workflow | [Workers docs](https://developers.cloudflare.com/workers/), [Wrangler](https://developers.cloudflare.com/workers/wrangler/) |
| **Zero Trust / IT administrator** | Access policies, Gateway (DNS/HTTP/network filtering), Tunnel deployment, device enrollment | [Cloudflare One docs](https://developers.cloudflare.com/cloudflare-one/) |
| **Platform/account owner** | Account structure, billing, [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/review-audit-logs/), API tokens and permissions, [Terraform provider](https://developers.cloudflare.com/terraform/) if managing config as code | [Fundamentals](https://developers.cloudflare.com/fundamentals/), [Terraform provider docs](https://developers.cloudflare.com/terraform/) |

## Training resources

- **[developers.cloudflare.com](https://developers.cloudflare.com/)** is the primary reference for every product — treat it as the source of truth over any third-party course or this framework.
- **[Cloudflare Learning Paths](https://developers.cloudflare.com/learning-paths/)** provide structured, product-specific tutorials (e.g., a SASE/Cloudflare One overview path, developer platform introductions) that are a better starting point than reading raw reference docs cold.
- **The Cloudflare Learning Center** publishes conceptual, vendor-neutral explainers (what is a WAF, what is DDoS, what is Zero Trust) that are useful for bringing non-specialist stakeholders up to speed without diving into product configuration.
- **Formal training and certification** is available through Cloudflare for partners and customers — check current offerings and delivery format directly with Cloudflare, as this changes over time.

## RACI-style guidance

Use a lightweight RACI to make ownership explicit before go-live — this table is a starting template, not a final answer:

| Activity | Network/DNS admin | Security/WAF operator | Dev platform engineer | Zero Trust/IT admin | Platform owner |
|---|---|---|---|---|---|
| DNS record changes | R/A | C | I | I | C |
| WAF rule changes (custom rules, exceptions) | I | R/A | C | I | C |
| Bot Management tuning | I | R/A | I | I | C |
| Workers/Pages deployment | I | I | R/A | I | C |
| Access policy changes | I | I | I | R/A | C |
| Account structure / billing | I | I | I | I | R/A |
| Terraform/IaC pipeline for Cloudflare config | C | C | R/A | C | A |

(R = Responsible, A = Accountable, C = Consulted, I = Informed — adjust to your org's actual structure; the point is that every row has an unambiguous owner before launch.)

## Design considerations

- **Don't assume your existing CDN or firewall admin can operate Cloudflare without ramp-up time.** The concepts transfer, but the configuration model (zones, rulesets, the dashboard/API split) doesn't — budget real training time, not "read the docs over a weekend."
- **Zero Trust adoption is often an IT/endpoint team's first exposure to Cloudflare at all** — if the rollout is driven by the security or network team without IT/endpoint involvement, device enrollment and end-user support will be under-resourced. See [Zero Trust Adoption Path](../adopt/zero-trust-adoption.md).
- **If you're adopting the developer platform (Workers/Pages/R2/D1)**, this is a genuinely new skill for most application teams — plan a real pilot with a supported learning curve rather than expecting immediate productivity (see [Building on the Developer Platform](../adopt/developer-platform.md)).
- **Assign IaC/Terraform ownership explicitly if you intend to manage Cloudflare configuration as code** (recommended at any meaningful scale — see [Infrastructure as Code](../foundation/infrastructure-as-code.md)) — this is usually a new responsibility that doesn't fit neatly into any one existing role.

## Checklist

- [ ] Every role in the table above has a named owner (not just a team)
- [ ] Training plan and timeline exists for each role before their first production responsibility
- [ ] RACI reviewed and agreed upon by all four functional groups, not just written by one team
- [ ] Escalation path defined for WAF false positives and Zero Trust access issues before go-live
- [ ] IaC/Terraform ownership assigned if configuration-as-code is in scope

## Further reading

- [Cloudflare Learning Paths](https://developers.cloudflare.com/learning-paths/)
- [Cloudflare Fundamentals](https://developers.cloudflare.com/fundamentals/)
- [Terraform provider docs](https://developers.cloudflare.com/terraform/)
- [Cloudflare One overview](https://developers.cloudflare.com/cloudflare-one/)
- [WAF overview](https://developers.cloudflare.com/waf/)
