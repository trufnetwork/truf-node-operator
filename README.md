# TRUF.NETWORK Node Operator Guide

This guide will walk you through the process of setting up and running a TRUF.NETWORK (TN) node. By following these steps, you'll be able to deploy a node, optionally become a validator, and contribute to the TN v2 mainnet.

## Prerequisites

Before you begin, ensure you have the following:

1. **Kwild**: The all-in-one binary for node operation and administration.
    - Download from the [latest GitHub release](https://github.com/trufnetwork/node/releases)

2. **Docker**: Required for running the PostgreSQL image.
    - Install from [Docker's official website](https://docs.docker.com/get-docker)

3. **PostgreSQL Client**: Required for state sync and database operations.
    - Install with `sudo apt -y install postgresql-16` (Ubuntu/Debian)

## Setup Steps

### 1. Generate Initial Configuration

Use `kwild` to create your initial configuration file:

```bash
kwild setup init \
  --genesis ./configs/network/v2/genesis.json \
  --root ./my-node-config \
  --p2p.bootnodes "4e0b5c952be7f26698dc1898ff3696ac30e990f25891aeaf88b0285eab4663e1#ed25519@node-1.mainnet.truf.network:26656,0c830b69790eaa09315826403c2008edc65b5c7132be9d4b7b4da825c2a166ae#ed25519@node-2.mainnet.truf.network:26656"
```

For detailed instructions on configuration options more relevant to a production setup, refer to our [Configuration Guide](docs/creating-config.md).

### 2. Enable State Sync

Edit the `config.toml` file in your node configuration directory to enable state sync. The following example assumes you used `./my-node-config` as your root directory in the previous step. Adjust the path if you used a different directory:

```bash
# Edit the config.toml file
sed -i '/\[state_sync\]/,/^\[/ s/enable = false/enable = true/' ./my-node-config/config.toml
sed -i 's/trusted_providers = \[\]/trusted_providers = ["0c830b69790eaa09315826403c2008edc65b5c7132be9d4b7b4da825c2a166ae#ed25519@node-2.mainnet.truf.network:26656"]/' ./my-node-config/config.toml
```

This will configure your node to use state sync for faster synchronization with the network.

### 3. Set Up PostgreSQL

For a quick setup, run Kwil's pre-configured PostgreSQL Docker image:

```bash
docker run -d -p 5432:5432 --name tn-postgres \
    -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    -v tn-pgdata:/var/lib/postgresql/data \
    --shm-size=1gb \
    kwildb/postgres:latest
```

The command above:
- `-v tn-pgdata:/var/lib/postgresql/data`: Creates a persistent volume named 'tn-pgdata' to store database data
- `--shm-size=1gb`: Allocates 1GB of shared memory for PostgreSQL operations (recommended for better performance)

#### Installing pg_dump for Snapshots

To enable state sync functionality, you'll need `pg_dump` installed. Here's how to install it:

For Ubuntu/Debian:
```bash
sudo apt-get update
sudo apt-get install postgresql-client-16
```

For CentOS/RHEL:
```bash
sudo yum install postgresql16
```

For macOS (using Homebrew):
```bash
brew install postgresql@16
```

Verify the installation:
```bash
pg_dump --version
```

This should show PostgreSQL version 16.x.x. The `pg_dump` utility is required for creating and restoring database snapshots during state sync.

If you prefer a custom PostgreSQL setup, ensure it meets the requirements specified in the [Kwil documentation](https://docs.kwil.com/docs/daemon/running-postgres).

**Security Warning**: It is recommended to not expose port 5432 publicly in production environments.

### 4. Deploy TN Node

Run the kwild binary to deploy your node:

```bash
kwild start -r ./my-node-config
```

You can run kwild in the background by adding `&` at the end of the command:

```bash
kwild start -r ./my-node-config &
```

This allows you to run other kwild commands in the same terminal session, such as checking node status or managing validators.

Ensure your firewall allows incoming connections on:
- JSON-RPC port (default: 8484)
- P2P port (default: 6600)

The `--statesync.enable` and `--statesync.trusted_providers` flags are optional and will help your node sync faster with the network using snapshots provided by the RPC servers.

### 5. Verify Node Synchronization

Before proceeding to become a validator, ensure your node is fully synced with the network:

```bash
kwild admin status
```

Look for the `syncing: false` in the output, and check that your `best_block_height` is close to the current network height.

### 6. Become a Validator (Optional)

To upgrade your node to a validator:

1. Ensure your node is fully synced with the network.
2. Submit a validator join request:

```bash
kwild validators join
```

3. Wait for approval from existing validators. You can check your join request status with:

```bash
kwild validators list-join-requests
```

Existing validators must approve your request. For each existing validator needed to approve:

```bash
kwild validators approve <your-node-id>
```

The node ID format for validator operations is: `<public key>#<key type>`. For example:
```bash
kwild validators approve 03dbe22b9922b5c0f8f60c230446feaa1c132a93caa9dae83b5d4fab16c3404a22#secp256k1
```

You can find your node's public key and key type by running:
```bash
kwild key info --key-file ./my-node-config/nodekey.json
```

Note: If you used a different directory name during setup (not `./my-node-config`), replace the path with your actual node configuration directory path.

This will show your node's public key and key type, which you'll need for validator operations.

You can always reach out to the community for help with the validator process.

### 7. Submit Your Node to Available Node List (Optional)

To help others discover your node:

1. Fork the TN repository.
2. Add your node information to the `configs/network/available_nodes.json` file.
3. Submit a Pull Request with your changes.

We'll review and merge your PR to include your node in the network's seed list.

## Network Configuration Files

Essential network configuration files are located in the `configs/network/` directory:

- `v2/genesis.json`: The network's genesis file.
- `v2/network-nodes.csv`: List of available nodes for peer discovery.

When setting up your node, refer to these files for network-specific parameters and peer information.

## Node ID Format

Node IDs in TRUF.NETWORK follow the format: `<public key>#<key type>@<IP address>:<port>`

You can find your node ID by running:
```bash
kwild key info --key-file ./my-node-config/nodekey.json
```

## Additional Resources

- [Kwil Documentation](https://docs.kwil.com)
- [Production Network Deployment Guide](https://docs.kwil.com/docs/node/production)

For further assistance, join our [Discord community](https://discord.com/invite/5AMCBYxfW4) or open an issue on our [GitHub repository](https://github.com/trufnetwork/truf-node-operator/issues).

Welcome to the TRUF.NETWORK! Your participation helps build a more robust and decentralized data infrastructure.
