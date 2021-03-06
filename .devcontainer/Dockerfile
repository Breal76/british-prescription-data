# Based on the example Dockerfile at:
# https://github.com/microsoft/vscode-dev-containers/blob/master/containers/debian/.devcontainer/Dockerfile

# Update the VARIANT arg in devcontainer.json to override the Debian version below
ARG VARIANT=buster
FROM mcr.microsoft.com/vscode/devcontainers/base:${VARIANT}

RUN git config --global core.editor "code --wait"

    # Install shellcheck (apt-get doesn't install the latest version needed by VS code, so installing from GitHub release, see:
    # https://askubuntu.com/a/1228181)
ARG SHELLCHECK_VERSION="v0.7.1"
ARG SECRETHUB_CLI_VERSION="v0.40.0"
ARG MONGO_SHELL_VERSION="4.4.0"
ARG MONGODB_TOOLS_VERSION="100.1.1"
SHELL ["/bin/bash", "-o", "pipefail", "-c"] 
# hadolint ignore=DL4001
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
# Install xz-utils to extract tarballs
    && apt-get -y install --no-install-recommends xz-utils=5.2.4-1 \
    && mkdir -p /tmp/shellcheck \
    && wget -qO- "https://github.com/koalaman/shellcheck/releases/download/${SHELLCHECK_VERSION}/shellcheck-${SHELLCHECK_VERSION}.linux.x86_64.tar.xz" | tar -xJv -C /tmp/shellcheck/ \
    && install -m 755 /tmp/shellcheck/shellcheck-${SHELLCHECK_VERSION}/shellcheck /usr/local/bin/shellcheck \
    && rm -rf /tmp/shellcheck \
    #
    # Install SecretHub CLI to manage our secrets
    && mkdir -p /tmp/secrethub \
    && wget -qO- "https://github.com/secrethub/secrethub-cli/releases/download/${SECRETHUB_CLI_VERSION}/secrethub-${SECRETHUB_CLI_VERSION}-linux-386.tar.gz" | tar -xz -C /tmp/secrethub/ \
    && install -m 755 /tmp/secrethub/bin/secrethub /usr/local/bin/secrethub \
    && rm -rf /tmp/secrethub \
    # Install Mongo shell
    && mkdir -p /tmp/mongodb_shell \
    && wget -qO- "https://downloads.mongodb.org/linux/mongodb-shell-linux-x86_64-debian10-${MONGO_SHELL_VERSION}.tgz" | tar -xz -C /tmp/mongodb_shell/ \
    && install -m 755 /tmp/mongodb_shell/mongodb-linux-x86_64-debian10-${MONGO_SHELL_VERSION}/bin/mongo /usr/local/bin/mongo \
    && rm -rf /tmp/mongodb_shell \
    #
    # Install Mongo database tools - needed for JSON import
    && mkdir -p /tmp/mongodb_tools \
    && wget -qO- "https://fastdl.mongodb.org/tools/db/mongodb-database-tools-debian10-x86_64-${MONGODB_TOOLS_VERSION}.tgz" | tar -xz -C /tmp/mongodb_tools/ \
    && install -m 755 /tmp/mongodb_tools/mongodb-database-tools-debian10-x86_64-${MONGODB_TOOLS_VERSION}/bin/mongoimport /usr/local/bin/mongoimport \
    && rm -rf /tmp/mongodb_tools \
    # Install the Confluent Cloud CLI - see:
    # https://docs.confluent.io/current/quickstart/cloud-quickstart/index.html#cloud-cli-install
    && curl -L --http1.1 https://cnfl.io/ccloud-cli | sh -s -- -b /usr/local/bin \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*
