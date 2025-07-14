# Using base Alpine MPI image
FROM ghcr.io/shoc-dev/containers/library/openmpi-builder

# Install prerequisite packages for GROMACS and common utilities
# gcc, g++ are essential for compiling GROMACS
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
    gfortran \
    gcc \
    g++

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

# Clone GROMACS source code. Using a specific release branch for stability.
RUN git clone --depth 1 --branch v2025.2 https://github.com/gromacs/gromacs.git

# Configure and build GROMACS
# - DGMX_BUILD_OWN_FFTW=OFF: Use the system-installed FFTW library.
# - DREGRESSIONTEST_DOWNLOAD=OFF: Do not download regression test data during build.
# - DCMAKE_BUILD_TYPE=Release: Optimize for performance.
# - DGMX_MPI=ON: Enable MPI support.
# - DCMAKE_INSTALL_PREFIX=/usr/local/gromacs: Install GROMACS to this directory.
RUN mkdir -p gromacs/build && cd gromacs/build && \
    cmake .. \
    -DGMX_BUILD_OWN_FFTW=OFF \
    -DREGRESSIONTEST_DOWNLOAD=OFF \
    -DCMAKE_BUILD_TYPE=Release \
    -DGMX_MPI=ON \
    -DCMAKE_INSTALL_PREFIX=/usr/local/gromacs && \
    make -j$(nproc) && \
    make install

# Set GROMACS environment variables to make executables and libraries easily accessible
ENV PATH="/usr/local/gromacs/bin:$PATH"
ENV LD_LIBRARY_PATH="/usr/local/gromacs/lib:$LD_LIBRARY_PATH"

# Clean up: remove source code and build dependencies to reduce image size
RUN rm -rf /opt/gromacs && \
    apk del .build-deps && \
    rm -rf /var/cache/apk/*

# The base image 'ghcr.io/shoc-dev/containers/library/openmpi-builder'
# already includes ENTRYPOINT ["sleep", "infinity"], so no need to redefine it here.
