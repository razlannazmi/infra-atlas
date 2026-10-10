---
role: Package manager; templates K8s manifests into charts
---
# Helm

> The package manager for Kubernetes. Bundles a set of related manifests into a
> versioned, templated, shareable unit called a **chart**.

- **Category:** Packaging & GitOps
- **Website:** <https://helm.sh>
- **Built with:** Go
- **License:** Apache-2.0 (CNCF graduated project)

---

## 1. What it is

Helm is to Kubernetes what `apt`/`yum`/`npm` are to their ecosystems. Instead of
hand-writing and `kubectl apply`-ing dozens of raw YAML files, you package them
into a **chart**: a directory of Go-templated manifests plus a `values.yaml` of
tunable defaults. A rendered, installed instance of a chart is a **release**.

## 2. What it's used for

- **Install complex apps in one command** — e.g. `helm install langfuse langfuse/langfuse`
  spins up the web app, worker, Postgres, ClickHouse, Redis, and S3 config together.
- **Parameterize per environment** — one chart, many `values.yaml` overlays (dev/stage/prod).
- **Version & roll back** — every `helm upgrade` is a revision you can `helm rollback` to.
- **Share/distribute** — charts are hosted in Helm repositories or OCI registries.
- **Dependencies** — a chart can declare **subcharts** (Redis, PostgreSQL, etc.).

In this platform, **every component is shipped as a Helm chart**, and Argo CD
renders those charts during GitOps sync.

## 3. Chart anatomy (the structure used across this platform)

```text
<component>/
├── Chart.yaml          # metadata + dependencies
├── values.yaml         # default configuration values
├── charts/             # downloaded subchart dependencies (helm dep update)
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl    # named template partials
│   └── NOTES.txt       # post-install message
└── .helmignore
```

**`Chart.yaml`** — identity + dependency graph:

```yaml
apiVersion: v2
name: my-service
version: 0.1.0            # chart version (SemVer)
appVersion: "1.4.2"      # the app image version it deploys
dependencies:
  - name: redis
    version: "19.x.x"
    repository: https://charts.bitnami.com/bitnami
    condition: redis.enabled
```

**`values.yaml`** — defaults referenced in templates as `{{ .Values.image.tag }}`:

```yaml
replicaCount: 2
image:
  repository: ghcr.io/example/my-service
  tag: "1.4.2"
resources:
  limits:
    nvidia.com/gpu: 1
```

**`templates/deployment.yaml`** — Go templating over those values:

```yaml
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

## 4. How to use

### Install the CLI
```sh
sudo snap install helm --classic          # Ubuntu
# or
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### Everyday commands
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo redis

helm install my-redis bitnami/redis -n data --create-namespace
helm install my-redis bitnami/redis -f values-prod.yaml
helm upgrade --install my-redis bitnami/redis --set replica.replicaCount=3

helm list -A
helm status my-redis -n data
helm rollback my-redis 1 -n data
helm uninstall my-redis -n data
```

- `--install` (with `upgrade`) — create if absent, else upgrade (idempotent; ideal for CI/GitOps).
- `-f <file>` — supply a values override file; repeatable, later files win.
- `--set key=value` — inline override for one value (good for image tags in pipelines).
- `-n / --create-namespace` — target/auto-create the namespace.

### Author / test a chart locally
```sh
helm create my-service          # scaffold a new chart
helm dependency update          # download subcharts into charts/
helm lint .                     # validate
helm template my-service . -f values-prod.yaml   # render without installing
helm install my-service . --dry-run --debug      # server-side dry run
```

### OCI registries (modern distribution)
```sh
helm push my-service-0.1.0.tgz oci://ghcr.io/example/charts
helm install my-service oci://ghcr.io/example/charts/my-service --version 0.1.0
```

## 5. Dependencies & how it's used here

- **Requires:** a working Kubernetes cluster + kubeconfig (nothing else — Helm 3 is client-only, no Tiller).
- **Consumed by:** Argo CD (renders charts during sync) and Argo CD Image Updater
  (writes `image.tag` back into `values.yaml`).
- **Pairs with:** [Kustomize](kustomize.md) for last-mile patching via Helm's
  post-renderer or Argo CD's `kustomize`-after-`helm` pipeline.

## 6. Gotchas

- Chart `version` ≠ app `appVersion`. Bump `version` on any template change.
- Secrets in `values.yaml` should come from [OpenBao](../05-security-secrets/openbao.md)
  or the External Secrets Operator — never commit plaintext secrets.
- `helm rollback` restores manifests, **not** stateful data (PVCs/DB rows persist).
