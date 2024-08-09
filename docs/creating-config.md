# Kwil Node Operator Configuration Guide

This guide outlines essential configurations for Kwil node operators. We'll use `kwil-admin` to generate the initial configuration file.

## Database Configuration

Kwil provides a pre-configured PostgreSQL Docker image for quick setup:

```bash
docker run -p 5432:5432 --name kwil-postgres -e "POSTGRES_HOST_AUTH_METHOD=trust" \
    kwildb/postgres:latest
```

If using a custom PostgreSQL setup, configure these parameters:

```
--app.pg-db-host string   PostgreSQL host address (default "127.0.0.1")
--app.pg-db-name string   Database name (default "kwild")
--app.pg-db-pass string   Database password
--app.pg-db-port string   Database port (default "5432")
--app.pg-db-user string   Database user name (default "kwild")
```

## Performance Optimization

```
--app.db-read-timeout duration   Database read timeout (recommended: 60s, default: 5s)
-r, --root-dir string            Kwild root directory (default "~/.kwild")
```

We recommend a 60s timeout due to the potentially long execution time of deeply nested TSN queries. The configuration file will be generated in the specified root directory.

## Network Configuration

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

## Peer Configuration

```
--chain.p2p.persistent-peers string   Persistent P2P peers
--chain.p2p.seeds string              Seed nodes for peer discovery
```

Recommendations:
- Use our provided node list as seeds for initial peer discovery.
- Set critical nodes as persistent peers for constant connectivity.

## Additional Configuration

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


{lets build example
"KWILD_APP_HOSTNAME=mynode.mycompany.com",
"KWILD_APP_PG_DB_HOST=kwil-postgres",
"KWILD_CHAIN_P2P_PERSISTENT_PEERS=825594e3d8abfc91f6e15823d0ef9272a8792f31@mynode.mycompany.com:26656,e4e3883da96d8cc705f477ad7724c7dabaaa11e8@3.129.133.203:26656",
"KWILD_CHAIN_P2P_EXTERNAL_ADDRESS=http://mynode.mycompany.com:26656",
"CONFIG_PATH=/root/.kwild",
}

## Example Configuration

```bash
kwil-admin setup peer \
    --app.pg-db-host node-postgres \
    --app.hostname mynode.mycompany.com \
    --chain.p2p.external-address http://mynode.mycompany.com:26656 \
    --chain.p2p.seeds 825594e3d8abfc91f6e15823d0ef9272a8792f31@mynode.mycompany.com:26656,e4e3883da96d8cc705f477ad7724c7dabaaa11e8@3.129.133.203:26656 \
    --root-dir ./tsn-config \
    --app.db-read-timeout 60s
```