# NVIDIA Jetson AGX Orin: Developer Kit

## Key specifications:
- GPU: Ampere architecture, 2048 CUDA cores, 64 Tensor Cores
- CPU: 12-core Arm Cortex-A78AE, up to 2.2GHz
- Memory: 64GB LPDDR5 (dev kit default), 204 GB/s bandwidth, shared between CPU+GPU
- AI perf: up to 275 TOPS INT8 sparse (170 TOPS dense) / 85 TFLOPS FP16
- Extra accelerators: 2x DLA (deep learning accelerator), 1x PVA (vision accelerator) — separate from GPU, usually idle unless targeted explicitly
- Power modes: configurable 15W-60W (MAXN = full power/perf)
- Storage: eMMC + NVMe M.2 slot
- OS/SDK: JetPack (Ubuntu-based Linux for Tegra / L4T), CUDA, TensorRT, Triton all supported
- No MIG support (Ampere iGPU doesn't expose it) — CUDA MPS is supported (JetPack 6.1+ / CUDA 12.5+)

## USP:
- Unified memory — CPU and GPU share the same LPDDR5 pool, no PCIe copy between host/device like discrete GPUs
- Same CUDA/TensorRT software stack as datacenter GPUs, so models built/trained on real GPUs port over directly
- Multiple concurrent accelerators on one chip (GPU + 2x DLA + PVA + video codec) enabling parallel AI pipelines, not just one
- Server-class AI performance (275 TOPS) in a small, low-power (15-60W), embeddable form factor
- Long embedded product lifecycle/support (years), unlike consumer GPU generations that churn fast

## Industry use cases:
- Robotics & autonomous machines (delivery/logistics robots, AMRs, drones/UAVs)
- Manufacturing (Industry 4.0 automated optical inspection, defect detection on production lines)
- Smart cameras/video analytics (retail, smart cities, transportation object detection)
- Medical devices (robotic surgery, endoscopy, real-time diagnostic imaging)
- Autonomous vehicles (in-vehicle/roadside real-time perception)

## Official reading / references:
- Developer Kit User Guide: https://developer.nvidia.com/embedded/learn/jetson-agx-orin-devkit-user-guide/index.html
- Technical Brief (specs/architecture PDF): https://www.nvidia.com/content/dam/en-zz/Solutions/gtcf21/jetson-orin/nvidia-jetson-agx-orin-technical-brief.pdf
- JetPack SDK: https://developer.nvidia.com/embedded/jetpack
- Jetson Linux (L4T) Developer Guide: https://docs.nvidia.com/jetson/archives/r36.4.3/DeveloperGuide/
- DLA getting started: https://developer.nvidia.com/blog/getting-started-with-the-deep-learning-accelerator-on-nvidia-jetson-orin/
- DLA performance guide: https://developer.nvidia.com/blog/maximizing-deep-learning-performance-on-nvidia-jetson-orin-with-dla/
- Deep Learning Accelerator SW (GitHub): https://github.com/NVIDIA/Deep-Learning-Accelerator-SW
- CUDA MPS docs: https://docs.nvidia.com/deploy/mps/latest/index.html
- Triton Inference Server on Jetson: https://docs.nvidia.com/deeplearning/triton-inference-server/archives/triton-inference-server-2450/user-guide/docs/examples/jetson/README.html
- Jetson Developer Forums (support/community, official NVIDIA-run): https://forums.developer.nvidia.com/c/agx-autonomous-machines/jetson-embedded-systems/