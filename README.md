# Org Devcontainer

A VS Code devcontainer for running Claude Code against AWS Bedrock.

**Image:** `ghcr.io/jswank/devcontainer:latest`  
**Base:** Ubuntu 24.04 · Claude Code · AWS CLI v2 · ripgrep

---

## Using the devcontainer

1. Copy [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json) into your project's `.devcontainer/` directory.
2. Open the project in VS Code and select **Reopen in Container** when prompted (or run `Dev Containers: Reopen in Container` from the command palette).
3. Authenticate with Bedrock (see below).

---

## Authenticating with AWS Bedrock

The devcontainer mounts your host's AWS SSO token cache, so if you've already authenticated on the host you're done.

**First-time / token expired:**

```bash
# On your host machine
aws sso login --profile cui-ai-user
```

Then reopen (or rebuild) the devcontainer. The token is shared automatically via the mount.

**No host AWS setup / terminal-only use:**

Inside the container, run:

```bash
use-bedrock
```

This runs `aws sso login` (device-code flow) and exports the resulting credentials into the current shell session.

---

## Adding tools

### Option A — devcontainer Features (preferred)

For standard tools, add a `features` block to your local copy of [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json):

```jsonc
{
  "image": "ghcr.io/jswank/devcontainer:latest",
  "features": {
    "ghcr.io/devcontainers/features/opentofu:1": {},
    "ghcr.io/devcontainers/features/kubectl-helm-minikube:1": {}
  }
}
```

Browse available features at [containers.dev/features](https://containers.dev/features).  
VS Code's **Dev Containers: Add Dev Container Configuration Files** command includes a feature picker.

### Option B — Dockerfile inheritance (for tools with no published Feature)

Replace the `image` line in [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json) with a `build` block:

```jsonc
{
  "build": { "dockerfile": "Dockerfile.local" }
}
```

Create `Dockerfile.local` alongside [`.devcontainer/devcontainer.json`](.devcontainer/devcontainer.json):

```dockerfile
FROM ghcr.io/jswank/devcontainer:latest

RUN apt-get update && apt-get install -y --no-install-recommends \
    my-custom-tool \
  && rm -rf /var/lib/apt/lists/*
```

**When to use which:** use a Feature if one exists; fall back to Dockerfile inheritance when no Feature is available or when you need precise control over how a tool is installed.

---

## Overriding Bedrock model IDs

The container defaults to org-blessed model IDs. Override any of them in your host environment before opening the devcontainer:

```bash
export ANTHROPIC_DEFAULT_SONNET_MODEL=us.anthropic.claude-sonnet-4-6
export ANTHROPIC_DEFAULT_OPUS_MODEL=us.anthropic.claude-opus-4-7-v1
export ANTHROPIC_DEFAULT_HAIKU_MODEL=us.anthropic.claude-haiku-4-5-20251001-v1:0
```
