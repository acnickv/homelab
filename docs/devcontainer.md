# Devcontainer Build and Maintenance Guide

## Scope

This guide is for all contributors, including AI agents, when making changes to devcontainer files or devcontainer-managed tooling.

## Baseline Requirements

1. Base image must use Fedora 44.
2. Base image must be pinned with both tag and digest.
3. Any software not installed with `dnf` must have an explicit version.
4. All versioned software must be maintainable by Renovate.
5. Python packages must be installed with `uv`.
6. Python must be version 3.14 and installed by `uv`.
7. Python dependencies must be defined in `pyproject.toml` and separated into `lint` and `test` dependency groups.
8. `ansible-core` must be pinned to `2.20`.
9. Ansible collections/roles must be version-pinned.

## Required Tooling

The devcontainer toolset must include:

**dnf-managed (no version pin required):**
- `curl`
- `gh` (GitHub CLI, used to download opencode release assets)
- `git-core`
- `jq`
- `neovim` (`nvim`)
- `podman`
- `podman-docker` (docker compatibility layer for molecule)
- `ripgrep` (`rg`)
- `sysstat` (systems analysis)
- `tig`

**Container image copy (Renovate-managed via Dockerfile datasource):**
- `uv` + `uvx` — copied from `ghcr.io/astral-sh/uv:<version>` multi-stage build stage

**Downloaded via `gh release download` (version-pinned ARG, Renovate-managed):**
- `opencode` — GitHub releases: `sst/opencode`

**Python packages via `uv` (version-pinned, Renovate-managed):**

Main dependencies (`pyproject.toml` `[project].dependencies`):
- `ansible-core==2.20.*`
- `prek`

`lint` dependency group:
- `ansible-lint`

`test` dependency group:
- `ansible-navigator`
- `molecule`
- `molecule-plugins[docker]`

Additional tools can be added later, but must follow the same pinning and Renovate requirements.

## Build Rules

### 1) Fedora Image Pinning

Always set the devcontainer image with both tag and digest, for example:

`<registry>/<image>:<fedora-44-tag>@sha256:<digest>`

Do not use unpinned image references.

### 2) `dnf` vs Non-`dnf` Installs

- `dnf` installs do not require manual version pins in the install command.
- Any non-`dnf` install method (downloaded binaries, install scripts, package managers, etc.) must pin exact versions.

### 3) Python Toolchain

- Install and use `uv`.
- Install Python `3.14` through `uv`.
- Keep dependencies in `pyproject.toml` with separate groups:
  - `lint`
  - `test`

### 4) Ansible

- Pin `ansible-core==2.20.*` in Python dependencies.
- Pin versions for all required Ansible collections and roles.

## Renovate Requirements

Every pinned dependency must be trackable by Renovate, including:

- Devcontainer base image tag+digest
- Python dependencies in `pyproject.toml`
- Non-`dnf` installed tools with explicit versions
- Ansible collections and roles

If a new pinned dependency is added, update Renovate configuration in the same change.

## Maintenance Policy

When updating devcontainer tooling:

1. Update pinned versions.
2. Update Renovate rules for any new or changed pinned dependency source.
3. Keep image pinning strict (Fedora 44 tag + digest).
4. Keep Python on `3.14` installed via `uv`.
5. Preserve dependency group split (`lint`, `test`) in `pyproject.toml`.
6. Keep `ansible-core` pinned to `2.20` and keep collections/roles pinned.

## Validation Steps for Devcontainer Changes

For every devcontainer update:

1. Rebuild the devcontainer.
2. Verify required baseline tools are present and version pins are still correct.
3. Run repository lint commands (if defined).
4. Run repository test commands (if defined).
5. Confirm Renovate can detect all pinned versions changed in the update.
