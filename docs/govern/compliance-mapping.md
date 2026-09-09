--8<-- "_snippets/disclaimer.md"

# Compliance Mapping

**This page is not legal advice.** It maps common regulatory concerns to Cloudflare capabilities that are documented as of this writing, so a compliance or legal team can evaluate them — it does not certify that using these features makes any specific workload compliant with any specific regulation. Compliance status depends on your full architecture, your contractual terms with Cloudflare, your data flows outside of Cloudflare, and your regulator's interpretation — none of which this framework can see. Always validate current certifications and scope against Cloudflare's own [Trust Hub](https://www.cloudflare.com/trust-hub/) and your organization's legal/compliance function before making any representation to an auditor, regulator, or customer.

## Key Cloudflare capabilities

| Regulatory concern | Relevant Cloudflare capability | Verify before relying on it |
|---|---|---|
| Data residency / data sovereignty | [Data Localization Suite](https://developers.cloudflare.com/data-localization/) — [Regional Services](https://developers.cloudflare.com/data-localization/regional-services/) (control which data centers decrypt/process traffic), [Geo Key Manager](https://developers.cloudflare.com/data-localization/geo-key-manager/) (control where TLS private keys are stored), [Customer Metadata Boundary](https://developers.cloudflare.com/data-localization/metadata-boundary/) (keep traffic metadata/logs within a region) | Current regional availability, which products are covered, and whether it's included in your plan or a separate Enterprise add-on |
| PCI DSS scope for payment flows | Cloudflare's own infrastructure compliance posture (see Trust Hub), plus architectural controls you configure: WAF in front of payment pages, [Client-Side Security](https://developers.cloudflare.com/client-side-security/) (formerly Page Shield) for detecting unauthorized script injection/Magecart-style skimming on checkout pages, TLS enforcement | PCI DSS scope reduction is an assessment made by your Qualified Security Assessor (QSA) against your specific cardholder data environment — Cloudflare features can *support* that assessment, they don't substitute for it |
| GDPR-style personal data handling | Data Localization Suite (above) for keeping EU traffic/metadata in-region; [DLP](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/) for detecting personal data leaving via Zero Trust-inspected traffic; standard contractual/DPA terms with Cloudflare (a legal matter, not a product feature) | GDPR compliance is an organizational program, not a checkbox — data processing agreements, lawful basis, subject access request handling, etc. are outside anything a CDN/security vendor can provide |
| Audit trail for change control | [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) records account/zone configuration changes | Retention period and export options for your plan; whether Audit Logs export via [Logpush](https://developers.cloudflare.com/logs/logpush/) is needed to meet your retention requirement |
| Vendor security certifications (SOC 2, ISO 27001, etc.) | Cloudflare publishes its certification posture via the [Trust Hub](https://www.cloudflare.com/trust-hub/) | **Do not restate specific certification names/scopes from memory in customer-facing material** — pull the current list and scope directly from the Trust Hub and Cloudflare's compliance documentation each time, as certifications are renewed, scoped, and occasionally added or dropped |

## Design considerations

### Data residency is a per-product, per-feature decision — not a global switch

Enabling the Data Localization Suite for HTTP traffic doesn't mean every Cloudflare product touching that traffic honors the same regional boundary — logging, analytics, DNS resolution, and other platform services each have their own data-handling documentation. Treat "we need data to stay in region X" as a product-by-product check against current docs, not a single toggle to flip. Start from the [Data Localization Suite overview](https://developers.cloudflare.com/data-localization/) and check each in-scope product's own data-handling notes.

### PCI DSS: Cloudflare is part of the environment, not the assessment

If cardholder data (or a service that processes it) sits behind Cloudflare, Cloudflare's proxy is part of your cardholder data environment (CDE) for PCI DSS purposes in most standard architectures — your QSA will want to know how Cloudflare is configured, not just that it's compliant as a company. Practical implications:

- Document which zones/routes handle payment pages and apply the strictest WAF/TLS baseline from [Policy & Guardrails](policy-and-guardrails.md) to them specifically.
- [Client-Side Security](https://developers.cloudflare.com/client-side-security/) is directly relevant to PCI DSS requirement 6.4.3 (protecting payment pages from unauthorized script changes) and 11.6.1 (client-side change/tamper detection) in the current standard — but verify the exact requirement numbering and language against the current PCI SSC standard version, since it has been revised.
- Cloudflare's own facility/infrastructure PCI DSS attestation (if published) covers Cloudflare's environment, not yours — your merchant/service provider PCI DSS report is a separate assessment of your own environment, which happens to include Cloudflare as a component.

### "Compliant" claims require a source, every time

Never write "Cloudflare is SOC 2 / ISO 27001 / PCI DSS compliant, therefore we are" in any internal or customer-facing document without a live link to the current Trust Hub page and a date. Certifications have scopes (which data centers, which products, which time period the audit covers) that a blanket statement erases. When in doubt, quote the Trust Hub directly and let compliance/legal interpret it.

## Decisions to make

- [ ] Which regulatory regimes actually apply to this workload (data residency jurisdiction, PCI DSS merchant level, GDPR as controller vs. processor, sector-specific rules)?
- [ ] Does the workload need the Data Localization Suite at all, or is standard Cloudflare routing acceptable for the data classification involved?
- [ ] Who is the internal owner of record for pulling current Trust Hub attestations when a customer or auditor asks?
- [ ] What's the process for re-verifying compliance-adjacent claims in this framework or in internal docs when Cloudflare updates its certification posture?
- [ ] Is Audit Log retention sufficient for your regulatory retention requirement as-is, or does it need to be pushed to external storage via Logpush?

## Checklist

- [ ] Compliance/legal team has reviewed the current [Trust Hub](https://www.cloudflare.com/trust-hub/) content directly — not a summary written months ago
- [ ] Data residency requirements (if any) mapped to specific Data Localization Suite components, product by product
- [ ] Payment-page routes identified and held to the strictest WAF/TLS/Client-Side Security baseline
- [ ] Audit Log retention/export meets regulatory retention requirements
- [ ] No compliance claim in customer-facing material lacks a current, dated source
- [ ] A named owner re-checks this mapping at least annually or after any material Cloudflare compliance announcement

## Further reading

- [Cloudflare Trust Hub](https://www.cloudflare.com/trust-hub/)
- [Data Localization Suite](https://developers.cloudflare.com/data-localization/)
- [Regional Services](https://developers.cloudflare.com/data-localization/regional-services/)
- [Geo Key Manager](https://developers.cloudflare.com/data-localization/geo-key-manager/)
- [Customer Metadata Boundary](https://developers.cloudflare.com/data-localization/metadata-boundary/)
- [Client-Side Security (formerly Page Shield)](https://developers.cloudflare.com/client-side-security/)
- [Cloudflare One Data Loss Prevention](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/)
- [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)
- [Logpush](https://developers.cloudflare.com/logs/logpush/)

See also: [Shared Responsibility Model](../secure/shared-responsibility.md) for the technical control boundary this compliance mapping builds on.
