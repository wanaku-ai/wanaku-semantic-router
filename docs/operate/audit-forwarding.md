# Forward semantic audit records

WSR sends the semantic audit records that Camel captures to Wanaku. This page describes how to turn forwarding on or off, check delivery, and enable the Wanaku receiver.

WSR controls Camel semantic audit capture and forwarding through `--audit-enabled`, `wsr.audit.enabled`, or `WSR_AUDIT_ENABLED`. It defaults to `true`. Catalog changes and republication are not required. Runtime settings override catalog audit settings, including expert filters, input capture, and sink selection. Preview contexts have no Wanaku sink.

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime \
  --semantic-route support-router --audit-enabled
```

Use `--audit-enabled=false` to disable capture and forwarding. The default endpoint is the Wanaku origin followed by `/api/v1/audit/semantic`. Set `--audit-url`, `wsr.audit.url`, or `WSR_AUDIT_URL` to use another complete URL. Explicit command options override generic properties, which override environment settings.

WSR selects the `memory` and `wanakuAudit` sinks with a memory capacity and dispatcher queue capacity of 1000 records each. It captures all experts without capturing input content.

After invoking a tool that performs a semantic evaluation, check delivery:

```sh
curl -s http://127.0.0.1:8091/api/v1/status | jq '.audit'
```

When enabled, `capture.enabled` is `true` and `capture.sinks` includes `wanakuAudit`. `received` counts records handed to WSR; `delivered` counts acknowledged deliveries. `failed` and `lastFailure` report delivery failures.

Enable the matching Wanaku receiver with `WANAKU_AUDIT_INGESTION_ENABLED=true`. The initial ingestion endpoint has no credential support. It uses Wanaku's existing management listener. Producer metadata is a claim from WSR, not verified caller identity. Audit forwarding does not reuse management tokens or authorize a tool call.

Camel supplies immutable evaluation, request and explicit decision records. WSR forwards only records that Camel emits. It does not infer a decision from a Boolean result or a route branch. Catalog authors can record actions with Camel's `SemanticAuditDecision` processor or `SemanticAudit` facade. Evidence IDs remain linked to the original evaluation records.

WSR excludes captured input, message bodies, headers and raw errors from forwarding. Typed results and bounded semantic metadata remain available, subject to Wanaku's redaction policy. Each request includes the runtime instance ID, forward name, namespace and catalog revision/digest.

Delivery is asynchronous and best effort. Camel owns the bounded queue. Each HTTP attempt has a one-second completion timeout. WSR makes at most three attempts, with 100-ms and 200-ms delays, for transport errors and HTTP 408, 429 or 5xx responses. Retry bodies retain the same source event ID. Other rejections and invalid acknowledgements stop delivery. Wanaku acknowledges in-memory acceptance; this is not durable persistence.

Routing continues when the receiver is unavailable. Runtime status includes audit delivery counters and Camel capture/drop counters. Camel allows five seconds to drain on shutdown, then cancels remaining work. There is no local durable spool. Check audit status when you check deployment readiness; a ready router can have incomplete audit coverage.

## Next steps

- [Runtime configuration](../reference/configuration.md): the audit settings and the other runtime settings.
- [Run a published semantic route](run-a-published-route.md): start the runtime.
