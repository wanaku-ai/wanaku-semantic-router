---
title: CI automation
description: The GitHub Actions workflows of Wanaku Semantic Router, the secrets for publishing, manual releases, and the native architecture image builds.
---

# CI automation

WSR carries the same seven GitHub Actions workflows as [Camel Integration Capability](https://github.com/wanaku-ai/camel-integration-capability/tree/main/.github/workflows).

| Workflow | Trigger | Purpose |
| --- | --- | --- |
| Build Main | Push to `main`, maintenance branches (`0.1.x`), `v*` tags, or manual dispatch | Verify Java 21 builds on Linux AMD64 and ARM64, upload test reports and packaged runtimes, smoke-test and publish containers and manifests. |
| Multiplatform PR Builds | PRs targeting `main` or maintenance branches | Verify Linux AMD64, Linux ARM64, and macOS. Upload test reports; container checks run on Linux. |
| Dependency Review | PRs | Review dependency changes for known vulnerabilities. |
| Markdown Lint | PRs changing Markdown or lint configuration | Check documentation with markdownlint-cli2. |
| early-access | Manual dispatch | Build and smoke-test both Linux architectures, publish containers and manifests, and publish a signed early-access runtime ZIP through JReleaser. |
| release | Manual dispatch | Prepare the Maven release tag and next development version on the selected branch, verify the release, and publish a signed runtime ZIP through JReleaser. |
| release-artifacts | Manual dispatch | Build the Maven release tag and publish containers and manifests for both Linux architectures. |

All builds install the SDK revision recorded in `.github/sdk-revision`. Maven verification runs both unit and packaged runtime integration tests. WSR retains its formatting, manifest assembly, and container smoke checks.

Dependabot checks Maven dependencies weekly, excluding Apache Camel dependencies as in CIC.

## Publishing setup

Configure these repository secrets before running publishing workflows:

- `QUAY_USERNAME` and `QUAY_PASSWORD`: credentials for `quay.io/wanaku/wanaku-semantic-router`.
- `GPG_PUBLIC_KEY`, `GPG_PRIVATE_KEY`, and `MAVEN_GPG_PASSPHRASE`: JReleaser artifact signing keys and passphrase.

GitHub release jobs use the automatically supplied `GITHUB_TOKEN` with `contents: write`. Publishing is restricted to `wanaku-ai/wanaku-semantic-router`. Enable the dependency graph for Dependency Review.

## Manual releases

For early access, dispatch `early-access` from the development branch with `currentDevelopmentVersion` matching the POM version, including `-SNAPSHOT` (for example, `0.3.0-SNAPSHOT`).

For a release, dispatch `release` with a release version without `-SNAPSHOT`, the next development version without `-SNAPSHOT`, and an existing release branch. For example: `0.1.0`, `0.1.1`, and `0.1.x`. The workflow creates `wanaku-semantic-router-0.1.0` and updates the branch to `0.1.1-SNAPSHOT`. WSR currently uses Camel and SDK snapshots, which release preparation explicitly permits.

After the release completes, dispatch `release-artifacts` with the same release version (`0.1.0`) to build the tagged containers. GitHub-token pushes do not automatically trigger another workflow.

The release ZIP contains the application JAR and its `lib/` directory. Extract both into the same directory and run `java -jar wanaku-semantic-router-VERSION.jar` with the desired command.

# Native architecture builds

The CI build follows the Camel Integration Capability container convention. Each native runner builds and verifies Maven artifacts before it builds the image with Podman. `ubuntu-latest` builds `x86_64`. `ubuntu-24.04-arm` builds `aarch64`. Pull request builds run the container checks on both runners. Pull request builds do not log in to Quay or publish images.

The `Build Main` workflow publishes images from `main` and `v*` tags in `wanaku-ai/wanaku-semantic-router`. It uses the `QUAY_USERNAME` and `QUAY_PASSWORD` repository secrets. The registry account must have write access to `quay.io/wanaku/wanaku-semantic-router`.

The workflow publishes an image for each architecture:

| Git reference | x86_64 tag | aarch64 tag | Combined manifest |
| --- | --- | --- | --- |
| `main` | `main-x86_64` | `main-aarch64` | `latest` |
| `v0.1.0` | `v0.1.0-x86_64` | `v0.1.0-aarch64` | `v0.1.0` |

`images.txt` lists the combined image name. `build-manifests.sh` creates and pushes the combined manifest after both native image pushes succeed. The manifest lets the container engine select the host architecture. Use a combined manifest digest when deployment must retain both architectures. Use an architecture image digest when deployment uses one architecture.

## Next steps

- [Build Wanaku Semantic Router](building.md): build the JAR and the container locally.
- [Test Wanaku Semantic Router](testing.md): the checks that the builds run.
