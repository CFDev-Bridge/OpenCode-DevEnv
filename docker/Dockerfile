FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# Instalar dependencias
RUN apt-get update && apt-get install -y \
    curl \
    git \
    nano \
    vim \
    sudo \
    ca-certificates \
    build-essential \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Instalar Node.js 22 LTS
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get update \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

# Crear usuario sin privilegios
RUN useradd -ms /bin/bash developer \
    && echo "developer ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER developer
WORKDIR /home/developer

# Configurar npm para instalaciones globales del usuario
RUN mkdir -p /home/developer/.npm-global

ENV NPM_CONFIG_PREFIX=/home/developer/.npm-global
ENV PATH=/home/developer/.npm-global/bin:$PATH

# Instalar OpenCode
RUN npm install -g opencode-ai

WORKDIR /workspace

ENV HOME=/home/developer
ENV TERM=xterm-256color

CMD ["bash"]