# Incident Response & Status Monitoring

When something goes wrong — a DDoS attack, an origin outage, a WAF rule blocking legitimate traffic, or Cloudflare itself having an incident — the difference between a contained event and a prolonged outage usually comes down to how fast the right person finds out and how well-rehearsed the response is. This page covers monitoring, alerting, response basics, and post-incident review for a Cloudflare-fronted architecture.

## Key Cloudflare capabilities

| Capability | Role in incident response |
|---|---|
| [Cloudflare Status](https://www.cloudflarestatus.com/) | Public status page for Cloudflare's own infrastructure — the first place to check "is this us or is this Cloudflare" during an incident |
| [Notifications](https://developers.cloudflare.com/notifications/) | Configurable alerts for security events (WAF triggers, DDoS activity), origin health (health check failures, 5xx spikes), and billing/usage anomalies |
| [Security level / Under Attack mode](https://developers.cloudflare.com/waf/tools/security-level/) | An aggressive, temporary security posture that adds interstitial challenges to mitigate active Layer 7 DDoS attacks |
| [WAF custom rules](https://developers.cloudflare.com/waf/custom-rules/) | The mechanism for a fast, targeted emergency block (specific IP, ASN, country, URI pattern) during an active incident |
| [Logpush](https://developers.cloudflare.com/logs/logpush/) | Near-real-time export of request/security logs to storage or a SIEM for both active investigation and post-incident analysis |
| [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/) | Records what configuration changes were made during the incident, by whom |
| [Security Center](https://developers.cloudflare.com/security-center/) | Consolidated view of security insights, infrastructure inventory, and (beta) security reports across an account |

## Design considerations

### Subscribe to Cloudflare Status before you need it

[Cloudflare Status](https://www.cloudflarestatus.com/) supports subscribing to incident and maintenance notifications, delivered via email, PagerDuty, or webhooks depending on your plan (per Cloudflare's own status page), in addition to the public page itself. Subscribe your on-call rotation *before* an incident, not while triaging one — during a Cloudflare-side incident, this is often the fastest way to confirm the issue isn't in your own configuration.

### Configure Notifications for the events that actually predict trouble

At minimum, configure [Notifications](https://developers.cloudflare.com/notifications/) for:

- WAF/security events crossing an anomalous threshold (a spike in blocked requests can indicate either an attack or a rule misfire against legitimate traffic — both need attention)
- DDoS attack detection and mitigation events
- Origin health check failures / elevated 5xx rates
- Certificate expiration and validation failures

Check the current [notification types reference](https://developers.cloudflare.com/notifications/notification-available/) for the specific catalog available on your plan — it changes over time and availability varies by plan tier.

### Runbook basics for a suspected attack

A minimal incident runbook for a suspected active attack:

1. **Confirm scope.** Check [Cloudflare Status](https://www.cloudflarestatus.com/) first — rule out a Cloudflare-side incident before assuming you're under attack.
2. **Check Security Analytics** in the dashboard for the affected zone to characterize the traffic (volumetric flood vs. targeted application-layer abuse vs. bot traffic).
3. **For an active Layer 7 flood**, enable [Under Attack mode](https://developers.cloudflare.com/waf/tools/security-level/) on the affected zone — it adds an interstitial challenge for all visitors, which is disruptive to legitimate users but appropriate as a temporary measure during a confirmed attack. Turn it off once the attack subsides; it isn't a steady-state setting.
4. **For a targeted pattern** (specific IP ranges, ASN, user-agent, URI), deploy a scoped [WAF custom rule](https://developers.cloudflare.com/waf/custom-rules/) in Block mode rather than the blunt, site-wide Under Attack mode — this contains the blast radius to actual malicious traffic.
5. **Document the emergency change** as it's made — see [Change Management](../govern/change-management.md) for the requirement to back-port any emergency change into Terraform the same day.
6. **Stand down deliberately** — remove or loosen emergency mitigations once traffic normalizes, rather than leaving them in place indefinitely "just in case," which itself becomes undocumented drift.

### Post-incident review

Once the immediate event is resolved, use [Logpush](https://developers.cloudflare.com/logs/logpush/)-exported logs and dashboard analytics to reconstruct the timeline:

- What was the actual attack/failure pattern, and would existing [Edge Security Baseline](edge-security-baseline.md) controls have caught it if tuned differently?
- Did [Notifications](https://developers.cloudflare.com/notifications/) fire in time to matter, or was the team alerted some other way first?
- What emergency changes were made (check [Audit Logs](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)), and have they all been reconciled back into the Terraform-managed baseline or explicitly reverted?
- Does this incident change the answer to any open question in [Policy & Guardrails](../govern/policy-and-guardrails.md) — e.g., should a rule that caught this move from the per-zone exception list into the org-wide baseline?

## Decisions to make

- [ ] Who is subscribed to Cloudflare Status notifications, and through what channel?
- [ ] What Notification types are configured, and does the alert route reach whoever is on call (not just an inbox nobody watches)?
- [ ] Who is authorized to enable Under Attack mode or deploy an emergency WAF rule without going through the standard change process?
- [ ] Is Logpush already configured and flowing to a SIEM/storage target *before* an incident, so logs are available when needed rather than set up reactively?
- [ ] What's the defined "stand down" trigger for reverting emergency mitigations?
- [ ] Is there a recurring post-incident review process, or does it happen only for major events?

## Checklist

- [ ] On-call rotation subscribed to Cloudflare Status
- [ ] Notifications configured for WAF/security events, DDoS activity, origin health, and certificate expiry
- [ ] Written runbook exists for "suspected attack" scenario, covering Under Attack mode and scoped custom rules
- [ ] Logpush configured and flowing before it's needed, not set up during the incident
- [ ] Emergency change process (from [Change Management](../govern/change-management.md)) referenced in the runbook
- [ ] Post-incident review is a standing practice, feeding back into [Policy & Guardrails](../govern/policy-and-guardrails.md)

## Further reading

- [Cloudflare Status](https://www.cloudflarestatus.com/)
- [Cloudflare Notifications](https://developers.cloudflare.com/notifications/)
- [Notification types reference](https://developers.cloudflare.com/notifications/notification-available/)
- [Security level / Under Attack mode](https://developers.cloudflare.com/waf/tools/security-level/)
- [WAF custom rules](https://developers.cloudflare.com/waf/custom-rules/)
- [Logpush](https://developers.cloudflare.com/logs/logpush/)
- [Audit Logs (v2)](https://developers.cloudflare.com/fundamentals/account/account-security/audit-logs/)
- [Security Center](https://developers.cloudflare.com/security-center/)

See also: [Change Management](../govern/change-management.md) for the emergency-change process referenced above, and [Edge Security Baseline](edge-security-baseline.md) for the controls being tuned during post-incident review.
