
# The implementation is based on MPI base images used in mpi-operator
# More details are available here: https://github.com/kubeflow/mpi-operator/tree/master/build/base
FROM ubuntu:24.04

# Define sshd port argument with default value
ARG SHOC_MPI_SSHD_PORT=2222

# Install dependencies, set capabilities, and clean up in a single layer.
# - openssh-client, openssh-server: For SSH functionality.
# - bash: Shell (included for clarity).
# - dnsutils: Provides nslookup (equivalent to bind-tools on Alpine).
# - libcap2-bin: Provides setcap to manage capabilities.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    openssh-client \
    openssh-server \
    bash \
    dnsutils \
    libcap2-bin \
    && \
    # Add capability to run sshd as non-root on privileged ports.
    setcap CAP_NET_BIND_SERVICE=+eip /usr/sbin/sshd && \
    # Clean up build dependencies.
    apt-get purge -y --auto-remove libcap2-bin && \
    # Clean up apt cache to reduce image size.
    rm -rf /var/lib/apt/lists/*

# Add privilege separation directory to run sshd as root.
# Also create the templates directory in the same layer.
RUN mkdir -p /var/run/sshd /etc/templates

# Add sshd_config template to templates directory (process placeholders later in child images)
# Copy to ~/.sshd_config and chown later in child images
COPY sshd_config /etc/templates/.sshd_config

# Add entrypoint file (copy to ~/__entrypoint.sh and chown later)
COPY entrypoint.sh /etc/templates/entrypoint.sh

# Append Port line to the sshd config template.
RUN echo "Port $SHOC_MPI_SSHD_PORT" >> /etc/templates/.sshd_config

# Configure SSH client and server.
# - Allow OpenSSH to talk to containers without asking for confirmation
#   by disabling StrictHostKeyChecking.
# - mpi-operator mounts the .ssh folder from a Secret. For that to work, we need
#   to disable UserKnownHostsFile to avoid write permissions.
# - Disabling StrictModes avoids directory and files read permission checks.
RUN sed -i 's/[ #]\(.*StrictHostKeyChecking \).*/ \1no/g' /etc/ssh/ssh_config && \
    echo "    UserKnownHostsFile /dev/null" >> /etc/ssh/ssh_config && \
    sed -i 's/[ #]\(.*Port \).*/ \1'$SHOC_MPI_SSHD_PORT'/g' /etc/ssh/ssh_config && \
    sed -i 's/#\(StrictModes \).*/\1no/g' /etc/ssh/sshd_config && \
    sed -i 's/#\(Port \).*/\1'$SHOC_MPI_SSHD_PORT'/g' /etc/ssh/sshd_config

# The original entrypoint is kept to allow the container to run indefinitely
# for interactive use or for other processes to attach.
ENTRYPOINT [ "sleep", "infinity" ]
