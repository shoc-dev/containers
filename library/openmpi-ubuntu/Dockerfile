# Define the version tag of the base MPI image
ARG SHOC_MPI_IMAGE_TAG=latest

# the SSH server and other basic configurations.
FROM ghcr.io/shoc-dev/containers/library/mpi-ubuntu:${SHOC_MPI_IMAGE_TAG}

# Set a non-interactive frontend for package installation to avoid prompts.
ARG DEBIAN_FRONTEND=noninteractive

# Install OpenMPI packages.
# - openmpi-bin: Contains the MPI compiler wrappers (mpicc, mpic++, etc.) and runtime commands (mpirun).
# - openmpi-common: Common files for OpenMPI.
# - libopenmpi-dev: Development headers and libraries for compiling MPI applications.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    openmpi-bin \
    openmpi-common \
    libopenmpi-dev \
    && \
    # Clean up apt cache to reduce image size.
    rm -rf /var/lib/apt/lists/*
