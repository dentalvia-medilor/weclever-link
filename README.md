# WeLink releases

This public repository hosts WeLink installers and Electron update metadata.
The application source remains in the private GitLab project.

The `Release WeLink` GitHub Actions workflow builds an explicitly selected
GitLab commit for Windows and macOS, validates the complete artifact set, and
publishes a single GitHub release.

## Release mapping

| Environment | GitLab ref | Version | GitHub release |
| --- | --- | --- | --- |
| Development | `develop` | `x.y.z-dev` | Prerelease |
| Staging | `release/*-staging` | `x.y.z-staging` | Prerelease |
| Production | `master` | `x.y.z` | Release |

The workflow is manual and refuses to replace an existing release.

Windows code signing is intentionally deferred. macOS publication requires a
Developer ID certificate and successful Apple notarization.

## Required GitHub Actions secrets

- `GITLAB_DEPLOY_USER`
- `GITLAB_READ_TOKEN`
- `MAC_CERTIFICATE_P12_BASE64`
- `MAC_CERTIFICATE_PASSWORD`
- `APPLE_API_KEY_P8_BASE64`
- `APPLE_API_KEY_ID`
- `APPLE_API_ISSUER_ID`
