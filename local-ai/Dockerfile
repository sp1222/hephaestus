FROM localai/localai@sha256:d78cd113b2bc5892d7533c1356e9c9725bb90e86a075356a63730f3d681ce4d7

# Add Node.js so `npx`-based MCP servers (e.g. @modelcontextprotocol/server-github)
# can run as stdio subprocesses. See LocalAI docs: "MCP and adding packages."
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl ca-certificates gnupg \
    && curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*
