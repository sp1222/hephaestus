FROM node:20-bookworm-slim AS node-base

FROM localai/localai@sha256:9d789d7aa61d08095f7697556076c950a3dcd53b17beed96ebdfab5dabdfb8b6

# Add the official Node.js runtime so `npx`-based MCP servers can run as stdio
# subprocesses without relying on a third-party NodeSource bootstrap script.
COPY --from=node-base /usr/local /usr/local

RUN node --version \
    && npm --version \
    && npx --version
