# Wanaku Semantic Router

Wanaku Semantic Router (WSR) runs a Barn catalog as an Apache Camel application. Barn authors and publishes the integration. Camel evaluates its semantic evaluations and executes fixed Kamelet branches. Wanaku discovers and governs the public MCP tool.

## Quick start

Use Java 21 or later and Maven 3.9 or later. Check out `wanaku-capabilities-java-sdk` beside WSR as `../sdk`, at the commit in `.github/sdk-revision`. Then build both:

```sh
mvn -B -ntp -f ../sdk/pom.xml clean install
mvn -B -ntp clean verify
```

Start a semantic route that is published in Barn. Set `TYPESAFE_API_KEY` for the default TypeSafe AI expert first:

```sh
java -jar target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar runtime --semantic-route support-route
```

## Documentation

The [Wanaku Semantic Router documentation](https://wanaku.ai/docs/semantic-router/next/) covers:

- [Get started](https://wanaku.ai/docs/semantic-router/next/get-started/): how WSR works, and a first route from local files.
- [Run WSR](https://wanaku.ai/docs/semantic-router/next/operate/): run a published route, configure experts and audit forwarding, and deploy on Kubernetes.
- [Reference](https://wanaku.ai/docs/semantic-router/next/reference/): the command line, runtime configuration, artifact contract, and preview API.
- [Contribute](https://wanaku.ai/docs/semantic-router/next/contributing/): build from source, run the tests, and release.

For Wanaku itself, see the [Wanaku documentation](https://wanaku.ai/docs/next/). For the semantic route editor, see the [Wanaku Barn documentation](https://wanaku.ai/docs/barn/next/).

## License

WSR is licensed under the [Apache License 2.0](LICENSE).
