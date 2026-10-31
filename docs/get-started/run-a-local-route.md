---
title: Run a route from local files
description: Build Wanaku Semantic Router and start the support route fixtures from local files without Barn, then see the tool that it registers in Wanaku.
---

# Run a route from local files

This tutorial starts WSR from the support route fixtures in this repository, without Barn. At the end, Wanaku lists the `route_support` tool from the `local-support` forward.

## Prerequisites

- Java 21 or later and Maven 3.9 or later.
- A Wanaku server with its management API at `http://localhost:8080`.
- An API key for the TypeSafe AI provider in `TYPESAFE_API_KEY`.

## Build WSR

Build the SDK revision and WSR as described in [Build Wanaku Semantic Router](../contributing/building.md#build-and-package).

## Start the route

The runtime accepts a local path or `file:` URI through `--routes-ref`. Barn publication, a Barn URL, a catalog index and catalog revision/digest pins are unnecessary. Wanaku registration remains required. Keep `semantic-router.properties` beside the route; WSR needs its expert bean, tool name, input profile, Kamelets and action configuration. The [artifact contract](../reference/artifact-contract.md) applies to these resources as well.

This complete example uses the repository's support route fixtures (the actions return demonstration responses). Run it from the repository root after `mvn verify`. Set `TYPESAFE_API_KEY` to your provider credential first:

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime \
  --routes-ref "$PWD/src/test/resources/catalog/support/support.camel.yaml" \
  --dependencies "file://$PWD/src/test/resources/catalog/support/support.dependencies.txt" \
  --registration-url http://localhost:8080 \
  --name local-support \
  --mcp-address http://localhost:8090/mcp \
  --expert supportExpert=org.apache.camel.component.typesafeai.TypeSafeAiSemanticAdapter \
  -p 'camel.component.typesafe-ai.api-key={{env:TYPESAFE_API_KEY}}' \
  -p camel.component.typesafe-ai.model=your-model
```

The example's adjacent manifest uses `main=support/support.camel.yaml`, `dependencies=support/support.dependencies.txt`, `configuration=support/service.properties` and `kamelets=support/kamelets`. WSR infers the resource root from the selected route and the manifest's relative `main` path. Each resource reference must stay inside that root. A route stored directly beside its resources can use `main=router.camel.yaml`, `dependencies=router.dependencies.txt`, `configuration=service.properties` and `kamelets=kamelets`. The manifest must specify `contract.version=1`, `camel.version=4.23.0`, `input.profile=message-to-string/v1`, the route's `expert.bean` and its MCP `tool.name`.

`--dependencies` optionally overrides the manifest's dependency file and may point outside the resource root. Omit it to use the manifest reference. Kamelet references can name a directory or individual files in one directory. Directory symlinks are unsupported; list the individual files or use a real directory. Configure credentials and expert implementations through deployment settings, as in Barn mode. Artifact configuration contains only `action.*` properties.

WSR copies the selected resources into a private work directory before dependency resolution and Camel startup. Shutdown removes that copy and leaves the original files untouched. Changes to source files require restart. Status reports `source=local-files`; registration labels use revision `local` and a SHA-256 digest of the sorted snapshot resource paths and content hashes. This digest identifies the local snapshot, rather than a Barn archive. `catalog.revision` in a reused manifest does not pin local resources.

Use `WSR_ROUTES_REF` / `WSR_DEPENDENCIES` or deployment properties `wsr.routes-ref` / `wsr.dependencies` for the same selection. Local mode cannot be combined with `--semantic-route` or `wsr.catalog.*` selection settings. Missing files and incompatible manifests produce a configuration error before registration.

## Next steps

- [Run a published semantic route](../operate/run-a-published-route.md): start WSR from a Barn publication.
- [Artifact and execution contract](../reference/artifact-contract.md): the files that a route needs.
