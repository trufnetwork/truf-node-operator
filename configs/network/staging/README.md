# Network Configuration: Staging

This directory contains the configuration files for the Truflation Staging Network.

## Files

- `genesis.json`: The genesis file for the staging network.
- `network-nodes.csv`: A list of initial validator nodes in the network.

## Public Keys

The `node_pubkey` in the `network-nodes.csv` file is represented in raw hexadecimal format.

For information on transforming these public keys to other formats used in node configurations, please refer to the CometBFT documentation on public key cryptography:

https://docs.cometbft.com/v0.38/spec/core/encoding#public-key-cryptography


### Validators

This list identifies the validator nodes by their identification. For a list of nodes information to use as peer, please refer to the [./network-nodes.csv](./network-nodes.csv) file.

| moniker / name | validator id | responsible |
| ------------- | ------------- | ------------- |
| truflation-node-01 | 4e0b5c952be7f26698dc1898ff3696ac30e990f25891aeaf88b0285eab4663e1 | @outerlook @MicBun |
| truflation-node-02 | 0c830b69790eaa09315826403c2008edc65b5c7132be9d4b7b4da825c2a166ae | @outerlook @MicBun |
| kwilteam0 | dc2240baa54023b06a3cfa95a5d0c8fccff6f5cd6b918e91bc9f4788cc3fc2cc | @truflation/team-tsn-kwil |
| depin-truflation-mn-node-svc-nl-01 (Simply Staking) | efa792bf1c3d39820bd901a83756fa3a35942945c3d614a08017549792a842bb | @truflation/team-simply-staking |
| Allnodes.dev | 8f5b6ca7e5cf4c38373ababf6cea03383d610cf22e33283c55dce5a14dc80d0e | @truflation/team-allnodes |
| Laguna-Node-01 | f2a67c72c8c1ddd4a60541e725db9aaffbb567e328ea4e685ea67dc56fb3337b | @alextsehx |
| Knit-Node-01 | b1e75c2305bc539b781950a8ef4218fb8830e47abc07ff1a4d2d870e809e1323 | @alextsehx |
| Hydrox-Node-01 | faa49ddc6e145687e17dc51183c8cb4dfe7efc56ff17da8258135cf3a12a0945 | @alextsehx |
| Trustednode-Node-01 | be177e0505be04b3d84980d36e717fa3c24878df5a242a136c3583d10cc83687 | @alextsehx |
| Link River | 59b398749cbeb982c565d7807ebd4b764e2042bc3d48cf9bf876a4333e2c404e | @truflation/team-link-river |
| truflation-staging-0 (Linkpool) | 8096bc52f64624a8caef73d4817f1abff8832467075f5134ce28bec134567e4b | @truflation/team-link-pool |
| TruLabs-Node-01 | df817faabddf5ccdc883458c93d1b0fd3eaa4de3f6119c38ddc7b88ba0c83a04 | @alextsehx|
| truflation-testnet-1 (Stakin) | ebe48104aa52a2f4c361a3b1ae8ca46e5df957fa8c3492ffb1fd9653a7b26894 | @truflation/team-stakin |
| Node Stake | f4e931c0029ad25486259a49d287d0dd4002c3f92f4961292e0321fc88080f12 | @truflation/team-nodestake |
| Usher labs | eee4ec38245dc931e47c01e1b33c7a9d7beb8b7830be974c3a097ac0820a5923 | @outerlook |
| Validator Cloud | 0c431c04583caa21be4de430f5fb8e9399f75aac37190dbeb31e20619e406b9a | @truflation/team-validator-cloud |
| QuantNode | 6587ed90eeb025a940119fed727567e7a160fcb37996ec86f13af6097de35bcd | @truflation/team-quantnode |
| Matrixed.Link | e8541b9cb6edfcb50042a965dc4b73cb44d7207c85920dd5ffab9ea48c992aaf | @truflation/team-matrixed-link |
| NorthWest | 318746ba3a770a99fe1dc038f01fe44f2e372b0681a29f68bc972a3e935e6d92 | @truflation/team-northwest-nodes |