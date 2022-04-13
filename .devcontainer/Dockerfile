ARG VARIANT=16-bullseye
ARG FLAVOUR=typescript
FROM mcr.microsoft.com/vscode/devcontainers/${FLAVOUR}-node:0-${VARIANT}

# Install Docker CE CLI.
RUN apt-get update && \
  apt-get install -y apt-transport-https ca-certificates curl gnupg2 lsb-release && \
  curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
  apt-get update && \
  apt-get install -y \
    docker-ce docker-ce-cli \
    containerd.io

# Install Docker Compose.
RUN export LATEST_COMPOSE_VERSION=$(curl -sSL "https://api.github.com/repos/docker/compose/releases/latest" | grep -o -P '(?<="tag_name": ").+(?=")') && \
  curl -sSL "https://github.com/docker/compose/releases/download/${LATEST_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
  chmod +x /usr/local/bin/docker-compose && \
  # And as a plugin invoked with 'docker compose'
  export DOCKER_PLUGINS="${HOME}/.docker/cli-plugins" && \
  mkdir -p ${DOCKER_PLUGINS} && \
  curl -sSL "https://github.com/docker/compose/releases/download/${LATEST_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o ${DOCKER_PLUGINS}/docker-compose && \
  chmod +x ${DOCKER_PLUGINS}/docker-compose

# [Optional] Install Homebrew (will not work on M1 Macs)
# RUN /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install additional OS packages.
RUN apt-get update && \
  export DEBIAN_FRONTEND=noninteractive && \
  apt-get -y install --no-install-recommends \
    python3-pip \
    python3-venv \
    inetutils-ping

# [Optional] Install an additional version of node using nvm.
# ARG EXTRA_NODE_VERSION=10
# RUN su node -c "source /usr/local/share/nvm/nvm.sh && nvm install ${EXTRA_NODE_VERSION}"

# Set zsh as the default shell.
RUN chsh -s /bin/zsh root && \
  chsh -s /bin/zsh node

# [Optional] Install yarn.
RUN npm install -g  yarn

# [Optional] Install the AWS CLI v2.
RUN curl -s "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip" -o "awscliv2-$(uname -m).zip" && \
  unzip -qq "awscliv2-$(uname -m).zip" && \
  ./aws/install && \
  rm -rf ./aws "awscliv2-$(uname -m).zip"

# [Optional] Install Supabase CLI.
RUN wget -q -O /tmp/supabase.deb https://github.com/supabase/cli/releases/download/v0.24.1/supabase_0.24.1_linux_$(dpkg --print-architecture).deb \
  && dpkg -i /tmp/supabase.deb \
  && rm /tmp/supabase.deb

# [Optional] install more global node packages.
RUN npm install -g \
  npm \
  yarn

WORKDIR /workspace
