# Renovate Config

[![Validate renovate config](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml/badge.svg)](https://github.com/pplancq/renovate-config/actions/workflows/validate-config.yml)
[![GitHub License](https://img.shields.io/github/license/pplancq/renovate-config)](https://github.com/pplancq/renovate-config?tab=MIT-1-ov-file#readme)

This repository contains the Renovate configuration used to manage dependency updates in our projects.

## Configuration

The main configuration is defined in `default.json` and includes:

- **extends**: Uses Renovate's recommended config.
- **timezone**: Europe/Paris.
- **labels**: PRs are labeled with "dependencies".
- **prConcurrentLimit**: Limit of 2 concurrent PRs.
- **prHourlyLimit**: Limit of 2 PRs per hour.
- **vulnerabilityAlerts**: enabled and adds the "security" label.
- **npm**:
  - versioning strategy "bump".
  - minimum release age: 3 days.
- **node**:
  - no update of engines in package.json.
  - no update of peerDependencies in package.json.
- **packageRules**:
  - Major updates are labeled "High".

> **Note:**  
> The default configuration **does not set any schedule**.  
> If you want to schedule updates (e.g., only on Monday morning), use the dedicated `schedule` config.

### Using the schedule config

To apply a schedule for updates, add the following extension to your configuration:

```json
{
  "extends": ["github>pplancq/renovate-config", "github>pplancq/renovate-config:schedule"]
}
```

The `schedule` config applies the following window: `after 00:00 before 16:00 on monday` (Europe/Paris timezone).

> **Note:**  
> The `schedule` config does **not** limit the number of PRs:
>
> - `prConcurrentLimit: 0` (no limit on concurrent PRs)
> - `prHourlyLimit: 0` (no limit on PRs per hour)

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
