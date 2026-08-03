---
date: 2026-07-28
keywords: []
---
# PACLDS

## Table of contents

## Installation

### MeluXina

> [!TIP]
> Configure a development environment using the following command:
>
> ```shell
> salloc --account p201412 --reservation gpudev -N 1 -p gpu -q dev -t 01:00:00
> ```

#### Prerequisites

Stow must be installed as follows:

```shell
mkdir -p /project/home/p201412/.local/opt /project/home/p201412/.local/src/stow
curl https://ftp.gnu.org/gnu/stow/stow-2.4.1.tar.gz -L -o /project/home/p201412/.local/src/stow/2.4.1.tar.gz
tar \
    --one-top-level=2.4.1 \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/stow \
    -f /project/home/p201412/.local/src/stow/2.4.1.tar.gz \
    -x
mkdir -p /project/home/p201412/.local/src/stow/2.4.1/build && cd $_
../configure --prefix=/project/home/p201412/.local
make && make install
```

Load the required modules:

```shell
module load env/release/2025.1
module load foss/2025a
module load CUDA/12.8.0
module load NVSHMEM/3.4.5-gompi-2025a-CUDA-12.8.0
module load CMake/3.31.3-GCCcore-14.2.0
module load Python/3.13.1-GCCcore-14.2.0
```

For GPU-aware MPI, load the following too:

```shell
module load UCX-CUDA/1.18.0-GCCcore-14.2.0-CUDA-12.8.0
module load GDRCopy/2.4.4-GCCcore-14.2.0
module load ompi-configs/UCX-CUDA
```

Proceed to install GROMACS as follows:

```shell
PKG=gromacs VER=2026.3
mkdir -p /project/home/p201412/.local/src/$PKG
curl https://ftp.gromacs.org/gromacs/gromacs-2026.3.tar.gz -L -o /project/home/p201412/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/$PKG \
    -f /project/home/p201412/.local/src/$PKG/$VER.tar.gz \
    -x
cd /project/home/p201412/.local/src/$PKG/$VER
cmake \
    -B build \
    -DCMAKE_CUDA_ARCHITECTURES=80 \
    -DCMAKE_INSTALL_PREFIX=/project/home/p201412/.local/opt/$PKG-$VER \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=ON \
    -DGMX_NVSHMEM=ON \
    -DGMX_OPENMP=ON \
    -DGMX_USE_CUFFTMP=OFF \
    -DNVSHMEM_ROOT=$EBROOTNVSHMEM
cmake --build build -j $(nproc)
cmake --install build
stow -d /project/home/p201412/.local/opt -t /project/home/p201412/.local $PKG-$VER
```

## qllm

### `data/test_code` on qllm

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

Once complete, run `grep "Performance:" bench_4gpu.log`:

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
