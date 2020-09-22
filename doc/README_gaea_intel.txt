Setup instructions for NOAA RDHPC Gaea using Cray Intel-18.0.6.288

module unload intel
module load intel/18.0.6.288
module unload cray-mpich
module load cray-mpich/7.7.11
module unload cray-netcdf
module load cmake/3.17.0
module li

> Currently Loaded Modulefiles:
>   1) modules/3.2.11.4                                 7) udreg/2.3.2-7.0.2.1_2.15__g8175d3d.ari          13) job/2.2.4-7.0.2.1_2.18__g36b56f4.ari            19) PrgEnv-intel/6.0.5                              25) darshan/3.2.1
>   2) eproxy/2.0.24-7.0.2.1_2.20__g8e04b33.ari         8) ugni/6.0.14.0-7.0.2.1_3.15__ge78e5b0.ari        14) dvs/2.12_2.2.164-7.0.2.1_3.8__g1afc88eb         20) craype-broadwell                                26) DefApps
>   3) intel/18.0.6.288                                 9) pmi/5.0.15                                      15) alps/6.6.59-7.0.2.1_3.7__g872a8d62.ari          21) cray-mpich/7.7.11                               27) hpcrpt/noaa-3
>   4) craype-network-aries                            10) dmapp/7.1.1-7.0.2.1_2.19__g38cf134.ari          16) rca/2.2.20-7.0.2.1_2.20__g8e3fb5b.ari           22) CmrsEnv                                         28) cmake/3.17.0
>   5) craype/2.6.3                                    11) gni-headers/5.0.12.0-7.0.2.1_2.4__g3b1768f.ari  17) atp/2.1.3                                       23) TimeZoneEDT
>   6) cray-libsci/19.06.1                             12) xpmem/2.2.20-7.0.2.1_2.15__g87eb960.ari         18) perftools-base/7.1.3                            24) globus-toolkit/6.0.17

# Need to use MPI wrappers in order to find pre-installed Cray MPICH
export CC=cc
export FC=ftn
export CXX=CC
export MPI_ROOT=/opt/cray/pe/mpt/7.7.11/gni/mpich-intel/16.0

mkdir -p /lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11/src

cd /lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11/src
# Note: remote access severely limited on gaea; need to do the git clone on a remote system and rsync to gaea
git clone -b ufs-v1.1.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS-external
cd NCEPLIBS-external
mkdir build && cd build
cmake -DBUILD_MPI=OFF -DCMAKE_INSTALL_PREFIX=/lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11 .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8 2>&1 | tee log.make
# no make install necessary

cd /lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11/src
# Note: remote access severely limited on gaea; need to do the git clone on a remote system and rsync to gaea
git clone -b ufs-v1.1.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS
cd NCEPLIBS
mkdir build && cd build
cmake -DEXTERNAL_LIBS_DIR=/lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11 -DCMAKE_INSTALL_PREFIX=/lustre/f2/pdata/esrl/gsd/ufs/modules/NCEPlibs-ufs-v1.1.0/intel-18.0.6.288/cray-mpich-7.7.11 .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8 2>&1 | tee log.make
make install VERBOSE=1 2>&1 | tee log.install


- END OF THE SETUP INSTRUCTIONS -


The following instructions are for building the ufs-weather-model (standalone;
not the ufs-mrweather app - for the latter, the model is built by the workflow)
with those libraries installed.

This is separate from NCEPLIBS-external and NCEPLIBS, and details on how to get
the code are provided here: https://github.com/ufs-community/ufs-weather-model/wiki

After checking out the code and changing to the top-level directory of ufs-weather-model,
the following commands should suffice to build the model.


module load intel/18.0.6.288
module unload cray-mpich
module load cray-mpich/7.7.11
module unload cray-netcdf
module load cmake/3.17.0
module li

module use -a /lustre/f2/pdata/esrl/gsd/ufs/modules/modulefiles/intel-18.0.6.288/cray-mpich-7.7.11
module load NCEPlibs/1.1.0

export CMAKE_Platform=gaea.intel
export CMAKE_C_COMPILER=cc
export CMAKE_CXX_COMPILER=CC
export CMAKE_Fortran_COMPILER=ftn

./build.sh 2>&1 | tee build.log
