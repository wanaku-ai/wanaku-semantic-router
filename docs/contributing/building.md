# Build Wanaku Semantic Router

This page describes how to build and package WSR from source, and how to apply the code formatting.

## Build and package

Use Java 21 or later and Maven 3.9 or later.

Check out `wanaku-capabilities-java-sdk` beside WSR as `../sdk`. Use the SDK commit listed in `.github/sdk-revision`. Install the SDK before you build WSR. CI installs that same commit on both native architectures.

```sh
mvn -B -ntp -f ../sdk/pom.xml clean install
mvn -B -ntp clean verify
```

The build creates `target/wanaku-semantic-router-0.3.0-SNAPSHOT.jar` and `target/lib/`. Keep the JAR and the library directory together. The JAR manifest contains the library paths. The tests use a local HTTP provider fixture. They do not need paid inference or backend services.

The runtime uses Camel `4.23.0` artifacts, which contain [PR #27494](https://github.com/apache/camel/pull/27494). Republish catalogs using `semantic.evaluation`; old `semantic.question` YAML is incompatible. Migrate Barn preview clients to the new request and response contract before deployment. The Camel BOM manages all Camel dependency versions. Preserve the complete packaged distribution or container image digest to reproduce a deployment.

## Format the code

The build applies the CIC Spotless rules during compilation. It uses Palantir Java Format and explicit imports. Use braces for control blocks. Apply formatting before you submit changes. Check committed formatting before you build a release.

```sh
mvn -B -ntp spotless:apply
mvn -B -ntp spotless:check
```

## Next steps

- [Test Wanaku Semantic Router](testing.md): the test suites that need more than a Maven build.
- [Run a route from local files](../get-started/run-a-local-route.md): start the JAR that you built.
