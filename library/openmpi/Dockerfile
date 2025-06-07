# Define the version tag of the base MPI image
ARG SHOC_MPI_IMAGE_TAG=latest

# Using base MPI image
FROM ghcr.io/shoc-dev/containers/library/mpi:${SHOC_MPI_IMAGE_TAG}

# Install OpenMPI package
RUN apk add --no-cache openmpi

ENTRYPOINT ["sleep", "infinity"]