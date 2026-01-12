# Authenticate to Environment Action

[![CI](https://github.com/flxbl-io/auth-environment/actions/workflows/ci.yml/badge.svg)](https://github.com/flxbl-io/auth-environment/actions/workflows/ci.yml)

A GitHub Action that authenticates to a Salesforce environment registered in [SFP Server](https://docs.flxbl.io/sfp-server). This action does **not** acquire locks on environments.

## How it Works

This action uses SFP Server to retrieve and authenticate to an environment that has been registered for your repository. Environment credentials are managed centrally in SFP Server.

## Usage

```yaml
- name: Authenticate to Environment
  id: auth-env
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'dev'
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

- name: Deploy to Environment
  run: sfp deploy -o ${{ steps.auth-env.outputs.alias }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `environment` | Name of the environment to authenticate | **Yes** | - |
| `sfp-server-url` | URL to SFP Server (e.g., `https://your-org.flxbl.io`) | **Yes** | - |
| `sfp-server-token` | SFP Server application token | **Yes** | - |
| `repository` | Repository name (`owner/repo` format) | No | Current repository |
| `alias` | Custom alias for the authenticated org | No | Same as `environment` |
| `auth-type` | Authentication type: `accessToken` (recommended) or `sfdxAuthUrl` | No | `accessToken` |

## Outputs

| Output | Description |
|--------|-------------|
| `alias` | Alias used for the authenticated org |

## Example: Deploy to Multiple Environments

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest
    strategy:
      matrix:
        environment: [dev, sit, uat]

    steps:
      - uses: actions/checkout@v4

      - name: Authenticate to ${{ matrix.environment }}
        id: auth
        uses: flxbl-io/auth-environment@v1
        with:
          environment: ${{ matrix.environment }}
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Deploy
        run: sfp deploy -o ${{ steps.auth.outputs.alias }}
```

## Authentication Types

The `auth-type` input controls how credentials are obtained:

- **`accessToken`** (default, recommended): Uses short-lived access tokens. More secure as tokens expire quickly.
- **`sfdxAuthUrl`**: Uses long-lived refresh tokens via SFDX Auth URL. Useful when you need persistent authentication.

```yaml
- name: Authenticate with sfdxAuthUrl
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'dev'
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
    auth-type: 'sfdxAuthUrl'
```

## Difference from lock-environment

This action (`auth-environment`) only authenticates to an environment without acquiring any locks. Use this when:

- You need read-only access to an environment
- The environment is dedicated and doesn't need locking
- You're running operations that don't require exclusive access

For exclusive access with locking, use [lock-environment](https://github.com/flxbl-io/lock-environment) instead.

## Prerequisites

- SFP Server instance with your repository configured
- Environment registered in SFP Server for your repository
- `sfp` CLI available in your workflow (use `sfops` Docker image)

## License

Copyright 2025 flxbl-io. All rights reserved. See [LICENSE](LICENSE) for details.
