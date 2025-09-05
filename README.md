# NGINX Deployment

This repository contains the Kubernetes manifests and Kustomize configuration used to deploy workloads into an Amazon EKS cluster using [Argo CD](https://argo-cd.readthedocs.io/).

Repo: [byteinfiniti/deploy](https://github.com/byteinfiniti/deploy)

---

## Repository Layout

```

gitops/
├─ apps/
│  └─ imagepull-demo-app.yaml      # Argo CD Application definition
└─ workloads/
└─ imagepull-demo/
├─ kustomization.yaml        # Kustomize entrypoint (controls image tag)
├─ namespace.yaml            # Namespace manifest
└─ deployment.yaml           # NGINX Deployment with probes

```

### `apps/`
Contains **Argo CD Application** resources. Each `Application` tells Argo CD:
- Which Git repository and branch to watch (`repoURL`, `targetRevision`).
- Which folder (`path`) contains the Kubernetes manifests.
- Which cluster/namespace to deploy those manifests into.
- The sync policy (manual vs automated).

### `workloads/`
Each subdirectory under `workloads/` represents one application or service.

- **`imagepull-demo/`**  
  A simple example app (NGINX on Alpine).  
  - `namespace.yaml`: Creates the `imagepull-demo` namespace.  
  - `deployment.yaml`: Defines the Deployment and container probes.  
  - `kustomization.yaml`: Aggregates the resources and sets the container image tag (`images:` block).

## Notes

* Manifests are managed via **Kustomize**. Update `gitops/workloads/imagepull-demo/kustomization.yaml` to change image versions or add new resources.
* All changes are delivered to the cluster through **GitOps pull**: commit → push → Argo CD sync → updated workloads on EKS.
* To add more apps, create new subdirectories under `gitops/workloads/` with their own `kustomization.yaml`, and register them with Argo CD via a new `Application` manifest under `gitops/apps/`.

