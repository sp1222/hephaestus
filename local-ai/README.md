# local-ai-node

A thin wrapper image around [`localai/localai`](https://hub.docker.com/r/localai/localai) that adds Node.js (and `npx`) to the container, so LocalAI/LocalAGI agents can run `npx`-based MCP servers as STDIO subprocesses — e.g. [`@modelcontextprotocol/server-github`](https://github.com/modelcontextprotocol/servers/tree/main/src/github).

## Why this exists

LocalAI's agent-scoped MCP support (`mcp_stdio_servers` in an agent config) works by spawning a command as a **child process inside the LocalAI container itself** — there's no separate "MCP runner" service. That means anything the command needs (a language runtime, a binary, etc.) has to already be present in the LocalAI image.

The upstream `localai/localai` images don't ship Node.js, so any MCP server distributed as an npm package (anything invoked via `npx`) fails with:

```
exec: "npx": executable file not found in $PATH
```

This wrapper adds Node.js on top of the stock image so those servers can run without introducing a separate pod, a network hop, or an MCP-protocol-translation layer (e.g. `mcpo`, `mcp-proxy`) just to bridge stdio-only tools.

Reference: LocalAI's own docs acknowledge this pattern under ["MCP and adding packages"](https://localai.io/features/mcp/) — installing runtime dependencies into the container is the documented, expected approach.

## What it does *not* do

- Does **not** enable Docker-in-Docker or `docker run`-based MCP servers (some LocalAI MCP examples spawn sibling containers via `docker run`, which needs a DinD sidecar or a mounted host Docker socket). This wrapper only adds a plain Node.js runtime for direct subprocess execution (`npx ...`), which carries none of that risk.
- Does **not** change any LocalAI configuration, entrypoint, or behavior — it is the stock image plus one `apt-get install nodejs` layer.

## Image

Published to GHCR:

```
ghcr.io/sp1222/local-ai-sha:<tag>
```

**Tagging scheme:**
- `sha-<git-commit-sha>` — traceable to the exact commit of this repo that produced the build (via `docker/metadata-action`).
- `local-ai-<upstream-digest-sha>` — traceable to the exact upstream `localai/localai` image digest this was built from (extracted from the `FROM` line in the Dockerfile).
- `latest` — floating pointer to the most recent build. Not recommended for production deployments; pin to one of the above instead.

Because the Dockerfile pins the upstream base with `FROM localai/localai@sha256:...` (a digest, not a mutable tag like `latest-gpu-vulkan`), every build is fully reproducible — rebuilding from the same Dockerfile always produces the same result regardless of what upstream publishes later.

## Verifying the image has Node.js

After rolling out:

```bash
kubectl get pods -n local-ai
kubectl exec -it <pod-name> -n local-ai -- npx --version
```

Should print a version number instead of `executable file not found in $PATH`.

## Using it: configuring an agent's STDIO MCP server

Once the image is running, configure the agent in the LocalAGI Web UI (**Build → agent → MCP Servers → STDIO Servers → Add STDIO Server**), or directly in the agent's JSON config under `mcp_stdio_servers`:

```json
{
  "mcp_stdio_servers": [
    {
      "name": "github",
      "cmd": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": [
        "GITHUB_PERSONAL_ACCESS_TOKEN=your_token_here"
      ]
    }
  ]
}
```

**Note:** this agent config is written to disk under the agent pool/state directory. Treat it as sensitive — it will contain the token in plaintext.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| `exec: "npx": executable file not found in $PATH` | Still running the unmodified upstream image — confirm the pod is using `local-ai-node`, not `localai/localai` |
| `<available_skills>` block stays empty in debug logs, model falls back to built-in Skills tools | MCP server never registered — check `mcp_stdio_servers` is set on the *agent* (not the model YAML), and check pod logs for a spawn/auth error |
| GitHub tool calls fail with an auth error | `GITHUB_PERSONAL_ACCESS_TOKEN` missing, expired, or lacking required scopes |
| `npx` hangs on first run | First invocation fetches the package from `registry.npmjs.org` — confirm the pod has network egress to npm; consider `npm install -g @modelcontextprotocol/server-github` in the Dockerfile to pre-bake it and avoid runtime fetches |
