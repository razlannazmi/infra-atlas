# infra-atlas

Personal library of AI and infrastructure items — reference docs, research notes,
and tooling shortlists around GPU infrastructure and ML platforms.

## Structure

- [`docs/infra-component/`](docs/infra-component/README.md) — the components used in an
  established organization's self-hosted AI/LLM platform on Kubernetes (GitOps,
  networking, observability, data, security, GPU compute), one doc per component.
- [`docs/research/`](docs/research/README.md) — personal experimentation record: idea backlog,
  hardware notes, and deep-dives on GPU infra topics (e.g. CUDA MPS, Triton vs vLLM,
  Jetson AGX Orin as a home-lab testbed).
- [`docs/tool-candidates/`](docs/tool-candidates/observability.md) — temporary shortlist of
  open-source tools being considered for the organization (currently observability).

Each folder is self-contained: docs only link to other docs in the same folder.
