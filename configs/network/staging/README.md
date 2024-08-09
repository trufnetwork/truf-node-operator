# Network Configuration: Staging

This directory contains the configuration files for the Truflation Staging Network.

## Files

- `genesis.json`: The genesis file for the staging network.
- `network-nodes.csv`: A list of initial validator nodes in the network.

## Public Keys

The `node_pubkey` in the `network-nodes.csv` file is represented in raw hexadecimal format.

For information on transforming these public keys to other formats used in node configurations, please refer to the CometBFT documentation on public key cryptography:

https://docs.cometbft.com/v0.38/spec/core/encoding#public-key-cryptography