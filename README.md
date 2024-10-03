# Renovate Config

[![Validate renovate config](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml/badge.svg)](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml)
[![GitHub License](https://img.shields.io/github/license/pplancq/renovate-config)](https://github.com/pplancq/renovate-config?tab=MIT-1-ov-file#readme)

This repository contains the Renovate configuration used to manage dependency updates in our projects.

## Configuration

The configuration is defined in the `default.json` file and includes the following elements:

- **extends**: Uses Renovate's recommended configuration.
- **timezone**: Europe/Paris.
- **schedule**: Updates are scheduled every Monday between midnight and 6 AM.
- **labels**: PRs are labeled with "dependencies".
- **assignees**: PRs are automatically assigned to `pplancq`.
- **prConcurrentLimit**: Limit of 10 concurrent PRs.
- **prHourlyLimit**: Limit of 10 PRs per hour.
- **vulnerabilityAlerts**: enabled and add labels "security".
- **npm**:
  - range strategy bump.
  - minimum release age 3 days.
- **node**:
  - no update engines on package.json.
  - no update peerDependencies on package.json.
- **packageRules**:
  - Grouping Babel packages under "babel packages".
  - Grouping React Query packages under "react-query packages".
  - Grouping TypeScript ESLint packages under "typescript-eslint packages".
  - Grouping Semantic-release packages under "semantic-release packages".
  - Major updates are labeled with "High".

## Usage

To use this configuration in your project, add the `renovate-config` repository as a configuration source in your `renovate.json` file:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>pplancq/renovate-config"]
}
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
