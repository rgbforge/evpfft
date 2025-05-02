# Fierro
**Fierro** (LANL code number C21030) is a modern C++ code designed to simulate quasi-static solid mechanics problems and transient, compressible material dynamic problems with Lagrangian methods, which have meshes with constant mass elements that move with the material, or with Eulerian methods, which have stationary meshes.  **Fierro** is designed to aid a) material model research that has historically been done using commercial implicit and explicit finite element codes, b) numerical methods research, and c) computer science research.  The linear Lagrangian finite element methods in **Fierro** supports user developed material models.  **Fierro** is built on the **ELEMENTS** library that supports a diverse suite of element types, including high-order elements, and quadrature rules. The mesh class within the **ELEMENTS** library is designed for efficient calculations on unstructured meshes and to minimize memory usage.  **Fierro** is designed to readily accommodate a range of numerical methods including continuous finite element, finite volume, and discontinuous Galerkin methods.  **Fierro** is designed to support explicit and implicit time integration methods as well as implicit optimization methods.  


## Computer implementation
**Fierro** is implemented in C++ following modern programming practices.  **Fierro** leverages the unique features of the **ELEMENTS** library, as such, this code serves as an example for solving a system of partial differential equations using the mesh class and geometric functions within the **ELEMENTS** library.  **Fierro** registers state at material points within the element, registers polynomial fields in the element, and registers kinematic variables at element vertices.  The routines for the state are designed for highly efficient computations and to minimize memory usage.  The loops are written to aid fine-grained parallelization and to allow vectorization. **Fierro** is a light-weight software application, and cleanly written following modern programming practices, so it useful for researching computer science based technologies for software performances portability.  

## Spatial discretization methods 
**Fierro** has an established conservative low-order Lagrangian finite element method, a low-order Lagrangian cell-centered finite volume method, and an arbitrary-order Lagrangian Discontinuous Galerkin method for solving the governing equations (e.g., mass, momentum, and energy evolution equations) for compressible material dynamics using unstructured hexahedral meshes.  These methods are combined with a multidirectional approximate Riemann solver (MARS) for improved accuracy on smooth flows and stable solutions near velocity discontinuities and large gradients in a flow. **Fierro** is designed for both low and high-order Lagrangian methods research and development but other types of numerical methods can be readily added to the code.  Likewise, other high-order methods can be studied within the code because it is built upon the **ELEMENTS** library that supports high-order elements and high-order quadrature rules.  Numerical methods are being added to **Fierro** to simulate quasi-static solid mechanics problems.  Likewise, direct Eulerian hydrodynamic methods can be investigated using **Fierro**.

## Temporal discretization methods 
**Fierro** supports a range of published multi-step time integration methods. The code has an explicit multi-step Runge Kutta time integration method. Implicit time integration methods can be implemented in **Fierro**.

# Usage

After installing the finite element solvers, it gives you access to `fierro-mesh-builder`,`fierro-parallel-explicit`,`fierro-parallel-implicit`, and the `fierro-voxelizer` executables. These can be run by calling the appropriate executable with the desired input. For example, to call the parallel explicit hydrodynamics solver, use the following command:
```
fierro-parallel-explicit input.yaml
```
Sample yaml input files for the explicit finite element solver can be found at:
`./src/Parallel-Solvers/Parallel-Explicit/Example_Yaml_Scripts`

The implicit solver and topology optimization modules can be called using:
```
fierro-parallel-implicit input.yaml
```

Sample yaml input files for the implicit finite element solver and topology optimization problem setups can be found at:
`./src/Parallel-Solvers/Implicit-Lagrange/Example_Yaml_Scripts`

