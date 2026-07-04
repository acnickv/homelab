# syntax=docker.io/docker/dockerfile:1.25.0@sha256:0adf442eae370b6087e08edc7c50b552d80ddf261576f4ebd6421006b2461f12
# check=error=true
FROM ghcr.io/astral-sh/uv:0.11.26@sha256:3d868e555f8f1dbc324afa005066cd11e1053fc4743b9808ca8025283e65efa5 AS uv

FROM docker.io/library/fedora:44@sha256:6c75d5bf57cb0fa5aa4b92c6a83c86c791644496d9ac230de7711f5b8ec3b898

RUN <<EOF
  dnf install -y \
    curl \
    gh \
    git-core \
    jq \
    neovim \
    podman \
    podman-docker \
    ripgrep \
    sysstat \
    tig
  dnf clean all \
  useradd --create-home --uid 1000 vscode \
  mkdir -p /opt/homelab \
  chown vscode:vscode /opt/homelab
EOF

USER vscode

COPY --from=uv /uv /uvx /home/vscode/.local/bin/

# renovate: datasource=github-releases depName=sst/opencode versioning=semver
ARG OPENCODE_VERSION=1.17.13
RUN gh release download "v${OPENCODE_VERSION}" \
        --repo sst/opencode \
        --pattern 'opencode-linux-x64.tar.gz' \
        --output - \
    | tar -xz -C /home/vscode/.local/bin opencode

WORKDIR /opt/homelab
COPY --chown=vscode:vscode pyproject.toml uv.lock ./
RUN uv python install 3.14 \
    && uv sync --frozen --all-groups --no-install-project --python 3.14

ENV PATH="/home/vscode/.local/bin:/opt/homelab/.venv/bin:$PATH"
