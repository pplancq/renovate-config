# Renovate Config

[![Validate renovate config](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml/badge.svg)](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml)
[![GitHub License](https://img.shields.io/github/license/pplancq/renovate-config)](https://github.com/pplancq/renovate-config?tab=MIT-1-ov-file#readme)

This repository contains the Renovate configuration used to manage dependency updates in our projects.

## Configuration

The main configuration is defined in `default.json` and includes:

- **extends**: `config:recommended`, `:timezone(Europe/Paris)` and the `security`, `npm`, `onboarding`, `github-action` and `terraform` presets.
- **dependencyDashboard**: enabled.
- **schedule**: `* 0-7 * * 1` (Monday from midnight to 8am, Europe/Paris).
- **prConcurrentLimit**: 3 concurrent PRs.
- **prHourlyLimit**: 0 (no hourly limit).
- **labels**: PRs are labeled "dependencies".
- **lockFileMaintenance**: enabled for every manager that supports lock files and follows the global schedule. Labels: `renovate/lockfile`, `dependencies`.
- **packageRules** (quarantine by update type, via `minimumReleaseAge`):
  - patch: 5 days, label `renovate/patch`.
  - minor: 10 days, label `renovate/minor`.
  - major: 25 days, only 1 open PR at a time (`prConcurrentLimit: 1`), labels `renovate/major` and `breaking-change`.
- **security**: see [Security config](#security-config).
- **npm**:
  - versioning strategy "bump".
  - no update of `engines.node` or `peerDependencies` in package.json.
  - grouped PRs: Babel, TanStack Query, typescript-eslint, shelter-ui, React ecosystem, Nx tooling, and development tooling. See [npm dependency groups](#npm-dependency-groups).
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

### npm dependency groups

The `npm` preset groups related dependencies into PRs by ecosystem or development responsibility:

| Group                     | Packages                                                                                                                                             |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Babel                     | `@babel/*`                                                                                                                                           |
| TanStack Query            | Packages sourced from `https://github.com/TanStack/query`                                                                                            |
| typescript-eslint         | `@typescript-eslint/*`                                                                                                                               |
| shelter-ui                | Packages sourced from `https://github.com/pplancq/shelter-ui`                                                                                        |
| React ecosystem           | `react`, `react-dom`, `@types/react`, `@types/react-dom`                                                                                             |
| Nx tooling                | `nx`, `@nx/*`, `@pplancq/nx-release-plugin`                                                                                                          |
| Linting tools and configs | `eslint`, `stylelint`, `prettier`, `@commitlint/cli`, and `@pplancq/{eslint,stylelint,prettier,commitlint}-config`                                   |
| Unit testing tools        | `vitest`, `vitest-*`, `@vitest/*` (except `@vitest/eslint` and `@vitest/eslint-*`), `@testing-library/*`, `jsdom`, `sass-true`                       |
| E2E testing tools         | `playwright`, `playwright-core`, `@playwright/*`, `@bdellegrazie/playwright-sonar-reporter`, `monocart-coverage-reports`                             |
| Mocking tools             | `msw`, `@msw/*` (including `@msw/playwright`), `fake-indexeddb`                                                                                      |
| Rstack build tooling      | `@rsbuild/*`, `@rslib/*`, `rsbuild-plugin-*`                                                                                                         |
| Vite tooling              | `vite`, `@vitejs/*`, `vite-plugin-*`                                                                                                                 |
| Storybook tooling         | `storybook`, `storybook-*` (including `storybook-react-rsbuild`), `@storybook/*`, `eslint-plugin-storybook`, `react-docgen-typescript`, `remark-gfm` |
| Visual testing tools      | `chromatic`, `@chromatic-com/storybook`                                                                                                              |
| TypeScript tooling        | `typescript`, `tsc-files`                                                                                                                            |
| Git hooks tooling         | `husky`, `lint-staged`                                                                                                                               |

Groups match package names or sources, not only `devDependencies`: tooling published in a configuration package's `dependencies` is also covered. Updates to `peerDependencies` remain disabled.

The linting group intentionally pairs linters and the four shared `@pplancq` configs without pulling in their plugins, parsers, or third-party configs. For example, `@vitest/eslint-plugin` remains independent, while `eslint-plugin-storybook` belongs to Storybook and Rsbuild lint integrations belong to Rstack. The existing typescript-eslint group remains separate.

`react-docgen-typescript` and `remark-gfm` are grouped with Storybook because these projects use them exclusively for documentation. Chromatic has its own visual testing group.

`@types/node`, `sass-embedded`, `postcss-discard-duplicates`, `concurrently`, and `nodemon` remain independent. React types stay in the React ecosystem group.

### Security config

The `security` preset (included in the default config) handles vulnerability alerts:

- `osvVulnerabilityAlerts: true` (OSV database, complementing GitHub Dependabot alerts).
- `vulnerabilityAlerts` enabled: Renovate's vulnerability PRs ignore the schedule and the PR limits, with a 3 days minimum release age.
- Labels `renovate/cve` and `security`.

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
