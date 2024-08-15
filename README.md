# TSN Node Operator Guide

This guide will walk you through the process of setting up and running a TSN node. By following these steps, you'll be able to deploy a node, optionally become a validator, and contribute to the TSN network.

## Prerequisites

Before you begin, ensure you have the following:

1. **Kwil-admin**: Used to generate the initial configuration file.
    - Download from the [latest GitHub release](https://github.com/kwilteam/kwil-db/releases)

2. **Docker**: Required for running the PostgreSQL image.
    - Install from [Docker's official website](https://docs.docker.com/get-docker/)

3. **TSN Binaries**: Necessary for node deployment.
    - Download from the [latest GitHub release](https://github.com/truflation/tsn-db/releases)

## Setup Steps

### 1. Generate Initial Configuration

Use `kwil-admin` to create your initial configuration file:

```bash
kwil-admin setup peer [flags]
```

For detailed instructions on configuration options, refer to our [Configuration Guide](docs/creating-config.md).

### 2. Set Up PostgreSQL

For a quick setup, run Kwil's pre-configured PostgreSQL Docker image:

```bash
docker run -d -p 5432:5432 --name tsn-postgres \
    -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    kwildb/postgres:latest
```

If you prefer a custom PostgreSQL setup, ensure it meets the requirements specified in the [configuration guide](https://docs.kwil.com/docs/daemon/running-postgres).

### 3. Deploy TSN Node

Run the TSN binaries to deploy your node:

```bash
kwild --root-dir /path/to/config/dir
```

Ensure your firewall allows incoming connections on the JSON-RPC port (default: 8484) and P2P port (default: 26656).

### 4. Become a Validator (Optional)

To upgrade your node to a validator:

1. Ensure your node is fully synced with the network.
2. Submit a validator join request:

   ```bash
   kwil-admin validators join --rpcserver /path/to/your/node.sock
   ```

3. Wait for approval from existing validators. You can check your join request status with:

   ```bash
   kwil-admin validators join-status <your-public-key> --rpcserver /path/to/your/node.sock
   ```

You can always ping us for help in the validator process.

### 5. Submit Your Node to Available Node List (Optional)

To help others discover your node:

1. Fork the TSN repository.
2. Add your node information to the `configs/network/available_nodes.json` file.
3. Submit a Pull Request with your changes.

We'll review and merge your PR to include your node in the network's seed list.

## Network Configuration Files

Essential network configuration files are located in the `configs/network/` directory:

- `genesis.json`: The network's genesis file.
- `network-nodes.csv`: List of available nodes for peer discovery.

When setting up your node, refer to these files for network-specific parameters and peer information.

## Additional Resources

- [Kwil Documentation](https://docs.kwil.com)

For further assistance, join our [Discord community](https://discord.com/invite/5AMCBYxfW4) or open an issue on our [GitHub repository](https://github.com/truflation/tsn-node-operator/issues).

Welcome to the TSN network! Your participation helps build a more robust and decentralized data infrastructure.
