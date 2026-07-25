FROM localai/localai@sha256:9d789d7aa61d08095f7697556076c950a3dcd53b17beed96ebdfab5dabdfb8b6

# Add Node.js so `npx`-based MCP servers (e.g. @modelcontextprotocol/server-github)
# can run as stdio subprocesses. See LocalAI docs: "MCP and adding packages."
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl ca-certificates gnupg \
    && curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*
