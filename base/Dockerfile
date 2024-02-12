FROM mambaorg/micromamba:focal-cuda-11.8.0

ARG CAUSTICS_BRANCH=dev

# Tell apt-get to not block installs by asking for interactive human input
ENV DEBIAN_FRONTEND=noninteractive \
    # Setup locale to be UTF-8, avoiding gnarly hard to debug encoding errors
    LANG=C.UTF-8  \
    LC_ALL=C.UTF-8

# Switch over to root user to install apt-get packages
USER root

# Install basic apt packages
RUN echo "Installing Apt-get packages..." \
    && apt-get update --fix-missing > /dev/null \
    && apt-get install -y apt-utils \
    && apt-get install -y \
        git \
        wget \
        zip \
        tzdata \
        python3-venv \
        graphviz \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Switch back to mamba user after all apt-get packages are installed
USER $MAMBA_USER

# Copy over the conda lock file from host machine to docker build engine
COPY --chown=$MAMBA_USER:$MAMBA_USER conda-linux-64.lock /tmp/conda-linux-64.lock

# Install the packages listed in the conda lock file
RUN micromamba install --name base --yes --file /tmp/conda-linux-64.lock \
    && micromamba clean --all --yes

# Set to activate mamba environment, otherwise python will not be found
ARG MAMBA_DOCKERFILE_ACTIVATE=1

# Install caustics from a branch for now
RUN echo "Caustics branch: ${CAUSTICS_BRANCH}"
ENV CAUSTICS_REPO="git+https://github.com/Ciela-Institute/caustics.git@${CAUSTICS_BRANCH}"

RUN echo "Installing caustics" \
    && pip install --no-cache ${CAUSTICS_REPO}
