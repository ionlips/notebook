---
date: 2026-11-04
keywords: [amd, meeting]
---
# Unlocking HPC Performance with AMD tools and GPUs: Day 1 (beginner)

`ssh ionlipsiuc@aac6.amd.com -i ~/.ssh/id_ed25519` is used to access the
system. Use `aac7` for the Cray-based system.

APUs simplify porting code from CPU to GPU.

Access to AAC6 and AAC7 lasts for the entire year.

<https://github.com/amd/HPCTrainingExamples>.

APUs have both CPUs and GPUs on the silicon.

MI300A first APU.

Host is the CPU; device is the GPU.

Host offloads to the device via kernels.

A memory model defines the rules for the synchronisation of memory
modifications between threads, compute hardware, and cache.

In discrete GPU systems, CPU and GPU memory spaces are separate and data needs
to be moved between the two spaces.

Managed memory is done by the OS.

`export HSA_XNACK=1` for managed memory.

`rocminfo | grep xnack`.

OpenMP is recommended for AMD GPUs (pragma-based languages).

HIP is a portable layer on top of ROCm and CUDA.

Converting from CUDA to HIP is easy.

Kokkos and RAJA are higher-level performance portability frameworks.

ROCm version 6.1 allows the use of C++17's native parallelism.

`git clone https://github.com/amd/HPCTrainingExamples.git`.

`ManagedMemory` is the fist hands-on exercise.
