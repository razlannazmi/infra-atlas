# 06 — GPU & Compute

The heart of the AI platform: turning pooled multi-node GPUs into on-demand,
scale-to-zero inference capacity.

| Component | What it does | File |
|-----------|--------------|------|
| **NVIDIA GPU Operator** | Drivers, device plugin, DCGM metrics, MIG/time-slicing | [gpu-operator.md](gpu-operator.md) |
| **Knative / KEDA** | Scale-to-zero autoscaling | [autoscaling.md](autoscaling.md) |
| **Serverless GPU** | On-demand, autoscaling, scale-to-zero GPU inference | [serverless-gpu.md](serverless-gpu.md) |

> Serverless GPU depends heavily on the **NVIDIA GPU Operator** and an
> **autoscaler** (Knative/KEDA) — see [gpu-operator.md](gpu-operator.md) and
> [autoscaling.md](autoscaling.md).

```mermaid
graph TB
    REQ[Inference request] --> GW[Gateway / Queue]
    GW -->|scale 0..N| AUTOS[Autoscaler - Knative/KEDA]
    AUTOS -->|schedule on GPU| POD[Model pod - nvidia.com/gpu]
    POD --> GPUOP[NVIDIA GPU Operator - drivers, DCGM, MIG, time-slicing]
    POD -->|idle timeout| SCALE0[Scale to zero - frees GPU]
```
