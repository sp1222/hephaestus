FROM localai/localai@sha256:0632c21ddbe440bb71bc7aa744796c227a51658641b216290e86d6894e1c705b

# Add Node.js so `npx`-based MCP servers (e.g. @modelcontextprotocol/server-github)
# can run as stdio subprocesses. See LocalAI docs: "MCP and adding packages."
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl ca-certificates gnupg \
    && curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*
