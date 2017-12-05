FROM centos:latest as centos-base
RUN yum -y update && \
    yum -y install bzip2 curl gcc gcc-c++ gcc-gfortran make wget file \
    gmp gmp-devel libmpc libmpc-devel mpfr mpfr-devel texi2html texinfo

# Build & Install Compilers seperately - this is a long running task
FROM centos-base as centos-compiler-build
# GCC 7.2.0
RUN curl -SL ftp://ftp.gnu.org/gnu/gcc/gcc-7.2.0/gcc-7.2.0.tar.gz | tar -xzC /usr/local/src 
RUN cd /usr/local/src/gcc-7.2.0 && ./contrib/download_prerequisites
RUN mkdir /usr/local/src/gcc-7.2.0_build
RUN  cd /usr/local/src/gcc-7.2.0_build && \
     /usr/local/src/gcc-7.2.0/configure --prefix=/opt/gcc-7.2.0 --enable-languages=c,c++,fortran --disable-multilib
RUN make -C /usr/local/src/gcc-7.2.0_build -j $(nproc)
RUN make -C /usr/local/src/gcc-7.2.0_build install

# GCC 6.3.0
RUN curl -SL ftp://ftp.gnu.org/gnu/gcc/gcc-6.3.0/gcc-6.3.0.tar.gz | tar -xzC /usr/local/src 
RUN cd /usr/local/src/gcc-6.3.0 && ./contrib/download_prerequisites
RUN mkdir /usr/local/src/gcc-6.3.0_build
RUN  cd /usr/local/src/gcc-6.3.0_build && \
     /usr/local/src/gcc-6.3.0/configure --prefix=/opt/gcc-6.3.0 --enable-languages=c,c++,fortran --disable-multilib
RUN make -C /usr/local/src/gcc-6.3.0_build -j $(nproc)
RUN make -C /usr/local/src/gcc-6.3.0_build install

# Build and Install Environment Modules
FROM centos-base as centos-modules-build
# todo delete
RUN yum -y install tcl tcl-devel
RUN curl -SL http://sourceforge.net/projects/modules/files/Modules/modules-3.2.10/modules-3.2.10.tar.gz | tar -xzC /usr/local/src
RUN mkdir /usr/local/src/modules-3.2.10_build
RUN cd /usr/local/src/modules-3.2.10_build && \
    CPPFLAGS="-DUSE_INTERP_ERRORLINE" /usr/local/src/modules-3.2.10/configure --prefix=/opt/modules-3.2.10
RUN make -C /usr/local/src/modules-3.2.10_build -j $(nproc)
RUN make -C /usr/local/src/modules-3.2.10_build install

FROM centos-base as centos-compilers
# TODO hoist
RUN yum -y install tcl tcl-devel 
COPY --from=centos-compiler-build /opt/gcc-7.2.0 /opt/gcc-7.2.0
COPY --from=centos-compiler-build /opt/gcc-6.3.0 /opt/gcc-6.3.0
COPY --from=centos-modules-build /opt/modules-3.2.10 /opt/modules-3.2.10
RUN ( echo "source /opt/modules-3.2.10/Modules/3.2.10/init/bash"; \
      echo "source /opt/modules-3.2.10/Modules/3.2.10/init/bash_completion" ) >> /etc/skel/.bashrc
ENV MODULESHOME=/opt/modules-3.2.10/ \
    MODULE_VERSION=3.2.10 \
    MODULEPATH=/opt/modules-3.2.10/Modules/3.2.10/modulefiles:/opt/modulefiles
COPY modulefiles/compilers /opt/modulefiles/compilers

FROM centos-compilers as centos-mpi-build
# MPICH 3.2.1
RUN curl -SL http://www.mpich.org/static/downloads/3.2.1/mpich-3.2.1.tar.gz | tar -xzC /usr/local/src
# Compile MPICH 3.2.1 under gnu 4.8.5
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/4.8.5-system && \
    mkdir /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER} --enable-debuginfo && \
    make -j $(nproc) && \
    make install 
# Compile MPICH 3.2.1 under gnu 6.3.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/6.3.0 && \
    mkdir /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER} --enable-debuginfo && \
    make -j $(nproc) && \
    make install 
# Compile MPICH 3.2.1 under gnu 7.2.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_${COMPILER_NAME}-${COMPILER_VER} --enable-debuginfo && \
    make -j $(nproc) && \
    make install 

# OpenMPI 3.0.0
RUN curl -SL https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz | tar -xzC /usr/local/src
# Compile OpenMPI 3.0.0 under gnu 4.8.5
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/4.8.5-system && \
    mkdir /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-3.0.0/configure --prefix=/opt/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install
# Compile OpenMPI 3.0.0 under gnu 6.3.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/6.3.0 && \
    mkdir /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-3.0.0/configure --prefix=/opt/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install
# Compile OpenMPI 3.0.0 under gnu 7.2.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-3.0.0/configure --prefix=/opt/openmpi-3.0.0_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install

# OpenMPI 1.10.2
RUN curl -SL https://www.open-mpi.org/software/ompi/v1.10/downloads/openmpi-1.10.2.tar.gz | tar -xzC /usr/local/src
# Compile OpenMPI 1.10.2 under gnu 4.8.5
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/4.8.5-system && \
    mkdir /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-1.10.2/configure --prefix=/opt/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install
# Compile OpenMPI 1.10.2 under gnu 6.3.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/6.3.0 && \
    mkdir /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-1.10.2/configure --prefix=/opt/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install
# Compile OpenMPI 1.10.2 under gnu 7.2.0
RUN . /opt/modules-3.2.10/Modules/3.2.10/init/sh && \
    module load compilers/gnu/7.2.0 && \
    mkdir /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    cd /usr/local/src/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER}_build && \
    /usr/local/src/openmpi-1.10.2/configure --prefix=/opt/openmpi-1.10.2_${COMPILER_NAME}-${COMPILER_VER} && \
    make -j $(nproc) && \
    make install

FROM centos-compilers
COPY --from=centos-mpi-build /opt/mpich-3.2.1_gnu-4.8.5 /opt/mpich-3.2.1_gnu-4.8.5 
COPY --from=centos-mpi-build /opt/mpich-3.2.1_gnu-6.3.0 /opt/mpich-3.2.1_gnu-6.3.0 
COPY --from=centos-mpi-build /opt/mpich-3.2.1_gnu-7.2.0 /opt/mpich-3.2.1_gnu-7.2.0 
COPY --from=centos-mpi-build /opt/openmpi-3.0.0_gnu-4.8.5 /opt/openmpi-3.0.0_gnu-4.8.5 
COPY --from=centos-mpi-build /opt/openmpi-3.0.0_gnu-6.3.0 /opt/openmpi-3.0.0_gnu-6.3.0 
COPY --from=centos-mpi-build /opt/openmpi-3.0.0_gnu-7.2.0 /opt/openmpi-3.0.0_gnu-7.2.0 
COPY --from=centos-mpi-build /opt/openmpi-1.10.2_gnu-4.8.5 /opt/openmpi-1.10.2_gnu-4.8.5 
COPY --from=centos-mpi-build /opt/openmpi-1.10.2_gnu-6.3.0 /opt/openmpi-1.10.2_gnu-6.3.0 
COPY --from=centos-mpi-build /opt/openmpi-1.10.2_gnu-7.2.0 /opt/openmpi-1.10.2_gnu-7.2.0 

# Install MPI modules
COPY modulefiles/mpi /opt/modulefiles/mpi
