

# Elasto-Viscoplastic Fast Fourier Transform-based (EVPFFT) Micromechanical Solver

EVPFFT is a parallel implementation of an elasto-viscoplastic fast Fourier transform-based (EVPFFT) micromechanical solver to facilitate computationally efficient crystal plasticit modeling of polycrystalline materials. Emphasis is placed on performance portability to allow for a single implementation of EVPFFT to run optimally on variety of homogeneous architectures that are comprised of multi-core central processing units(CPUs), and on heterogeneous computer architectures comprised of multi-core CPUs and graphics processing units (GPUs) from different vendors. To achieve this, we utilize MATAR, a C++ software library that allows the straightforward creation and usage of multidimensional dense or sparse matrix and array data structures that are also portable across disparate architectures using Kokkos, a performance portable library. Also, we utilize the message passing interface (MPI) to distribute the computational workload amongst the processors. The heFFTe (Highly Efficient FFT for Exascale) library is used to facilitate the performance portability of the fast Fourier transforms (FFTs) computation.

# Building EVPFFT as a standalone program

EVPFFT depends on the following to build:

1. MPI
2. [MATAR](https://github.com/lanl/MATAR)
3. [Kokkos](https://github.com/kokkos/kokkos)
4. [HeFFTe](https://github.com/icl-utk-edu/heffte) (FFTW,CUFFT,ROCFFT)
5. [HDF5](https://www.hdfgroup.org/solutions/hdf5/)

To make it easy to build EVPFFT, we have included `scripts/build_scripts/build_evpfft.sh` which when executed will download and install all required dependencies and EVPFFT. It is important to note that EVPFFT depends on three main libraries which can be time consuming to download from scratch namely, MPI, FFTW (MPI version), and HDF5 (MPI version). Therefore the user is advised to download these packages themselves either using `sudo apt install` of Anaconda package manager.


## Building EVPFFT 
Install HDF5 (MPI version) and FFTW (MPI version) with `sudo apt install` which will install the libraries in the default location. Then run the build script as above.

If you would like to build HDF5 and FFTW from scratch then use `--build_hdf5` and `--build_fftw` options of the build script.

If you do not want to build HDF5 and FFTW from scratch because you already have a version on your system remove the `--build_hdf5` and `--build_fftw` option from the command. If your installed HDF5 and FFTW are located in a directory other than the default linux directories for libraries, then specify the following before running the build script.

```
export HDF5_ROOT=/path/to/where/HDF5/lib/and/include/folders/are/located
export FFTW_ROOT=/path/to/where/FFTW/lib/and/include/folders/are/located
```

# Using EVPFFT as a standalone program

To get help on how to run `evpfft` use the `-h` or `--help` opton:

```
./evpfft --help
```

Which gives the following output:

```
Required arguments:
  -f, --infile   <path>     input file (full path)
...
```

We have provided example input files in the `example_input_files` directory. In summary, EVPFFT needs these files:

1. input file (see `example_input_files/example_evpfft_standalone_inputfile.txt`)
2. plastic parameters file (see `example_input_files/example_plastic_parameters.txt`)
3. elastic parameters file (see `example_input_files/example_elastic_parameters.txt`)
4. microstructure RVE file (see `example_input_files/random_microstructure_8x8x8.txt`)

With all these files available you can now run EVPFFT as:

```
mpirun -np 4 evpfft --infile=example_evpfft_standalone_inputfile.txt
```
The user must update the file paths inside `example_evpfft_standalone_inputfile.txt` to be those on thier machine.  The absolute file path can be found by typing in the terminal `pwd` where the input file resides.

# Additional Information

These information are for users interested in more customization of the build process. You can add to the `cmake` options in `build_evpfft.sh` as needed.

Scripts for building each dependency are available in the `install_scripts` folder and can be run individually.

Note that MATAR is used as an include header file.

If you already have an installation of HDF5 (parallel version) on you system you can use your installation, just comment out the part of the `build_evpfft.sh` that calls `install_hdf5.sh`.

During the installation of HeFFTe libray we assume the required FFT backend (FFTW, CUFFT, ROCM) is installed on the system. If this is not the case then look at the [HeFFTe installation page](https://github.com/icl-utk-edu/heffte) for more information.

The EVPFFT `CMakeLists.txt` uses the following default values to build EVPFFT:

```
  TWO_SIGN_SLIP_SYSTEMS: OFF
  NON_SCHMID_EFFECTS: OFF
  ABSOLUTE_NO_OUTPUT: OFF
  ENABLE_PROFILE: OFF
```

To change these default options include the `-D OPTION=<value>` in the `cmake` option, E.g. `-D ABSOLUTE_NO_OUTPUT=ON` in the `build_evpfft.sh`.

# Using EVPFFT for Lattice Structure Homogenization

Example for input files needed to run EVPFFT for lattice structure homogenization is shown in `example_input_files/lattice_input_files`. In that file you will see how to set up evpft input file, elastic and plastic parameter files.

Provide a vtk file type that contains information about which grid point is solid (1) or void (0), example is shown in `example_input_files/lattice_input_files/void_in_solid.vtk`.

Run EVPFFT as:
```
  mpirun -n 1 evpfft -f tension_11.txt -m 2
```
the `-m 2` option tells EVPFFT to use the vtk lattice file microstructure file type.

# Fierro
**Fierro** (LANL code number C21030) is a modern C++ code designed to simulate quasi-static solid mechanics problems and transient, compressible material dynamic problems with Lagrangian methods, which have meshes with constant mass elements that move with the material, or with Eulerian methods, which have stationary meshes.  **Fierro** is designed to aid a) material model research that has historically been done using commercial implicit and explicit finite element codes, b) numerical methods research, and c) computer science research.  The linear Lagrangian finite element methods in **Fierro** supports user developed material models.  **Fierro** is built on the **ELEMENTS** library that supports a diverse suite of element types, including high-order elements, and quadrature rules. The mesh class within the **ELEMENTS** library is designed for efficient calculations on unstructured meshes and to minimize memory usage.  **Fierro** is designed to readily accommodate a range of numerical methods including continuous finite element, finite volume, and discontinuous Galerkin methods.  **Fierro** is designed to support explicit and implicit time integration methods as well as implicit optimization methods.  

