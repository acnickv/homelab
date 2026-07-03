# homelab

Repository that stores all of the automation code required for running and managing my homelab environment.

If your change touches `.devcontainer/` or devcontainer tool versions, read [`docs/devcontainer.md`](docs/devcontainer.md) first.

This repository will contain code responsible for:

* system maintenance
* service deployment
* random operational activities

Deployment of services as containers, where possible, and managed as `systemd` services using `podman` for container execution.

List of services supported:

* coredns (to be implemented)
* hashicorp consul (to be implemented)
* hashicorp vault (to be implemented)