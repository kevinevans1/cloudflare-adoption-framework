# Continuous Improvement

--8<-- "_snippets/disclaimer.md"

Cloudflare ships product changes continuously — new capabilities, new defaults, and periodic deprecations of legacy features for their modern replacements. A Manage-phase operating model needs a deliberate, scheduled process for absorbing that change, not a reactive one triggered when a legacy feature breaks or a new capability would have saved weeks of custom work. This page defines that cadence and how it connects back to the rest of the framework.

## Key Cloudflare capabilities

| Capability | What it's for | Docs |
|---|---|---|
| Cloudflare changelog | The canonical, chronological feed of product changes across every Cloudflare product area | [developers.cloudflare.com/changelog](https://developers.cloudflare.com/changelog/) |
| Product-specific changelogs | Filtered changelog views scoped to a single product (Workers, Load Balancing, WAF, etc.) | e.g. [Workers changelog](https://developers.cloudflare.com/changelog/product/workers/) |
| Migration guides | Cloudflare-authored guidance for moving off a specific deprecated feature | e.g. [Page Rules migration guide](https://developers.cloudflare.com/rules/reference/page-rules-migration/) |

## Review cadence

A quarterly cadence is a reasonable default. Higher-change-velocity environments (active migration, rapid team growth, regulated industries adding new compliance requirements) may warrant monthly review of at least the highest-priority items.

| Cadence | Activity | Owner |
|---|---|---|
| Continuous | Subscribe to the changelog (RSS or product-scoped feed) for products actively in use | Platform/architecture lead |
| Monthly | Triage new changelog entries: does anything affect current configuration, introduce a new default, or deprecate something in use? | Platform team |
| Quarterly | Full configuration and policy review: guardrails ([Policy & Guardrails](../govern/policy-and-guardrails.md)), account/zone structure, unused features, plan-tier fit | Cross-functional (platform, security, FinOps) |
| Annually | Re-run the [Assessing Workload Fit](../strategy/workload-fit.md) exercise for any workload that's grown or changed materially since onboarding | Architecture/strategy owner |

## Staying current with the changelog

The changelog at [developers.cloudflare.com/changelog](https://developers.cloudflare.com/changelog/) is organized chronologically and by product/category, with an RSS feed for subscription. Two practical patterns work well:

1. **Subscribe narrowly, review broadly.** Set up feeds only for products your organization actually uses (Workers, Load Balancing, WAF, DNS) to avoid noise, but do a broader unfiltered skim quarterly — a narrow subscription will miss new products you don't yet use.
2. **Route changelog triage through the same change-management process as everything else.** A changelog entry that changes a default or deprecates a feature in use should go through [Change Management](../govern/change-management.md) like any other configuration change — not get applied ad hoc by whoever reads it first.

## Incorporating new products into the foundation

When a changelog entry introduces a genuinely new capability (not just an iteration on an existing one), treat adopting it as a small version of the full framework lifecycle rather than an ungoverned one-off:

- **Strategy**: Does this change the answer to any [Assessing Workload Fit](../strategy/workload-fit.md) decision already made?
- **Foundation**: Does it need new [Identity & Access Control](../foundation/identity-and-access.md) roles, [Naming & Tagging](../foundation/naming-and-tagging.md) conventions, or [Infrastructure as Code](../foundation/infrastructure-as-code.md) module updates before anyone uses it in production?
- **Govern**: Does it need a new guardrail before broad use, or does an existing guardrail already cover it adequately?

Skipping this for something that looks small is how organizations end up with inconsistent, ungoverned use of a product that later needs retrofitting.

## Deprecation handling

Cloudflare's general pattern for retiring a legacy feature: announce the replacement, run both in parallel for a transition period, publish a migration guide, then force or automatically migrate remaining users. The clearest current example is **Page Rules being replaced by dedicated Rules products** (Cache Rules, Configuration Rules, Origin Rules, Redirect Rules, Transform Rules) built on the Ruleset Engine — see [Performance Operations](performance-operations.md) for the caching-specific piece, and the [Page Rules migration guide](https://developers.cloudflare.com/rules/reference/page-rules-migration/) for current migration status.

A practical deprecation-handling workflow:

| Step | Action |
|---|---|
| 1. Detect | Changelog entry, dashboard deprecation notice, or migration-guide publication |
| 2. Assess | Which zones/accounts/workloads are actually affected? Check via Terraform state or dashboard audit, not memory |
| 3. Plan | Follow Cloudflare's migration guide where one exists; sequence by risk (start with lowest-traffic, lowest-risk zones) |
| 4. Validate | Run old and new configurations in parallel where possible; compare behavior before fully cutting over |
| 5. Cut over | Remove the legacy configuration once validated — don't leave both in place indefinitely, which is itself a source of confusing, overlapping behavior |
| 6. Record | Update Infrastructure as Code and any internal documentation to reflect the new baseline |

## Design considerations

- **Don't let "it still works" substitute for migrating off deprecated features.** Cloudflare typically gives significant advance notice before forcing a migration, but waiting until forced migration removes your ability to sequence and validate the change on your own schedule.
- **Changelog review is a poor substitute for reading a full migration guide when one exists.** The changelog tells you *that* something changed; the migration guide tells you *what to actually do about it* and what won't carry over automatically.
- **Continuous improvement needs a named owner, or it doesn't happen.** Quarterly review is easy to schedule and easy to silently skip without an accountable owner and a standing calendar invite.

## Checklist

- [ ] Changelog subscription in place for every actively used product category
- [ ] Quarterly configuration and policy review scheduled with a named owner
- [ ] A documented process routes changelog-driven changes through [Change Management](../govern/change-management.md)
- [ ] No zone is running a known-deprecated feature without an active migration plan
- [ ] New product adoption runs through a lightweight version of Strategy/Foundation/Govern rather than being ungoverned
- [ ] Annual re-assessment scheduled for workloads that have grown or changed materially

## Further reading

- [Cloudflare changelog](https://developers.cloudflare.com/changelog/)
- [Page Rules migration guide](https://developers.cloudflare.com/rules/reference/page-rules-migration/)
- [Change Management](../govern/change-management.md)
- [Policy & Guardrails](../govern/policy-and-guardrails.md)
