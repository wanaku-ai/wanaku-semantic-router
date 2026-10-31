---
title: Run WSR in a container
description: Build the Wanaku Semantic Router (WSR) container image, run it with Podman, check the image with the smoke script, and find the published image tags.
---

# Run WSR in a container

This page describes how to build the WSR container image, run it, and check it.

Build the container after `mvn verify` succeeds. The Dockerfile copies the packaged JAR and `target/lib/`. It does not run a second Maven build.

```sh
podman build -t wanaku-semantic-router:local .
podman run --rm --network wanaku-private \
  --env TYPESAFE_API_KEY --env TYPESAFE_MODEL \
  --env WSR_SEMANTIC_ROUTE=support-route \
  --env WSR_BARN_URL=http://barn:8180 \
  --env WSR_REGISTRATION_URL=http://wanaku:8080 \
  --env WSR_MCP_ADDRESS=http://wsr:8090/mcp \
  --env WSR_BIND=0.0.0.0 \
  wanaku-semantic-router:local runtime
```

Set `--bind 0.0.0.0` for container network access. Set the advertised MCP address to the container service name. Use separate processes for runtime and preview. Pin the built image digest for deployment. Preserve the complete JAR distribution to retain the resolved snapshot artifacts.

The image creates the `wsr` user with UID 10001 and root group membership. Its writable directories grant the same access to that group. OpenShift can assign an arbitrary UID with root group membership. Its home is `/home/wsr`. Packaged libraries are readable by that user. Java temporary files and default catalog extraction use `/home/wsr/tmp`. Dependency resolution uses a private repository inside the extracted catalog directory. It does not write to `/root/.m2`.

A custom `--work-directory` must be writable by the process UID or its assigned groups.

The packaged SDK Maven downloader resolves external non-Camel dependencies from Maven Central. It does not require the Maven CLI. It does not read `settings.xml`. WSR waits up to two minutes for resolution. On timeout, it interrupts the worker and closes the downloader. Cancellation follows SDK resolver behavior. See [SDK reuse](../contributing/sdk-reuse.md) for repository and cleanup limits.

Run the container checks after the image build:

```sh
./scripts/container-smoke.sh wanaku-semantic-router:local
```

The checks verify the default runtime user and an arbitrary UID. They check writable directories and readable libraries. They run CLI help and version commands from the packaged image. They resolve one fixed-release Maven dependency through the packaged SDK downloader as the arbitrary UID. They require no inference provider or backend services.

Local container checks require a running Podman backend. Use the `Multiplatform PR Builds` workflow when local execution is unavailable. Verify both architecture results before deploying the image.

CI publishes images for `x86_64` and `aarch64` to `quay.io/wanaku/wanaku-semantic-router`. See [native architecture builds](../contributing/ci.md#native-architecture-builds) for the tags.

## Next steps

- [Deploy on Kubernetes and OpenShift](kubernetes.md): run the image with health probes.
- [Runtime configuration](../reference/configuration.md): the environment settings of the runtime.
