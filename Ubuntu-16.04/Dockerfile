FROM ubuntu:16.04
LABEL maintainer="Stephen Roberts"
RUN apt-get update && \
    apt-get install -y build-essential curl libgmp-dev libmpc-dev libmpfr-dev \
                       gcc-multilib numactl gfortran tcl tcl-dev
RUN curl -SL http://sourceforge.net/projects/modules/files/Modules/modules-3.2.10/modules-3.2.10.tar.gz | tar -xzC /usr/local/src && \
    cd /usr/local/src/modules-3.2.10 && \
    CPPFLAGS="-DUSE_INTERP_ERRORLINE" ./configure --prefix=/opt/modules-3.2.10 && \
    make && \
    make install && \
    echo "source /opt/modules-3.2.10/Modules/3.2.10/init/bash" >> /etc/skel/.bashrc && \
    echo "source /opt/modules-3.2.10/Modules/3.2.10/init/bash_completion" >> /etc/skel/.bashrc
ENV MODULESHOME=/opt/modules-3.2.10/ \
    MODULE_VERSION=3.2.10 \
    MODULEPATH=/opt/modules-3.2.10/Modules/3.2.10/modulefiles:/opt/modulefiles

# Build & Install Compilers
# GNU 7.2.0
RUN curl -SL ftp://ftp.gnu.org/gnu/gcc/gcc-7.2.0/gcc-7.2.0.tar.gz | tar -xzC /usr/local/src && \
    cd /usr/local/src/gcc-7.2.0 && \
    ./contrib/download_prerequisites && \
    mkdir /usr/local/src/gcc-7.2.0_build && \
    cd /usr/local/src/gcc-7.2.0_build && \
    /usr/local/src/gcc-7.2.0/configure --prefix=/opt/gcc-7.2.0 --enable-languages=c,c++,fortran && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/gcc-7.2.0_build
# Install Compiler Environment modules
COPY modulefiles/compilers /opt/modulefiles/compilers

# Compile MPI Libraries for system compilers
# Download MPICH 3.2.1 - Separate download for multi-compiler builds
RUN curl -SL http://www.mpich.org/static/downloads/3.2.1/mpich-3.2.1.tar.gz | tar -xzC /usr/local/src
# Compile MPICH 3.2.1 under gnu 5.4.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/5.4.0-system && \
    mkdir /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER} --enable-debuginfo && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build

# Compile MPICH 3.2.1 under gnu 7.2.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER} --enable-debuginfo && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build

# Download OpenMPI 3.0.0 - Separate download for multi-compiler builds
RUN curl -SL https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz | tar -xzC /usr/local/src
# Compile OpenMPI 3.0.0 under gnu 5.4.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/5.4.0-system && \
    mkdir /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-3.0.0/configure --prefix=/opt/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build
# Compile OpenMPI 3.0.0 under gnu 7.2.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-3.0.0/configure --prefix=/opt/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build

# Download OpenMPI 2.1.2 - Separate download for multi-compiler builds
RUN    curl -SL https://www.open-mpi.org/software/ompi/v2.1/downloads/openmpi-2.1.2.tar.gz | tar -xzC /usr/local/src
# Compile OpenMPI 2.1.2 under gnu 5.4.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/5.4.0-system && \
    mkdir /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-2.1.2/configure --prefix=/opt/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build
# Compile OpenMPI 2.1.2 under gnu 5.4.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-2.1.2/configure --prefix=/opt/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install && \
    rm -rf /usr/local/src/openmpi-2.1.2_${COMPILER_NAME}-${COMPILER_VER}_build
# Install MPI modules
COPY modulefiles/mpi /opt/modulefiles/mpi
