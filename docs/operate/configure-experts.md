---
title: Configure classifier and guard experts
description: Enable the classifier and guard expert beans that a published catalog names, set their bean properties, and provision the Wolf Defender model.
---

# Configure classifier and guard experts

A published catalog names a classifier expert and an optional guard expert. This page describes how to enable these expert beans and set their properties.

Enable every expert referenced by the published catalog explicitly. The classifier
remains `expert.bean`; an optional Boolean guard uses `guard.expert.bean`,
`guard.operation`, and `guard.rejectWhen` in the catalog manifest. Missing guard
beans fail startup before readiness or forward registration.

Repeat `--expert` for the classifier and guard:

```shell
--expert supportExpert=org.apache.camel.component.typesafeai.TypeSafeAiSemanticAdapter \
--expert injectionGuard=org.apache.camel.component.wolfdefender.WolfDefenderSemanticAdapter \
-p 'wsr.expert.injectionGuard.properties.modelDirectory={{env:WOLF_MODEL_DIRECTORY}}'
```

Bean properties use `wsr.expert.<bean>.properties.<property>` in deployment files
or `--property` options. Camel resolves property placeholders and converts values
to setter types before native evaluation validation and service startup. Unknown
properties or invalid values fail startup with sanitized diagnostics. Credentials
and model paths belong to the deployment; Barn catalogs contain no such settings.

The distribution includes `camel-wolf-defender` at the same Camel version as the
semantic language. Provision its pinned model and tokenizer files locally, then
set `WOLF_MODEL_DIRECTORY` to that directory. WSR never downloads model files.
Camel owns each expert's lifecycle, including shutdown of model resources.
Deploy this runtime before publishing guarded catalogs; restart for new revisions.

## Next steps

- [Run a published semantic route](run-a-published-route.md): start the runtime with the experts.
- [Test Wanaku Semantic Router](../contributing/testing.md): run the Wolf Defender smoke test.
