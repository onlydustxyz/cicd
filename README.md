# CICD

This repository contains shared GitHub workflows and shared (generic) Dockerfiles to be used by your CICD.

## GitHub Workflows

### The Docker workflow

The Docker workflow allows to build, tag and push Docker images from a GitHub action in just a few lines.

#### Usage

Create a GitHub workflow (`.yml` file) in `.github/workflows` as you usualy do. Then, use the docker-workflow from this repository
thanks to the `uses` keyword.

Here is a working example:

```yml
name: docker

on:
  push:
    branches:
      - 'main'
    tags:
      - 'v*'

jobs:
  docker:
    uses: onlydustxyz/cicd/.github/workflows/docker-workflow.yml@main
    with:
      image_name: od-badge-signup
      image_tag: ${{ github.ref_name }}
      dockerfile: rocket-rs
      datadog_label: '[{"source": "deathnote", "service": "od-badge-signup"}]'
    secrets: inherit
```

#### Inputs

- `image_name` is **required**. It corresponds to the name of the docker image that will be built.
- `image_tag` is **required**. It is the tag of the docker image.
- `dockerfile` is not required. This allows to use [Dockerfiles from this repository](./dockerfiles/) (eg. `rocket-rs`). If not set, your repository **must** contain a Dockerfile at the root directory.
- `datadog_label` is not required. It will be passed as a build argument named `DATADOG_LABEL` to the Dockerfile (see [example](./dockerfiles/rocket-rs/Dockerfile#L42)).

#### Secrets

It expects the following secrets to be set, which should be already the case in our GitHub organisation:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `DOCKER_REGISTRY`

## Dockerfiles

### Dockerfile rocket-rs

This is a generic Dockerfile meant to be used for Rust applications that use the [Rocket web framework](https://rocket.rs/).

Note: make sure the app binary is named "service" by adding those 3 lines to Cargo.toml:

```toml
[[bin]]
name = "service"
path = "src/main.rs"
```
