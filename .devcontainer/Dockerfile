FROM mcr.microsoft.com/devcontainers/base:debian13@sha256:71b6fe7c4e07ec747a94a72b2a17f2f8c286eaa7b1c59862888df764efb8b6c6

# cspell:disable
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir -p /commandhistory \
    && touch /commandhistory/.bash_history \
    && chown -R vscode /commandhistory \
    && echo "$SNIPPET" >> /home/vscode/.bashrc
# cspell:enable

ARG YQ_VERSION=4.53.6
ARG CUDA_VERSION=13-1

# cspell:disable-next-line
RUN arch="$(dpkg --print-architecture)" \
    # Download the pinned yq binary and verify its SHA-256 checksum.
    && yq_binary="yq_linux_${arch}" \
    && yq_package="${yq_binary}.tar.gz" \
    && yq_base_url="https://github.com/mikefarah/yq/releases/download/v${YQ_VERSION}" \
    && cd /tmp \
    && curl -fsSLO "${yq_base_url}/${yq_package}" \
    && curl -fsSLO "${yq_base_url}/checksums" \
    && awk -v package="${yq_package}" '$1 == package { print $19 "  " $1 }' checksums | sha256sum -c - \
    && tar -xzf "${yq_package}" \
    && install -m 0755 "${yq_binary}" /usr/local/bin/yq \
    && rm -rf "${yq_package}" "${yq_binary}" checksums \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        # Native QMD dependencies are compiled during dependency installation.
        build-essential \
        # Additional utilities for AI agents and development.
        jq \
        ripgrep \
    # Install QMD's CUDA runtime on amd64; other architectures retain the CPU backend.
    && if [ "$arch" = amd64 ]; then \
        curl -fsSL \
            "https://developer.download.nvidia.com/compute/cuda/repos/debian13/x86_64/cuda-keyring_1.1-1_all.deb" \
            -o /tmp/cuda-keyring.deb \
        && dpkg -i /tmp/cuda-keyring.deb \
        && rm /tmp/cuda-keyring.deb \
        && apt-get update \
        && apt-get install -y --no-install-recommends \
            "cuda-cudart-${CUDA_VERSION}" \
            "libcublas-${CUDA_VERSION}"; \
    fi \
    && rm -rf /var/lib/apt/lists/* \
    # Verify the installed yq version.
    && yq --version
