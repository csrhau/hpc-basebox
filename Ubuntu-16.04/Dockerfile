FROM ubuntu:16.04
LABEL maintainer="Stephen Roberts"
RUN apt-get update && \
    apt-get install -y build-essential curl libgmp-dev libmpc-dev libmpfr-dev \
                       gcc-multilib numactl
# TODO Install Environment Modules - remember system compiler is a module

# Build GNU compilers. Note: For final setup, this should also include 6.3.0
#RUN curl -SL ftp://ftp.gnu.org/gnu/gcc/gcc-7.2.0/gcc-7.2.0.tar.gz | tar -xzC /usr/local/src && \
#    cd /usr/local/src/gcc-7.2.0 && \
#    ./contrib/download_prerequisites && \
#    mkdir /usr/local/src/gcc-7.2.0_build && \
#    cd /usr/local/src/gcc-7.2.0_build && \
#    /usr/local/src/gcc-7.2.0/configure --prefix=/opt/gcc-7.2.0 --enable-languages=c,c++,fortran && \
#    make -j 2 && \
#    make install && \
#    rm -rf /usr/local/src/gcc-7.2.0_build

# Download MPI Libraries
# Seperate download/compile stages to allow different compiler/mpi combinations
RUN curl -SL http://www.mpich.org/static/downloads/3.2.1/mpich-3.2.1.tar.gz | tar -xzC /usr/local/src && \
    curl -SL https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz | tar -xzC /usr/local/src 

# Compile MPI Libraries
RUN mkdir /usr/local/src/mpich-3.2.1_GCCVER_build && \
    cd /usr/local/src/mpich-3.2.1_GCCVER_build && \
    /usr/local/src/mpich-3.2.1/configure --prefix=/opt/mpich-3.2.1_GCCVER && \
    make -j 2 && \
    make install && \
    rm -rf /usr/local/src/mpich-3.2.1_GCCVER_build 

# Finished
