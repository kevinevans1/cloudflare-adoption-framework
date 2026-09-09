# Product Map by Phase

--8<-- "_snippets/disclaimer.md"

A navigational aid: every Cloudflare product or feature referenced in this framework, mapped to the phase(s) where it's most relevant. Use it to jump straight to the page covering a product you're evaluating, or to confirm a phase's guardrails account for everything that product touches. Phases are listed in first-encountered order; a product used throughout the lifecycle (DNS, Workers) lists every phase where it recurs as a distinct decision point.

## Network & DNS

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| Zones & Accounts | Foundation | [Accounts & Organizations](../foundation/accounts-and-organizations.md) |
| DNS records (proxied / DNS-only) | Foundation, Adopt | [DNS Foundation](../foundation/dns-foundation.md), [Migrating DNS](../adopt/migrating-dns.md) |
| DNSSEC | Foundation, Secure | [DNS Foundation](../foundation/dns-foundation.md), [Edge Security Baseline](../secure/edge-security-baseline.md) |
| Anycast network | Foundation | [Network Foundation](../foundation/network-foundation.md) |
| BYOIP | Foundation | [Network Foundation](../foundation/network-foundation.md) |
| Magic Transit | Foundation, Strategy | [Network Foundation](../foundation/network-foundation.md), [Assessing Workload Fit](../strategy/workload-fit.md) |
| Cloudflare WAN (formerly Magic WAN) | Foundation, Strategy | [Network Foundation](../foundation/network-foundation.md), [Assessing Workload Fit](../strategy/workload-fit.md) |
| Load Balancing (health checks, steering policies) | Adopt, Manage | [Onboarding a Site](../adopt/onboarding-a-site.md), [Reliability Operations](../manage/reliability-operations.md) |

## Security & Zero Trust

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| WAF (Web Application Firewall) | Adopt, Secure | [Rolling Out Edge Security](../adopt/rolling-out-security.md), [Edge Security Baseline](../secure/edge-security-baseline.md) |
| Managed Rulesets / Ruleset Engine | Adopt, Secure | [Rolling Out Edge Security](../adopt/rolling-out-security.md), [Edge Security Baseline](../secure/edge-security-baseline.md) |
| Bot Management | Adopt, Secure | [Rolling Out Edge Security](../adopt/rolling-out-security.md), [Edge Security Baseline](../secure/edge-security-baseline.md) |
| SSL/TLS encryption modes (incl. Full strict) | Foundation, Secure | [Network Foundation](../foundation/network-foundation.md), [Edge Security Baseline](../secure/edge-security-baseline.md) |
| Authenticated Origin Pulls / mTLS | Secure | [Edge Security Baseline](../secure/edge-security-baseline.md), [Data Protection](../secure/data-protection.md) |
| Cloudflare Access | Foundation, Adopt, Secure | [Identity & Access Control](../foundation/identity-and-access.md), [Zero Trust Adoption Path](../adopt/zero-trust-adoption.md), [Zero Trust Security Architecture](../secure/zero-trust-architecture.md) |
| Cloudflare Gateway | Adopt, Secure | [Zero Trust Adoption Path](../adopt/zero-trust-adoption.md), [Zero Trust Security Architecture](../secure/zero-trust-architecture.md) |
| Cloudflare Tunnel | Adopt, Secure | [Zero Trust Adoption Path](../adopt/zero-trust-adoption.md), [Zero Trust Security Architecture](../secure/zero-trust-architecture.md) |
| Cloudflare One Client (formerly WARP) | Adopt, Secure | [Zero Trust Adoption Path](../adopt/zero-trust-adoption.md), [Zero Trust Security Architecture](../secure/zero-trust-architecture.md) |
| CASB (Cloud & SaaS findings) | Secure, Govern | [Data Protection](../secure/data-protection.md), [Compliance Mapping](../govern/compliance-mapping.md) |
| DLP (Data Loss Prevention) | Secure, Govern | [Data Protection](../secure/data-protection.md), [Compliance Mapping](../govern/compliance-mapping.md) |
| Incident response / status monitoring | Secure | [Incident Response & Status Monitoring](../secure/incident-response.md) |

## Developer platform

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| Workers | Adopt, Manage | [Building on the Developer Platform](../adopt/developer-platform.md), [Performance Operations](../manage/performance-operations.md), [Cost Optimization](../manage/cost-optimization.md) |
| Pages | Adopt | [Building on the Developer Platform](../adopt/developer-platform.md) |
| R2 | Adopt, Manage | [Building on the Developer Platform](../adopt/developer-platform.md), [Cost Optimization](../manage/cost-optimization.md) |
| D1 | Adopt, Manage | [Building on the Developer Platform](../adopt/developer-platform.md), [Reliability Operations](../manage/reliability-operations.md) |
| Workers KV | Adopt | [Building on the Developer Platform](../adopt/developer-platform.md) |
| Durable Objects | Adopt, Manage | [Building on the Developer Platform](../adopt/developer-platform.md), [Reliability Operations](../manage/reliability-operations.md) |
| Queues | Adopt | [Building on the Developer Platform](../adopt/developer-platform.md) |
| Smart Placement | Manage | [Performance Operations](../manage/performance-operations.md) |
| Wrangler | Foundation, Adopt | [Infrastructure as Code](../foundation/infrastructure-as-code.md), [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md) |
| Terraform provider | Foundation, Adopt | [Infrastructure as Code](../foundation/infrastructure-as-code.md), [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md) |

## Performance & caching

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| Cache Rules | Adopt, Manage | [Onboarding a Site](../adopt/onboarding-a-site.md), [Performance Operations](../manage/performance-operations.md) |
| Tiered Cache | Manage | [Performance Operations](../manage/performance-operations.md) |
| Argo Smart Routing | Manage | [Performance Operations](../manage/performance-operations.md) |
| Images (transformations) / Polish | Manage | [Performance Operations](../manage/performance-operations.md) |
| Page Rules (legacy) | Manage | [Performance Operations](../manage/performance-operations.md), [Continuous Improvement](../manage/continuous-improvement.md) |

## Observability & operations

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| Zone Analytics | Manage | [Observability & Analytics](../manage/observability.md) |
| GraphQL Analytics API | Manage | [Observability & Analytics](../manage/observability.md) |
| Logpush | Manage, Secure | [Observability & Analytics](../manage/observability.md), [Incident Response & Status Monitoring](../secure/incident-response.md) |
| Log Explorer | Manage, Secure | [Observability & Analytics](../manage/observability.md), [Incident Response & Status Monitoring](../secure/incident-response.md) |
| Workers Observability (Logs, Traces, Tail Workers, real-time logs) | Manage | [Observability & Analytics](../manage/observability.md) |
| Cloudflare changelog | Manage, Govern | [Continuous Improvement](../manage/continuous-improvement.md), [Change Management](../govern/change-management.md) |

## Commercial & governance

| Product / feature | Primary phase(s) | Where covered |
|---|---|---|
| Plan tiers (Free/Pro/Business/Enterprise) | Plan, Manage | [Choosing a Plan Tier](../plan/choosing-a-plan.md), [Cost Optimization](../manage/cost-optimization.md) |
| Billing & usage | Manage, Govern | [Cost Optimization](../manage/cost-optimization.md), [Cost Governance](../govern/cost-governance.md) |
| Compliance certifications & data residency | Govern, Secure | [Compliance Mapping](../govern/compliance-mapping.md), [Data Protection](../secure/data-protection.md) |

## Further reading

- [Glossary](glossary.md) — definitions for every product/feature named above
- [Related Frameworks](related-frameworks.md) — how this map relates to Cloudflare's own Reference Architecture library
