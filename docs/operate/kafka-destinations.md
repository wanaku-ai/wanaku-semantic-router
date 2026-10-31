# Send messages to Kafka destinations

A semantic route can select a Kafka sink Kamelet as a destination. This page describes how to configure the Kamelet and its credentials.

WSR includes the `camel-kafka` component. Upload a native Kafka sink Kamelet in Barn. Select it as a semantic destination. Set its `topic` and `bootstrapServers` configuration values. Use broker addresses that the WSR process can reach. Publish the semantic route and start WSR with its route name.

WSR sends the original message to the selected Kafka topic. It returns a text acknowledgement after the sink completes. A Kafka send failure returns a tool error.

Use `env:KAFKA_PASSWORD` for a password parameter in Barn. Set `KAFKA_PASSWORD` in the WSR process environment. Use a Secret-backed environment variable in Kubernetes or OpenShift. Keep the Kamelet's password parameter marked as a secret in its configuration schema. Set the Kafka security protocol and authentication mechanism required by your broker.

The Kafka dependency uses the packaged Camel version. Other Camel components must also be included in the WSR distribution before a catalog can declare them. WSR does not download another Camel version at startup.

## Next steps

- [Run a published semantic route](run-a-published-route.md): start the runtime with the published route.
- [Artifact and execution contract](../reference/artifact-contract.md): the dependencies that a catalog can declare.
