Setup instructions for NOAA WCOSS DELL machine using Intel-18.0.1.163

NOTE: set "export INSTALL_PREFIX=..." as required for your installation

export INSTALL_PREFIX=/gpfs/dell2/emc/_YOUR_GROUP_/noscrub/$USER/wrk/UFS_build

. /usrx/local/prod/lmod/lmod/init/sh
module purge
module load EnvVars/1.0.3
module load ips/18.0.1.163
module load impi/18.0.1
module load lsf/10.1
module load cmake/3.16.2
module li

# Currently Loaded Modules:
#    1) EnvVars/1.0.3   2) ips/18.0.1.163   3) impi/18.0.1   4) lsf/10.1   5) cmake/3.16.2

export CC=icc
export CXX=icpc
export FC=ifort

export CMAKE_C_COMPILER=mpiicc
export CMAKE_CXX_COMPILER=mpiicpc
export CMAKE_Fortran_COMPILER=mpiifort
export CMAKE_Platform=wcoss_dell_p3

export PNG_ROOT=/usrx/local/prod/packages/gnu/4.8.5/libpng/1.2.59

mkdir -p ${INSTALL_PREFIX}/src
cd ${INSTALL_PREFIX}/src

git clone -b ufs-v2.0.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS-external
cd NCEPLIBS-external
mkdir build && cd build
cmake -DBUILD_PNG=OFF -DBUILD_MPI=OFF -DBUILD_NETCDF=OFF -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} -DDEPLOY=ON .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8

cd ${INSTALL_PREFIX}/src
git clone -b ufs-v2.0.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS
cd NCEPLIBS
mkdir build && cd build
cmake -DCMAKE_PREFIX_PATH=${INSTALL_PREFIX} -DCMAKE_INSTALL_PREFIX=${INSTALL_PREFIX} -DDEPLOY=ON -DOPENMP=ON .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8 2>&1 | tee log.make
make deploy 2>&1 | tee log.deploy


- END OF THE SETUP INSTRUCTIONS -


The following instructions are for building the ufs-weather-model (standalone;
not the UFS applications - for the latter, the model is built by the workflow)
with those libraries installed.

This is separate from NCEPLIBS-external and NCEPLIBS, and details on how to get
the code are provided here: https://github.com/ufs-community/ufs-weather-model/wiki

After checking out the code and changing to the top-level directory of ufs-weather-model,
the following commands should suffice to build the model.


cd ${INSTALL_PREFIX}/src
git clone -b ufs-v2.0.0 --recursive https://github.com/ufs-community/ufs-weather-model
cd ufs-weather-model

export CC=icc
export CXX=icpc
export FC=ifort

export CMAKE_C_COMPILER=mpiicc
export CMAKE_CXX_COMPILER=mpiicpc
export CMAKE_Fortran_COMPILER=mpiifort
export CMAKE_Platform=wcoss_dell_p3

. /usrx/local/prod/lmod/lmod/init/sh
module purge
module load EnvVars/1.0.3
module load ips/18.0.1.163
module load impi/18.0.1
module load lsf/10.1
module load cmake/3.16.2

module use ${INSTALL_PREFIX}/modules
module load esmf/8.0.0
module load bacio/2.4.1
module load crtm/2.3.0
module load g2/3.4.1
module load g2tmpl/1.9.1
module load ip/3.3.3
module load nceppost/dceca26
module load nemsio/2.5.2
module load sp/2.3.3
module load w3emc/2.7.3
module load w3nco/2.4.1
module load gfsio/1.4.1
module load sfcio/1.4.1
module load sigio/2.3.2
./build.sh 2>&1 | tee build.log
