# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2025-01-10

### Added

- Initial release as standalone Marketplace action
- Authenticate to environments via SFP Server
- Automatic fallback to server-based sandbox/scratch login
- Native fallback using SFDX Auth URL
- DevHub-based sandbox login fallback
- Rich output including org details (username, org ID, instance URL, access token)
- Aligned header with sfp-pro CLI style

### Features

- **SFP Server Authentication**: Query registered environments from SFP Server
- **Server Fallback**: Automatically try `sfp server sandbox login` or `sfp server scratch login`
- **Native Fallback**: Use SFDX Auth URL when SFP Server is unavailable
- **DevHub Sandbox Login**: Authenticate to sandboxes via authenticated DevHub
- **Detailed Outputs**: org-id, username, instance-url, access-token, auth-method
- **No Locking**: Authentication-only, no environment locks acquired

[Unreleased]: https://github.com/flxbl-io/auth-environment/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/flxbl-io/auth-environment/releases/tag/v1.0.0
