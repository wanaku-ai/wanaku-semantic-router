# Test Wanaku Semantic Router

This page describes the test suites that need more than a Maven build.

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
