# Run a published semantic route

WSR runs under its service authority. Its configured backend credentials determine action access. Caller attribution does not grant delegated authority. Place the MCP endpoint on a private network that Wanaku can reach. Place the preview endpoint on a private network that Barn can reach.

## Start a runtime

1. Publish the route in Barn.
2. Set `TYPESAFE_API_KEY` in the WSR process environment.
3. Start WSR with the route name.

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime \
  --semantic-route support-route
```

This local command uses Barn at `http://localhost:8180`, Wanaku management at `http://localhost:8080`, and MCP at `http://localhost:8090/mcp`. It registers the forward as `support-route` in namespace `default`. Set `TYPESAFE_MODEL` to select a model. Without it, WSR uses the Camel TypeSafe AI component's default model.

Use different service addresses with these options:

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime \
  --semantic-route support-route \
  --barn-url http://barn:8180 \
  --registration-url http://wanaku:8080 \
  --bind 0.0.0.0 --mcp-address http://wsr:8090/mcp
```

Barn resolves the current published revision. WSR reads that selection once at startup. It downloads the archive and verifies its digest, revision, and runtime contract. A later publication does not change a running WSR. Restart WSR to select that publication. Draft edits do not change the selected publication. An explicit `--catalog-revision` selects a stored revision. An explicit digest or catalog pin must match the resolved publication.

The route name must identify one saved definition. An ambiguous name returns an error. A route must have a published revision. Older definitions with one publication can use that publication. Older definitions with several publications and no current selection need an explicit revision or a successful publish operation.

The default expert comes from the published expert snapshot. WSR supplies its packaged TypeSafe AI adapter when that snapshot selects `camel-typesafe-ai`. WSR uses environment references for the API key and optional model. A custom expert needs explicit `--expert` configuration. File settings and component properties override provider defaults. Barn stores no provider credential values.

## Explicit catalog pins

Use catalog options when you already have the publication metadata. This mode retains the existing explicit configuration contract.

1. Publish a definition in Barn.
2. Copy the catalog name, revision, and SHA-256 digest from the publication result.
3. Set the Barn URL and Wanaku management URL.
4. Set the advertised MCP address to an address that Wanaku can reach.
5. Configure the expert bean and provider settings.
6. Supply credential values through environment variables.
7. Start WSR with the publication values.

Replace the catalog name, revision, and digest in this example. Set `TYPESAFE_API_KEY` and `TYPESAFE_MODEL` in the process environment before startup.

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime \
  --barn-url http://barn:8080 \
  --service-catalog semantic-REPLACE-ID-1 \
  --service-catalog-system service \
  --catalog-revision 1 \
  --catalog-sha256 REPLACE-WITH-PUBLICATION-SHA256 \
  --registration-url http://wanaku:8080 \
  --name semantic-support --namespace default \
  --mcp-address http://wsr:8090/mcp \
  --bind 0.0.0.0 --mcp-port 8090 \
  --data-dir /var/lib/wsr \
  --expert supportExpert=org.apache.camel.component.typesafeai.TypeSafeAiSemanticAdapter \
  --property 'camel.component.typesafe-ai.api-key={{env:TYPESAFE_API_KEY}}' \
  --property 'camel.component.typesafe-ai.model={{env:TYPESAFE_MODEL}}'
```

WSR downloads the selected catalog ZIP from Barn's data store. It verifies the archive before it starts Camel. Select a location that the process can write. WSR creates the data directory and a private extraction directory inside it. Shutdown removes that extraction directory.

See [runtime configuration](../reference/configuration.md#runtime-options) for each option, its property, and its default.

## Stop the runtime

Send SIGTERM to stop WSR. During startup, shutdown requests cancellation and releases acquired resources before shutdown completes. A stopped runtime cannot start again. It removes its own forward before it stops Camel. A replacement process has a different instance label. Shutdown leaves that replacement's forward registered. If Wanaku is unreachable during shutdown, the process reports the cleanup failure. Wanaku's normal reconnect behavior then observes the stopped upstream.

## Next steps

- [Runtime configuration](../reference/configuration.md): the options, environment settings, and limits of the runtime.
- [Deploy on Kubernetes and OpenShift](kubernetes.md): run the runtime with health probes.
- [Forward semantic audit records](audit-forwarding.md): send evaluation records to Wanaku.
