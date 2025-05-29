# TRUF.NETWORK Quick Installation Guide

This guide provides step-by-step instructions for setting up a TRUF.NETWORK node on a fresh Ubuntu installation.

## Prerequisites Installation

Run the following commands to install all required dependencies:

```bash
# 1) Docker Engine & Compose v2 plugin (plus GCC via build-essential)
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release build-essential
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list >/dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# 1b) Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker

# 1c) Add your user to the docker group
sudo usermod -aG docker $USER

# 1d) Install PostgreSQL client (includes pg_dump)
sudo apt-get install -y postgresql-client

# 2) Install Go (required for building kwild)
LATEST_GO_VERSION=$(curl -sSL https://go.dev/VERSION?m=text | head -n1)
echo "Installing ${LATEST_GO_VERSION}..."
curl -fsSL "https://go.dev/dl/${LATEST_GO_VERSION}.linux-amd64.tar.gz" \
  -o "${LATEST_GO_VERSION}.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "${LATEST_GO_VERSION}.linux-amd64.tar.gz"
rm "${LATEST_GO_VERSION}.linux-amd64.tar.gz"

# 2b) Add Go to PATH
grep -qxF 'export GOPATH=$HOME/go' ~/.bashrc \
  || echo 'export GOPATH=$HOME/go' >> ~/.bashrc
grep -qxF 'export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' ~/.bashrc \
  || echo 'export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' >> ~/.bashrc

# 3) Reload your shell
source ~/.bashrc

# 4) Install Taskfile (go-task)
go install github.com/go-task/task/v3/cmd/task@latest

# 5) Clone the node repository and build kwild
git clone https://github.com/trufnetwork/node.git
cd node
task build

# 6) Add the built kwild binary to PATH
sudo cp .build/kwild /usr/local/bin/
sudo chmod +x /usr/local/bin/kwild

# 7) Apply new docker group immediately
newgrp docker
```

## Verify Installation

Verify that everything is installed correctly:

```bash
docker --version
docker compose version
pg_dump --version
go version
task --version
kwild version
```

## Node Setup

Now that you have all prerequisites installed, follow these steps to set up your node:

```bash
# 1) Go to home directory
cd

# 2) Clone the TRUF.NETWORK node operator repository
git clone https://github.com/trufnetwork/truf-node-operator.git
cd truf-node-operator

# 3) Generate initial configuration
kwild setup init \
  --genesis ./configs/network/v2/genesis.json \
  --root ./my-node-config \
  --p2p.bootnodes "4e0b5c952be7f26698dc1898ff3696ac30e990f25891aeaf88b0285eab4663e1#ed25519@node-1.mainnet.truf.network:26656,0c830b69790eaa09315826403c2008edc65b5c7132be9d4b7b4da825c2a166ae#ed25519@node-2.mainnet.truf.network:26656"

# 4) Enable state sync (for faster synchronization)
sed -i '/\[state_sync\]/,/^\[/ s/enable = false/enable = true/' ./my-node-config/config.toml
sed -i 's/trusted_providers = \[\]/trusted_providers = ["0c830b69790eaa09315826403c2008edc65b5c7132be9d4b7b4da825c2a166ae#ed25519@node-2.mainnet.truf.network:26656"]/' ./my-node-config/config.toml

# 5) Set up PostgreSQL using Docker
docker run -d -p 5432:5432 --name tn-postgres \
    -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    -v tn-pgdata:/var/lib/postgresql/data \
    --shm-size=1gb \
    kwildb/postgres:latest

# 5b) Wait for PostgreSQL to initialize
echo "Waiting for PostgreSQL to initialize..."
sleep 10

# 6) Create systemd service for kwild
sudo tee /etc/systemd/system/kwild.service << EOF
[Unit]
Description=TRUF.NETWORK Node Service
After=network.target tn-postgres.service
Requires=tn-postgres.service

[Service]
Type=simple
User=$USER
WorkingDirectory=$(pwd)
ExecStart=$(which kwild) start -r ./my-node-config
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# 7) Create systemd service for PostgreSQL
sudo tee /etc/systemd/system/tn-postgres.service << EOF
[Unit]
Description=TRUF.NETWORK PostgreSQL Service
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=$USER
ExecStart=docker start -a tn-postgres
ExecStop=docker stop tn-postgres
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

# 8) Enable and start services
sudo systemctl daemon-reload
sudo systemctl enable tn-postgres
sudo systemctl enable kwild
sudo systemctl start tn-postgres
sudo systemctl start kwild

# 9) Check service status
echo "Checking service status..."
sudo systemctl status kwild
```

## Check Node Status

To check if your node is running properly:

```bash
# Check service status
sudo systemctl status kwild

# Check node status
kwild admin status
```

Your node is fully synced when you see `syncing: false` and your `best_block_height` is close to the current network height.

## Troubleshooting

If you encounter any issues:

1. Check service status: `sudo systemctl status kwild`
2. Watch logs in real-time: `sudo journalctl -u kwild -f`
   - Press `Ctrl+C` to stop watching
3. Check Docker container status: `docker ps`
4. Check PostgreSQL logs: `docker logs tn-postgres`

### Common Commands

```bash
# Watch kwild logs in real-time
sudo journalctl -u kwild -f

# Watch last 100 lines of logs
sudo journalctl -u kwild -n 100

# Watch logs since last boot
sudo journalctl -u kwild -b

# Watch logs with timestamps
sudo journalctl -u kwild -f --output=short-precise
```

For more detailed configuration options and validator setup, refer to the main [README.md](../README.md). 