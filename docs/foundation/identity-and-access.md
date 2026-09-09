# Identity & Access Control

Every other foundation decision — account structure, DNS changes, WAF rules, Zero Trust policy — is only as safe as the identity model controlling who and what can make those changes. Cloudflare splits this into two problems: **human access** to the dashboard and API (account roles, SSO, audit logging) and **machine access** for automation (API tokens vs. legacy keys). Design both before onboarding your first production zone — not after an incident forces the conversation.

## Key Cloudflare capabilities

| Capability | What it does | Reference |
|---|---|---|
| Account roles (RBAC) | Predefined roles (Super Administrator, Administrator, Billing, Administrator Read Only, and more) scoped to the whole account | [Roles and permissions](https://developers.cloudflare.com/fundamentals/manage-members/roles/) |
| Role scopes | Assign a role at account scope, domain (zone) scope, or resource scope instead of account-wide | [Role scopes](https://developers.cloudflare.com/fundamentals/manage-members/scope/) |
| Manage members | Invite, remove, and adjust member roles; requires Super Administrator | [Manage account members](https://developers.cloudflare.com/fundamentals/manage-members/manage/) |
| Dashboard SSO | Enforce SAML/OIDC single sign-on for dashboard login, tied to your email domain | [Dashboard SSO](https://developers.cloudflare.com/fundamentals/manage-members/dashboard-sso/) |
| API tokens | Scoped, expiring, revocable credentials for automation and CI/CD | [Create API token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/) |
| Global API key (legacy) | Single, user-wide credential with the same permissions as the user — no scoping | [Get Global API key (legacy)](https://developers.cloudflare.com/fundamentals/api/get-started/keys/) |
| Audit Logs | Standardized, near-complete record of account and zone configuration changes | [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) |

## Design considerations

**Super Administrator is a keys-to-the-kingdom role.** It can edit any setting, manage billing and members, create account-owned API tokens, and revoke other Super Administrators' access. That last property is a feature — no single admin can lock everyone else out — but it also means every holder is a full blast-radius risk: one phished account can touch DNS, WAF, Zero Trust policy, and billing at once. Treat it like a domain admin credential in Active Directory, not a generic "power user" flag:

- Keep the Super Administrator group as small as operationally possible — a handful of named individuals, not a team-wide default.
- Require 2FA (a prerequisite for several other features, including Organizations) or SSO enforcement for every Super Administrator, no exceptions.
- Use scoped, lower-privilege roles (Administrator, Billing, Administrator Read Only, or domain/resource-scoped roles) for day-to-day work, and reserve Super Administrator for break-glass and account-level changes.
- Review Super Administrator membership on a fixed cadence (quarterly is a reasonable default) via [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) — check who has the role, not just who used it.

**Role scoping beats account-wide roles once you have more than a handful of zones.** Cloudflare lets you assign roles at account, domain (zone), or resource scope (e.g., a specific Tunnel). A contractor managing only `marketing.example.com` should get a domain-scoped role, not an account-scoped Administrator role that can also touch production DNS. Use scoped roles from the start — retrofitting them after everyone has account-wide access is a much harder conversation.

**SSO should be table stakes, not an Enterprise-only aspiration.** Dashboard SSO — SAML/OIDC login through your corporate IdP for anyone on your verified email domain — is available on all account types, not gated to Enterprise. A team past its first few members has no good reason to rely on individual Cloudflare passwords over the IdP's MFA and lifecycle management (deprovisioning a leaver should immediately cut off Cloudflare access). Set it up before member count outgrows manual tracking.

**API tokens, not the Global API key, for every machine identity.** The Global API key is a single, user-scoped credential with the owner's exact permissions — no expiry, no IP restriction, one per user, painful to rotate. API tokens fix this: scoped to specific zones and permissions (e.g., "Zone DNS Edit" on one zone), with a TTL and IP restriction. Terraform, Wrangler, GitHub Actions, and every other automation should use a purpose-built token — never a shared Global API key. See [Infrastructure as Code](infrastructure-as-code.md) and [CI/CD & Infrastructure Delivery](../adopt/cicd-and-delivery.md).

**Audit logging is your evidence trail, but only if someone reads it.** Audit Logs (v2) capture a standardized record of account- and zone-level changes across most Cloudflare products, via the dashboard or the `/accounts/{account_id}/logs/audit` endpoint. Wire it into your SIEM rather than treating the dashboard as the only consumer — a Super Administrator role change or DNS record deletion deserves the same alerting rigor as an equivalent AWS IAM or Azure AD change.

## Decisions to make

| Decision | Recommended default | When to deviate |
|---|---|---|
| Who holds Super Administrator | 2–4 named individuals with 2FA/SSO enforced | Larger orgs may need a break-glass account plus a small operating group, still tightly bounded |
| Machine credentials | Scoped, expiring API tokens per automation use case | Never — the Global API key should not be used for new automation |
| Human login | SSO enforced via corporate IdP | Very small teams pre-IdP may start with 2FA-only, but plan to migrate |
| Contractor/vendor access | Domain- or resource-scoped roles | Account-scoped only for internal platform team members |
| Audit log consumption | Forwarded to SIEM/log pipeline with alerting on privileged role changes | Dashboard-only review acceptable only for very small accounts |

## Checklist

- [ ] Super Administrator membership documented, minimized, and reviewed on a recurring cadence
- [ ] 2FA or SSO enforced for all Super Administrators (required for Organizations creation)
- [ ] Dashboard SSO configured against your corporate IdP for all dashboard users
- [ ] Domain- or resource-scoped roles used for contractors, agencies, and single-zone owners
- [ ] All automation (Terraform, Wrangler, CI/CD) uses scoped API tokens, not the Global API key
- [ ] Audit Logs forwarded to a SIEM or log pipeline, with alerts on member/role changes
- [ ] Legacy Global API keys inventoried and migrated off wherever still in use

## Further reading

- [Roles and permissions](https://developers.cloudflare.com/fundamentals/manage-members/roles/)
- [Role scopes](https://developers.cloudflare.com/fundamentals/manage-members/scope/)
- [Manage account members](https://developers.cloudflare.com/fundamentals/manage-members/manage/)
- [Dashboard SSO](https://developers.cloudflare.com/fundamentals/manage-members/dashboard-sso/)
- [API: Get started](https://developers.cloudflare.com/fundamentals/api/get-started/)
- [Create API token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/)
- [Get Global API key (legacy)](https://developers.cloudflare.com/fundamentals/api/get-started/keys/)
- [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)
