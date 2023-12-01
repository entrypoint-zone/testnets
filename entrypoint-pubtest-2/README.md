# entrypoint-pubtest-2

- [Testnet Details](#testnet-details)
- [Endpoints](#endpoints)
- [Explorers](#explorers)
- [Listings](#listings)
- [Run a Full Node](#run-a-full-node)
  - [Using Cosmovisor](#using-cosmovisor)
  - [Become a Validator](#become-a-validator)
  - [Handling Upgrades Using Cosmovisor](#handling-upgrades-using-cosmovisor)
- [More Docs and Tooling](#more-docs-and-tooling)
- [Contributors](#contributors)

## Testnet Details

- **Chain ID**: `entrypoint-pubtest-2`
- **Launch date**: 2023-10-25
- **Current version**: `v1.2.0`
- **Launch version**: `v1.1.1`
- **Genesis file**: included in this folder.

Visit the [Scheduled Upgrades](./UPGRADES.md) page for details on previous, current and upcoming versions.

## Endpoints

RPC:

- https://testnet-rpc.entrypoint.zone/
- https://entrypoint-testnet-rpc.itrocket.net/
- https://entrypoint-testnet-rpc.stakerhouse.com/
- https://rpc.entrypoint.banyumas-ngapak.online/
- https://entrypoint-testnet.rpc.nodex.one

REST:

- https://testnet-rest.entrypoint.zone/swagger/
- https://entrypoint-testnet-api.itrocket.net/
- https://entrypoint-testnet-rest.stakerhouse.com/
- https://entrypoint-testnet.api.nodex.one

## Explorers

- https://explorer.entrypoint.zone/entrypoint provided by [Simply Staking](https://simplystaking.com/)
- https://testnet.ping.pub/entrypoint provided by [Ping.pub](https://ping.pub/)
- https://explorer.hexnodes.co/entrypoint provided by [Hexnodes](https://hexnodes.co/)
- https://test.anode.team/entrypoint provided by [ANODE.TEAM](https://anode.team/)
- https://exp.utsa.tech/entrypoint-test provided by [lesnik | UTSA](https://utsa.gitbook.io/services)
- https://explorer.stavr.tech/Entrypoint-Testnet provided by [STAVR](https://github.com/obajay)
- https://testnet.itrocket.net/entrypoint provided by [ITRocket](https://itrocket.net)
- https://cosmotracker.com/entrypoint provided by [StakerHouse](https://stakerhouse.com/).
- https://explorer.moonbridge.team/entrypoint-test provided by [Moonbridge](https://services.moonbridge.team/moonbridge/).
- https://testnet.explorer.tcnetwork.io/entrypoint provided by [TC NETWORK](https://tcnetwork.io/).
- https://explorer.indonode.net/entrypoint-testnet provided by [Indonode](https://indonode.net/).
- https://cosmoscan.top/EntryPoint provided by [2xstake.com](https://2xStake.com).
- https://explorer.nodex.one/entrypoint provided by [nodex.one](https://docs.nodex.one).
- https://explorer.moonbridge.team/entrypoint-test provided by [Moonbridge](https://moonbridge.team/).

## Listings

- https://github.com/ping-pub/ping.pub/blob/main/chains/testnet/entrypoint.json
- https://github.com/cosmos/chain-registry/tree/master/testnets/entrypointtestnet
- https://testnet.cosmos.directory/entrypointtestnet

## Run a Full Node

> Based on material from:
> 
> - https://docs.cosmos.network/main/tooling/cosmovisor
> - https://docs.osmosis.zone/networks/join-mainnet/#set-up-cosmovisor

### Using Cosmovisor

> Note: if you are syncing from scratch (height 0), you will need to start from the [v1.1.1 binary](https://github.com/entrypoint-zone/testnets/releases/tag/v1.1.1) instead. After the node starts syncing, you will need to follow the [upgrades log](UPGRADES.md) to download the right binaries according to the upgrades that were performed historically.

Download binary and genesis (Note: genesis only needed if syncing from scratch):

- Binary from: https://github.com/entrypoint-zone/testnets/releases/tag/v1.2.0.
- Genesis from: https://github.com/entrypoint-zone/testnets/blob/main/entrypoint-pubtest-2/genesis.json.

For convenience, copy the downloaded binary to `$GOPATH/bin/` with the name `entrypointd` for easy execution:

- `cp <downloaded-binary> $GOPATH/bin/entrypointd` (make sure `$GOPATH` variable exists)

Initialise node config files:

- `entrypointd init <node-name> --chain-id entrypoint-pubtest-2`.

Install and configure Cosmovisor:

- Install Cosmovisor: `go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest`.
- If you need a specific version of go you can run e.g. `go install golang.org/dl/go1.20@latest`. The executable will be `go1.20` in this example.
- Set environment variables:
    ```bash
    echo "# Setup Cosmovisor" >> ~/.profile
    echo "export DAEMON_NAME=entrypointd" >> ~/.profile
    echo "export DAEMON_HOME=$HOME/.entrypoint" >> ~/.profile
    echo "export DAEMON_ALLOW_DOWNLOAD_BINARIES=true" >> ~/.profile
    echo "export DAEMON_LOG_BUFFER_SIZE=512" >> ~/.profile
    echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
  
    # Check https://docs.cosmos.network/main/tooling/cosmovisor for more configuration options.
    ```
- Apply environment variables: `source ~/.profile`.
- Initialise Cosmovisor directories: `cosmovisor init <path-to-entrypointd-binary>`.

Configure node:

- Configure `minimum-gas-prices` in `nano ~/.entrypoint/config/app.toml`.
  - Recommended value: `0.01ibc/8A138BC76D0FB2665F8937EC2BF01B9F6A714F6127221A0E155106A45E09BCC5`.
- Configure `persistent_peers` in `nano ~/.entrypoint/config/config.toml`.
  - Recommended value: `persistent_peers = "81bf2ade773a30eccdfee58a041974461f1838d8@185.107.68.148:26656,d57c7572d58cb3043770f2c0ba412b35035233ad@80.64.208.169:26656"`.
- Move the downloaded genesis file to `~/.entrypoint/config/genesis.json` if you're syncing from scratch.
- Configure State Sync in `$DAEMON_HOME/config/config.toml` by following [the guide in the explorer](https://testnet.ping.pub/entrypoint/statesync).
  - You will need two RPC servers and also the `trust_height` and `trust_hash` values.
  - Alternative RPC servers can be obtained from the [Endpoints](#endpoints) or [More Docs And Tooling](#more-docs-and-tooling) sections.

At this point `cosmovisor run` will be the equivalent of running `entrypointd`. In fact, to run the node you can use `cosmovisor run start`. **It is highly recommended to run the EntryPoint as a service**, so that it can run in the background. You will need to replicate the environment variables defined above.

Here's an example service file with some placeholder (`<...>`) values:

```bash
[Unit]
Description=EntryPoint Node
After=network.target

[Service]
Type=simple
User=<USER>
ExecStart=<HOME>/go/bin/cosmovisor run start --home <NODE-HOME>
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

Environment="DAEMON_NAME=entrypointd"
Environment="DAEMON_HOME=/home/<USER>/.entrypoint"  # Double-check this!
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=true"
Environment="DAEMON_LOG_BUFFER_SIZE=512"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"

[Install]
WantedBy=multi-user.target
```

At this point you should visit the [Scheduled Upgrades](./UPGRADES.md) page for details on current and upcoming versions.

### Become a Validator

EntryPoint currently uses `ibc/8A138BC76D0FB2665F8937EC2BF01B9F6A714F6127221A0E155106A45E09BCC5` (ATOM) as the fee token and `uentry` (ENTRY) for voting power. In order to become a validator you will need to request an amount of both of these tokens from the testnet maintainers.

### Handling Upgrades Using Cosmovisor

The public testnet is expected to require upgrades as new features are added or bugs are discovered. This is a rough guide for handling upgrades using Cosmovisor:

- Download new binary from https://github.com/entrypoint-zone/testnets/releases or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile`.
- Register the upgrade: `cosmovisor add-upgrade <upgrade-name> <path-to-new-entrypointd-binary>`.
- Wait for the upgrade height and monitor your node's logs to ensure everything goes well.

## More Docs And Tooling

- [Klein - Install Nodes With Just a Click](https://klein.run/), provided by [node101](https://node101.io/).
- [Testnet information and guides](https://docs.nodex.one/networks/testnet/entrypoint) provided by [nodex.one](https://twitter.com/NodeXEmperor).
- [State sync guide](https://ivans-organization-17.gitbook.io/cosmos-node/entrypoint) provided by [tarabukinivan](https://explorer.entrypoint.zone/entrypoint/staking/entrypointvaloper1hzw08lptr8fa07f35ff0azxt7qtsh90srqpfx7).
- [Guides in Russian](https://teletype.in/@lesnik13utsa/ngyL41zQdXu) provided by [lesnik | UTSA](https://utsa.gitbook.io/services).
- [Guides, snapshot services](https://itrocket.net/services/testnet/entrypoint) provided by [ITRocket](https://itrocket.net/).
- [Telegram governance bot](https://t.me/itrocket_testnet_proposal_bot) provided by [ITRocket](https://itrocket.net).
- [RPC scanner](https://itrocket.net/services/testnet/entrypoint/public-rpc/) provided by [ITRocket](https://itrocket.net).
- [Testnet information and guides](https://stakerhouse.com/testnets/entrypoint/) provided by [StakerHouse](https://stakerhouse.com/).
- [Guides in Turkish](https://github.com/molla202/Entrypoint-pubtest-2/blob/main/README.md) provided by [CoreNode](CoreNode.info).
- [Guides, state sync](https://services.moonbridge.team/moonbridge/testnet/entrypoint) provided by [Moonbridge](https://services.moonbridge.team/moonbridge/).
- [Guides, state sync, snapshots](https://docs.indonode.net/testnet/entrypoint) provided by [Indonode](https://indonode.net/).
- [Testnet information and guides](nodeist.net/Entrypoint/) provided by [nodeist](https://nodeist.net/).

## Contributors

Special thanks to:

- [Ping.pub](https://ping.pub/)
- [node101](https://node101.io/)
- [Hexnodes](https://hexnodes.co/)
- [nodex.one](https://twitter.com/NodeXEmperor)
- [ANODE.TEAM](https://anode.team/)
- [lesnik | UTSA](https://utsa.gitbook.io/services)
- [tarabukinivan](https://explorer.entrypoint.zone/entrypoint/staking/entrypointvaloper1hzw08lptr8fa07f35ff0azxt7qtsh90srqpfx7)
- [STAVR](https://github.com/obajay)
- [ITROCKET](https://itrocket.net)
- [StakerHouse](https://stakerhouse.com/)
- BANYUMAS||NGAPAK
- [CoreNode](CoreNode.info)
- [Moonbridge](https://services.moonbridge.team/moonbridge/)
- [TC NETWORK](https://tcnetwork.io/)
- [Indonode](https://indonode.net/)
- [2xstake.com](https://2xStake.com)
- [nodex.one](https://docs.nodex.one)
- [nodeist](https://nodeist.net/)
- [Moonbridge](https://moonbridge.team/)
