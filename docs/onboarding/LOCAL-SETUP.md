# Local Development Setup

> **For:** Developers contributing code to ZarishSphere
> **OS:** Ubuntu 22.04 / 24.04 LTS (primary supported platform)
> **Estimated time:** 30–60 minutes for first-time setup

---

## Prerequisites

- Ubuntu 22.04 or 24.04 LTS (other Linux distros work but are not officially supported)
- 8 GB RAM minimum (16 GB recommended)
- 20 GB free disk space
- Internet connection
- GitHub account with access to zarishsphere organization

---

## Step 1 — Install Core Tools

```bash
# System packages
sudo apt-get update
sudo apt-get install -y curl wget git git-lfs jq yq make \
  build-essential ca-certificates gnupg openssl unzip

# Go 1.26.1
GO_VERSION="go1.26.1.linux-amd64"
wget -q "https://go.dev/dl/${GO_VERSION}.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "${GO_VERSION}.tar.gz"
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> ~/.bashrc
source ~/.bashrc
go version  # Should show: go version go1.26.1 linux/amd64

# Node.js 22 LTS via nvm
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22
node --version  # Should show: v22.x.x

# pnpm (faster npm)
npm install -g pnpm
pnpm --version

# Flutter 3.41.2
cd ~ && git clone https://github.com/flutter/flutter.git -b 3.41.2 --depth 1
echo 'export PATH="$PATH:$HOME/flutter/bin"' >> ~/.bashrc
source ~/.bashrc
flutter --version

# Docker (if not installed)
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
# Log out and back in, then:
docker --version
```

---

## Step 2 — Install GitHub CLI

```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | \
  sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] \
  https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list
sudo apt-get update && sudo apt-get install -y gh
gh auth login
gh auth status  # Should show your username
```

---

## Step 3 — Clone Your Target Repository

```bash
mkdir -p ~/zarishsphere
cd ~/zarishsphere

# Example: clone the FHIR engine
gh repo clone zarishsphere/zs-core-fhir-engine

# Or patient service
gh repo clone zarishsphere/zs-svc-patient

cd zs-core-fhir-engine
```

---

## Step 4 — Start the Local Dev Stack

```bash
# Clone the dev environment repo
gh repo clone zarishsphere/zs-iac-dev-environment ~/zarishsphere/zs-iac-dev-environment
cd ~/zarishsphere/zs-iac-dev-environment

# Start all services
docker compose up -d

# Wait ~60 seconds, then verify
docker compose ps
```

**Service URLs after startup:**

| Service | URL | Credentials |
|---------|-----|-------------|
| PostgreSQL 18.3 | localhost:5432 | zarish / zarish_dev_password |
| NATS 2.12.5 | nats://localhost:4222 | — |
| NATS Monitor | http://localhost:8222 | — |
| Valkey 9.0.3 | localhost:6379 | — |
| Typesense 30.1 | http://localhost:8108 | API key: zarish_typesense_dev |
| Keycloak 26.5.6 | http://localhost:8443 | admin / zarish_admin |
| Grafana 12.x | http://localhost:3000 | admin / zarish_grafana |
| Prometheus | http://localhost:9090 | — |
| Traefik | http://localhost:8080 | — |

---

## Step 5 — Run a Service Locally

```bash
cd ~/zarishsphere/zs-core-fhir-engine

# Copy environment template
cp .env.example .env

# Run database migrations
make migrate-up

# Start with live reload (air)
make dev
# Service now running at http://localhost:8000

# Run tests
make test

# Run linter
make lint
```

---

## Step 6 — Set Up AI Coding Agent (Optional)

```bash
# Install Claude Code (Anthropic's AI coding CLI)
npm install -g @anthropic-ai/claude-code

# Set your API key (get free key at console.anthropic.com)
echo 'export ANTHROPIC_API_KEY="sk-ant-YOUR-KEY"' >> ~/.bashrc
source ~/.bashrc

# Install aider (alternative AI coding tool)
pip3 install --user --break-system-packages aider-chat
echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bashrc
source ~/.bashrc

# Use in any repo:
cd ~/zarishsphere/zs-svc-patient
claude
# or
aider
```

---

## Verify Everything Works

```bash
# Go
go version          # go version go1.26.1 linux/amd64

# Node.js
node --version      # v22.x.x

# Flutter
flutter --version   # Flutter 3.41.2

# Docker
docker --version    # Docker version 29.3.x

# gh CLI
gh auth status      # Logged in to github.com

# Dev stack
docker compose -f ~/zarishsphere/zs-iac-dev-environment/docker-compose.yml ps
# All services should show "Up"
```

---

## Troubleshooting

**Docker permission denied:**
```bash
sudo usermod -aG docker $USER && newgrp docker
```

**Port already in use:**
```bash
sudo lsof -i :5432  # Find what's using port
sudo systemctl stop postgresql  # Stop system PostgreSQL if running
```

**Go module not found:**
```bash
go env GOPATH  # Should be $HOME/go
export PATH=$PATH:$(go env GOPATH)/bin
```

**Flutter pub get fails:**
```bash
flutter doctor  # Check for issues
flutter pub cache repair
```

---

*Setup issues? Open an issue in zs-docs-platform with label `dev-environment`.*
