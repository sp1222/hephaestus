# Hephaestus

Container image factory mostly for my homelab use and experimentations. Hephaestus builds and publishes images to ghcr.io via GitHub Actions.

## Images

Images built in this repository

- [ollama-rocm-gfx-1010](ollama-rocm/gfx-1010/README.md)
- [local-ai](local-ai/README.md)

## Builds

Images are built and pushed to ghcr.io automatically when the `Dockerfile` changes on `main`.
Manual rebuilds can be triggered via `workflow_dispatch` in GitHub Actions.
