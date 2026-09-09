# Change Management

Security and network configuration on Cloudflare is powerful enough to break production in one API call — a WAF rule in Block mode with an over-broad expression, a DNS record change, a TLS mode flip. Change management here isn't process for its own sake; it's the difference between "we caught this in staging" and "we caught this in Search Console/the incident channel."

## Key Cloudflare capabilities

| Capability | Role in change management |
|---|---|
| [Ruleset versioning](https://developers.cloudflare.com/ruleset-engine/about/rulesets/) | Rulesets (WAF custom rules, rate limiting, etc.) are versioned objects — each modification creates a new version, and you can deploy a prior version to roll back. |
| Log-only / simulate mode | WAF [custom rules](https://developers.cloudflare.com/waf/custom-rules/) and [managed rulesets](https://developers.cloudflare.com/waf/managed-rules/) support a **Log** action distinct from **Block** or **Managed Challenge** — the standard way to stage a rule against real traffic before it can affect it. |
| [Terraform](https://developers.cloudflare.com/terraform/) plan/apply workflow | Turns "someone clicked a button in the dashboard" into a reviewable diff with a named author and an approval step. |
| [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) | The record of what actually changed, when, and by whom — including changes made outside the reviewed pipeline. |
| [Cloudflare API](https://developers.cloudflare.com/api/) | Everything Terraform does ultimately calls this — useful to know when debugging why a Terraform-managed resource drifted (something else called the API directly). |

## Design considerations

### Stage in log-only mode before you block

For any new or materially modified WAF rule, rate limiting rule, or managed ruleset deployment:

1. Deploy it in **Log** mode first.
2. Observe matched traffic against real production requests for a defined soak period.
3. Confirm no legitimate traffic is caught.
4. Flip to **Block** (or **Managed Challenge**) in a separate, reviewed change.

This is standard practice for the [Rulesets engine](https://developers.cloudflare.com/ruleset-engine/) — rule expressions are easy to write too broadly (e.g., matching a legitimate API client's user-agent string).

The two changes — "deploy in log mode" and "flip to enforce" — should be **separate commits/PRs**, not one change with a manual follow-up someone might forget.

### Versioned rulesets and rollback

Because rulesets are versioned, rollback should mean "redeploy the previous version" — not reconstructing what it looked like from memory. This only works if the ruleset's desired state lives in Terraform (see [Policy & Guardrails](policy-and-guardrails.md)): Terraform state plus version control gives you an exact prior version to `apply` back to, rather than relying on Cloudflare's version history alone under time pressure.

### The plan/apply review workflow

A minimal but effective workflow for security/network changes:

1. Change proposed as a PR against the Terraform configuration (zone settings, ruleset rules, DNS records, Access policies, etc.).
2. CI runs `terraform plan` and posts the diff to the PR — reviewers should be able to see exactly what will change without applying it.
3. A human who did not author the change reviews the plan output, not just the code — a rule expression can look correct and still evaluate wrong.
4. On approval and merge, CI runs `terraform apply` (or a controlled human triggers it) against the target environment.
5. For anything touching production WAF/DNS/Access policy: apply outside of peak traffic windows where feasible, and have someone actively watching analytics/error rates immediately after.

### Who approves what

Not every change needs the same rigor. A reasonable tiering:

| Change type | Suggested approval bar |
|---|---|
| New custom rule in Log-only mode | Standard PR review (one peer) |
| Flipping an existing rule from Log to Block/Challenge | PR review + a second approver from the security team |
| Managed ruleset mode/sensitivity change (org-wide baseline) | Security team sign-off, since it affects every zone under that baseline |
| DNS record changes on production apex/critical hostnames | PR review + a second approver; consider a scheduled change window |
| TLS mode change (e.g., loosening Full Strict to Full) | Security team sign-off required — this is a materially weaker posture, not a routine config edit |
| [Zero Trust Access](../secure/zero-trust-architecture.md) policy changes affecting who can reach an internal app | Security/IT team sign-off; treat like an IAM change, not a network change |

### Emergency changes

Incidents (see [Incident Response](../secure/incident-response.md)) sometimes require an out-of-band change — enabling [Under Attack mode](https://developers.cloudflare.com/waf/tools/security-level/) during an active Layer 7 DDoS attack, or hot-blocking an IP range. When that happens:

- The person making the emergency change is still identifiable (named API token/account, not a shared credential).
- The emergency change is back-ported into Terraform within the same business day, so the "temporary" fix doesn't quietly become permanent, undocumented state.
- A brief post-incident note records what was changed, why, and whether it should become a permanent part of the baseline.

## Decisions to make

- [ ] What is the minimum soak time in Log-only mode before a new rule can be enforced?
- [ ] Who has standing authorization to make emergency changes outside the normal PR flow, and how is that logged?
- [ ] Does every rule/policy category have an assigned approval tier, or is it decided ad hoc per change?
- [ ] How quickly must an emergency out-of-band change be back-ported into Terraform?
- [ ] Is there a defined rollback owner/procedure for a bad WAF deploy that's actively blocking legitimate traffic right now?

## Checklist

- [ ] Every new/modified security rule ships in Log-only mode before enforcement
- [ ] Terraform plan output is a required part of PR review for zone/security config changes
- [ ] Approval tiers defined per change type (see table above)
- [ ] Emergency change process defined, including same-day back-port into source control
- [ ] Audit Logs periodically reconciled against Terraform state to catch out-of-band changes
- [ ] Rollback procedure tested at least once (not just documented) — practice reverting a ruleset to a prior version

## Further reading

- [Ruleset Engine — rulesets and versions](https://developers.cloudflare.com/ruleset-engine/about/rulesets/)
- [WAF custom rules](https://developers.cloudflare.com/waf/custom-rules/)
- [WAF managed rules](https://developers.cloudflare.com/waf/managed-rules/)
- [Security level / Under Attack mode](https://developers.cloudflare.com/waf/tools/security-level/)
- [Cloudflare Terraform provider](https://developers.cloudflare.com/terraform/)
- [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)
- [Cloudflare API reference](https://developers.cloudflare.com/api/)

See also: [Policy & Guardrails](policy-and-guardrails.md) for what the baseline being changed actually contains, and [Incident Response](../secure/incident-response.md) for the emergency-change scenario this page references.
