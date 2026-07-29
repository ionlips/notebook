---
date: 2026-07-21
keywords: [gromacs, ichec, paclds]
---
# Install GROMACS locally

## qllm

Install CUDA as follows:

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

Install Open MPI with CUDA-aware support via UCX:

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

The [Getting good performance from mdrun] section of the GROMACS documentation
recommends compiling FFTW from source with the correct flags for your system:

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

We can now proceed to install GROMACS:

```shell
PKG=gromacs VER=2026.3
mkdir -p ~/.local/src/$PKG
curl -L https://ftp.gromacs.org/gromacs/gromacs-2026.3.tar.gz -o ~/.local/src/$PKG/$VER.tar.gz
tar \
    --one-top-level=$VER \
    --strip-components=1 \
    -C ~/.local/src/$PKG \
    -f ~/.local/src/$PKG/$VER.tar.gz \
    -x
cd ~/.local/src/$PKG/$VER
cmake \
    -B build \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=$HOME/.local/opt/$PKG-$VER \
    -DGMX_FFT_LIBRARY=fftw3 \
    -DGMX_GPU=CUDA \
    -DGMX_MPI=on
cmake --build build -j$(sysctl -n hw.ncpu)
cmake --install build
stow -d ~/.local/opt -t ~/.local $PKG-$VER
```

For the sake of completeness, the following is the output from CMake:

