# Crypto-Voucher

Building the platform behind crypto voucher payments — secure, observable
services shipped on a shared, automated delivery pipeline.

## What we do

We run a set of focused microservices that handle risk scoring, payments and
the surrounding infrastructure. Everything is containerised, deployed to
Kubernetes (EKS) with Helm, and gated by automated security and quality
checks before it reaches production.

## How we build

- **CI/CD** — reusable GitHub Actions workflows shared across every repo
  (lint + test, SAST, dependency & image scanning, build → push → deploy).
- **Runtime** — Docker images on a shared `microservice` Helm chart,
  running on AWS EKS across `stage` and `prod`.
- **Security** — Semgrep (SAST), Trivy (filesystem / config / image scans)
  and CycloneDX SBOMs tracked in Dependency-Track.
- **Infrastructure** — managed as code with Terraform.

## Key repositories

- [`.github`](https://github.com/Crypto-Voucher/.github) — org-wide defaults: reusable workflows and PR templates
- [`github_iac`](https://github.com/Crypto-Voucher/github_iac) — Terraform for repos, branch protection, teams and secrets
- [`infra_charts`](https://github.com/Crypto-Voucher/infra_charts) — shared Helm charts (the `microservice` chart)

## Contributing

Work is tracked in Jira — branches follow `CV-XXXX` and pull requests use the
org PR template. See each repo's `README` for setup and the
[`.github`](https://github.com/Crypto-Voucher/.github) repo for the shared
CI/CD conventions.