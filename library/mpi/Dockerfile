# The implementation is based on MPI base images used in mpi-operator
# More details are available here: https://github.com/kubeflow/mpi-operator/tree/master/build/base
FROM debian:trixie-slim

# Define sshd port argument with default value
ARG SHOC_MPI_SSHD_PORT=2222

# Install openssh (client and server) and libcap to manage capabilities
RUN apt update && apt install -y --no-install-recommends \
    openssh-server \
    openssh-client \
    libcap2-bin \
    && rm -rf /var/lib/apt/lists/*
    
# Add priviledge separation directoy to run sshd as root.
RUN mkdir -p /var/run/sshd

# Add templates directory
RUN mkdir -p /etc/templates

# Add sshd_config template to templates directory (process placeholders later in child images)
# Copy to ~/.sshd_config and chown later in child images
COPY sshd_config /etc/templates/.sshd_config

# Add entrypoint file (copy to ~/__entrypoint.sh and chown later)
COPY entrypoint.sh /etc/templates/entrypoint.sh

# Append Port line to sshd config file
RUN echo "Port $SHOC_MPI_SSHD_PORT" >> /etc/templates/.sshd_config

# Add capability to run sshd as non-root.
RUN setcap CAP_NET_BIND_SERVICE=+eip /usr/sbin/sshd

# Cleanup
RUN apt remove libcap2-bin -y

# Allow OpenSSH to talk to containers without asking for confirmation
# by disabling StrictHostKeyChecking.
# mpi-operator mounts the .ssh folder from a Secret. For that to work, we need
# to disable UserKnownHostsFile to avoid write permissions.
# Disabling StrictModes avoids directory and files read permission checks.
RUN sed -i "s/[ #]\(.*StrictHostKeyChecking \).*/ \1no/g" /etc/ssh/ssh_config \
    && echo "    UserKnownHostsFile /dev/null" >> /etc/ssh/ssh_config \
    && sed -i "s/[ #]\(.*Port \).*/ \1$SHOC_MPI_SSHD_PORT/g" /etc/ssh/ssh_config \
    && sed -i "s/#\(StrictModes \).*/\1no/g" /etc/ssh/sshd_config \
    && sed -i "s/#\(Port \).*/\1$SHOC_MPI_SSHD_PORT/g" /etc/ssh/sshd_config
