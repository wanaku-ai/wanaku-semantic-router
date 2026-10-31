---
title: Run Wanaku Semantic Router
description: "Run the Wanaku Semantic Router runtime and preview service: start a published route, configure experts and audit, and deploy it on Kubernetes."
---

# Run Wanaku Semantic Router

These pages describe how to run the Wanaku Semantic Router (WSR) runtime and preview service in a deployment.

## Pages in this section

- [Run a published semantic route](run-a-published-route.md): start the runtime from a Barn publication, pin a publication, and stop the runtime.
- [Initialize from Git](initialize-from-git.md): load deployment files from a Git repository before startup.
- [Configure classifier and guard experts](configure-experts.md): enable the expert beans that a catalog names.
- [Forward semantic audit records](audit-forwarding.md): send evaluation records to Wanaku and check delivery.
- [Send messages to Kafka destinations](kafka-destinations.md): use a Kafka sink Kamelet as a destination.
- [Run the semantic evaluation preview](preview-service.md): start the service that Barn uses to test evaluations.
- [Run WSR in a container](container.md): build, run, and check the container image.
- [Deploy on Kubernetes and OpenShift](kubernetes.md): run the runtime with the example Deployment and health probes.

## Next steps

- [Runtime configuration](../reference/configuration.md): the environment settings, options, and limits of the runtime.
