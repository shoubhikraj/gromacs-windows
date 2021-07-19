# gromacs-windows
Precompiled Windows binaries for GROMACS are provided in the releases. These binaries can be run on Windows machines (Windows 10 and 8, and possibly Windows 7) without any further compilation.

The binaries use AVX2_256 instruction sets and were compiled with Microsoft Visual Studio 2019 (compiler v19.28.29913) for 64-bit machines (x64).

## Build procedure

Requires Microsoft Visual Studio 2019 (or 2017) and Intel MKL / FFTW3 also (optionally) Ninja

If using Intel MKL, run from "x64 Native Tools" command prompt in the directory where GROMACS source is extracted:

```
set CC=cl
set CXX=cl
set CFLAGS=-DMKL_ILP64
set CXXFLAGS=-DMKL_ILP64

cmake -GNinja -S. -B./build -DGMX_GPU=off -DGMX_FFT_LIBRARY=mkl -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=off -DMKL_INCLUDE_DIR="C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/include" -DMKL_LIBRARIES="C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_intel_ilp64.lib;C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_sequential.lib;C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_core.lib"

cd build
ninja -j 4
```
Change the paths if you did not use the default installation paths when installing Intel oneAPI. If you do not have Ninja installed, then replace `-GNinja` with `-G"NMake Makefiles"` and at the end instead of running `ninja -j 4`, run `nmake`.
