# Performance Operations

--8<-- "_snippets/disclaimer.md"

Performance operations on Cloudflare center on a small number of configuration levers that compound with each other: how much traffic is served from cache, how the rest is routed to origin, where Worker code executes relative to the systems it calls, and how media assets are optimized before reaching the browser. None require traditional capacity planning — they're tuned continuously against observed traffic, which is why this is a Manage-phase activity, not a one-time Adopt-phase step.

The core operating metric is **cache hit ratio** — the single number reflecting the combined effect of Cache Rules, Tiered Cache, and (for cacheable-but-dynamic content) Argo Smart Routing. Everything else on this page either improves hit ratio or improves the experience for traffic that can't be cached.

## Key Cloudflare capabilities

| Capability | What it does | Docs |
|---|---|---|
| Cache Rules | The current, rule-based mechanism for controlling what's eligible for cache, cache TTLs, and cache key construction — the modern replacement for the caching-related settings that used to live in Page Rules | [Cache Rules](https://developers.cloudflare.com/cache/how-to/cache-rules/) |
| Tiered Cache | Organizes Cloudflare's data centers into a hierarchy of lower and upper tiers so a cache miss at the edge can often be satisfied by a nearby upper-tier data center instead of going to origin; Smart Tiered Cache automatically picks the best upper tier per origin | [Tiered Cache](https://developers.cloudflare.com/cache/how-to/tiered-cache/) |
| Argo Smart Routing | Uses real-time network performance data to route requests across the fastest available path to origin, rather than the default shortest path, for traffic that isn't served from cache | [Argo Smart Routing](https://developers.cloudflare.com/argo-smart-routing/) |
| Smart Placement | Automatically runs a Worker in the Cloudflare location that minimizes total request latency, based on where its backend dependencies actually are, rather than always running nearest the client | [Smart Placement](https://developers.cloudflare.com/workers/configuration/smart-placement/) |
| Images | Cloudflare's current image platform: on-the-fly resizing/format conversion (via URL or Workers `fetch()`), plus managed hosted-image storage and variants | [Images](https://developers.cloudflare.com/images/) |
| Polish | A one-click origin-image recompression feature (strips metadata, applies lossy/lossless compression) for images already served from your own origin, distinct from Images' transformation pipeline | [Polish](https://developers.cloudflare.com/images/polish/) |

## Design considerations

- **Page Rules is legacy for caching.** If any zone still relies on Page Rules for cache behavior (Cache Level, Edge Cache TTL, Bypass Cache on Cookie), plan a migration to Cache Rules — running both together produces confusing, overlapping behavior. Check the [Page Rules migration guide](https://developers.cloudflare.com/rules/reference/page-rules-migration/) for current status and gotchas (for example, "eligible for cache" behaves differently by default than the old Cache Everything toggle).
- **Tiered Cache and Argo Smart Routing solve different problems.** Tiered Cache reduces origin trips for *cacheable* content by consolidating misses through fewer upper-tier data centers. Argo Smart Routing improves the path for content that must still reach origin (dynamic responses, misses, API calls). Most performance-sensitive zones benefit from evaluating both, not treating them as substitutes.
- **Smart Placement isn't automatically better for every Worker.** It's most valuable when a Worker makes multiple round trips to a specific, geographically fixed backend (a single-region database, an internal API). A CPU-bound Worker with no backend calls, or one whose backend is itself globally distributed, may see little benefit. Smart Placement also only considers locations already receiving real traffic for that Worker — it can't place cold-start traffic somewhere it's never run.
- **Cache key design determines hit ratio more than TTL does.** An overly specific custom cache key (including query strings or headers that don't actually vary the response) fragments the cache and silently tanks hit ratio; this is a more common root cause of poor performance than TTL misconfiguration.
- **Image optimization has two separate products, not one.** Polish recompresses images already served from your existing origin URLs, no markup changes needed. Images (transformations, via URL path or Workers) creates new, resized/reformatted variants — reach for it when you need responsive images, on-the-fly cropping, or a managed image storage/delivery pipeline. Don't apply Polish to images already generated through Images; they're already optimized.

## Cache hit ratio as a KPI

Treat cache hit ratio as an operating metric with an owner and a trend line, not a one-time tuning exercise:

| Signal | What it usually means | First place to look |
|---|---|---|
| Hit ratio drops after a deploy | A cache key or Cache Rule regression, or new dynamic/personalized content | Recent Cache Rules or origin header changes |
| Hit ratio is low but stable | Cache Rules aren't matching the traffic mix that actually exists | Cache Rules expression coverage vs. real traffic in Analytics |
| Hit ratio is high but origin load is still high | A small share of very expensive uncached requests dominates origin cost | GraphQL Analytics API filtered to cache status `MISS`/`DYNAMIC` |
| Hit ratio varies a lot by region | Tiered Cache topology or origin TTL settings interact badly with request distribution | Tiered Cache configuration and Smart Tiered Cache eligibility |

## Decisions to make

| Decision | Options | Notes |
|---|---|---|
| Cache Rules or remaining Page Rules? | Migrate fully to Cache Rules / leave stable legacy Page Rules until forced migration | Don't run both against overlapping paths — pick one system of record per hostname |
| Tiered Cache topology | Smart Tiered Cache (automatic) / manually pinned upper tier | Smart Tiered Cache is the right default; manual pinning is a narrow, latency-sensitive edge case |
| Argo Smart Routing: enable broadly or selectively? | All zones / only latency-sensitive or geographically distant-from-origin zones | Cost/benefit depends on origin distance from users — evaluate per zone, not organization-wide |
| Smart Placement: per-Worker or blanket policy? | Enable per Worker based on backend topology / leave disabled by default | Best applied selectively — see design considerations above |
| Image strategy | Polish only / Images transformations / both | Depends on whether images are already at fixed origin URLs vs. need dynamic variants |

## Checklist

- [ ] Cache hit ratio tracked as a recurring KPI with an assigned owner
- [ ] No zone relies on both Page Rules and Cache Rules for overlapping cache behavior
- [ ] Cache key design reviewed for unnecessary fragmentation (query strings, headers, cookies)
- [ ] Tiered Cache (ideally Smart Tiered Cache) enabled for zones with geographically distributed traffic
- [ ] Argo Smart Routing evaluated for zones with significant client-to-origin distance
- [ ] Smart Placement evaluated per Worker based on backend topology, not applied blindly
- [ ] Image optimization approach (Polish vs. Images transformations) matches how images are actually served today

## Further reading

- [Cache Rules](https://developers.cloudflare.com/cache/how-to/cache-rules/)
- [Page Rules migration guide](https://developers.cloudflare.com/rules/reference/page-rules-migration/)
- [Tiered Cache](https://developers.cloudflare.com/cache/how-to/tiered-cache/)
- [Argo Smart Routing](https://developers.cloudflare.com/argo-smart-routing/)
- [Smart Placement](https://developers.cloudflare.com/workers/configuration/smart-placement/)
- [Images](https://developers.cloudflare.com/images/)
- [Polish](https://developers.cloudflare.com/images/polish/)

See also [Cost Optimization](cost-optimization.md) for how these same levers reduce origin cost, and [Reliability Operations](reliability-operations.md) for how routing and health checks interact with failover.
