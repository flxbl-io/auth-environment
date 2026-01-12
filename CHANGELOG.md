# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 1.0.0 (2026-01-12)


### Features

* add manual trigger to CI workflow ([a4ce572](https://github.com/flxbl-io/auth-environment/commit/a4ce5727175ab123be891dc219200a8d539946eb))
* initial release of auth-environment action ([26d1572](https://github.com/flxbl-io/auth-environment/commit/26d15723d22691ac2b120d508af3ac9b0523a6c5))


### Bug Fixes

* make alias and auth-type optional flags ([555437f](https://github.com/flxbl-io/auth-environment/commit/555437f15db64d36dfb23dc36ff225470abbf85b))
* remove auth-type input ([895a1b9](https://github.com/flxbl-io/auth-environment/commit/895a1b91690f19df671ca7bf819d2ec7ad679498))
* use correct sfp server environment login command ([4125a49](https://github.com/flxbl-io/auth-environment/commit/4125a499e47febeb6f34bc5213c0ed5b3b2d0453))
* use GHA_TOKEN for release-please ([b7d38fa](https://github.com/flxbl-io/auth-environment/commit/b7d38fac281caa57752d6d3033779174d6c20d87))

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
