# Glossary

--8<-- "_snippets/disclaimer.md"

Terms used throughout this framework, each with a short definition and a link to the canonical Cloudflare documentation. Where Cloudflare has recently renamed a product, the current name is the heading with the prior name noted — check the linked page for current status, since naming keeps evolving.

**Account** — A container for one or more zones and members, with roles and permissions applied at the account level. Account-level products (Workers, Pages, Security Center, and others) can affect some or all zones within it. See [Accounts, zones, and profiles](https://developers.cloudflare.com/fundamentals/concepts/accounts-and-zones/).

**Anycast** — A network routing technique where the same IP address is announced from many data centers simultaneously, and standard Internet routing (BGP) delivers each request to a topologically nearby location. This underlies Cloudflare's global network, giving it both low latency and inherent DDoS resilience. See [Cloudflare IP addresses](https://developers.cloudflare.com/fundamentals/concepts/cloudflare-ip-addresses/).

**Argo Smart Routing** — A performance product that uses Cloudflare's real-time network telemetry to route traffic across the fastest available path to origin, rather than the Internet's default (often not shortest) path, for requests that reach origin. See [Argo Smart Routing](https://developers.cloudflare.com/argo-smart-routing/).

**Authenticated Origin Pulls (AOP)** — A configuration that adds mutual TLS client-certificate authentication between Cloudflare and your origin, so the origin can verify a request genuinely came from Cloudflare rather than directly from the Internet. See [Authenticated Origin Pulls](https://developers.cloudflare.com/ssl/origin-configuration/authenticated-origin-pull/).

**Bot Management** — Cloudflare's Enterprise-tier bot detection product, which scores each request (1–99) using a layered set of detection engines so you can write targeted rules against automated traffic instead of blanket-blocking it. Lower-tier equivalents include Bot Fight Mode and Super Bot Fight Mode. See [Bot Management](https://developers.cloudflare.com/bots/get-started/bot-management/).

**BYOIP (Bring Your Own IP)** — A configuration that lets you announce your own IP address space from Cloudflare's network, for use with Magic Transit, Spectrum, CDN, or Gateway/dedicated egress. See [BYOIP](https://developers.cloudflare.com/byoip/).

**CASB (Cloud Access Security Broker)** — An API-driven Cloudflare One capability that connects to SaaS and cloud provider APIs to find misconfigurations, unauthorized sharing, shadow IT, and other post-login risks; surfaced in the dashboard as **Cloud & SaaS findings**. Distinct from Gateway, which inspects traffic in transit. See [Cloud & SaaS findings](https://developers.cloudflare.com/cloudflare-one/cloud-and-saas-findings/).

**CDN (Content Delivery Network)** — Cloudflare's original core product category: caching and delivering content from edge data centers close to end users rather than always reaching back to origin. See [Reference Architecture — CDN](https://developers.cloudflare.com/reference-architecture/architectures/cdn/).

**Cloudflare Access** — The Zero Trust product that replaces a corporate VPN for application access, gating each request to an internal or SaaS application behind an identity- and device-aware policy. See [Access applications](https://developers.cloudflare.com/cloudflare-one/applications/).

**Cloudflare Gateway** — Cloudflare's Secure Web Gateway (SWG): a set of DNS-, network- (Layer 4), and HTTP-layer (Layer 7) policies that inspect and filter outbound traffic from users and devices to the Internet. See [Traffic policies](https://developers.cloudflare.com/cloudflare-one/traffic-policies/).

**Cloudflare One Client** *(formerly WARP)* — The client application installed on a user's device that securely and privately forwards its traffic to Cloudflare's network so Gateway and Access policies can be enforced regardless of user location. See [About the Cloudflare One Client](https://developers.cloudflare.com/cloudflare-one/team-and-resources/devices/cloudflare-one-client/).

**Cloudflare Tunnel** — A way to connect an origin or private network to Cloudflare without any publicly routable IP address or open inbound port, using the `cloudflared` daemon to establish an outbound-only connection. See [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/).

**Cloudflare WAN** *(formerly Magic WAN)* — A SASE product replacing traditional enterprise WAN architecture: connects sites, data centers, and cloud environments through Cloudflare's network for connectivity, security, and centralized control of east-west/internal traffic. See [Cloudflare WAN](https://developers.cloudflare.com/cloudflare-wan/).

**D1** — Cloudflare's serverless SQL database, built on SQLite semantics, queryable from a Worker or via HTTP API, with optional asynchronous read replication. See [D1](https://developers.cloudflare.com/d1/).

**DLP (Data Loss Prevention)** — A Cloudflare One capability that scans HTTP traffic and SaaS application content for sensitive data patterns (financial data, secrets, source code, and similar), and can be used standalone or alongside CASB. See [Data Loss Prevention](https://developers.cloudflare.com/cloudflare-one/data-loss-prevention/).

**DNS-only record** *(gray cloud)* — A DNS record served by Cloudflare's authoritative DNS but not proxied: queries resolve to the real origin IP, and Cloudflare provides no HTTP-layer protection, caching, or analytics for that hostname. Required for non-HTTP protocols (SSH, RDP, game servers, etc.). See [Proxy status](https://developers.cloudflare.com/dns/proxy-status/).

**DNSSEC** — A DNS security extension where Cloudflare digitally signs zone records so resolvers can validate they haven't been forged or tampered with in transit. See [DNSSEC](https://developers.cloudflare.com/dns/dnssec/).

**Durable Object** — A Workers primitive providing a single-instance, globally addressable unit of stateful compute with its own strongly consistent, transactional storage. Used for coordination and real-time state, and as the underlying building block for other products, including D1. See [What are Durable Objects?](https://developers.cloudflare.com/durable-objects/concepts/what-are-durable-objects/).

**Full (strict)** — The most secure Cloudflare SSL/TLS encryption mode: Cloudflare connects to the origin over HTTPS and validates the origin's certificate is valid, unexpired, and matches the hostname (issued by a public CA or Cloudflare Origin CA). See [SSL/TLS encryption modes](https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/).

**GraphQL Analytics API** — A single GraphQL endpoint exposing Cloudflare's analytics datasets (HTTP requests, Firewall events, Load Balancing, and more) for programmatic, filtered, and aggregated querying — the same data that powers dashboard visualizations. See [GraphQL Analytics API](https://developers.cloudflare.com/analytics/graphql-api/).

**KV (Workers KV)** — A globally distributed, low-latency key-value store optimized for high-read, infrequently-written data, accessed from Workers or via API. See [Workers KV](https://developers.cloudflare.com/kv/).

**Load Balancing** — A product that distributes traffic across multiple origin pools using health checks and a configurable steering policy (latency-based, geographic, proximity-based, and others), with automatic failover away from unhealthy origins. See [Load Balancing](https://developers.cloudflare.com/load-balancing/).

**Logpush** — A mechanism for continuously exporting raw Cloudflare logs (HTTP requests, Workers trace events, DNS, and other datasets) to a third-party or self-hosted destination such as R2, S3, Splunk, or Datadog. See [Logpush](https://developers.cloudflare.com/logs/logpush/).

**Magic Transit** — A network security and performance product serving as the front door to an on-premises, cloud-hosted, or hybrid IP network. Absorbs DDoS attacks and accelerates traffic before forwarding clean packets to origin infrastructure. See [Magic Transit](https://developers.cloudflare.com/magic-transit/).

**Managed Ruleset** — A ruleset authored and maintained by Cloudflare (rather than by the customer) and deployed into an account's or zone's Ruleset Engine phase — for example, the Cloudflare Managed Ruleset used by the WAF. See [Cloudflare Managed Ruleset](https://developers.cloudflare.com/waf/managed-rules/reference/cloudflare-managed-ruleset/).

**mTLS (Mutual TLS)** — A TLS handshake in which both parties present and validate certificates, not just the server — used by Cloudflare both for Authenticated Origin Pulls (Cloudflare-to-origin) and for API Shield's client-certificate authentication (client-to-Cloudflare). See [Mutual TLS (API Shield)](https://developers.cloudflare.com/api-shield/security/mtls/).

**Pages** — Cloudflare's platform for deploying frontend/static and framework-based sites directly from a Git repository, with automatic builds and preview deployments; increasingly converges with Workers' static-assets capabilities. See [Pages](https://developers.cloudflare.com/pages/).

**Proxied record** *(orange cloud)* — A DNS record whose traffic is routed through Cloudflare's network: DNS queries resolve to a Cloudflare Anycast IP, and HTTP/HTTPS requests are proxied, cached, and protected according to that zone's configuration. See [Proxy status](https://developers.cloudflare.com/dns/proxy-status/).

**Queue** — A managed message queue product integrated with Workers, providing guaranteed at-least-once delivery, batching, and Worker-to-Worker messaging without egress charges. See [Queues](https://developers.cloudflare.com/queues/).

**R2** — Cloudflare's S3-API-compatible object storage, priced with no charge for egress bandwidth (see [Cost Optimization](../manage/cost-optimization.md) for how this affects total cost). See [R2](https://developers.cloudflare.com/r2/).

**Ruleset** — An ordered set of rules that execute together within a specific phase of Cloudflare's Ruleset Engine (for example, the WAF phase or the Cache Rules phase); the underlying engine behind WAF, Cache Rules, Configuration Rules, Transform Rules, and more. See [Rulesets](https://developers.cloudflare.com/ruleset-engine/about/rulesets/).

**Smart Placement** — A Workers feature that automatically runs a Worker's code in the Cloudflare location that minimizes total request latency, based on measured performance against the Worker's actual backend dependencies, rather than always executing nearest the client. See [Smart Placement](https://developers.cloudflare.com/workers/configuration/smart-placement/).

**Terraform provider** — Cloudflare's official HashiCorp Terraform provider, giving Infrastructure-as-Code access to the Cloudflare API for managing zones, DNS, WAF rules, Workers, and most other resources. See [Cloudflare Terraform provider](https://developers.cloudflare.com/terraform/).

**Tiered Cache** — A caching feature that organizes Cloudflare's data centers into a hierarchy of lower and upper tiers, so cache misses at the edge are often satisfied by a nearby upper-tier data center instead of reaching origin. Reduces both origin load and the number of data centers connecting directly to origin. See [Tiered Cache](https://developers.cloudflare.com/cache/how-to/tiered-cache/).

**WAF (Web Application Firewall)** — Cloudflare's product for inspecting and filtering incoming HTTP/API requests against sets of rules (managed and custom) to block common web application attacks. See [Web Application Firewall](https://developers.cloudflare.com/waf/).

**Workers** — Cloudflare's serverless compute platform, running JavaScript/TypeScript, Python, and other language runtimes directly on Cloudflare's network at the request path, billed on requests and CPU time. See [Workers](https://developers.cloudflare.com/workers/).

**Wrangler** — The official command-line tool for developing, testing, and deploying Workers (and related Developer Platform resources), configured via a project's `wrangler` configuration file. See [Wrangler](https://developers.cloudflare.com/workers/wrangler/).

**Zero Trust** — Cloudflare's security model and product suite (Access, Gateway, Tunnel, CASB, DLP, and more) that verifies every request based on identity and device context rather than trusting anything by virtue of network location — delivered as part of the broader Cloudflare One platform. See [Cloudflare One](https://developers.cloudflare.com/cloudflare-one/).

**Zone** — A domain or subdomain added to Cloudflare, and the unit at which most CDN, security, and DNS settings are configured. A zone belongs to exactly one account. See [Accounts, zones, and profiles](https://developers.cloudflare.com/fundamentals/concepts/accounts-and-zones/).