<!-- markdownlint-disable MD013 -->
```text
-- The C compiler identification is GNU 11.5.0
-- The CXX compiler identification is GNU 11.5.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found Python3: /usr/bin/python3.9 (found suitable version "3.9.25", minimum required is "3.9") found components: Interpreter Development Development.Module Development.Embed
-- Selected GPU FFT library - cuFFT
-- Found OpenMP_CXX: -fopenmp (found version "4.5")
-- Found OpenMP: TRUE (found version "4.5") found components: CXX
-- Performing Test CFLAGS_WARN_NO_MISSING_FIELD_INITIALIZERS
-- Performing Test CFLAGS_WARN_NO_MISSING_FIELD_INITIALIZERS - Success
-- Performing Test CFLAGS_EXCESS_PREC
-- Performing Test CFLAGS_EXCESS_PREC - Success
-- Performing Test CFLAGS_COPT
-- Performing Test CFLAGS_COPT - Success
-- Performing Test CFLAGS_NOINLINE
-- Performing Test CFLAGS_NOINLINE - Success
-- Performing Test CXXFLAGS_WARN_NO_MISSING_FIELD_INITIALIZERS
-- Performing Test CXXFLAGS_WARN_NO_MISSING_FIELD_INITIALIZERS - Success
-- Performing Test CXXFLAGS_EXCESS_PREC
-- Performing Test CXXFLAGS_EXCESS_PREC - Success
-- Performing Test CXXFLAGS_COPT
-- Performing Test CXXFLAGS_COPT - Success
-- Performing Test CXXFLAGS_NOINLINE
-- Performing Test CXXFLAGS_NOINLINE - Success
-- Looking for include file unistd.h
-- Looking for include file unistd.h - found
-- Looking for include file pwd.h
-- Looking for include file pwd.h - found
-- Looking for include file dirent.h
-- Looking for include file dirent.h - found
-- Looking for include file time.h
-- Looking for include file time.h - found
-- Looking for include file sys/time.h
-- Looking for include file sys/time.h - found
-- Looking for include file io.h
-- Looking for include file io.h - not found
-- Looking for include file sched.h
-- Looking for include file sched.h - found
-- Looking for include file xmmintrin.h
-- Looking for include file xmmintrin.h - found
-- Looking for gettimeofday
-- Looking for gettimeofday - found
-- Looking for sysconf
-- Looking for sysconf - found
-- Looking for nice
-- Looking for nice - found
-- Looking for fsync
-- Looking for fsync - found
-- Looking for _fileno
-- Looking for _fileno - not found
-- Looking for fileno
-- Looking for fileno - found
-- Looking for _commit
-- Looking for _commit - not found
-- Looking for sigaction
-- Looking for sigaction - found
-- Performing Test HAVE_BUILTIN_CLZ
-- Performing Test HAVE_BUILTIN_CLZ - Success
-- Performing Test HAVE_BUILTIN_CLZLL
-- Performing Test HAVE_BUILTIN_CLZLL - Success
-- Looking for clock_gettime in rt
-- Looking for clock_gettime in rt - found
-- Looking for feenableexcept in m
-- Looking for feenableexcept in m - found
-- Looking for fedisableexcept in m
-- Looking for fedisableexcept in m - found
-- Checking for sched.h GNU affinity API
-- Performing Test sched_affinity_compile
-- Performing Test sched_affinity_compile - Success
-- Looking for include file mm_malloc.h
-- Looking for include file mm_malloc.h - found
-- Looking for include file malloc.h
-- Looking for include file malloc.h - found
-- Checking for _mm_malloc()
-- Checking for _mm_malloc() - supported
-- Looking for posix_memalign
-- Looking for posix_memalign - found
-- Looking for memalign
-- Looking for memalign - not found
-- MPI is not compatible with thread-MPI. Disabling thread-MPI.
-- Found MPI_CXX: /home/ilipsiuc/.local/opt/open-mpi-5.0.10/lib/libmpi.so (found version "3.1")
-- Found MPI: TRUE (found version "3.1") found components: CXX
-- GROMACS library will use OpenMPI 5.0.10
-- Performing Test HAS_WARNING_NO_OLD_STYLE_CAST
-- Performing Test HAS_WARNING_NO_OLD_STYLE_CAST - Success
-- Performing Test HAS_WARNING_NO_CAST_QUAL
-- Performing Test HAS_WARNING_NO_CAST_QUAL - Success
-- Performing Test HAS_WARNING_NO_SUGGEST_OVERRIDE
-- Performing Test HAS_WARNING_NO_SUGGEST_OVERRIDE - Success
-- Performing Test HAS_WARNING_NO_SUGGEST_DESTRUCTOR_OVERRIDE
-- Performing Test HAS_WARNING_NO_SUGGEST_DESTRUCTOR_OVERRIDE - Success
-- Performing Test HAS_WARNING_NO_ZERO_AS_NULL_POINTER_CONSTANT
-- Performing Test HAS_WARNING_NO_ZERO_AS_NULL_POINTER_CONSTANT - Success
-- Using default binary suffix: "_mpi"
-- Using default library suffix: "_mpi"
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE
-- Looking for C++ include pthread.h
-- Looking for C++ include pthread.h - found
-- Performing Test TEST_ATOMICS
-- Performing Test TEST_ATOMICS - Success
-- Atomic operations found
-- Performing Test PTHREAD_SETAFFINITY
-- Performing Test PTHREAD_SETAFFINITY - Success
-- Detecting best SIMD instructions for this CPU
-- Checking for GCC x86 inline asm
-- Checking for GCC x86 inline asm - supported
-- Detected build CPU features - aes apic avx avx2 avx512f avx512cd avx512bw avx512vl avx512secondFMA clfsh cmov cx8 cx16 f16c fma htt intel lahf mmx msr nonstop_tsc pcid pclmuldq pdcm pdpe1gb popcnt pse rdrnd rdtscp sse2 sse3 sse4.1 sse4.2 ssse3 tdt x2apic
-- Detected build CPU brand - Intel(R) Xeon(R) Platinum 8260L CPU @ 2.40GHz
-- Performing Test C_march_skylake_avx512_FLAG_ACCEPTED
-- Performing Test C_march_skylake_avx512_FLAG_ACCEPTED - Success
-- Performing Test C_march_skylake_avx512_COMPILE_WORKS
-- Performing Test C_march_skylake_avx512_COMPILE_WORKS - Success
-- Performing Test CXX_march_skylake_avx512_FLAG_ACCEPTED
-- Performing Test CXX_march_skylake_avx512_FLAG_ACCEPTED - Success
-- Performing Test CXX_march_skylake_avx512_COMPILE_WORKS
-- Performing Test CXX_march_skylake_avx512_COMPILE_WORKS - Success
-- Detected best SIMD instructions for this CPU - AVX_512
-- Enabling 512-bit AVX-512 SIMD instructions using CXX flags:  -march=skylake-avx512
-- Performing Test _callconv___vectorcall
-- Performing Test _callconv___vectorcall - Failed
-- Performing Test _callconv___regcall
-- Performing Test _callconv___regcall - Failed
-- Performing Test _callconv_
-- Performing Test _callconv_  - Success
-- Found CUDAToolkit: /home/ilipsiuc/.local/opt/cuda-12.9.2/targets/x86_64-linux/include (found suitable version "12.9.86", minimum required is "12.1")
-- The CUDA compiler identification is NVIDIA 12.9.86 with host compiler GNU 11.5.0
-- Detecting CUDA compiler ABI info
-- Detecting CUDA compiler ABI info - done
-- Check for working CUDA compiler: /home/ilipsiuc/.local/opt/cuda-12.9.2/bin/nvcc - skipped
-- Detecting CUDA compile features
-- Detecting CUDA compile features - done
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_50
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_50 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_52
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_52 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_60
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_60 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_61
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_61 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_70
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_70 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_75
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_75 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_80
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_80 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_86
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_86 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_89
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_89 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_90
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_90 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_100
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_100 - Success
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_120
-- Performing Test GMX_CUDA_FLAG_ACCEPTS_SM_120 - Success
-- Compiling GROMACS for CUDA architectures: 50;52-real;60-real;61-real;70-real;75-real;80-real;86-real;89-real;90-real;100-real;120
-- Found OpenMP_CXX: -fopenmp (found version "4.5")
-- Found OpenMP_CUDA: -fopenmp (found version "4.5")
-- Found OpenMP: TRUE (found version "4.5") found components: CXX CUDA
-- Adding work-around for issue compiling CUDA code with glibc 2.23 string.h
-- Performing Test NVCC_HAS_USE_FAST_MATH
-- Performing Test NVCC_HAS_USE_FAST_MATH - Success
-- Performing Test NVCC_HAS_STATIC_GLOBAL_TEMPLATE_STUB_FALSE
-- Performing Test NVCC_HAS_STATIC_GLOBAL_TEMPLATE_STUB_FALSE - Success
-- Performing Test NVCC_HAS_PTXAS_WARN_DOUBLE_USAGE
-- Performing Test NVCC_HAS_PTXAS_WARN_DOUBLE_USAGE - Success
-- Performing Test NVCC_HAS_PTXAS_WERROR
-- Performing Test NVCC_HAS_PTXAS_WERROR - Success
-- Performing Test NVCC_HAS_DIAG_SUPPRESS_177
-- Performing Test NVCC_HAS_DIAG_SUPPRESS_177 - Success
-- Checking for mpi-ext.h header
-- Performing Test HAVE_MPI_EXT
-- Performing Test HAVE_MPI_EXT - Success
-- Performing Test MPI_SUPPORTS_CUDA_AWARE_DETECTION
-- Performing Test MPI_SUPPORTS_CUDA_AWARE_DETECTION - Success
-- Checking for MPI_SUPPORTS_CUDA_AWARE_DETECTION - yes
-- Torch not found. Neural network potential support will be disabled.
-- Detected build CPU vendor - Intel
-- Detected build CPU family - 6
-- Detected build CPU model - 85
-- Detected build CPU stepping - 7
-- Checking for 64-bit off_t
-- Checking for 64-bit off_t - present
-- Checking for fseeko/ftello
-- Checking for fseeko/ftello - present
-- Checking for SIGUSR1
-- Checking for SIGUSR1 - found
-- Checking for pipe support
-- Checking for system XDR support
-- Checking for system XDR support - not present
-- Checking for module 'fftw3f'
--   Found fftw3f, version 3.3.11
-- Looking for fftwf_plan_many_dft in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_plan_many_dft in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - found
-- Looking for fftwf_plan_many_dft_r2c in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_plan_many_dft_r2c in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - found
-- Looking for fftwf_plan_many_dft_c2r in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_plan_many_dft_c2r in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - found
-- Looking for fftwf_have_simd_sse in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_have_simd_sse in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - not found
-- Looking for fftwf_have_sse in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_have_sse in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - not found
-- Looking for fftwf_have_simd_sse2 in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so
-- Looking for fftwf_have_simd_sse2 in /home/ilipsiuc/.local/opt/fftw-3.3.11/lib/libfftw3f.so - found
-- Using external FFT library - FFTW3
-- Looking for sgemm_
-- Looking for sgemm_ - not found
-- Could NOT find BLAS (missing: BLAS_LIBRARIES)
-- Using GROMACS built-in BLAS.
-- Could NOT find LAPACK (missing: LAPACK_LIBRARIES)
    Reason given by package: LAPACK could not be found because dependency BLAS could not be found.

-- Using GROMACS built-in LAPACK.
-- No image conversion possible without ImageMagick
-- Performing Test HAS_WARNING_EVERYTHING
-- Performing Test HAS_WARNING_EVERYTHING - Failed
-- Performing Test HAVE_NO_DEPRECATED_COPY
-- Performing Test HAVE_NO_DEPRECATED_COPY - Success
-- Looking for open_memstream
-- Looking for open_memstream - found
-- Looking for dlopen
-- Looking for dlopen - found
-- Performing Test HAS_NO_STRINGOP_TRUNCATION
-- Performing Test HAS_NO_STRINGOP_TRUNCATION - Success
-- Could NOT find HDF5 (missing: HDF5_LIBRARIES HDF5_INCLUDE_DIRS C) (Required is at least version "1.10.7")
-- Performing Test HAS_WARNING_NO_CAST_FUNCTION_TYPE_STRICT
-- Performing Test HAS_WARNING_NO_CAST_FUNCTION_TYPE_STRICT - Success
-- Performing Test HAS_NO_UNUSED
-- Performing Test HAS_NO_UNUSED - Success
-- Performing Test HAS_NO_UNUSED_PARAMETER
-- Performing Test HAS_NO_UNUSED_PARAMETER - Success
-- Performing Test HAS_NO_MISSING_DECLARATIONS
-- Performing Test HAS_NO_MISSING_DECLARATIONS - Success
-- Performing Test HAS_NO_NULL_CONVERSIONS
-- Performing Test HAS_NO_NULL_CONVERSIONS - Success
-- Looking for inttypes.h
-- Looking for inttypes.h - found
-- Performing Test HAS_WARNING_NO_DEPRECATED_NON_PROTOTYPE
-- Performing Test HAS_WARNING_NO_DEPRECATED_NON_PROTOTYPE - Success
-- Performing Test COMPILER_HAS_HIDDEN_VISIBILITY
-- Performing Test COMPILER_HAS_HIDDEN_VISIBILITY - Success
-- Performing Test COMPILER_HAS_HIDDEN_INLINE_VISIBILITY
-- Performing Test COMPILER_HAS_HIDDEN_INLINE_VISIBILITY - Success
-- Performing Test COMPILER_HAS_DEPRECATED_ATTR
-- Performing Test COMPILER_HAS_DEPRECATED_ATTR - Success
-- Could NOT find Sphinx (missing: SPHINX_EXECUTABLE) (Required is at least version "4.0.0")
-- Could NOT find LATEX (missing: LATEX_COMPILER)
-- Configuring done (39.6s)
-- Generating done (0.7s)
-- Build files have been written to: /home/ilipsiuc/.local/src/gromacs/2026.3/build
```
<!-- markdownlint-enable MD013 -->

