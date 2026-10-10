# Ideation

This file contains the list of ideas to explore for this project. Each entry is a
candidate direction — not all of them will get built, this is just the running backlog
of "things worth investigating" before they get promoted into actual research/build work.

---

## Idea 1: Shared-GPU multi-tenancy testbed (using Jetson as home lab)

Problem: team has a limited number of GPUs and multiple projects need to share them.
Jetson AGX Orin will act as a home lab / testbed to prototype and validate GPU-sharing
approaches at small scale before applying the same patterns to our real (larger) GPU
pool. Goal is to use well-established, production-proven techniques only — not novel
research contributions.

Constraint: MIG (Multi-Instance GPU) is NOT supported on Jetson AGX Orin (Ampere iGPU
does not expose it — only Jetson Thor/Blackwell and datacenter Ampere+/Hopper/Blackwell
GPUs like A100/H100 support it). Document as the future/production-grade option once we
have datacenter-class GPUs, but it cannot be prototyped on this hardware.

Testable layers, in order of experimentation:

### 1. CUDA MPS (Multi-Process Service) — spatial sharing across processes.
   Confirmed supported on Jetson since JetPack 6.1 / CUDA 12.5. Lets multiple project
   processes execute concurrently on the GPU instead of serializing. First experiment:
   run two real project workloads as separate processes under MPS, measure concurrent
   throughput vs sequential baseline.

### 2. Triton Inference Server — production pattern for "many models/projects, one GPU."
   Ships officially for Jetson via JetPack. Supports concurrent model execution
   (multiple models/instances resident and running at once) and dynamic batching, with
   per-model scheduling policy. Treat this as the target serving architecture: each
   team project = one model in the Triton model repository.

### 3. Kubernetes-style scheduling (k3s + NVIDIA device plugin, time-slicing) — the
   fairness/queueing layer, for when the problem is also "whose job runs right now,"
   not just co-execution. k3s is light enough to prototype on a single Jetson as a
   stand-in for a future multi-node setup. Slurm + GRES is the equivalent pattern more
   common in HPC/academic settings — worth considering if the team prefers a job
   scheduler mental model over Kubernetes.

Orthogonal wins (stack with any of the above, reduce contention regardless of sharing
layer chosen):
- Quantization (INT8/FP16, 2:4 structured sparsity on Ampere Tensor Cores) — shrinks
  each project's footprint so more fit in the same shared pool.
- DLA offload — Orin has 2x Deep Learning Accelerator engines, ~40% of the SoC's peak
  TOPS, that sit idle unless explicitly targeted via TensorRT. Offloading CNN-shaped
  work (conv/pooling/batchnorm — not transformer/attention layers, unsupported on DLA
  as of JetPack 6.2) frees the GPU for other projects. Established, production-proven
  (used in NVIDIA DeepStream).

Suggested experiment order: MPS (validate spatial co-execution) -> Triton (validate
production-style multi-model serving) -> k3s/time-slicing (validate fairness/queueing
across teams) -> layer in quantization + DLA offload throughout to reduce contention.
