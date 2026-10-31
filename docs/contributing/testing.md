---
title: Test Wanaku Semantic Router
description: What the Wanaku Semantic Router tests check, how to evaluate classification quality, and how to run the Wolf Defender smoke test with a model.
---

# Test Wanaku Semantic Router

This page describes what the tests check, how to evaluate classification quality, and the test suites that need more than a Maven build.

## Validation scope

The reference checks cover native semantic selection, auxiliary Kamelet loading, MCP discovery and invocation, explicit no-match, provider failure, malformed results, archive safety, digest checks, runtime compatibility, fetch failure, failed Camel startup, failed registration cleanup, accepted POST response loss, full HTTP completion deadlines, action failure, preview timeout and capacity, registration ordering, shutdown, and a packaged JAR smoke test. Deterministic fixtures check integration behavior. They do not measure classification quality.

## Evaluate classification quality

For model evaluation, use a separate deployment and a representative set of saved examples. Run semantic evaluation previews first. Compare each expected label with its actual label. Review no-match and error cases separately. Use demonstration actions before enabling operations with backend side effects. Record the provider, model, revision, and distribution or image digest with the results.

## Wolf Defender smoke test

Run the provisioned-model smoke test with
`WOLF_MODEL_DIRECTORY=/models/wolf-defender mvn -Dtest=WolfDefenderIntegrationTest test`.
Without that variable the test is skipped; deterministic guard routing tests
cover rejection, evaluation failure, and accepted dispatch without inference.

Initial verification used a locally installed Camel `4.23.0-SNAPSHOT` Wolf
Defender JAR (Maven metadata timestamp `20261009105227`, SHA-256
`c69acd50a02108d28292b733bd334bdeaaff399c32669df60cad2aadf30a5da0`).
Camel PR 27564 was merged as `5073253e66bdfa0f9f8cc80c8900a97466b0740c`.
These identify the verified artifact and upstream change, rather than pinning
future snapshot resolution. Release builds must verify that the selected Camel
snapshot contains the native `injection` operation and `modelDirectory` property.

## Next steps

- [CI automation](ci.md): the workflows that run the tests.
- [Configure classifier and guard experts](../operate/configure-experts.md): the guard expert that this test uses.
