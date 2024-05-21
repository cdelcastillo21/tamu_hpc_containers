FROM ubuntu:20.04

LABEL maintainer="TACC-Carlos D. <carlosd@tacc.utexas.edu>"
ARG DEBIAN_FRONTEND=noninteractive

# Install necessary packages
RUN apt-get update && apt-get install -y \
    build-essential \
    openmpi-bin \
    openmpi-common \
    libopenmpi-dev

# Copy the MPI "Hello World" script into the container
COPY mpi_hello_world.c /mpi_hello_world.c

# Compile the MPI "Hello World" script
RUN mpicc -o /mpi_hello_world /mpi_hello_world.c

# Set the entrypoint to run the MPI program
ENTRYPOINT ["mpirun", "-np", "4", "/mpi_hello_world"]