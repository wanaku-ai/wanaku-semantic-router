---
title: Command line
description: The runtime and preview commands of Wanaku Semantic Router, the order in which settings apply, the exit codes, and the help and version options.
---

# Command line

This page describes the `runtime` and `preview` commands, the order in which settings apply, and the exit codes.

## Commands

Start a published semantic route by its Barn name. Set `TYPESAFE_API_KEY` in the process environment for the default TypeSafe AI expert. A deployment properties file is optional. Use [runtime configuration](../reference/configuration.md) for environment settings and [Kubernetes and OpenShift](../operate/kubernetes.md) for a deployment example.

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar --help
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar --version
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime --semantic-route support-route
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime --help
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar preview --help
```

Published route startup defaults to Barn at `http://localhost:8180` and Wanaku at `http://localhost:8080`. WSR resolves the current published catalog once. It verifies the fixed revision and digest before startup. It keeps that publication until restart. Kubernetes can set `WSR_SEMANTIC_ROUTE`, `WSR_BARN_URL`, `WSR_REGISTRATION_URL`, and `WSR_MCP_ADDRESS`.

Use `--expert NAME=CLASS` to configure a custom expert bean. Use `--property KEY=VALUE` (`-p`) for provider and Camel component settings. Repeat these options for additional settings. Use environment references for credentials. Settings apply in this order: file, environment, generic properties, and named options. A later source overrides an earlier source.

Each command accepts `--help` (`-h`) and `--version` (`-V`). These options do not start a service or read deployment settings. The version output contains the packaged WSR version and the Camel version. An unpackaged build uses `development` for the WSR version.

The CLI returns `0` for help and version output. It returns `2` for invalid arguments or invalid deployment settings. It returns `1` for startup or execution failures. Configuration validation errors omit supplied values. Operational errors report the failure type and runtime status without provider error messages. Stop a running service with `SIGTERM` or `Ctrl+C`. The shutdown hook closes the service and removes the runtime forward when that process owns it.

Use `--init-from` to initialize deployment files from Git before service startup. Use `--data-dir` to select runtime catalog storage and the initialization directory. WSR uses the SDK initializer and retry policy. See [initialization](../operate/initialize-from-git.md) for source, cache, and timeout behavior. Runtime routes come from the selected Barn publication or local resources.

For startup without Barn, use `runtime --routes-ref file:///path/to/router.camel.yaml --dependencies file:///path/to/dependencies.txt` with an adjacent semantic manifest and Wanaku deployment settings. See the [complete local launch example](../get-started/run-a-local-route.md).

## Next steps

- [Runtime configuration](configuration.md): each runtime option, its property, and its default.
- [Initialize from Git](../operate/initialize-from-git.md): the `--init-from` options.
