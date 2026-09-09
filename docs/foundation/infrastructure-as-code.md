# Infrastructure as Code

Cloudflare's dashboard is fast enough that it's genuinely tempting to manage everything by hand — click to add a DNS record, a WAF rule, a Zero Trust Access policy. That works until you have more than a handful of zones, more than one engineer touching configuration, or a compliance requirement to show what changed and why. At that point, dashboard-only management becomes a liability: no diff before a change goes live, no review step, no audit trail beyond [Audit Logs](identity-and-access.md), and no reliable way to reproduce your configuration in a second account after an acquisition or disaster recovery.

Treating Cloudflare configuration as code — DNS, WAF rules, Zero Trust policy, Workers bindings — is a foundational decision, not an optimization to defer.

## Key Cloudflare capabilities

| Capability | What it does | Reference |
|---|---|---|
| Terraform provider (`cloudflare/cloudflare`) | Official HashiCorp Terraform provider covering zones, DNS records, WAF/rulesets, Zero Trust (Access, Gateway, Tunnel), Workers, and most other account/zone resources | [Terraform Registry: cloudflare/cloudflare](https://registry.terraform.io/providers/cloudflare/cloudflare/latest/docs), [Cloudflare Terraform docs](https://developers.cloudflare.com/terraform/) |
| Pulumi provider | Equivalent resource coverage for teams standardized on Pulumi (TypeScript, Python, Go, C#, Java) instead of HCL | [Pulumi Cloudflare provider](https://www.pulumi.com/registry/packages/cloudflare/) |
| `terraform import` | Bring existing, dashboard-created resources under Terraform management without recreating them | [Import Cloudflare resources](https://developers.cloudflare.com/terraform/advanced-topics/import-cloudflare-resources/) |
| API tokens for CI | Scoped, expiring credentials for pipeline authentication instead of the legacy Global API key | [Create API token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/) |

## Design considerations

**Terraform is the default; Pulumi is the alternative for teams already standardized on it.** Terraform has by far the broadest community adoption and most complete first-party documentation — tutorials, import guides, a dedicated docs section. Pulumi's provider is generated from the same API surface with comparable coverage, plus general-purpose languages (TypeScript, Python, Go) instead of HCL — useful if your team's IaC investment is already there. Don't run both against the same resources; pick one per account/zone so two systems don't fight over the same DNS record or WAF rule.

**Authenticate with a scoped API token, injected via environment variable — never a Global API key.** Both providers accept `CLOUDFLARE_API_TOKEN` (or the Pulumi equivalent) instead of the legacy email + Global API key pair. Scope the CI token narrowly: a token editing DNS on your pipeline's zones doesn't also need Zero Trust or billing permissions. See [Identity & Access Control](identity-and-access.md) for why this matters beyond convenience.

**Repository and module structure should mirror your account/zone boundaries, not your org chart.** A pattern that scales reasonably well:

```
infra/
├── modules/
│   ├── zone-baseline/       # DNSSEC, SSL mode, standard WAF managed rulesets
│   ├── waf-custom-rules/    # Shared custom rule library, parameterized per zone
│   └── zero-trust-app/      # Reusable Access application + policy pattern
├── environments/
│   ├── production/
│   │   ├── zone-example-com.tf
│   │   └── zero-trust.tf
│   └── staging/
│       └── zone-staging-example-com.tf
└── backend.tf               # Remote state (e.g., Terraform Cloud, S3+DynamoDB, or equivalent)
```

Put genuinely reusable patterns — a standard WAF baseline, a standard Access application shape — into modules, and keep per-zone specifics (hostnames, custom rule exceptions) in the environment layer. Resist one giant module that parameterizes everything; a few opinionated, composable modules age better than one that accepts thirty variables.

**Import before you rebuild.** Almost every organization adopting Cloudflare IaC starts with resources already in the dashboard from initial onboarding. Use `terraform import` (or the Pulumi equivalent) to bring these under management rather than deleting and recreating them — recreating a zone or Tunnel is disruptive and sometimes destructive (new zone IDs, new tunnel credentials). Import incrementally — DNS records and zone settings first, then WAF rules, then Zero Trust — validating `terraform plan` shows no unexpected diff after each batch.

**Drift detection is a process, not a one-time setup.** The dashboard stays fully functional after you adopt IaC, so someone will make an emergency hand-edit during an incident — that's fine, provided you reconcile it. Run `terraform plan` on a schedule (nightly CI is sufficient) and treat any unexpected diff as a signal to import the manual change or revert it, never to silently `apply` away. Skip this and IaC state quietly diverges from dashboard reality until the next `apply` produces a surprising, disruptive plan.

**WAF rules, DNS, and Zero Trust policy benefit from IaC differently.** DNS records are high-volume, low-risk-per-change — a good first target for full IaC coverage. WAF custom and rate-limiting rules change more often as threats evolve, so keep the module flexible enough for a security engineer to add a rule without a platform-team bottleneck, while still requiring PR review. Zero Trust Access policies are identity-and-authorization-critical — treat changes with the same rigor as a production IAM policy change, since a misconfigured one can lock out legitimate users or expose an internal application.

## Decisions to make

| Decision | Recommended default | When to deviate |
|---|---|---|
| Terraform vs. Pulumi | Terraform, for broadest documentation and community support | Pulumi if your team already standardized on it for other clouds |
| State backend | Remote, locked state (Terraform Cloud or equivalent) | Local state acceptable only for single-operator experimentation |
| Module boundary | Reusable modules for shared baselines; environment-specific files for per-zone detail | — |
| CI authentication | Scoped API token per pipeline/environment | Never share one token across environments |
| Drift handling | Scheduled `plan` job with alerting on diff | — |

## Checklist

- [ ] Chosen Terraform or Pulumi as the single source of truth per account/zone (not both)
- [ ] CI/CD pipeline authenticates with a scoped API token, not the Global API key — see [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md)
- [ ] Remote state configured with locking before more than one engineer touches the codebase
- [ ] Existing dashboard-created resources imported rather than recreated
- [ ] Module structure separates reusable baselines from per-zone specifics
- [ ] Scheduled drift-detection job in place, with a documented process for reconciling manual changes
- [ ] Pull request review required for WAF and Zero Trust policy changes at minimum

## Further reading

- [Cloudflare Terraform docs](https://developers.cloudflare.com/terraform/)
- [Terraform Registry: cloudflare/cloudflare provider](https://registry.terraform.io/providers/cloudflare/cloudflare/latest/docs)
- [Terraform provider version 5 upgrade guide](https://registry.terraform.io/providers/cloudflare/cloudflare/latest/docs/guides/version-5-upgrade)
- [Import Cloudflare resources](https://developers.cloudflare.com/terraform/advanced-topics/import-cloudflare-resources/)
- [Pulumi Cloudflare provider](https://www.pulumi.com/registry/packages/cloudflare/)
- [Create API token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/)
