# hephaestus

Container image factory mostly for my homelab use or experimentations, builds and publishes images to ghcr.io via GitHub Actions.

## Images

Images built in this repository

[ollama-rocm-gfx-1010](ollama-rocm/gfx-1010/README.md)

## Builds

Images are built and pushed to ghcr.io automatically when the `Dockerfile` changes on `main`.
Manual rebuilds can be triggered via `workflow_dispatch` in GitHub Actions.

To update to a new Ollama release:
1. Check [ollama/ollama releases](https://github.com/ollama/ollama/releases) for new ROCm tags
2. Update `FROM ollama/ollama:X.XX.X-rocm` in the relevant Dockerfile
3. Push to `main` — the workflow will build and push automatically
