FROM mcr.microsoft.com/devcontainers/base@sha256:1f851004adcd3dff3776b4a1da86727cf52280b0fdc9d5b0568c9c7d74274286

# cspell:disable
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir -p /commandhistory \
    && touch /commandhistory/.bash_history \
    && chown -R vscode /commandhistory \
    && echo "$SNIPPET" >> /home/vscode/.bashrc
# cspell:enable

ARG YQ_VERSION=4.53.6

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
    && rm -rf /var/lib/apt/lists/* \
    # Verify the installed yq version.
    && yq --version
