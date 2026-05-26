# Crypto-Voucher / .github

Org-wide defaults for the **Crypto-Voucher** GitHub organization. GitHub
automatically picks up the contents of this repo as fallbacks for every
other repo in the org.

## What lives here

### PR templates

| File                                  | When                                         |
|---------------------------------------|----------------------------------------------|
| `pull_request_template.md`            | **Default** — auto-applied to every new PR. Use for Jira-linked work. |
| `PULL_REQUEST_TEMPLATE/chore.md`      | Hotfixes, dependency bumps, docs, refactor — anything without a Jira task. Pick via URL param. |

To open a PR with the `chore` template, append `?template=chore.md` to the
new-PR URL, e.g.:

```
https://github.com/Crypto-Voucher/<repo>/compare/master...feature-branch?template=chore.md
```

A repo can override either by adding its own `.github/pull_request_template.md`
or `.github/PULL_REQUEST_TEMPLATE/*.md`.

### `workflows/*.yml`

Reusable workflows (declared with `on: workflow_call`) that service repos
invoke from their own thin wrappers:

| File                   | Purpose                                                          |
|------------------------|------------------------------------------------------------------|
| `pr-compliance.yml`    | Branch naming (`CV-\d{4,5}`) + PR description sections check     |
| `code-quality.yml`     | Node lint + unit tests (`.nvmrc` for version, npm by default)    |
| `sast.yml`             | Semgrep static analysis with the org's preset rule bundles       |
| `security-build.yml`   | Trivy FS + config + image scan, CycloneDX SBOM upload to Dependency-Track |
| `deploy.yml`           | Build → push to ECR+GHCR → `helm upgrade` on EKS → Telegram notify |

Inputs and required secrets/variables for each workflow are documented in
header comments inside the file. The corresponding service-repo wrapper
templates live in
[`Crypto-Voucher/github_iac`](https://github.com/Crypto-Voucher/github_iac/tree/master/templates/service-repo).

## Versioning

Service repos pin reusable workflows to a major tag:

```yaml
uses: Crypto-Voucher/.github/.github/workflows/ci.yml@v1
```

Tagging convention:

- `vX.Y.Z` — immutable specific release. Created on every push to `master`
  that changes a workflow.
- `vX` — mutable, points at the most recent `vX.Y.Z`. Service repos use
  this so they get bug fixes for free without re-pinning.

### Releasing a patch / non-breaking change

```bash
git tag v1.0.1
git tag -f v1 v1.0.1
git push origin v1.0.1
git push origin v1 --force
```

### Releasing a breaking change

```bash
git tag v2.0.0
git tag v2
git push origin v2.0.0 v2
# v1 stays on the last v1.x.y — existing callers keep working until they migrate
```

## Editing workflows

PRs to this repo go through the standard org gate (PR + admin review). Once
merged, **tag immediately** — without a tag bump, no service consumes the
change.

When adding a new input or changing defaults:

- Non-breaking (new optional input, default value tweak that doesn't break
  existing callers) → bump minor (`v1.1.0`), move `v1`.
- Breaking (rename input, remove input, change required input semantics) →
  bump major (`v2.0.0`), do not move `v1`. Update consumer wrapper
  templates in `github_iac/templates/service-repo/` to point at `@v2`.

## Related repos

- **[github_iac](https://github.com/Crypto-Voucher/github_iac)** — Terraform that
  manages this repo's existence, branch protection, teams, and the
  `protected_repos` allowlist of services pinned to these workflows.
- **[infra_charts](https://github.com/Crypto-Voucher/infra_charts)** —
  Helm `microservice` chart that `deploy.yml` pulls from
  `oci://<ecr>/charts/microservice`.
