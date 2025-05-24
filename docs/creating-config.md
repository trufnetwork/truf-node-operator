# TRUF.NETWORK Node Configuration Guide

This guide outlines essential configurations for TRUF.NETWORK Node operators. We'll use `kwild` to generate the initial configuration file.

## Database Configuration

Kwil provides a pre-configured PostgreSQL Docker image for quick setup:

```bash
docker run -p 5432:5432 --name kwil-postgres -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    -v postgres_data:/var/lib/postgresql/data \
    kwildb/postgres:latest
```

## Network Configuration

### Genesis File

```
-g, --genesis string   Path to genesis file
```

You should use this option to specify the genesis file provided in the `configs/network/v2` directory of the truf-node-operator repository. This is crucial for joining the correct network.

- The genesis file is mandatory for joining an existing TRUF.NETWORK.
- It defines the initial state of the blockchain, including initial validators and other network parameters.
- Using the correct genesis file ensures your node starts with the same state as other nodes in the network.

Example usage:

```bash
kwild setup init -g ./configs/network/v2/genesis.json \
  --p2p.bootnodes "<revealed-soon>" \
  [other options]
```

### Listening Addresses

```
--rpc.listen                        JSON-RPC listen address (default "0.0.0.0:8484")
--p2p.listen string                 P2P listen address (default "tcp://0.0.0.0:6600")
--p2p.external_address string       P2P external address for peer advertising
```

Important:
- Open the JSON-RPC port (8484) and P2P port (6600) in your firewall.
- Ensure the P2P external address is reachable by other peers.

### Peer Configuration

```
--p2p.bootnodes string          Bootstrap nodes for initial network connection
--statesync.trusted_providers   List of the trusted snapshot providers in the node ID format
```

Recommendations:
- Set critical nodes as bootnodes for initial network connection. We recommend that you add our TN nodes as bootnodes.
    For example: `--p2p.bootnodes "revealed-soon"`
- Use the node list provided in `configs/network/v2/network-nodes.csv` as seeds for initial peer discovery
- Configure trusted providers for state sync using `--statesync.trusted_providers` with node IDs from `configs/network/v2/network-nodes.csv`

### Custom Database Configuration

If using a custom PostgreSQL setup, configure these parameters:

```
--db.host string   PostgreSQL host address (default "127.0.0.1")
--db.dbname string   Database name (default "kwild")
--db.pass string   Database password
--db.port string   Database port (default "5432")
--db.user string   Database user name (default "kwild")
```

## Additional Configuration

```
--db.read_timeout duration   Database read timeout (recommended: 60s, default: 5s)
```

We recommend a 60s timeout due to the potentially long execution time of deeply nested TN queries. The configuration file will be generated in the specified root directory.

For a complete list of options:
- Refer to the [Kwil documentation](https://docs.kwil.com)
- Run `kwild --help` or `kwild setup init --help`

After generating the configuration, you can manually edit the file for further customization.

## Generating the Configuration

Use `kwild` to generate your configuration file:

```bash
kwild setup init [flags]
```

Replace `[flags]` with the desired configuration options from this guide.

Remember to review and adjust these settings based on your specific requirements and infrastructure.

## Example Configuration

```bash
kwild setup init \
    -g ./configs/network/v2/genesis.json \
    --p2p.bootnodes "revealed-soon" \
    --p2p.external_address mynode.mycompany.com:port \
    --root ./tn-config \
    --db.read_timeout 60s
```
