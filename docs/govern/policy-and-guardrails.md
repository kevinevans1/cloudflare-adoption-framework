# Policy & Guardrails

Cloudflare has no native "organizational policy" that cascades to every zone automatically, the way a cloud provider's policy service might. It has [Terraform](https://developers.cloudflare.com/terraform/), the [Rulesets engine](https://developers.cloudflare.com/ruleset-engine/), and account/zone-level API resources — turning those primitives into guardrails is the adopting organization's job. This page covers what a sane baseline looks like and how to keep it from drifting.

## Key Cloudflare capabilities

| Capability | Role in guardrails |
|---|---|
| [Rulesets engine](https://developers.cloudflare.com/ruleset-engine/) | Single rules language underlying [WAF custom rules](https://developers.cloudflare.com/waf/custom-rules/), [rate limiting rules](https://developers.cloudflare.com/waf/rate-limiting-rules/), transform rules, and more. A baseline "phase" ruleset can be deployed identically across zones. |
| [WAF managed rulesets](https://developers.cloudflare.com/waf/managed-rules/) | The [Cloudflare Managed Ruleset](https://developers.cloudflare.com/waf/managed-rules/reference/cloudflare-managed-ruleset/) and [OWASP Core Ruleset](https://developers.cloudflare.com/waf/managed-rules/reference/owasp-core-ruleset/) are Cloudflare-curated and updated centrally — the guardrail decision is *how* they're deployed (mode, sensitivity, scope), not their content. |
| [Terraform provider (`cloudflare/cloudflare`)](https://developers.cloudflare.com/terraform/) | The mechanism for making a policy declarative and repeatable instead of a checklist someone runs by hand in the dashboard. |
| [Account-level API tokens and roles](https://developers.cloudflare.com/fundamentals/api/reference/permissions/) | Constrains *who* can apply changes outside the guardrail process in the first place — a guardrail is only as strong as the ability to bypass it. |
| [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) | The detective control that proves the guardrail held (or catches the exception that didn't go through the approved path). |

## Design considerations

### What belongs in a baseline vs. what belongs to each zone

Not everything should be centrally enforced. A workable split:

| Org-wide baseline (no exceptions without sign-off) | Per-zone / per-team discretion |
|---|---|
| [Cloudflare Managed Ruleset](https://developers.cloudflare.com/waf/managed-rules/reference/cloudflare-managed-ruleset/) enabled in a defined mode on every proxied zone | Custom rules tuned to an application's specific traffic patterns |
| Minimum [TLS mode](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) (typically Full or Full Strict — never Flexible for anything handling sensitive data) | Cache rules, page rules/transform rules specific to that app |
| [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/) or equivalent origin-access restriction | Bot Management thresholds tuned per traffic profile |
| DNSSEC enabled where the zone's registrar supports it | Zone-specific rate limiting thresholds |
| A default-deny stance on newly delegated subdomains until explicitly onboarded | Client-Side Security policy specifics |

### Preventing config drift

Drift happens for a boring reason: someone made a one-off change in the dashboard to fix an incident, and it never made it back into source control. Two structural defenses:

1. **Treat the dashboard as read-only for anything under guardrail.** If a change must happen live during an incident (see [Change Management](change-management.md)), it gets back-ported into Terraform in the same day, not "eventually."
2. **Run `terraform plan` on a schedule against every managed zone/account**, not just on merge to main. A scheduled plan that shows unexpected diffs *is* your drift detector — it means either the guardrail was bypassed or Terraform state is stale.

### Org-wide policy vs. per-zone exceptions

Some zones legitimately need to deviate — a legacy origin that can't yet do Full Strict TLS, a partner-facing API with different rate limiting needs. Handle exceptions as a first-class, auditable thing rather than a silent module override:

- An exception has an owner, an expiry/review date, and a documented reason.
- Exceptions live in the same Terraform codebase as the baseline (e.g., as a variable override per zone), not in a parallel manually-managed zone.
- Review the exception list on a fixed cadence — quarterly is reasonable — and treat "we forgot why this exception exists" as a finding, not a shrug.

## Decisions to make

- [ ] Which managed ruleset(s) and which mode (Log / Challenge / Block) are mandatory on every zone?
- [ ] Is there a minimum TLS mode floor, and is Flexible banned outright for anything beyond static marketing pages?
- [ ] Who is authorized to make a change outside of the Terraform-reviewed path, and under what conditions (see [Change Management](change-management.md))?
- [ ] Where do zone-level exceptions live, and who reviews them on what cadence?
- [ ] What triggers a re-baseline — a new managed ruleset version, a new product (e.g., adding [API Shield](https://developers.cloudflare.com/api-shield/) to the baseline once APIs are in scope)?
- [ ] How is the Terraform state itself secured and who can run `apply` against production zones?

## Checklist

- [ ] Baseline WAF, TLS, and origin-access rules defined as Terraform modules, not dashboard clicks
- [ ] Every proxied zone provisioned through the same module (or an explicit, tracked exception)
- [ ] Scheduled `terraform plan` job alerts on drift
- [ ] Exception list has owners and review dates
- [ ] API token scopes limit who can change zone settings outside the pipeline
- [ ] Audit Logs reviewed periodically for changes that didn't originate from CI/CD

## Further reading

- [Cloudflare Terraform provider](https://developers.cloudflare.com/terraform/)
- [Ruleset Engine overview](https://developers.cloudflare.com/ruleset-engine/)
- [WAF managed rules](https://developers.cloudflare.com/waf/managed-rules/)
- [WAF custom rules](https://developers.cloudflare.com/waf/custom-rules/)
- [SSL/TLS encryption modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/)
- [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/)
- [API token permissions reference](https://developers.cloudflare.com/fundamentals/api/reference/permissions/)
- [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)

See also: [Change Management](change-management.md) for how changes to this baseline get approved, and [Infrastructure as Code](../foundation/infrastructure-as-code.md) for the underlying Terraform foundation.
