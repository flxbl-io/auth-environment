# Authenticate to Environment Action

[![CI](https://github.com/flxbl-io/auth-environment/actions/workflows/ci.yml/badge.svg)](https://github.com/flxbl-io/auth-environment/actions/workflows/ci.yml)

A GitHub Action that authenticates to a Salesforce environment using [SFP Server](https://docs.flxbl.io/sfp-server) with automatic fallback to sandbox/scratch org login. This action does **not** acquire locks on environments.

## Usage

### With SFP Server (Recommended)

```yaml
- name: Authenticate to Environment
  id: auth-env
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'dev'
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

- name: Deploy to Environment
  run: |
    sfp deploy -o ${{ steps.auth-env.outputs.alias }}
    echo "Authenticated as: ${{ steps.auth-env.outputs.username }}"
```

### With Native Auth URL (Fallback)

```yaml
- name: Authenticate to Environment
  id: auth-env
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'dev'
    env-auth-url: ${{ secrets.DEV_SFDX_AUTH_URL }}
```

### With DevHub Sandbox Login

```yaml
- name: Authenticate to DevHub
  uses: flxbl-io/auth-devhub@v1
  with:
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

- name: Authenticate to Sandbox
  id: auth-env
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'mysandbox'
    devhub-alias: 'devhub'
```

### Hybrid (SFP Server with Fallback)

```yaml
- name: Authenticate to Environment
  id: auth-env
  uses: flxbl-io/auth-environment@v1
  with:
    environment: 'staging'
    sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
    sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}
    env-auth-url: ${{ secrets.STAGING_SFDX_AUTH_URL }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `environment` | Name of the environment to authenticate | **Yes** | - |
| `sfp-server-url` | URL to SFP Server (e.g., `https://your-org.flxbl.io`) | No | - |
| `sfp-server-token` | SFP Server application token | No | - |
| `env-auth-url` | SFDX Auth URL for fallback authentication | No | - |
| `devhub-alias` | Alias of authenticated DevHub for sandbox login | No | `devhub` |
| `repository` | Repository name (`owner/repo` format) | No | Current repository |
| `alias` | Custom alias for the authenticated org | No | Same as `environment` |

> **Note**: At least one authentication method must be available:
> - SFP Server credentials (`sfp-server-url` + `sfp-server-token`), OR
> - Native auth URL (`env-auth-url`), OR
> - Authenticated DevHub (`devhub-alias`) for sandbox login

## Outputs

| Output | Description |
|--------|-------------|
| `alias` | Alias used for the authenticated org |
| `org-id` | Org ID of the environment |
| `username` | Username of the authenticated user |
| `instance-url` | Instance URL of the environment |
| `access-token` | Access token (use with caution) |
| `auth-method` | Method used: `sfp-server`, `server-sandbox`, `server-scratch`, or `native` |

## Authentication Flow

```
+-------------------------------------------------------------+
|                    auth-environment                         |
+-------------------------------------------------------------+
|                                                             |
|  1. Check if SFP Server credentials provided                |
|     +-- Yes --> Try SFP Server environment get              |
|     |           +-- Success --> Done                        |
|     |           +-- Not Found --> Try server fallback       |
|     |                            +-- Scratch (has @) --> 2a |
|     |                            +-- Sandbox --> 2b         |
|     |                            +-- Both fail --> 3        |
|     +-- No --> Go to step 3                                 |
|                                                             |
|  2a. Server scratch login                                   |
|      +-- Success --> Done                                   |
|      +-- Failure --> Continue to 2b or 3                    |
|                                                             |
|  2b. Server sandbox login                                   |
|      +-- Success --> Done                                   |
|      +-- Failure --> Go to step 3                           |
|                                                             |
|  3. Check if env-auth-url provided                          |
|     +-- Yes --> Authenticate with auth URL --> Done         |
|     +-- No --> Go to step 4                                 |
|                                                             |
|  4. Try DevHub sandbox login                                |
|     +-- Success --> Done                                    |
|     +-- Failure --> Error                                   |
|                                                             |
+-------------------------------------------------------------+
```

## Prerequisites

- **For SFP Server**: Environment registered in SFP Server with valid credentials
- **For Native**: Valid SFDX Auth URL for the environment
- **For DevHub Sandbox**: Authenticated DevHub org
- **Runtime**: `sfp` CLI must be available (use `sfops` Docker image)

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
        run: |
          sfp deploy -o ${{ steps.auth.outputs.alias }}
```

## Difference from lock-environment

This action (`auth-environment`) only authenticates to an environment without acquiring any locks. Use this when:

- You need read-only access to an environment
- The environment is dedicated and doesn't need locking
- You're running operations that don't require exclusive access

For exclusive access with locking, use [lock-environment](https://github.com/flxbl-io/lock-environment) instead.

## License

Copyright 2025 flxbl-io. All rights reserved. See [LICENSE](LICENSE) for details.

## Support

- [Documentation](https://docs.flxbl.io)
- [Issues](https://github.com/flxbl-io/auth-environment/issues)
- [flxbl.io](https://flxbl.io)
