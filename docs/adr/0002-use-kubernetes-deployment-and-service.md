# ADR-0002: Use Kubernetes Deployment and Service for Robot Workloads

## Status

Accepted

## Context

Robot services must remain available even when an application container or Pod
fails. Pod IP addresses are also ephemeral and can change whenever Pods are
recreated.

Directly creating and accessing individual Pods would couple clients to
temporary Pod instances and would not provide declarative replica management.

## Decision

Robot workloads will be managed using Kubernetes Deployments.

Deployments will define the desired replica count and automatically recreate
failed Pods.

Services will provide stable networking and select Pods through Kubernetes
labels.

For the initial single-node development environment, a NodePort Service will be
used to verify access from the macOS host to the k3s workload.

## Consequences

### Positive

- Failed Pods are recreated automatically.
- Replica counts are managed declaratively.
- Clients do not depend on individual Pod IP addresses.
- The workload can later support rolling updates and horizontal scaling.
- Kubernetes manifests can be managed through Git and ArgoCD.

### Negative

- NodePort is not the preferred production exposure method.
- Local container images must currently be imported into the k3s containerd
  image store.
- Additional health probes are required to detect application-level failures.

## Future Work

- Replace NodePort with ClusterIP and Ingress.
- Add readiness and liveness probes.
- Push images to Harbor instead of manually importing local images.
- Manage deployments through ArgoCD.
