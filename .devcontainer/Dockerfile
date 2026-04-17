FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

# nodejs/npm: required by Claude Code install script.
# ripgrep: used by Claude Code (USE_BUILTIN_RIPGREP=0 in shell profile).
# awscli: required for Bedrock SSO auth (use-bedrock function).
RUN apt-get update && apt-get install -y --no-install-recommends \
    nodejs \
    npm \
    ripgrep \
    awscli \
  && rm -rf /var/lib/apt/lists/*

# Org AWS config — contains SSO routing only, no secrets.
COPY --chown=vscode:vscode aws-config /home/vscode/.aws/config

# Install Claude Code as the non-root user
USER vscode
RUN curl -fsSL https://claude.ai/install.sh | bash

# Shell profile: Bedrock auth helpers and Claude Code env vars
COPY --chown=vscode:vscode bash_profile /home/vscode/.bash_profile
