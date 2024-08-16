# TSN Node Configuration Guide

This guide outlines essential configurations for TSN Node operators. We'll use `kwil-admin` to generate the initial configuration file.

## Database Configuration

Kwil provides a pre-configured PostgreSQL Docker image for quick setup:

```bash
docker run -p 5432:5432 --name kwil-postgres -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    kwildb/postgres:latest
```

## Network Configuration

### Genesis File

```
-g, --genesis string   Path to genesis file
```

You should use this option to specify the genesis file provided in the `configs/network/<network_name>` directory of the tsn-node-operator repository. This is crucial for joining the correct network.

- The genesis file is mandatory for joining an existing TSN network.
- It defines the initial state of the blockchain, including initial validators and other network parameters.
- Using the correct genesis file ensures your node starts with the same state as other nodes in the network.

Example usage:

```bash
kwil-admin setup peer -g ./configs/network/staging/genesis.json \
  --chain.p2p.persistent-peers c6d2ea1e573d207cc31b7e17c771ab8ca2091b22@staging.node-1.tsn.test.truflation.com:26656,34599966ce4b67628f4cfa99fdca74ea2d039018@staging.node-2.tsn.test.truflation.com:26656 \
  [other options]
```

### Listening Addresses

```
--app.jsonrpc-listen-addr string    JSON-RPC listen address (default "0.0.0.0:8484")
--chain.p2p.listen-addr string      P2P listen address (default "tcp://0.0.0.0:26656")
--chain.p2p.external-address string P2P external address for peer advertising
--app.hostname string               Server hostname for RPC endpoint
```

Important:
- Open the JSON-RPC port (8484) and P2P port (26656) in your firewall.
- Ensure the P2P external address is reachable by other peers.
- `hostname` and `p2p.external-address` can be the same or different based on your setup.

### Peer Configuration

```
--chain.p2p.persistent-peers string   Persistent P2P peers
--chain.p2p.seeds string              Seed nodes for peer discovery
```

Recommendations:
- Set critical nodes as persistent peers for constant connectivity. We recommend that you add our TSN nodes as persistent peers.
    For example: `--chain.p2p.persistent-peers c6d2ea1e573d207cc31b7e17c771ab8ca2091b22@staging.node-1.tsn.test.truflation.com:26656,34599966ce4b67628f4cfa99fdca74ea2d039018@staging.node-2.tsn.test.truflation.com:26656`
- Use the node list provided in `configs/network/<network_name>/network-nodes.csv` as seeds for initial peer discovery

### Custom Database Configuration

If using a custom PostgreSQL setup, configure these parameters:

```
--app.pg-db-host string   PostgreSQL host address (default "127.0.0.1")
--app.pg-db-name string   Database name (default "kwild")
--app.pg-db-pass string   Database password
--app.pg-db-port string   Database port (default "5432")
--app.pg-db-user string   Database user name (default "kwild")
```

## Additional Configuration

```
--app.db-read-timeout duration   Database read timeout (recommended: 60s, default: 5s)
-r, --root-dir string            Kwild root directory (default "~/.kwild")
```

We recommend a 60s timeout due to the potentially long execution time of deeply nested TSN queries. The configuration file will be generated in the specified root directory.

For a complete list of options:
- Refer to the [Kwil documentation](https://docs.kwil.com)
- Run `kwil-admin --help`

After generating the configuration, you can manually edit the file for further customization.

## Generating the Configuration

Use `kwil-admin` to generate your configuration file:

```bash
kwil-admin setup peer [flags]
```

Replace `[flags]` with the desired configuration options from this guide.

Remember to review and adjust these settings based on your specific requirements and infrastructure.

## Example Configuration

```bash
kwil-admin setup peer \
    -g ./configs/network/staging/genesis.json \
    --chain.p2p.persistent-peers c6d2ea1e573d207cc31b7e17c771ab8ca2091b22@staging.node-1.tsn.test.truflation.com:26656,34599966ce4b67628f4cfa99fdca74ea2d039018@staging.node-2.tsn.test.truflation.com:26656
    --app.hostname mynode.mycompany.com \
    --chain.p2p.external-address http://mynode.mycompany.com:26656 \
    --root-dir ./tsn-config \
    --app.db-read-timeout 60s
```