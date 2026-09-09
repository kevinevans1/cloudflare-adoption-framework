--8<-- "_snippets/disclaimer.md"

# CI/CD & Infrastructure Delivery

Shipping application code and shipping infrastructure configuration (DNS, WAF, Zero Trust policy) are related but distinct pipelines on Cloudflare. Conflating them causes problems: application deploys should be fast and frequent, while infrastructure changes to production DNS or security policy usually warrant more deliberate review.

This page covers both — Wrangler and Workers Builds for application delivery, and pushing Terraform/Pulumi-managed configuration through a pipeline for infrastructure delivery — plus how environments and preview deployments fit into each.

## Key Cloudflare capabilities

| Capability | What it does | Reference |
|---|---|---|
| Wrangler CLI | Cloudflare's official CLI for developing, testing, and deploying Workers (and Pages) | [Wrangler overview](https://developers.cloudflare.com/workers/wrangler/) |
| Workers Builds | Native CI/CD: connect a GitHub or GitLab repo and Cloudflare builds and deploys automatically on push | [CI/CD overview](https://developers.cloudflare.com/workers/ci-cd/), [Git integration](https://developers.cloudflare.com/workers/ci-cd/builds/git-integration/) |
| `wrangler-action` | Official GitHub Action for deploying via Wrangler from your own GitHub Actions workflow | [GitHub Actions](https://developers.cloudflare.com/workers/ci-cd/external-cicd/github-actions/) |
| Preview URLs | A unique, shareable URL generated automatically for every uploaded Worker version | [Preview URLs](https://developers.cloudflare.com/workers/versions-and-deployments/preview-urls/) |
| Wrangler environments | Named environment blocks (`[env.staging]`, `[env.production]`) that deploy as distinct Workers | [Environments](https://developers.cloudflare.com/workers/wrangler/environments/) |
| Deploy Hooks | A unique URL that triggers a manual build for one branch via HTTP POST — useful for headless CMS-triggered rebuilds | [Deploy Hooks](https://developers.cloudflare.com/workers/ci-cd/builds/deploy-hooks/) |

## Application delivery: Wrangler, Workers Builds, and GitHub Actions

**Workers Builds is the lowest-friction path for most teams.** Connecting a GitHub or GitLab repository directly (individual or organization accounts; self-hosted Git instances aren't currently supported) gets automatic build-and-deploy on every push to a selected branch, plus build status surfaced back as PR comments, check runs, or commit statuses — no pipeline YAML to maintain. Start here unless you have a specific reason for a self-managed pipeline.

**Reach for `wrangler-action` in your own GitHub Actions workflow when you need more control** — running tests before deploy, deploying multiple Workers from a monorepo, or integrating the deploy step into a larger pipeline that also handles Terraform/Pulumi (see below). A minimal pattern:

```yaml
name: Deploy Worker
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: cloudflare/wrangler-action@v4
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
```

Authenticate with a scoped API token stored as a repository or environment secret — never the legacy Global API key — consistent with the guidance in [Identity & Access Control](../foundation/identity-and-access.md).

**Use preview URLs and Wrangler environments for staging, not ad hoc naming.** Every uploaded Worker version automatically gets a unique preview URL — useful for reviewing a specific version before it takes production traffic, with zero extra configuration. For a persistent staging environment (as opposed to a per-commit preview), define a named environment in your Wrangler configuration:

```jsonc
// wrangler.jsonc
{
  "name": "checkout-api",
  "env": {
    "staging": {
      "vars": { "API_HOST": "https://staging.example.com" }
    },
    "production": {
      "vars": { "API_HOST": "https://api.example.com" }
    }
  }
}
```

Each named environment deploys as a *separate* Worker (`checkout-api-staging`, `checkout-api-production`) — bindings and variables don't inherit automatically between them, so each environment block needs its own complete configuration. This mechanical detail should inform your [naming convention](../foundation/naming-and-tagging.md) from the start.

## Infrastructure delivery: pushing Terraform/Pulumi through a pipeline

Application code and infrastructure configuration (DNS records, WAF rules, Zero Trust policy) benefit from different pipeline rigor:

| Change type | Pipeline behavior |
|---|---|
| Application code (Worker deploy) | Fast, frequent, often automatic on merge to main via Workers Builds or `wrangler-action` |
| Non-production infrastructure (staging zone, dev Zero Trust policy) | Automatic `terraform apply` on merge, low ceremony |
| Production DNS / WAF / Zero Trust policy | `terraform plan` posted as a PR comment for review; `apply` gated behind manual approval |

Run `terraform plan` (or the Pulumi equivalent) in CI on every pull request touching infrastructure code, post the output as a PR comment, and require review before merge — the same rigor as any other production infrastructure change. Gate `apply` on production-scoped state behind manual approval even if non-production applies automatically; a DNS or Access policy mistake in production has a different blast radius than an unexpected staging Worker version. See [Infrastructure as Code](../foundation/infrastructure-as-code.md) for module structure and drift-detection guidance this pipeline should enforce.

## Environments across the stack

| Environment | Worker deployment | Infrastructure (Terraform/Pulumi) state |
|---|---|---|
| Preview (per-branch/PR) | Automatic preview URL per version | N/A — application-only |
| Staging | `env.staging` Worker, auto-deployed on merge to a staging branch | Separate Terraform workspace/state, auto-applied |
| Production | `env.production` Worker, deployed on merge to main (Workers Builds) or a tagged release | Separate workspace/state, plan-reviewed and manually approved before apply |

## Checklist

- [ ] Workers Builds connected for straightforward auto-deploy needs, or `wrangler-action` used where custom pipeline logic is required
- [ ] CI authenticates with a scoped API token stored as a secret, never a Global API key
- [ ] Wrangler environments defined for staging/production, with naming that anticipates the `<name>-<environment>` Worker naming behavior
- [ ] Preview URLs used for reviewing individual versions before promoting to a named environment
- [ ] Infrastructure changes run `plan` in CI with output posted for review before any `apply`
- [ ] Production infrastructure `apply` gated behind manual approval; non-production may auto-apply
- [ ] Deploy Hooks configured for any workflow needing externally-triggered rebuilds (e.g., CMS content updates)

## Further reading

- [Wrangler overview](https://developers.cloudflare.com/workers/wrangler/)
- [CI/CD overview](https://developers.cloudflare.com/workers/ci-cd/)
- [Git integration (Workers Builds)](https://developers.cloudflare.com/workers/ci-cd/builds/git-integration/)
- [GitHub Actions](https://developers.cloudflare.com/workers/ci-cd/external-cicd/github-actions/)
- [Preview URLs](https://developers.cloudflare.com/workers/versions-and-deployments/preview-urls/)
- [Wrangler environments](https://developers.cloudflare.com/workers/wrangler/environments/)
- [Deploy Hooks](https://developers.cloudflare.com/workers/ci-cd/builds/deploy-hooks/)
