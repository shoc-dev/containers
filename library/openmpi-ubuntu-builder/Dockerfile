FROM ghcr.io/shoc-dev/containers/library/openmpi-ubuntu

# Set a non-interactive frontend for package installation to avoid prompts.
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential \
    && \
    # Clean up apt cache to reduce image size.
    rm -rf /var/lib/apt/lists/*
