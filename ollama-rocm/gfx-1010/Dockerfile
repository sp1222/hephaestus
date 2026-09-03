FROM ollama/ollama:0.33.3-rocm

# RX 5700 XT (gfx1010) ROCm workarounds
# Override HSA for supported RDNA 1.0 GFX version
ENV HSA_OVERRIDE_GFX_VERSION=10.1.0
# Use old engine which is more stable on RDNA 1.0 cards
ENV OLLAMA_NEW_ENGINE=false
# Disable SDMA which causes hangs on some RDNA 1.0 cards
ENV HSA_ENABLE_SDMA=0
# Increase load timeout for large models
ENV OLLAMA_LOAD_TIMEOUT=30m
# Set max GPU resource usages to 100%
ENV GPU_MAX_HEAP_SIZE=100
ENV GPU_MAX_ALLOC_PERCENT=100
ENV GPU_SINGLE_ALLOC_PERCENT=100

# TensileLibrary symlink fix for gfx1010
# The RX 5700 XT reports as gfx1010 but comparable gfx1030 library exists
RUN ln -sf /usr/lib/ollama/rocm/rocblas/library/TensileLibrary_lazy_gfx1030.dat \
           /usr/lib/ollama/rocm/rocblas/library/TensileLibrary_lazy_gfx1010.dat

ENTRYPOINT ["/bin/ollama"]
CMD ["serve"]
