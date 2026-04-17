FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

# ripgrep: used by Claude Code (USE_BUILTIN_RIPGREP=0 in shell profile).
# unzip: required by AWS CLI installer.
RUN apt-get update && apt-get install -y --no-install-recommends \
    ripgrep \
    unzip \
  && rm -rf /var/lib/apt/lists/*

# awscli v2: required for Bedrock SSO auth (use-bedrock function).
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip" -o /tmp/awscliv2.zip \
  && unzip /tmp/awscliv2.zip -d /tmp \
  && /tmp/aws/install \
  && rm -rf /tmp/awscliv2.zip /tmp/aws

# Org AWS config — contains SSO routing only, no secrets.
COPY --chown=vscode:vscode aws-config /home/vscode/.aws/config

# Install Node.js via nvm (pre-installed in base image) and Claude Code
USER vscode
RUN curl -fsSL https://claude.ai/install.sh | bash

# Shell aliases/env: Bedrock auth helpers and Claude Code env vars
COPY --chown=vscode:vscode bash_aliases /home/vscode/.bash_aliases
