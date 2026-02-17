# Joining TRUF.NETWORK Testnet (testnet-v1)

This directory contains the configuration files required to join the TRUF.NETWORK testnet.

## Network Information

- **Chain ID**: `testnet-v1`
- **Genesis File**: `genesis.json` (in this directory)
- **Bootnodes**: See `network-nodes.csv`

## Quick Start

To initialize your node configuration and join the testnet, follow these steps:

### 1. Prerequisites
- Install `kwild` (v0.10.3+)
- Install `docker` (to run the database)
- Install `postgresql-client-16` (for state sync)

### 2. Start PostgreSQL

Kwil requires a PostgreSQL database to store state. Run the pre-configured Kwil Postgres image:

```bash
docker run -d --name tn-db-postgres \
  -p 5432:5432 \
  -e "POSTGRES_HOST_AUTH_METHOD=trust" \
  -v tn_db_data:/var/lib/postgresql/data \
  ghcr.io/trufnetwork/kwil-postgres:latest
```

### 3. Initialize Configuration

Run the following command from the root of your configuration directory:

```bash
kwild setup init \
    --genesis ./configs/network/testnet-v1/genesis.json \
    --p2p.bootnodes "0368d4f915451a5c58782b804c49cc784bfcd2866e8f6c21799e3577f2441a5799#secp256k1@3.141.77.16:6600,0237d8c37fe15c4837027701efad2db3cf2705abbf8a6cf7c77c4ea5f895a436eb#secp256k1@13.58.112.159:6600" \
    --state-sync.enable \
    --state-sync.trusted-providers "0368d4f915451a5c58782b804c49cc784bfcd2866e8f6c21799e3577f2441a5799#secp256k1@3.141.77.16:6600" \
    --root ./tn-testnet-config
```

### 4. Start the Node

```bash
kwild start --root ./tn-testnet-config
```

## Troubleshooting

- **Firewall**: Ensure ports `6600` (P2P) and `8484` (RPC) are open.
- **Peers**: If your node is not finding peers, ensure the bootnodes are reachable.
- **Logs**: Check `kwild.log` in your root directory for errors.
