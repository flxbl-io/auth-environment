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

- name: Fetch Org Info
  run: sf org display -o ${{ steps.auth-env.outputs.alias }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `environment` | Name of the environment to authenticate | **Yes** | - |
| `sfp-server-url` | URL to SFP Server (e.g., `https://your-org.flxbl.io`) | **Yes** | - |
| `sfp-server-token` | SFP Server application token | **Yes** | - |
| `repository` | Repository name (`owner/repo` format) | No | Current repository |
| `alias` | Custom alias for the authenticated org | No | Same as `environment` |

## Outputs

| Output | Description |
|--------|-------------|
| `alias` | Alias used for the authenticated org |

## Example: Query Data from Environment

```yaml
jobs:
  query:
    runs-on: ubuntu-latest
    container: ghcr.io/flxbl-io/sfops:latest

    steps:
      - uses: actions/checkout@v4

      - name: Authenticate to production
        id: auth
        uses: flxbl-io/auth-environment@v1
        with:
          environment: 'production'
          sfp-server-url: ${{ secrets.SFP_SERVER_URL }}
          sfp-server-token: ${{ secrets.SFP_SERVER_TOKEN }}

      - name: Query records
        run: sf data query -q "SELECT Id, Name FROM Account LIMIT 10" -o ${{ steps.auth.outputs.alias }}
```

## Difference from lock-environment

This action (`auth-environment`) only authenticates to an environment without acquiring any locks. Use this when:

- You need read-only access to an environment
- The environment is dedicated and doesn't need locking
- You're running operations that don't require exclusive access

**For installing packages or making changes to an environment, use [lock-environment](https://github.com/flxbl-io/lock-environment) instead** to ensure exclusive access.

## Prerequisites

- SFP Server instance with your repository configured
- Environment registered in SFP Server for your repository
- `sfp` CLI available in your workflow (use `sfops` Docker image)

## License

Copyright 2025 flxbl-io. All rights reserved. See [LICENSE](LICENSE) for details.
