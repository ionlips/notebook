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

The project requirements are as follows:

-   **plin3-r160**: 171,658 atoms with 14 replicas requiring 500 ns of
    simulation time. This results in 7,000 ns of simulation time. A PLUMED
    patch is required for this. Currently, PLUMED supports version 2024.3 of
    GROMACS but there is a [pull request] which adds support for version
    2026.3. Repplica exchange will be carried out every 1,000 steps.
-   **plin3-full**: 793,718 atoms with 23 replicas requiring 500 ns of
    simulation time. This results in 11,500 ns of simulation time. A PLUMED
    patch is required for this (i.e., same as previous system).
-   **plin3-r160-LD**: 1,136,688 atoms. Requires 500 ns of simulation time. A
    cuFFTMP-enabled build would work well here.
-   **plin3-full-LD**: 1,356,656 atoms. Requires 500 ns of simulation time. A
    cuFFTMp-enabled build would work well here.

Based on the above, the following builds of GROMACS will be done:

-   GROMACS version 2024.3 patched with PLUMED version 2.9.4 (since this is the
    only version of PLUMED available on MeluXina).
-   GROMACS version 2025.0 patched with PLUMED version 2.10.1.
-   GROMACS version 2026.3 patched with the aforementioned pull request branch
    (this may or may not work).
-   cuFFTMp-enabled GROMACS version 2026.3.
-   NVSHMEM-enabled GROMACS version 2026.3.

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

#### PLUMED version 2.9.4-patched GROMACS version 2024.3

Load the required modules:

```shell
module purge
module load env/release/2025.1

module load CMake/3.31.3-GCCcore-14.2.0
module load CUDA/12.8.0
module load PLUMED/2.9.4-foss-2025a
```

Proceed to install GROMACS as follows:

```shell
PKG=gromacs_plumed-2.9.4 VER=2024.3
mkdir -p /project/home/p201412/.local/src/$PKG
curl https://ftp.gromacs.org/gromacs/gromacs-2024.3.tar.gz -L -o /project/home/p201412/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/$PKG \
    -f /project/home/p201412/.local/src/$PKG/$VER.tar.gz \
    -x
cd /project/home/p201412/.local/src/$PKG/$VER
plumed patch -p
cmake \
    -B build \
    -DCMAKE_INSTALL_PREFIX=/project/home/p201412/.local/opt/$PKG-$VER \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DGMX_CUDA_TARGET_SM=80 \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=ON \
    -DGMX_OPENMP=ON \
    -DGMX_THREAD_MPI=OFF
cmake --build build -j $(nproc)
cmake --install build
stow -d /project/home/p201412/.local/opt -t /project/home/p201412/.local $PKG-$VER
```

#### PLUMED version 2.10.1-patched GROMACS version 2025.0

Load the required modules:

```shell
module purge
module load env/release/2025.1

module load foss/2025a
```

Proceed to install PLUMED as follows:

<!-- markdownlint-disable MD013 -->
```shell
PKG=plumed VER=2.10.1
mkdir -p /project/home/p201412/.local/src/$PKG
curl https://github.com/plumed/plumed2/releases/download/v2.10.1/plumed-src-2.10.1.tgz -L -o /project/home/p201412/.local/src/$PKG/$VER.tgz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/$PKG \
    -f /project/home/p201412/.local/src/$PKG/$VER.tgz \
    -x
cd /project/home/p201412/.local/src/$PKG/$VER
./configure \
    --prefix=/project/home/p201412/.local/opt/$PKG-$VER \
    --enable-rpath \
    LDFLAGS="-L$EBROOTFLEXIBLAS/lib -Wl,-rpath,$EBROOTFLEXIBLAS/lib" \
    LIBS="-lflexiblas"
make -j $(nproc) && make install
stow -d /project/home/p201412/.local/opt -t /project/home/p201412/.local $PKG-$VER
```
<!-- markdownlint-enable MD013 -->

Load the required modules:

```shell
module load CMake/3.31.3-GCCcore-14.2.0
module load CUDA/12.8.0
```

Proceed to install GROMACS as follows:

```shell
PKG=gromacs_plumed-2.10.1 VER=2025.0
mkdir -p /project/home/p201412/.local/src/$PKG
curl https://ftp.gromacs.org/gromacs/gromacs-2025.0.tar.gz -L -o /project/home/p201412/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/$PKG \
    -f /project/home/p201412/.local/src/$PKG/$VER.tar.gz \
    -x
cd /project/home/p201412/.local/src/$PKG/$VER
plumed patch -p
cmake \
    -B build \
    -DCMAKE_INSTALL_PREFIX=/project/home/p201412/.local/opt/$PKG-$VER \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DGMX_CUDA_TARGET_SM=80 \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=ON \
    -DGMX_OPENMP=ON \
    -DGMX_THREAD_MPI=OFF
cmake --build build -j $(nproc)
cmake --install build
stow -d /project/home/p201412/.local/opt -t /project/home/p201412/.local $PKG-$VER
```

