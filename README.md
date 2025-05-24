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
  -g ./configs/network/v2/genesis.json \
  --root-dir ./my-node-config/ \
  --p2p.bootnodes "<revealed-soon>"
```

For detailed instructions on configuration options more relevant to a production setup, refer to our [Configuration Guide](docs/creating-config.md).

### 2. Set Up PostgreSQL

For a quick setup, run Kwil's pre-configured PostgreSQL Docker image:

```bash
docker run -d -p 5432:5432 --name tn-postgres \
    -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    -v postgres_data:/var/lib/postgresql/data \
    kwildb/postgres:latest
```

If you prefer a custom PostgreSQL setup, ensure it meets the requirements specified in the [Kwil documentation](https://docs.kwil.com/docs/daemon/running-postgres).

**Security Warning**: Do not expose port 5432 publicly and make sure to set passwords for your database in production environments.

### 3. Deploy TN Node

Run the kwild binary to deploy your node:

```bash
kwild start --root-dir ./my-node-config/ --statesync.enable=true --statesync.trusted_providers='revealed-soon'
```

Ensure your firewall allows incoming connections on:
- JSON-RPC port (default: 8484) 
- P2P port (default: 6600)

The `--statesync.enable` and `--statesync.trusted_providers` flags are optional and will help your node sync faster with the network using snapshots provided by the RPC servers.

### 4. Verify Node Synchronization

Before proceeding to become a validator, ensure your node is fully synced with the network:

```bash
kwild admin status
```

Look for the `syncing: false` in the output, and check that your `best_block_height` is close to the current network height.

### 5. Become a Validator (Optional)

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

You can always reach out to the community for help with the validator process.

### 6. Submit Your Node to Available Node List (Optional)

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
kwild key info --key-file /path/to/your/nodekey.json
```

## Additional Resources

- [Kwil Documentation](https://docs.kwil.com)
- [Production Network Deployment Guide](https://docs.kwil.com/docs/node/production)

For further assistance, join our [Discord community](https://discord.com/invite/5AMCBYxfW4) or open an issue on our [GitHub repository](https://github.com/trufnetwork/truf-node-operator/issues).

Welcome to the TRUF.NETWORK! Your participation helps build a more robust and decentralized data infrastructure.
