# gitops-argocd

GitOps repository written by **Ali Zandieh**  for installing and managing core platform services (DevOps tooling) and their ArgoCD manifests on Kubernetes, using [Argo CD](https://argo-cd.readthedocs.io/).

Git is the single source of truth: everything declared here is reconciled into the cluster by Argo CD, and changes are made through commits and pull requests rather than `kubectl apply`.

## Repository layout

```
.
├── eu-west-1-saha/          # Cluster/environment directory (with region prefix)
├── .pre-commit-config.yaml  # Local commit-time checks (see below)
├── .gitignore
└── README.md
```

Each top-level directory represents one cluster/environment, named `<region>-<cluster-name>`. Everything Argo CD should deploy to that cluster lives under it.

```
eu-west-1-saha
├── apps
│   ├── piston
│   └── test
├── argo-installation
├── bootstrap
└── core-services
    ├── adminer
    ├── ai-mcp
    ├── argo
    ├── bitbucket-runner
    ├── cert-manager
    ├── external-dns
    ├── external-secrets
    ├── istio
    ├── istio-ingress
    ├── karpenter
    ├── keda
    ├── kube-system
    ├── kyverno
    ├── monitoring
    ├── oauth2-proxy
    ├── traefik
    └── velero
```

## How it works

1. Argo CD is bootstrapped in the cluster and pointed at this repository.
2. A root Application (app-of-apps) discovers the manifests under the cluster directory.
3. Each core service is defined as an Argo CD `Application` pointing at a Helm chart, Kustomize overlay or plain manifests.
4. Argo CD continuously compares live state with Git and syncs (or reports drift) automatically.


## Prerequisites

- A Kubernetes cluster
- `kubectl` with access to the cluster
- Argo CD installed in the `argocd` namespace
- [`pre-commit`](https://pre-commit.com/) for local checks

## Getting started

Clone the repo and enable the pre-commit hooks:

```bash
git clone https://github.com/Alizandieh/gitops-argocd.git
cd gitops-argocd
brew install pre-commit
pre-commit install
```

Installing ArgoCD and bootstrap a cluster by applying its root application:


Please read this document:[`Readme`](./eu-west-1-saha/argo-installation/README.md)


Then check status in the Argo CD UI or CLI:

```bash
argocd app list
argocd app get <app-name>
```

## Making changes

1. Create a branch and edit or add manifests under the relevant cluster directory.
2. Commit. The pre-commit hooks run automatically.
3. Open a pull request and get it reviewed.
4. After merge, Argo CD picks up the change and syncs it to the cluster.

Avoid changing resources by hand in the cluster; Argo CD will treat that as drift and may revert it.

### Adding a new service

1. Add the chart, overlay or manifests for the service under the cluster directory.
2. Add an Argo CD `Application` (or an entry in the ApplicationSet) that references it.
3. Merge and let Argo CD sync it.


## Secrets

Never commit plaintext secrets. Use an external secret store or an encrypted-secrets approach (for example External Secrets, Sealed Secrets or SOPS) and commit only the references or encrypted payloads.




