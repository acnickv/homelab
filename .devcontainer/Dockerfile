FROM docker.io/library/fedora:44@sha256:6c75d5bf57cb0fa5aa4b92c6a83c86c791644496d9ac230de7711f5b8ec3b898

RUN dnf install -y \
        curl \
        git-core \
        jq \
        neovim \
        podman \
        podman-docker \
        ripgrep \
        sysstat \
        tig \
    && dnf clean all

# renovate: datasource=pypi depName=uv versioning=pep440
ARG UV_VERSION=0.11.26
RUN curl -fsSL \
        "https://github.com/astral-sh/uv/releases/download/${UV_VERSION}/uv-x86_64-unknown-linux-gnu.tar.gz" \
    | tar -xz -C /usr/local/bin --strip-components=1 \
        uv-x86_64-unknown-linux-gnu/uv \
        uv-x86_64-unknown-linux-gnu/uvx

# renovate: datasource=github-releases depName=sst/opencode versioning=semver
ARG OPENCODE_VERSION=1.17.13
RUN curl -fsSL \
        "https://github.com/sst/opencode/releases/download/v${OPENCODE_VERSION}/opencode-linux-x64.tar.gz" \
    | tar -xz -C /usr/local/bin opencode

WORKDIR /opt/homelab
COPY pyproject.toml uv.lock ./
RUN uv python install 3.14 \
    && uv sync --frozen --all-groups --no-install-project --python 3.14

ENV PATH="/opt/homelab/.venv/bin:$PATH"
