# SimGadget
This repository contains the code from N-GenIC to generate initial conditions for cosmological simulations. Secondly, it contains the Gadget code for simulating the evolution of these intial conditions.

## Installing N-GenIC
The N-GenIC code needs the following libraries:

GSL, HDF5 and FFTW.

On clusters they can be loaded as modules. Example:

```
module load gsl/2.7
module load hdf5/1.10.8_slurm
```

### Install correct version of FFTW
N-GenIC requires an older version of the FFTW code, namely version 2. This version can be installed from their website. To install the libary at a specific path `path`, one can specify this with the `prefix` option. Furthermore, the options `shared` and `mpi` have to be enabled. 


```
wget https://www.fftw.org/fftw-2.1.5.tar.gz
tar -xvzf fftw-2.1.5.tar.gz
cd fftw-2.1.5
./configure --prefix=<path> --enable-shared --enable-mpi
make
make install
```

This will install the library in `path/lib/`. To make sure that during the compilation of N-GenIC, the library will be found, one can set 

`export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:path/lib/`

### Compile N-GenIC
To make sure that the FFTW libary is found, one needs to specify in the `Makefile` inside the N-GenIC folder, the following two options, which speficy the location of the previously installed FFTW library.

```
FFTW_INCL= -I path/include
FFTW_LIBS= -L path/lib
```

Then the source code can be compiled:

```
cd ngenic
make
```

## Generate Initial Conditions with N-GenIC

### Parameter file
Inside the `parameterfiles` folder, several example files are included. They all describe the same physical system only with different numbers of particles. The input parameters are described with comments and should be self-explanatory. Otherwise the user manual of Gadget can be consulted for further explanations: 
https://wwwmpa.mpa-garching.mpg.de/gadget4/gadget4_manual.pdf

The parameter file needs to specify the location where the generated initial conditions will be saved. In the example files, this is the folder `./ICs/`. The folder **must exist before** running the code.

### Run the code 
To run the code, the following command can be executed.

`./N-GenIC parameterfiles/lsf_32.param`

This will generate $32^3$ particles. It runs the parameterfile that is stored in the `parameterfiles` folder and will save the generates data in the folder `./ICs/`. By default two initial condition files will be generated; The binary file `lsf_32` which is compatible with GADGET and `lsf_32.csv` which is a readable .csv file containing the particle positions and velocities compatible with the IPPL code. 

The initial conditions can also be generated in parallel with the command:

`mpiexec -np 4 ./N-GenIC parameterfiles/lsf_32.param`

This generates the initial conditions in parallel on 4 processors.

## Simulations with GADGET

### Compile GADGET

For running the Gadget code, the same FFTW2 libray is used. The installation is above, and in the same way, the path to the library must be specified in the `Makefile` of Gadget (folder `Gadget2`)

```
FFTW_INCL= -I path/include
FFTW_LIBS= -L path/lib
```

The code can be compiled with:

`make`


### Setup Parameter file

Similary to the N-GenIC, the parameter file with all relevant physical and computational parameters needs to be set. When using initial conditions generated with N-GenIC several values **must be match**. This is the `TimeBegin` (otherwise it is physically wrong), all the cosmological constants like the density parameters (Omega), Hubble parameters, BoxSize and the system units. The example files are already set up to match their initial condition files. (The initial condition parameter files are specified in the Gadget parameter files). Also here, the Output directory of the results must be specified and exist **before** running the code. 

### Run Simulation with Gadget

To run the code the following command can be executed:

`./Gadget2/Gadget2 Gadget2/parameterfiles/lsf_32.param`


In this example file, it accesses the input file from the folder `ngenic/ICs/` with the name `lsf_32` and saves them in the folder `Results/lsf_32/`.

The code can also be executed in parallel with the command:

`mpiexec -np 4 ./Gadget2/Gadget2 Gadget2/parameterfiles/lsf_32.param`

which runs the code on 4 processors in parallel.




















