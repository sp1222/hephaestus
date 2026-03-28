# Ollama ROCm for GFX-1010 (AMD's RX 5700 XT GPU)

Custom Ollama image with AMD ROCm support for the RX 5700 XT (gfx1010).

**Image:** `ghcr.io/sp1222/ollama-rocm-gfx-1010:latest`

#### Background

The RX 5700 XT requires several workarounds to run inference with ROCm and Ollama:

1. **BIOS: Above 4G Decoding must be enabled** — without this the GPU BAR is limited to 256MB instead of 8GB, preventing GPU inference entirely. CSM must be disabled and IOMMU enabled alongside it.

2. **HSA_OVERRIDE_GFX_VERSION=10.1.0** — The RX 5700 XT is gfx1010 but ROCm doesn't fully recognize it without this override. Do not use 10.3.0.

3. **TensileLibrary symlink fix** — ROCm ships `TensileLibrary_lazy_gfx1030.dat` but not `gfx1010.dat`. A symlink is required:
```
   ln -sf TensileLibrary_lazy_gfx1030.dat TensileLibrary_lazy_gfx1010.dat
```

4. **OLLAMA_NEW_ENGINE=false** — Required for stable GPU inference on this hardware.

5. **HSA_ENABLE_SDMA=0** — Disables SDMA to prevent memory transfer issues.

#### Usage
```bash
docker run -d \
  --name ollama-rocm-gfx-1010 \
  --restart unless-stopped \
  --device /dev/kfd \
  --device /dev/dri \
  --group-add video \
  --group-add render \
  -v ollama-models:/root/.ollama \
  -v ollama-rocm-cache:/root/.cache \
  -p 11434:11434 \
  ghcr.io/sp1222/ollama-rocm-gfx-1010:latest
```

#### Hardware

| Component | Details |
|-----------|---------|
| GPU | AMD Radeon RX 5700 XT 8GB (gfx1010) |
| ROCm | 6.3 |
| Host OS | Ubuntu 24.04.4 LTS |
| Kernel | 6.17.0 HWE |

#### Environment Variables

| Variable | Value | Purpose |
|----------|-------|---------|
| `HSA_OVERRIDE_GFX_VERSION` | `10.1.0` | Force gfx1010 recognition |
| `OLLAMA_NEW_ENGINE` | `false` | Use stable engine |
| `HSA_ENABLE_SDMA` | `0` | Disable SDMA |
| `OLLAMA_LOAD_TIMEOUT` | `30m` | Allow time for model load |
| `GPU_MAX_HEAP_SIZE` | `100` | Full VRAM heap |
| `GPU_MAX_ALLOC_PERCENT` | `100` | Full VRAM allocation |
| `GPU_SINGLE_ALLOC_PERCENT` | `100` | Full single allocation |
