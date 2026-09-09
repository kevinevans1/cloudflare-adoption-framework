# Data Protection

Data protection on Cloudflare spans three distinct trust boundaries: the client-to-edge connection, the edge-to-origin connection, and data at rest in Cloudflare's own storage products. Each has a separate configuration surface, and getting one right does not imply the others are covered.

## Key Cloudflare capabilities

| Boundary | Capability | Docs |
|---|---|---|
| Client ↔ edge | TLS termination, [TLS modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) | [ssl/origin-configuration/ssl-modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) |
| Edge ↔ origin | [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/) (mTLS from Cloudflare to your origin) | [ssl/origin-configuration/authenticated-origin-pull](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/) |
| Client ↔ edge (mTLS to your app/API) | [Client certificates](https://developers.cloudflare.com/ssl/client-certificates/) using Cloudflare's managed CA or your own (BYOCA on Enterprise) | [ssl/client-certificates](https://developers.cloudflare.com/ssl/client-certificates/) |
| API-specific mTLS | [API Shield mTLS](https://developers.cloudflare.com/api-shield/security/mtls/) | [api-shield/security/mtls](https://developers.cloudflare.com/api-shield/security/mtls/) |
| Data at rest — object storage | [R2 encryption at rest](https://developers.cloudflare.com/r2/reference/data-security/) — automatic, no configuration required | [r2/reference/data-security](https://developers.cloudflare.com/r2/reference/data-security/) |
| Sensitive data in transit/at rest across Zero Trust traffic | [DLP](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/) | [cloudflare-one/data-loss-prevention](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/) |
| Application secrets | [Workers secrets](https://developers.cloudflare.com/workers/configuration/secrets/) | [workers/configuration/secrets](https://developers.cloudflare.com/workers/configuration/secrets/) |

## Design considerations

### TLS mode is a single setting with outsized consequences

[SSL/TLS encryption modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/) — Off, Flexible, Full, Full (strict) — determine whether the edge-to-origin leg is encrypted and validated at all:

| Mode | Client ↔ edge | Edge ↔ origin | When it's appropriate |
|---|---|---|---|
| Flexible | HTTPS | Plain HTTP | Effectively never for anything handling credentials, sessions, or sensitive data — traffic between Cloudflare and your origin is unencrypted |
| Full | HTTPS | HTTPS, no certificate validation | Origin has *a* TLS certificate (even self-signed) but not one from a trusted CA |
| Full (strict) | HTTPS | HTTPS, certificate validated | The baseline for production — origin has a valid certificate (Cloudflare's free [Origin CA](https://developers.cloudflare.com/ssl/origin-configuration/origin-ca/) is sufficient and purpose-built for this) |

Flexible is a common default left over from initial onboarding that never gets revisited — treat it as a finding in any security review, not an acceptable steady state for production traffic.

### Authenticated Origin Pulls closes the "bypass Cloudflare entirely" gap

TLS mode secures the edge-to-origin *content*; it doesn't prove the connection came from Cloudflare. [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/) has the origin require and validate a client certificate that only Cloudflare (or your configured zone) presents, so a request that bypasses Cloudflare's proxy — because the origin IP leaked — is rejected at the TLS handshake rather than reaching application code. This is the primary defense against direct-to-origin attacks that route around your entire edge security stack.

### Client certificates and API Shield mTLS are a different boundary

[Client certificates](https://developers.cloudflare.com/ssl/client-certificates/) authenticate the *inbound* client to Cloudflare — useful for machine-to-machine API traffic, IoT devices, or service-to-service calls where you control both ends. Cloudflare's managed CA covers most use cases; Enterprise accounts can bring their own CA. [API Shield's mTLS](https://developers.cloudflare.com/api-shield/security/mtls/) applies this specifically to API traffic alongside schema and JWT validation. Don't conflate this with Authenticated Origin Pulls — one authenticates clients to Cloudflare, the other authenticates Cloudflare to your origin.

### R2 encryption at rest is automatic, but key management is Cloudflare's, not yours

[R2 encrypts all objects and metadata at rest](https://developers.cloudflare.com/r2/reference/data-security/) using AES-256-GCM by default, with Cloudflare managing the keys — no setup step, no option to disable it. This is a "check the box, move on" control for most compliance purposes, but be precise in documentation: this is Cloudflare-managed key encryption, not customer-managed keys (bring-your-own-key). If a specific regulatory requirement mandates customer-controlled keys, verify current R2 documentation for whether that model is supported before assuming it is.

### Secrets belong in Workers secrets, not environment variables or source

[Workers secrets](https://developers.cloudflare.com/workers/configuration/secrets/) are encrypted bindings, distinct from plaintext environment variables — once set via `wrangler secret put` or the dashboard, their values aren't retrievable through the dashboard or Wrangler afterward. Any credential, API key, or token a Worker needs at runtime belongs here, not in a plaintext var, and never committed to the repository backing your [Infrastructure as Code](../foundation/infrastructure-as-code.md).

### DLP for Zero Trust traffic is a detection layer, not a blocking guarantee

[DLP](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/) profiles detect sensitive data patterns in traffic Gateway inspects. It's a strong control for accidental exposure (a user pasting a credential into an unsanctioned SaaS app) but is pattern/heuristic-based — treat it as a detective and preventive control layered on top of data classification and access control, not a substitute for them.

## Decisions to make

- [ ] Is any production zone still running Flexible TLS, and is there a plan to move to Full Strict?
- [ ] Is Authenticated Origin Pulls enabled, and has the origin IP ever been exposed in DNS history, SPF records, or other leaks that would need rotation?
- [ ] Which APIs or services need client certificate authentication, and is Cloudflare's managed CA sufficient or is BYOCA required?
- [ ] Does any compliance requirement mandate customer-managed encryption keys for stored data, and does current R2 documentation support that model?
- [ ] Are all Worker credentials stored as secrets, or are any still in plaintext environment variables or source control?
- [ ] Are DLP profiles defined for this organization's actual sensitive data categories, or left at generic defaults?

## Checklist

- [ ] No production zone on Flexible TLS
- [ ] Full (strict) TLS with a valid origin certificate (Origin CA or public CA) on every production zone
- [ ] Authenticated Origin Pulls enabled and origin firewalled to reject non-Cloudflare connections
- [ ] Client certificates deployed for machine-to-machine/API traffic where applicable
- [ ] All Worker secrets stored via Workers secrets, none in plaintext vars or source
- [ ] DLP profiles tuned to actual sensitive data categories, reviewed periodically

## Further reading

- [SSL/TLS encryption modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/)
- [Cloudflare Origin CA certificates](https://developers.cloudflare.com/ssl/origin-configuration/origin-ca/)
- [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/)
- [Client certificates / mTLS](https://developers.cloudflare.com/ssl/client-certificates/)
- [API Shield mTLS](https://developers.cloudflare.com/api-shield/security/mtls/)
- [R2 data security (encryption at rest)](https://developers.cloudflare.com/r2/reference/data-security/)
- [Workers secrets](https://developers.cloudflare.com/workers/configuration/secrets/)
- [Cloudflare One Data Loss Prevention](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/)

See also: [Shared Responsibility Model](shared-responsibility.md) for how these controls map to customer vs. Cloudflare responsibility, and [Compliance Mapping](../govern/compliance-mapping.md) for how they support regulatory obligations.
