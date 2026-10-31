# Semantic evaluation preview API

The preview service exposes one endpoint. This page describes its request, its response, and its errors.

## Request

`POST /api/v1/preview` accepts:

```json
{
  "expertBean": "supportExpert",
  "operation": "choice",
  "parameters": {
    "instructions": "Select the support action.",
    "criteria": {"billing": "Invoices", "technical": "Technical problems", "no_match": "Neither action"}
  },
  "state": "I have a question about an invoice."
}
```

`expertBean`, `operation`, and `state` are required. Omitted `parameters` defaults to `{}`. State accepts text, objects, or arrays according to expert capabilities. Parameters contain literal JSON, including nested values; preview does not expand placeholders. The selected expert defines the parameters and their defaults.

Each request creates a native `SemanticEvaluation` in an isolated Camel context. Its state selector is `${body}`. The context has no action routes and evaluates the native expression directly. Preview accepts no executable YAML and never dispatches actions. It exposes neither expert discovery nor batch evaluation.

## Response

A choice success returns:

```json
{"resultType":"choice","value":"billing","diagnostics":{}}
```

`resultType` comes from the expert contract. Values retain their native types:

| Result type | JSON value |
| --- | --- |
| `boolean` | Boolean |
| `choice` | String |
| `score` | Number |
| `classification` | Array of label strings, including an empty array |

Diagnostics include only expert-supplied `probability`, `probabilities`, and `confidence`. WSR does not invent diagnostics.

The old `input`/`instructions`/`criteria`/`message` request and `label` response contract no longer apply. Update Barn clients alongside deployment.

## Errors and limits

| Failure | HTTP status / error |
| --- | --- |
| Invalid request, disabled expert, unsupported operation, invalid parameters or input | 400 / `invalid_request`, before inference |
| Capacity exhaustion | 429 |
| Provider failure or malformed evaluation result | 502 / `evaluation_failed` |
| Evaluation timeout | 504 / `evaluation_timeout` |

Preview retains Bearer authentication, a 64 KiB request limit, timeout cancellation, and bounded concurrency. A cancelled evaluation retains its capacity slot until its worker exits.

## Next steps

- [Run the semantic evaluation preview](../operate/preview-service.md): start the preview service.
- [Artifact and execution contract](artifact-contract.md): the semantic evaluation that a catalog declares.
