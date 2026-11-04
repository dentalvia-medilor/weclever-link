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

The workflow is manual and refuses to replace an incomplete or inconsistent
existing release. A safe rerun reuses a complete release only after verifying
its GitLab source commit and exact artifact manifest.

GitHub releases are the source of truth for the next version. Publication never
modifies the selected GitLab source branch.

Windows code signing is intentionally deferred. macOS publication requires a
Developer ID certificate and successful Apple notarization.

## Publish a release

1. Open **Actions > Release WeLink > Run workflow** in this repository.
2. Keep the workflow branch set to `main`.
3. Select the target environment and its matching GitLab ref:
   - `dev` with `develop`;
   - `staging` with a `release/*-staging` branch;
   - `prod` with `master`.
4. Leave **version** empty for the normal patch increment, or enter a higher
   semantic version such as `2.0.0`. Do not include `v`, `-dev`, or `-staging`.
5. Run the workflow and wait for the `validate`, `build-windows`, `build-macos`,
   and `publish` jobs to succeed.
6. Open the resulting GitHub release and verify that it is not a draft and
   contains 12 assets: the Windows installer and blockmap, two macOS
   architectures in DMG and ZIP formats with their blockmaps, and both update
   metadata files.

The workflow resolves the exact GitLab commit before starting the builds, so
Windows and macOS are always produced from the same source revision. The
release notes record that commit for traceability.

## Versioning

When **version** is empty, the workflow compares the version stored in the
selected GitLab ref with the latest matching GitHub releases, then increments
the highest patch version it finds. It automatically appends `-dev` or
`-staging` when required.

Do not update `electron-builder.dev.json`, `electron-builder.staging.json`, or
`package.json` merely to synchronize a version after publication. GitHub
releases are authoritative and protected GitLab branches are never modified by
this workflow.

## Retry and troubleshooting

- If a build or publication job fails before the GitHub release is created,
  use **Re-run failed jobs** on the same workflow run.
- If the release already exists and contains the complete expected assets, no
  retry is needed: the publication is complete.
- Never delete or replace a complete release just to rerun the workflow.
- An existing incomplete or inconsistent release is rejected intentionally.
  Inspect the failed run and the release assets before making any manual
  change.
- A failure in `validate` usually means the environment and GitLab ref do not
  match, the requested version is not higher than the current one, or GitLab
  read credentials are unavailable.
- A macOS failure should be investigated in the signing, notarization, or
  signature verification step. Do not publish the Windows artifacts alone.

The workflow must finish green. There is no post-release GitLab synchronization
job and no GitLab write credential is required.

## Required GitHub Actions secrets

- `GITLAB_DEPLOY_USER`
- `GITLAB_READ_TOKEN`
- `MAC_CERTIFICATE_P12_BASE64`
- `MAC_CERTIFICATE_PASSWORD`
- `APPLE_API_KEY_P8_BASE64`
- `APPLE_API_KEY_ID`
- `APPLE_API_ISSUER_ID`