<!-- markdownlint-disable MD013 -->
#### PLUMED version [Smith-Group/kenref-plumed2:gromacs-2026-patch]-patched GROMACS version 2026.3
<!-- markdownlint-enable MD013 -->

Load the required modules:

```shell
module purge
module load env/release/2025.1

module load foss/2025a
```

Proceed to install PLUMED as follows:

```shell

```

#### NVSHMEM-enabled build

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

#### cuFFTMp-enabled build

Load the required modules:

```shell
module load NVHPC/25.3-CUDA-12.8.0
module load foss/2025a
module load CUDA/12.8.0
module load UCX-CUDA/1.18.0-GCCcore-14.2.0-CUDA-12.8.0
module load CMake/3.31.3-GCCcore-14.2.0
```

The [cuFFTMp-enabled GROMACS installation guide] states that NVSHMEM must be
available via `LD_LIBRARY_PATH` prior to installing a cuFFTMp-enabled build. On
MeluXina, this is available here:
`$NVHPC/Linux_x86_64/25.3/comm_libs/12.8/nvshmem`; add it as follows:

```shell
export LD_LIBRARY_PATH=$NVHPC/Linux_x86_64/25.3/comm_libs/12.8/nvshmem/lib:$LD_LIBRARY_PATH
```

> [!NOTE]
> `NVHPC` is defined after loading NVHPC/25.3-CUDA-12.8.0. We used
> `EBROOTNVSHMEM` for NVSHMEM above, but note that since these are compiled via
> EasyBuild, both variables result in the same location.

Proceed to install GROMACS as follows:

```shell
PKG=gromacs_cufftmp VER=2026.3
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
    -DcuFFTMp_ROOT=$NVHPC/Linux_x86_64/25.3/math_libs/12.8 \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=ON \
    -DGMX_OPENMP=ON \
    -DGMX_USE_CUFFTMP=ON
cmake --build build -j $(nproc)
cmake --install build
stow -d /project/home/p201412/.local/opt -t /project/home/p201412/.local $PKG-$VER
```

#### GROMACS with PLUMED patch for replica exchange

As per [PLUMED's documentation], the newest version of GROMACS does not support
a PLUMED patch. Thus, we choose GROMACS 2024.3 for this patch. As per the
[code-specific notes], we should ensure that the following CMake options are
disabled/enabled: `-DGMX_MPI=ON` and `-DGMX_THREAD_MPI=OFF`.

Load the required modules:

```shell
module load foss/2025a
module load CUDA/12.8.0
module load UCX-CUDA/1.18.0-GCCcore-14.2.0-CUDA-12.8.0
module load CMake/3.31.3-GCCcore-14.2.0
module load PLUMED/2.9.4-foss-2025a
```

Proceed to install GROMACS as follows:

```shell
PKG=gromacs_plumed VER=2024.3
mkdir -p /project/home/p201412/.local/src/$PKG
curl https://ftp.gromacs.org/gromacs/gromacs-2024.3.tar.gz -L -o /project/home/p201412/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C /project/home/p201412/.local/src/$PKG \
    -f /project/home/p201412/.local/src/$PKG/$VER.tar.gz \
    -x
cd /project/home/p201412/.local/src/$PKG/$VER
plumed patch -p
cmake \
    -B build \
    -DCMAKE_CUDA_ARCHITECTURES=80 \
    -DCMAKE_INSTALL_PREFIX=/project/home/p201412/.local/opt/$PKG-$VER \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=ON \
    -DGMX_OPENMP=ON \
    -DGMX_THREAD_MPI=OFF
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

[pull request]: <https://github.com/plumed/plumed2/pull/1439>
[Smith-Group/kenref-plumed2:gromacs-2026-patch]: <https://github.com/Smith-Group/kenref-plumed2/tree/gromacs-2026-patch>
[cuFFTMp-enabled GROMACS installation guide]: <https://manual.gromacs.org/current/install-guide/index.html#using-cufftmp>
[PLUMED's documentation]: <https://www.plumed.org/doc-v2.9/user-doc/html/index.html>
[code-specific notes]: <https://www.plumed.org/doc-v2.9/user-doc/html/gromacs-2024-3.html>
