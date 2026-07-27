---
date: 2026-07-21
keywords: [gromacs, ichec, paclds]
---
# Install GROMACS locally

1.  Install CUDA as follows:

    <!-- markdownlint-disable MD013 -->
    ```shell
    VER=12.9.2
    mkdir -p ~/.local/src/cuda
    curl \
        -L https://developer.download.nvidia.com/compute/cuda/$VER/local_installers/cuda_${VER}_575.57.08_linux.run \
        -o ~/.local/src/cuda/$VER.run
    sh ~/.local/src/cuda/$VER.run \
        --defaultroot=$HOME/.local/opt/cuda-$VER \
        --no-man-page \
        --silent \
        --toolkit \
        --toolkitpath=$HOME/.local/opt/cuda-$VER
    ```
    <!-- markdownlint-enable MD013 -->

    Ensure to add the following to your `.rc` file (i.e., `.bashrc`):[^1]

    ```text
    export CUDA_HOME=$HOME/.local/opt/cuda-$VER
    export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
    export PATH=$CUDA_HOME/bin:$PATH
    ```

1.  Install Open MPI with CUDA-aware support via UCX:

    <!-- markdownlint-disable MD013 -->
    ```shell
    PKG=ucx VER=1.21.0
    mkdir -p ~/.local/src/$PKG
    curl \
        -L https://github.com/openucx/ucx/releases/download/v1.21.0/ucx-1.21.0.tar.gz \
        -o ~/.local/src/$PKG/$VER.tar.gz
    tar \
        --one-top-level=$VER \
        --strip-components=1 \
        -C ~/.local/src/$PKG \
        -f ~/.local/src/$PKG/$VER.tar.gz \
        -x
    mkdir -p ~/.local/src/$PKG/$VER/build && cd $_
    ../contrib/configure-release \
        --prefix=$HOME/.local/opt/$PKG-$VER \
        -with-cuda=$HOME/.local/opt/cuda-12.9.2
    make -j$(sysctl -n hw.ncpu) && make install
    stow -d ~/.local/opt -t ~/.local $PKG-$VER
    PKG=open-mpi VER=5.0.10
    mkdir -p ~/.local/src/$PKG
    curl \
        -L https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.10.tar.gz \
        -o ~/.local/src/$PKG/$VER.tar.gz
    tar \
        --one-top-level=$VER \
        --strip-components=1 \
        -C ~/.local/src/$PKG \
        -f ~/.local/src/$PKG/$VER.tar.gz \
        -x
    mkdir -p ~/.local/src/$PKG/$VER/build && cd $_
    ../configure \
        --prefix=$HOME/.local/opt/$PKG-$VER \
        --with-cuda=$HOME/.local/opt/cuda-12.9.2 \
        --with-ucx=$HOME/.local/opt/ucx-1.21.0
    make -j$(sysctl -n hw.ncpu) && make install
    stow -d ~/.local/opt -t ~/.local $PKG-$VER
    ```
    <!-- markdownlint-enable MD013 -->

1.  The [Getting good performance from mdrun] section of the GROMACS
    documentation recommends compiling FFTW from source with the correct flags
    for your architecture:

    ```shell
    PKG=fftw VER=3.3.11
    mkdir -p ~/.local/src/$PKG
    curl -L https://www.fftw.org/fftw-3.3.11.tar.gz -o ~/.local/src/$PKG/$VER.tar.gz
    tar \
        --one-top-level=$VER \
        --strip-components=1 \
        -C ~/.local/src/$PKG \
        -f ~/.local/src/$PKG/$VER.tar.gz \
        -x
    mkdir -p ~/.local/src/$PKG/$VER/build && cd $_
    ../configure \
        --enable-avx \
        --enable-avx2 \
        --enable-avx512 \
        --enable-float \
        --enable-shared \
        --enable-sse2 \
        --prefix=$HOME/.local/opt/$PKG-$VER
    make -j$(sysctl -n hw.ncpu) && make install
    stow -d ~/.local/opt -t ~/.local $PKG-$VER
    ```

[^1]: Be careful with just copy and pasting it due to the inclusion of the
    `VER` variable.

[Getting good performance from mdrun]: https://manual.gromacs.org/current/user-guide/mdrun-performance.html
