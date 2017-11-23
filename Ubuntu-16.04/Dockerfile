FROM ubuntu:16.04
LABEL maintainer="Stephen Roberts"
RUN apt-get update && \
    apt-get install -y build-essential curl
RUN curl -SL http://www.mpich.org/static/downloads/3.2.1/mpich-3.2.1.tar.gz | tar -xzC /usr/local/src && \
    curl -SL https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz | tar -xzC /usr/local/src 
