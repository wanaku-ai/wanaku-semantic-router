---
title: Run the semantic evaluation preview
description: Start the Wanaku Semantic Router preview service that Barn calls to test a semantic evaluation, with its token, timeout, and concurrency options.
---

# Run the semantic evaluation preview

Barn uses the preview service to test a semantic evaluation before it publishes a route. The service evaluates one request without action routes or Kamelets.

Configure the same expert bean and provider settings as the runtime. Start the semantic evaluation service.

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar preview \
  --bind 0.0.0.0 --port 8092 \
  --preview-timeout-ms 10000 --max-concurrent 4 \
  --preview-token-env WSR_PREVIEW_TOKEN \
  --expert supportExpert=org.apache.camel.component.typesafeai.TypeSafeAiSemanticAdapter \
  --property 'camel.component.typesafe-ai.api-key={{env:TYPESAFE_API_KEY}}' \
  --property 'camel.component.typesafe-ai.model={{env:TYPESAFE_MODEL}}'
```

Set `WSR_PREVIEW_TOKEN`, `TYPESAFE_API_KEY`, and `TYPESAFE_MODEL` in the process environment before startup. Set Barn's preview URL to `http://wsr-preview:8092/api/v1/preview`. Configure the matching token reference in Barn. The service creates an isolated Camel context per request without action routes. Barn must send `expertBean`, `operation`, optional `parameters` (default `{}`), and `state`; read `resultType`, typed `value`, and `diagnostics` from the response. See [the preview contract](../reference/preview-api.md). Migrate old preview clients and republish catalogs using `semantic.evaluation` before deploying this runtime; it does not translate old question YAML.

| Preview option | Property | Default |
| --- | --- | --- |
| `--bind` | `wsr.preview.bind` | `127.0.0.1` |
| `--port`, `--preview-port` | `wsr.preview.port` | `8092` |
| `--preview-timeout-ms` | `wsr.preview.timeout-ms` | `10000` |
| `--max-concurrent` | `wsr.preview.max-concurrent` | `4` |
| `--preview-token-env` | `wsr.preview.token-env` | No token |

## Next steps

- [Semantic evaluation preview API](../reference/preview-api.md): the request, the response, and the errors.
- [Configure classifier and guard experts](configure-experts.md): enable the expert beans that the preview uses.