The GUI can be run in the anaconda enviroment by typing:
```
fierro-gui
```
The anaconda distributions of Fierro are located [here](https://anaconda.org/FierroMechanics).



## Material models
The classical ideal gas model is the only material model implemented in the code, and it is useful for verification tests of the software and simple gas dynamic simulations. The linear Lagrangian finite element methods for explicit material dynamics have an interface to user developed material models. The interface is to enable **Fierro** to be used for model research and development that has historically been done with commercial explicit finite element codes. 

To include your own custom material models, you need to implement them under `Fierro/Parallel-Solvers/User-Material-Interface` and re-build the project.
Steps:
1. Create an anaconda environment for your build
2. Install **Fierro** anaconda dependencies with `conda install elements fierro-trilinos-cpu elements -c fierromechanics`
3. [Clone the code](#cloning-the-code)
4. [Build the code](#building-the-code)

Now, if all went correctly, you should be able to run your custom **Fierro** build by calling the executable located at `Fierro/build/bin/fierro`. The executables can be installed into your system directories with the `make install` command as well.

# Cloning the code
If the user has set up ssh keys with GitHub, type
```
git clone --recursive ssh://git@github.com/lanl/Fierro.git
```
The code can also be cloned using
```
git clone --recursive https://github.com/lanl/Fierro.git
```

# Building the code
Building the code from source allows you to compile with more targeted hardware optimizations that could offer a potentially faster executable. 
To build it yourself, run the following from the root directory. The native CPU architecture will automatically be taken into account. 

GPU hardware will be leveraged according to the distribution of Trilinos that **Fierro** is built against.
You are welcome to only compile the desired solver, or all of the currently available ones. 

## Building dependencies
**Fierro** depends on both ELEMENTS and Trilinos. If you are building **Fierro** for hardware optimizations, you should also build ELEMENTS from source. ELEMENTS is included in the **Fierro** source distribution and building ELEMENTS is enabled by default when building **Fierro**.

As for Trilinos, we recommend installing the Anaconda package for the desired build into a new Anaconda environment to satisfy **Fierro**'s dependency rather than building it from source. If you do wish to build it from source, however, sample build scripts for Trilinos can be found in `Fierro/Trilinos-Build-Scripts`. Build scripts for all Anaconda packages can be found in `Fierro/Anaconda-Packages/`.

## Alternative Build Workflows
In addition to the primary build workflow described above, there are build scripts for a variety of alternative workflows. These scripts can be found under `Fierro/scripts`. Although there are multiple scripts shown in this directory, the only one that will be run directly by the user is ***build-fierro.sh***
### Building the explicit and implicit Lagrangian methods with Trilinos+Kokkos
Explicit Lagrangian codes are being added to the repository that are written using MATAR+Kokkos and run with fine-grained parallellism on multi-core CPUs and GPUs.  Build scripts are provided for each Lagrangian code, and those scripts follow those used in the [MATAR](https://github.com/lanl/MATAR/) GitHub repository. The scripts to build the Lagrangian codes (that use MATAR+Kokkos) are in the scripts folder.  The user must update the modules loaded by the build scripts (for the compiler etc.), and then type
Immediate help with all scripts can be had running
```
source {path-to-repo}/build-fierro.sh --help
```
The default run will build the **full-app** settting up the **Explicit*** solver with **serial** Kokkos for a **linux** computer (non-HPC machine)
Running with ```--help``` option will list all parameters and their accepted arguments
If the scripts fail to build a Lagrangian code, then carefully review the modules used and the computer architecture settings.  
For building on a Mac, please see the extra info in the BUILD.md
#A more lenghtly discussion of the build scripts is provided in the MATAR GitHub repository. 

For help with [Trilinos](https://github.com/trilinos/Trilinos/wiki/New-Trilinos-Developers)
For help with [Kokkos](https://github.com/kokkos/kokkos/wiki/Compiling) compilation

### Updating submodules
The ELEMENTS library and MATAR library can be updated to the newest release using
```
git submodule update --remote --merge
```

# Contributing to Fierro
As an open source software team, we greatly appreciate any and all community involvement. There are many ways to contribute to Fierro, from tidying up code sections to implementing novel solvers. 
To streamline the integration of community contributions, we follow the following guidelines.

## Writing commit messages

Write your commit messages using these standard prefixes:
- `BUG:` Fix for runtime crash or incorrect result
- `COMP:` Compiler error or warning fix
- `DOC:` Documentation change
- `ENH:` New functionality
- `PERF:` Performance improvement
- `STYLE:` No logic impact (indentation, comments)
- `WIP:` Work In Progress not ready for merge

The commit message should assist in the review process and allow future developers to understand the change.  To that end, please follow these few rules:

1. Try to keep the subject line below 72 characters, ideally 50.
2. Be concise, but honor the change.
3. If the change is uncharacteristically large (or diverse), consider breaking the change into multiple distinct commits.
4. If the commit references a specific issue, link it in the message

[This](https://cbea.ms/git-commit/) is a great post on how to write a good commit message. 

## The PR Process

If you are new to Fierro development and don't have push access to the repository, here are the steps:
1. [Fork and clone](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) the repository.
2. Create a branch for your work.
3. [Push](https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository) the branch to your GitHub fork.
4. Build and test your changes.
5. Update any necessary documentation.
6. Synchronize your branch with changes made to the upstream repository since the last merge/fork.
7. Create a [Pull Request](https://github.com/lanl/Fierro/pulls).

This corresponds to the **Fork & Pull Model** described in the [GitHub collaborative development](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/getting-started/about-collaborative-development-models) documentation.

## Integrating a PR

Integrating your contributions to Fierro is relatively straightforward; here is the checklist:
- All tests pass
- The changes build with no new compiler warnings/errors
- All feedback has been addressed
- Consensus is reached. This usually means that at least two reviewers approved the changes (or added a `LGTM` comment) and at least one business day passed without anyone objecting. `LGTM` is an acronym for Looks Good to Me.
- If you do NOT have push access, a Fierro core developer will integrate your PR. 

## Benevolent dictators for life

The [benevolent dictators](https://en.wikipedia.org/wiki/Benevolent_dictator_for_life) can integrate changes to keep the platform healthy and help interpret or address conflict related to the contribution guidelines and the platform as a whole.

These currently include:
- [Nathaniel Morgan](https://github.com/nathanielmorgan)
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

# Using EVPFFT with Fierro

To use EVPFFT as a user material model for Fierro, build Fierro with the following cmake flags `-DBUILD_EVPFFT_FIERRO=ON`, choose the FFT backend with `-D<USE_FFTW,USE_CUFFT,USE_ROCFFT>=ON`, and use `-DABSOLUTE_NO_OUTPUT=ON` to specify that EVPFFT does not provide output per element which can be cumbersome and memory intensive. It is best to modify EVPFFT to output for a few elements of interst.

See `example_input_files/taylor_anvil.yaml` for the yaml file setup when using EVPFFT with Fierro.

Also, input files for each material should be defined as `evpfft1.in`, `evpfft2.in`, etc. and should be provided in the directory Fierro is being run from.

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

