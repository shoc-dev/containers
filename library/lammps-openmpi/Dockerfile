# Using base Alpine MPI image
FROM ghcr.io/shoc-dev/containers/library/openmpi-builder

# Install prerequisite packages
RUN apk add --no-cache \
    openmpi \
    openmpi-dev \
    fftw \
    fftw-dev \
    libarchive \
    hwloc \
    libjpeg-turbo \
    libpng \
    libxml2 \
    libxslt \
    python3 \
    py3-numpy \
    py3-scipy \
    py3-mpi4py \
    py3-setuptools \
    py3-wheel \
    py3-pip \
    git \
    bash \
    curl \
    tar \
    bzip2 \
    linux-headers \
    libstdc++ \
    gfortran

# Install build dependencies separately so we can remove them later
RUN apk add --no-cache --virtual .build-deps \
    build-base \
    cmake \
    python3-dev \
    fftw-dev \
    libarchive-dev \
    hwloc-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    libxml2-dev \
    libxslt-dev \
    openmpi-dev \
    git

WORKDIR /opt

# Clone LAMMPS source
RUN git clone --depth 1 https://github.com/lammps/lammps.git

# Configure and build LAMMPS
RUN mkdir -p lammps/build && cd lammps/build && \
    cmake -C ../cmake/presets/most.cmake ../cmake \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D CMAKE_BUILD_TYPE=Release \
    -D BUILD_MPI=on \
    -D LAMMPS_EXCEPTIONS=on \
    -D BUILD_TOOLS=on \
    -D BUILD_SHARED_LIBS=on && \
    make -j$(nproc) && \
    make install

# Clean up: remove build tools and source
RUN rm -rf /opt/lammps && \
    apk del .build-deps && \
    rm -rf /var/cache/apk/*