## MeluXina

Our respective project code is p201412. GROMACS was installed as follows:

```shell
salloc --account p201412 --reservation gpudev -N 1 -p gpu -q dev -t 01:00:00
```

### Prerequisites

Ensure Stow is installed as follows:

```shell
mkdir -p /project/home/p201412/.local/src/stow
curl -L https://ftp.gnu.org/gnu/stow/stow-2.4.1.tar.gz -o /project/home/p201412/.local/src/stow/2.4.1.tar.gz
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

Install CUDA as follows:

<!-- markdownlint-disable MD013 -->
```shell
VER=13.3.1
mkdir -p /project/home/p201412/.local/src/cuda
curl \
    -L https://developer.download.nvidia.com/compute/cuda/$VER/local_installers/cuda_${VER}_610.43.02_linux.run \
    -o /project/home/p201412/.local/src/cuda/$VER.run
sh /project/home/p201412/.local/src/cuda/$VER.run \
    --defaultroot=/project/home/p201412/.local/opt/cuda-$VER \
    --no-man-page \
    --silent \
    --toolkit \
    --toolkitpath=/project/home/p201412/.local/opt/cuda-$VER
```
<!-- markdownlint-enable MD013 -->

> [!IMPORTANT]
> Don't forget to add it to your `PATH`.

[^1]: Be careful with just copy and pasting it due to the inclusion of the
    `VER` variable.

[Getting good performance from mdrun]: https://manual.gromacs.org/current/user-guide/mdrun-performance.html
