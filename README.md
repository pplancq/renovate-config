# Renovate Config

[![Validate renovate config](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml/badge.svg)](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml)
[![GitHub License](https://img.shields.io/github/license/pplancq/renovate-config)](https://github.com/pplancq/renovate-config?tab=MIT-1-ov-file#readme)

This repository contains the Renovate configuration used to manage dependency updates in our projects.

## Configuration

The main configuration is defined in `default.json` and includes:

- **extends**: `config:recommended`, `:timezone(Europe/Paris)` and the `security`, `npm`, `onboarding`, `github-action` and `terraform` presets.
- **dependencyDashboard**: enabled.
- **schedule**: `before 8am on monday` (Europe/Paris).
- **prConcurrentLimit**: 3 concurrent PRs.
- **prHourlyLimit**: 0 (no hourly limit).
- **labels**: PRs are labeled "dependencies".
- **lockFileMaintenance**: enabled for every manager that supports lock files, monthly (`before 7am on the first day of the month`). Labels: `renovate/lockfile`, `dependencies`.
- **packageRules** (quarantine by update type, via `minimumReleaseAge`):
  - patch: 5 days, label `renovate/patch`.
  - minor: 10 days, label `renovate/minor`.
  - major: 25 days, only 1 open PR at a time (`prConcurrentLimit: 1`), labels `renovate/major` and `breaking-change`.
- **security**: see [Security config](#security-config).
- **npm**:
  - versioning strategy "bump".
  - no update of `engines.node` or `peerDependencies` in package.json.
  - grouped PRs: Babel, TanStack Query, typescript-eslint, shelter-ui, React ecosystem (`react`, `react-dom`, `@types/react`, `@types/react-dom`), Nx tooling (`@nx/*`, `nx`, `@pplancq/nx-release-plugin`), linting tools and configs (`@pplancq/*-config`, Commitlint, Stylelint, ESLint, and Prettier).
  - Node.js and npm: versions declared in `mise.toml` and the `packageManager` key of package.json are grouped in a single PR.
- **terraform**:
  - versioning strategy "bump".
  - providers and modules are grouped in a "Terraform providers" PR.
  - Terraform core version updates are grouped in a "Terraform version" PR.
  - pre-commit hooks are grouped in a "pre-commit hooks" PR.
  - label `tech/devops`.
- **github-action**: actions are pinned by digest (`pinDigests: true`), label `tech/devops`.
- **onboarding**: onboarding PR with the config file `.github/renovate.json`, extending this config.
- **No auto-merge.**

### Security config

The `security` preset (included in the default config) handles vulnerability alerts:

- `osvVulnerabilityAlerts: true` (OSV database, complementing GitHub Dependabot alerts).
- `vulnerabilityAlerts` enabled: Renovate's vulnerability PRs ignore the schedule and the PR limits.
- Labels `renovate/cve` and `security`, with a 3 days minimum release age.

### Ignoring or delaying a dependency

In your project's `renovate.json`:

```json
{
  "extends": ["github>pplancq/renovate-config"],
  "packageRules": [
    { "matchPackageNames": ["some-package"], "enabled": false },
    { "matchPackageNames": ["other-package"], "minimumReleaseAge": "60 days" }
  ]
}
```

### Using the schedule config (legacy)

The default config already applies a schedule, so `schedule` is only kept for projects that still use it. It applies `after 00:00 before 16:00 on monday` (Europe/Paris) and disables the PR limits (`prConcurrentLimit: 0`, `prHourlyLimit: 0`).

```json
{
  "extends": ["github>pplancq/renovate-config", "github>pplancq/renovate-config:schedule"]
}
```

### Terraform config

The `terraform` preset enables and configures Renovate managers for Terraform and pre-commit repositories:

- **terraform**: providers and modules declared in `.tf` / `.tfvars` files.
- **terraform-version**: Terraform core version (`.terraform-version`, `required_version` block, etc.).
- **github-actions**: already handled by the `github-action` preset; actions used in Terraform workflows (e.g. `hashicorp/setup-terraform`, `terraform-linters/setup-tflint`) are pinned by SHA.
- **pre-commit**: hooks declared in `.pre-commit-config.yaml`.

To use only the Terraform preset, add the following extension to your configuration:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>pplancq/renovate-config", "github>pplancq/renovate-config:terraform"]
}
```

> **Note:**
> The `github-action` preset pins GitHub Actions by digest (`pinDigests: true`).
> Terraform-related actions such as `hashicorp/setup-terraform` or `terraform-linters/setup-tflint` will be pinned to a SHA automatically.

## Usage

To use this configuration in your project, add the `renovate-config` repository as a config source in your `renovate.json` file:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>pplancq/renovate-config"]
}
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
