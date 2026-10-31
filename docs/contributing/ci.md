---
title: CI automation
description: The GitHub Actions workflows of Wanaku Semantic Router, the secrets for publishing, manual releases, and the native architecture image builds.
---

# CI automation

WSR carries the same seven GitHub Actions workflows as [Camel Integration Capability](https://github.com/wanaku-ai/camel-integration-capability/tree/main/.github/workflows) (CIC). The workflow files are copies of the CIC files with the project name changed. Keep them that way: when you change a workflow in one repository, make the same change in the other.

| Workflow | Trigger | Purpose |
| --- | --- | --- |
| Build Main | Push to `main`, maintenance branches (`0.1.x`), `v*` tags, or manual dispatch | Build on Linux AMD64 and ARM64, upload test reports, and publish containers and manifests. |
| Multiplatform PR Builds | PRs targeting `main` or maintenance branches | Build and test on Linux and macOS, and upload test reports. |
| Dependency Review | PRs | Review dependency changes for known vulnerabilities. |
| Markdown Lint | PRs changing Markdown or lint configuration | Check documentation with markdownlint-cli2. |
| early-access | Manual dispatch | Build both Linux architectures, publish containers and manifests, and publish a signed early-access runtime ZIP through JReleaser. |
| release | Manual dispatch | Prepare the Maven release tag and next development version on the selected branch, build the release, and publish a signed runtime ZIP through JReleaser. |
| release-artifacts | Manual dispatch | Build the Maven release tag and publish containers and manifests for both Linux architectures. |

The build, PR, and early-access workflows install the `wanaku-capabilities-java-sdk` branch that has the same name as the WSR branch, or `main` if no such branch exists. The `pom.xml` file selects the SDK version that the build uses.

Dependabot checks Maven dependencies weekly, excluding Apache Camel dependencies as in CIC.

## Differences from CIC

These differences come from how WSR is packaged:

- WSR ships a thin JAR and a `lib/` directory, not a single JAR with dependencies. The `release` and `early-access` workflows put both in a ZIP file, and JReleaser publishes it as a `JAVA_BINARY` distribution. CIC publishes its JAR as a `SINGLE_JAR` distribution.
- PR builds do not run on Windows.

## Publishing setup

Configure these repository secrets before running publishing workflows:

- `QUAY_USERNAME` and `QUAY_PASSWORD`: credentials for the container registry. The workflows build and push containers only when both secrets are set.
- `GPG_PUBLIC_KEY`, `GPG_PRIVATE_KEY`, and `MAVEN_GPG_PASSPHRASE`: JReleaser artifact signing keys and passphrase.

The `CONTAINER_REGISTRY` and `CONTAINER_GROUP` repository variables select the registry and the image group. They default to `quay.io` and `wanaku`. A fork can set them to publish to its own registry.

GitHub release jobs use the automatically supplied `GITHUB_TOKEN` with `contents: write`. Enable the dependency graph for Dependency Review.

## Manual releases

For early access, dispatch `early-access` from the development branch with `currentDevelopmentVersion` matching the POM version, including `-SNAPSHOT` (for example, `0.3.0-SNAPSHOT`).

For a release, dispatch `release` with the release version, the next development version without `-SNAPSHOT`, and an existing release branch. For example: `0.3.0`, `0.3.1`, and `0.3.x`. The workflow creates the Maven tag `wanaku-semantic-router-0.3.0`, updates the branch to `0.3.1-SNAPSHOT`, and publishes the GitHub release `v0.3.0`.

After the release completes, dispatch `release-artifacts` with the same release version (`0.3.0`) to build the tagged containers. GitHub-token pushes do not automatically trigger another workflow.

The release ZIP contains the application JAR and its `lib/` directory. Extract both into the same directory and run `java -jar wanaku-semantic-router-VERSION.jar` with the desired command.

# Native architecture builds

Each native runner builds the Maven artifacts before it builds the image with Podman. `ubuntu-latest` builds `x86_64`. `ubuntu-24.04-arm` builds `aarch64`. Pull request builds do not build or publish images.

The workflow publishes an image for each architecture:

| Git reference or release version | x86_64 tag | aarch64 tag | Combined manifest |
| --- | --- | --- | --- |
| `main` | `main-x86_64` | `main-aarch64` | `latest` |
| `v0.3.0` tag | `v0.3.0-x86_64` | `v0.3.0-aarch64` | `v0.3.0` |
| `0.3.0` (`release-artifacts`) | `0.3.0-x86_64` | `0.3.0-aarch64` | `0.3.0` |

`images.txt` lists the combined image name. `build-manifests.sh` creates and pushes the combined manifest after both native image pushes succeed. The manifest lets the container engine select the host architecture. Use a combined manifest digest when deployment must retain both architectures. Use an architecture image digest when deployment uses one architecture.

To check a locally built image, run `./scripts/container-smoke.sh` as described in [Run WSR in a container](../operate/container.md).

## Next steps

- [Build Wanaku Semantic Router](building.md): build the JAR and the container locally.
- [Test Wanaku Semantic Router](testing.md): the checks that the builds run.
