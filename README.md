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

- [How Wanaku Semantic Router works](docs/get-started/overview.md)
- [Run a route from local files](docs/get-started/run-a-local-route.md), without Barn
- [Run a published semantic route](docs/operate/run-a-published-route.md)
- [Deploy on Kubernetes and OpenShift](docs/operate/kubernetes.md)
- [Command line](docs/reference/cli.md) and [runtime configuration](docs/reference/configuration.md)
- [Build Wanaku Semantic Router](docs/contributing/building.md)

The [documentation home](docs/index.md) lists all pages.

## License

WSR is licensed under the [Apache License 2.0](LICENSE).
