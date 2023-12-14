ARG RELEASE_VERSION=23.11
FROM nvcr.io/nvidia/pytorch:${RELEASE_VERSION}-py3

ARG CAUSTICS_BRANCH=main

# Setup environment to match variables set by repo2docker as much as possible
# Tell apt-get to not block installs by asking for interactive human input
ENV DEBIAN_FRONTEND=noninteractive \
    # Set username, uid and gid (same as uid) of non-root user the container will be run as
    NB_USER=jovyan \
    NB_UID=1000 \
    # Use /bin/bash as shell, not the default /bin/sh (arrow keys, etc don't work then)
    SHELL=/bin/bash \
    # Setup locale to be UTF-8, avoiding gnarly hard to debug encoding errors
    LANG=C.UTF-8  \
    LC_ALL=C.UTF-8

# All env vars that reference other env vars need to be in their own ENV block
# Path to the python environment where the jupyter notebook packages are installed
# Home directory of our non-root user
ENV HOME=/home/${NB_USER}


RUN echo "Creating ${NB_USER} user..." \
    # Create a group for the user to be part of, with gid same as uid
    && groupadd --gid ${NB_UID} ${NB_USER}  \
    # Create non-root user, with given gid, uid and create $HOME
    && useradd --create-home --gid ${NB_UID} --no-log-init --uid ${NB_UID} ${NB_USER} \
    # Make sure that /home/${NB_USER} is owned by non-root user, so we can install things there
    && chown -R ${NB_USER}:${NB_USER} ${HOME} \
    && chown -R ${NB_USER}:${NB_USER} /workspace

# Install basic apt packages
RUN echo "Installing Apt-get packages..." \
    && apt-get update --fix-missing > /dev/null \
    && apt-get install -y apt-utils \
    git \
    wget \
    zip \
    tzdata \
    python3-venv \
    graphviz \
    > /dev/null \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Add TZ configuration - https://github.com/PrefectHQ/prefect/issues/3061
ENV TZ UTC
# ========================

# Install caustics from a branch for now
RUN echo "Caustics branch: ${CAUSTICS_BRANCH}"
ENV CAUSTICS_REPO="git+https://github.com/Ciela-Institute/caustics.git@${CAUSTICS_BRANCH}"

RUN echo "Installing caustics" \
    && pip install --no-cache ${CAUSTICS_REPO}

# Set to non-root user and user's home dir for working directory
USER ${NB_USER}
WORKDIR ${HOME}
