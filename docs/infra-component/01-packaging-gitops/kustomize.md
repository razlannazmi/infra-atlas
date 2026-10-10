---
role: Template-free YAML overlay/patching (env-specific config)
label: Kustomize
integrates_with: [helm]
---
# Kustomize (kustomization)

> Template-free Kubernetes configuration management. Customizes raw YAML through
> **overlays** and **patches** instead of a templating language.

- **Category:** Packaging & GitOps
- **Website:** <https://kustomize.io>
- **Built into:** `kubectl` (`kubectl apply -k`) and Argo CD
- **License:** Apache-2.0 (Kubernetes SIG-CLI project)

---

## 1. What it is

Kustomize lets you take a set of **base** manifests and produce environment-specific
variants (**overlays**) by declaratively patching them — no Go templates, no `{{ }}`.
The driving file is `kustomization.yaml`, which lists resources, patches, images,
name prefixes, common labels, config/secret generators, and more.

Where **Helm** uses *templating + values*, Kustomize uses *base + patch overlays*.
They are complementary, not competitors.

## 2. What it's used for

- **Env promotion** — one `base/`, then `overlays/dev`, `overlays/staging`, `overlays/prod`.
- **Image pinning** — the `images:` transformer rewrites tags (this is exactly what
  [Argo CD Image Updater](image-updater.md) edits when using the Kustomize write-back method).
- **Injecting config** — `configMapGenerator` / `secretGenerator` create hashed
  ConfigMaps/Secrets that trigger rolling restarts on change.
- **Cross-cutting metadata** — apply `commonLabels`, `namespace`, `namePrefix` to all resources.

## 3. Structure

```text
myapp/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   └── replicas-patch.yaml
    └── prod/
        ├── kustomization.yaml
        └── resources-patch.yaml
```

**`base/kustomization.yaml`**
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
commonLabels:
  app.kubernetes.io/part-of: ai-platform
```

**`overlays/prod/kustomization.yaml`**
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: prod
resources:
  - ../../base
images:
  - name: ghcr.io/example/my-service
    newTag: "1.4.2"          # <- Image Updater edits this line
patches:
  - path: resources-patch.yaml
    target:
      kind: Deployment
      name: my-service
replicas:
  - name: my-service
    count: 3
```

**`resources-patch.yaml`** (strategic-merge patch)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-service
spec:
  template:
    spec:
      containers:
        - name: my-service
          resources:
            limits:
              nvidia.com/gpu: 1
```

## 4. How to use

```sh
kustomize build overlays/prod                 # render to stdout
kubectl apply -k overlays/prod                # build + apply (kubectl built-in)
kubectl kustomize overlays/prod | kubectl apply -f -

kustomize edit set image ghcr.io/example/my-service=:1.4.3   # scripted tag bump
kustomize edit set namespace prod
```

- `build <dir>` — renders the final YAML (base + all transformers/patches).
- `apply -k <dir>` — the kubectl-native shortcut for `kustomize build | kubectl apply`.
- `kustomize edit set image ...` — programmatic image rewrite (used by CI and Image Updater).

### With Helm

Kustomize can post-process a rendered Helm chart:
```yaml
# kustomization.yaml
helmCharts:
  - name: redis
    repo: https://charts.bitnami.com/bitnami
    version: 19.0.0
    releaseName: cache
patches:
  - path: redis-tweaks.yaml
```
Render with `kustomize build --enable-helm`.

## 5. Dependencies & how it's used here

- **Requires:** nothing extra — it ships inside `kubectl` (`-k`) and Argo CD.
- **Consumed by:** Argo CD (native Kustomize support) and Image Updater
  (`kustomize` write-back strategy edits the `images:` list or `overlays/*/kustomization.yaml`).
- **Pairs with:** [Helm](helm.md) — Helm renders, Kustomize patches the output.

## 6. Helm vs Kustomize (quick call)

| Need | Prefer |
|------|--------|
| Distribute a reusable, parameterized app | **Helm** |
| Patch an existing/base manifest per env | **Kustomize** |
| Package with subchart dependencies | **Helm** |
| No templating, plain reviewable YAML diffs | **Kustomize** |
| Best of both | Helm chart **+** Kustomize overlay |
