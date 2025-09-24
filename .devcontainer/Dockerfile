FROM mcr.microsoft.com/devcontainers/miniconda:1-3

ARG ENV_NAME=base
ARG USER_NAME=vscode

# Set system wide environment variables
ENV CONDA_ROOT="/opt/conda"

# Install git-lfs (needed for some repos, e.g. huggingface)
RUN apt-get update && \
    apt-get install -y git-lfs && \
    git lfs install && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy environment.yml (if found) to a temp location so we update the environment. Also
# copy "noop.txt" so the COPY instruction does not fail if no environment.yml exists.
COPY environment.yml* .devcontainer/noop.txt /tmp/conda-tmp/
RUN if [ -f "/tmp/conda-tmp/environment.yml" ]; then \
        umask 0002 && \
        $CONDA_ROOT/bin/conda env create -y -n ${ENV_NAME} -f /tmp/conda-tmp/environment.yml && \
        $CONDA_ROOT/bin/conda clean -afy; \
    else \
        echo "No environment.yml found, skipping conda environment creation"; \
    fi \
    && rm -rf /tmp/conda-tmp

USER $USER_NAME

# Set user environment variables
ENV PATH="$CONDA_ROOT/envs/$ENV_NAME/bin:$CONDA_ROOT/bin:$PATH"

# Activate conda environment by default
# RUN echo "source $CONDA_ROOT/etc/profile.d/conda.sh" >> /home/$USER_NAME/.bashrc
RUN echo "source $CONDA_ROOT/etc/profile.d/conda.sh" >> /home/$USER_NAME/.bashrc && \
    echo "conda activate ${ENV_NAME}" >> /home/$USER_NAME/.bashrc && \
    echo "auto_activate: true" >> /home/$USER_NAME/.condarc && \
    echo "default_activation_env: ${ENV_NAME}" >> /home/$USER_NAME/.condarc

# [Optional] Uncomment to install a different version of Python than the default
# RUN conda install -y python=3.6 \
#     && pip install --no-cache-dir pipx \
#     && pipx reinstall-all

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
