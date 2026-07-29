---
date: 2026-07-28
keywords: []
---
# PACLDS

## `data/test_code` on qllm

`cd` into
`/ichec/work/staff/ilipsiuc/paclds/data/test_code/simulations_files`.

Run the following:

```shell
mpirun \
    --bind-to none \
    -np 4 \
    gmx_mpi mdrun \
        -bonded gpu \
        -deffnm bench_4gpu \
        -nb gpu \
        -noconfout \
        -npme 1 \
        -nsteps 50000 \
        -nstlist 200 \
        -ntomp 24 \
        -pin on \
        -pinstride 1 \
        -pme gpu \
        -resetstep 25000 \
        -s dopc-dope_dogl_3-1_T298.tpr \
        -update gpu
```

Once complete, run `grep -A2 "Performance:" bench_4gpu.log`. This gave us the following:

```shell
Performance:       64.321        0.373        2.687           59.208
```

Attempt a run using a single GPU:

```shell
CUDA_VISIBLE_DEVICES=0 \
mpirun \
    --bind-to none \
    -np 1 \
    gmx_mpi mdrun \
        -bonded gpu \
        -deffnm bench_1gpu \
        -nb gpu \
        -noconfout \
        -nsteps 50000 \
        -nstlist 200 \
        -ntomp 24 \
        -pin on \
        -pinstride 1 \
        -pme gpu \
        -resetstep 25000 \
        -s dopc-dope_dogl_3-1_T298.tpr \
        -update gpu
```
