![GitHub All Releases](https://img.shields.io/github/downloads/ShoubhikRaj/gromacs-windows/total)

# Gromacs-windows
Precompiled Windows binaries for GROMACS are provided in the releases. These binaries can be run on Windows machines (Windows 10 and 8, and possibly Windows 7) without any further compilation.

The binaries use AVX2_256 instruction sets and were compiled with Microsoft Visual Studio 2019 (compiler v19.28.29913) for 64-bit machines (x64).

## How to use:
1) Download an archive from the [release section](https://github.com/ShoubhikRaj/gromacs-windows/releases). There are multiple versions e.g. compiled with MPI, or with OpenMP. Choose the one that suits you.
2) Unpack the archive in any location on your PC, keeping in mind that the path shouldn't ideally contain spaces, just to be safe.
3) Download and install the Visual C++ redistributables [here](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)
4) (Only for the MPI version) Download and install MS-MPI v10.1.2 from [here](https://www.microsoft.com/en-us/download/details.aspx?id=100593).

When running gromacs, double click the `GMXRC.bat` file amd a command prompt will come up with the GROMACS executable and data files in PATH. From that command prompt, you can run `gmx <commands>` to run GROMACS. For the MPI-parallel version, the executable is `gmx_mpi`, but it has to be launched with MS-MP by running `mpiexec -np 4 gmx_mpi <commands>` (to run 4 parallel processes).

## Compilation details

This is just the technical details of how the binaries were generated, no need to read further if you are only using the binaries.

### Requirements

1. Microsoft Visual Studio 2019 (only install the compiler & toolset for Desktop development)
2. Intel MKL (from [Intel oneAPI base toolkit](https://software.intel.com/content/www/us/en/develop/tools/oneapi/base-toolkit/download.html)) OR, [FFTW3](http://www.fftw.org/download.html)
3. (Optional) [Ninja](https://mesonbuild.com/Getting-meson.html) (Get the msi installer for meson-ninja)

### Build procedure

Requires Microsoft Visual Studio 2019 (or 2017) and Intel MKL / FFTW3 also (optionally) Ninja

If using Intel MKL, run from "x64 Native Tools" command prompt in the directory where GROMACS source is extracted:

```
set CC=cl
set CXX=cl

cmake -GNinja -S. -B./build -DGMX_GPU=off -DGMX_FFT_LIBRARY=mkl -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=off -DMKL_INCLUDE_DIR="C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/include" -DMKL_LIBRARIES="C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_intel_lp64.lib;C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_sequential.lib;C:/Program Files (x86)/Intel/oneAPI/mkl/2021.1.1/lib/intel64/mkl_core.lib"

cd build
ninja -j 4
```
Change the paths if you did not use the default installation paths when installing Intel oneAPI. If you do not have Ninja installed, then replace `-GNinja` with `-G"NMake Makefiles"` and at the end instead of running `ninja -j 4`, run `nmake`.

If using FFTW3 library, run from "x64 Native Tools" command prompt:
```
set CC=cl
set CXX=cl

cmake -GNinja -S. -B./build -DGMX_GPU=off -DGMX_FFT_LIBRARY=fftw3 -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=off -DFFTWF_INCLUDE_DIR="G:/Installer/gromacs-2021.1/fftw/fftw-3.3.5-dll64" -DFFTWF_LIBRARY="G:/Installer/gromacs-2021.1/fftw/fftw-3.3.5-dll64/libfftw3f-3.lib"

cd build
ninja -j 4
```
The `-DFFTWF_INCLUDE_DIR` and `-DFFTWF_LIBRARY` paths have to be changed according to where you have the FFTWF files.

Also not the use of front slashes in the paths, using backslash will cause issues as CMake seems to treat backslashes as escape character, even on Windows.

If compiling with MS-MPI, just add the extra flag `-DGMX_MPI=on` to the CMake build command. CMake should be able to detect MS-MPI from environment variables. Note that turning on MPI parallelisation will turn off the default thread-MPI of by GROMACS. (OpenMP parallelisation will still work)

----

If there are any problems with the binaries, feel free to post an issue.

---

Gromacs source code and releases can be found in https://gitlab.com/gromacs/gromacs. The Intel MKL libraries can be obtained for free as part of the Intel oneAPI base toolkit from https://software.intel.com/content/www/us/en/develop/tools/oneapi/base-toolkit.html. As Gromacs is LGPL and Intel MKL license is not compatible with that, I am not allowed to distribute binaries linked with MKL.
