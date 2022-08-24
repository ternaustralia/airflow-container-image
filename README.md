# TERN's multi-arch Airflow container image

This repository provides container images for `linux/amd64` and `linux/arm64`.

This repository automatically checks the upstream Bitbucket repository for changes in the `master` branch. If there are changes, it will build a new container image and push it to the GitHub Container Registry and Docker Hub. The upstream private Bitbucket repository is https://bitbucket.org/terndatateam/docker-airflow/src/master/.

## Container images

### [ghcr.io/ternaustralia/airflow](https://github.com/ternaustralia/airflow-container-image/pkgs/container/airflow)

This image support for `linux/amd64` and `linux/arm64` with the following tags:

- `latest`
- `major.minor.patch` - e.g. `2.1.4`
- `major.minor.patch-build.<git-short-hash>` - e.g. `2.1.4-build.c584b88`

### [docker.io/ternaustralia/airflow](https://hub.docker.com/repository/docker/ternau/airflow)

This image by default only supports `linux/amd64`. However, this repository provides `linux/arm64` support with the following tags:

- `latest-arm64`
- `major.minor.patch-arm64.<git-short-hash>` - e.g. `2.1.4-arm64.c584b88`

## Running the GitHub Action locally

Test the GitHub Actions workflows using [`act`](https://github.com/nektos/act).

Prior to running any workflows with `act`, you will need to create a `.env` file in the root of the repository. See [.env-template](.env-template) for what environment variables are required.

Note: this will change the state of the `main` branch by updating [`CURRENT_COMMIT_HASH`](CURRENT_COMMIT_HASH) file if the latest upstream commit hash is different.

### Build-multi arch container image

```
act --secret-file .env --env GITHUB_REPOSITORY=edmondchuc/mirror-tern-docker-airflow workflow_dispatch
```

_Note: running the build workflow with `act` will fail on the last step where it builds and pushes to the GitHub Container Registry. The reason it fails is because we do not provide it with the values for `github.actor` to correctly authenticate with the registry. This is not a problem as we are only interested in seeing that building the container image succeeds._

## How does this work?

This repository stores the current build version in the [`CURRENT_COMMIT_HASH`](CURRENT_COMMIT_HASH) file and performs a scheduled check roughly every 30 minutes via GitHub Actions to see if the upstream repository has any new commits. If there are new commits, it will build a new container image targetting `linux/amd64` and `linux/arm64` platforms and push it to the GitHub Container Registry and Docker Hub.

See [`.github/workflows/build.yml`](.github/workflows/build.yml) for the workflow that performs the build.
