---
title: How Wanaku Semantic Router works
description: How Wanaku Semantic Router loads one pinned catalog revision, registers its MCP tool with Wanaku, handles tool calls, and forwards audit records.
---

# How Wanaku Semantic Router works

Wanaku Semantic Router (WSR) runs a Barn catalog as an Apache Camel application. Barn authors and publishes the integration. Camel evaluates its semantic evaluations and executes fixed Kamelet branches. Wanaku discovers and governs the public MCP tool.

## Runtime behavior

One process loads one selected service from one fixed catalog revision. It downloads the Barn `DataStore` ZIP through the existing catalog endpoint. It checks the SHA-256 digest, revision, Camel version, main file, and auxiliary resources. It loads action settings and named expert beans before Camel starts.

WSR uses the maintained LangChain4j MCP client for initialization and tool discovery before it registers the Wanaku forward. A startup failure stops Camel and removes extracted files. A registered forward contains the active revision and digest as labels. It also records the catalog's optional Camel build metadata. This metadata describes catalog publication, not the runtime binaries. Shutdown removes the forward only when its instance label still belongs to that process. Updates require restart or replacement with explicit publication pins.

The MCP tool accepts `message`, a required string. A selected branch returns the action result. `no_match` returns the configured no-match text. Provider failures, malformed answers, and action failures return MCP tool errors. Evaluation output selects a fixed label. It cannot supply a destination URI.

WSR enables Camel's semantic audit capture and forwards records to Wanaku with bounded retries, preserving typed results and evidence IDs. Capture and forwarding default on and are controlled by `--audit-enabled`; catalogs need no audit configuration. See [audit forwarding](../operate/audit-forwarding.md) for receiver setup and delivery limits.

## Next steps

- [Run a route from local files](run-a-local-route.md): start WSR without Barn.
- [Run a published semantic route](../operate/run-a-published-route.md): start WSR from a Barn publication.
- [Artifact and execution contract](../reference/artifact-contract.md): the catalog that Barn publishes for WSR.
