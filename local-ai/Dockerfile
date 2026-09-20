FROM node:20-bookworm-slim AS node-base

FROM localai/localai@sha256:0632c21ddbe440bb71bc7aa744796c227a51658641b216290e86d6894e1c705b

# Add the official Node.js runtime so `npx`-based MCP servers can run as stdio
# subprocesses without relying on a third-party NodeSource bootstrap script.
COPY --from=node-base /usr/local /usr/local

RUN node --version \
    && npm --version \
    && npx --version
