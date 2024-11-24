ARG PYTHON_VERSION=3.12
FROM python:${PYTHON_VERSION}-slim as builder

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Configure apt and install packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    libffi-dev \
    libssl-dev \
    openssh-client \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && mkdir -p /workspace \
    && chown -R $USERNAME:$USERNAME /workspace

# Install uv
# https://docs.astral.sh/uv/configuration/installer/
ENV XDG_BIN_HOME=/usr/local/bin
ENV UV_LINK_MODE=copy

# Install Python tools
RUN python -m pip install --no-cache-dir \
    uv \
    ruff \
    mypy \
    pytest \
    pytest-cov \
    pytest-asyncio \
    mkdocs \
    mkdocs-material \
    mkdocstrings[python] \
    mike

# Set up the workspace
WORKDIR /workspaces

# Switch to non-root user
USER $USERNAME

# Configure Git
RUN git config --global core.editor "code --wait" \
    && git config --global init.defaultBranch main

# Set default shell to bash and adjust path for pyproject scripts
ENV SHELL /bin/bash
ENV PATH=/home/$USERNAME/.local/bin:$PATH
