# NVIDIA Triton Inference Server

## What it is
Triton Inference Server is NVIDIA's open-source model-serving platform. It provides a single
inference service (HTTP/REST, GRPC, and a C API) that can host and run models from multiple
frameworks side by side, on CPU or GPU, in the datacenter, cloud, or at the edge. Instead of
writing custom serving code per framework, teams point Triton at a model repository and get
production features (batching, concurrent model execution, versioning, metrics) for free.

## Key features
- **Multi-framework backend support**: TensorRT, TensorRT-LLM, PyTorch (LibTorch/TorchScript),
  ONNX Runtime, OpenVINO, Python (custom backend), vLLM, and plain C++ backends — all served
  through one endpoint.
- **Dynamic batching & concurrent execution**: automatically batches incoming requests and can
  run multiple models (or multiple instances of the same model) concurrently on one GPU to
  maximize throughput/utilization.
- **Model ensembles & BLS (Business Logic Scripting)**: chain multiple models/preprocessing
  steps into a single pipeline executed server-side.
- **Model management**: hot-reload, versioning, and explicit/poll model-control modes without
  restarting the server.
- **OpenAI-compatible frontend**: can expose an OpenAI-style chat/completions API (useful for
  LLM serving via vLLM/TensorRT-LLM backends).
- **Observability**: built-in Prometheus metrics, plus Performance Analyzer and Model Analyzer
  tooling for tuning batch size/instance count.
- **Deployment targets**: datacenter/cloud GPUs, and edge/embedded devices (Jetson) via a
  lightweight shared-library mode.

## Latest release
- **Version**: 2.71.0
- **Corresponds to NGC container**: `26.07` (i.e. `nvcr.io/nvidia/tritonserver:26.07-py3`)
- **Published**: 2026-07-29
- **Source**: https://github.com/triton-inference-server/server/releases/tag/v2.71.0

### Highlights in 2.71.0
- OpenAI-compatible frontend: added a streaming tool-call parse buffer limit to prevent
  excessive memory usage during streaming tool calls.
- PyTorch backend: enabled PyTorch 2 batching; added support for loading NV embedding layers.
- HSTU generative-recommender support via the PyTorch AOTI serving path.
- TensorRT backend: added multi-device (multi-GPU) inference support.
- Python client: preserves output datatype for empty (zero-element) tensors.
- Core: improved model-readiness reporting (`TRITONSERVER_ServerModelIsReady`) — unresolved
  models now correctly report `ready=false`.
- Python backend: fixed a deadlock in decoupled BLS and a use-after-free in the `async_exec`
  pybind callback.
- Security: restriction header values redacted from OpenAI-frontend startup logs; inference
  restriction enforced in the SageMaker MME invoke handler.
- TRT-LLM container pinned to TensorRT-LLM 1.2.1.

### Reference links
- GitHub: https://github.com/triton-inference-server/server
- Releases: https://github.com/triton-inference-server/server/releases
- NGC container tags: https://ngc.nvidia.com/catalog/containers/nvidia:tritonserver/tags
