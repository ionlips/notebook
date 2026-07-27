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

[^1]: Be careful with just copy and pasting it due to the inclusion of the
    `VER` variable.
