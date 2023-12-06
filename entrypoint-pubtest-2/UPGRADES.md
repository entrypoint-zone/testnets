# Scheduled Upgrades

## v1.3.0

### Schedule

| Date             | Testnet plan                                     |
|------------------|--------------------------------------------------|
| December 7 2023  | EntryPoint v1.3.0 is live on the testnet         |
| December 6 2023  | Submit and pass v1.3.0 software upgrade proposal |

- **Version before upgrade**: `v1.2.0`
- **Version after upgrade**: `v1.3.0` (binaries from [here](https://github.com/entrypoint-zone/testnets/releases/tag/v1.3.0))

### Upgrade Details

- **Upgrade height**: **`678842`**
- Estimated upgrade time: `2023-12-07 ~14:00 UTC`

### Upgrade Instructions (with Cosmovisor)

The steps to upgrade to the new version using Cosmovisor depends on whether you have `DAEMON_ALLOW_DOWNLOAD_BINARIES` set to `true`/`false`. If you are running Cosmovisor from a service file, this variable should be specified in the service file itself. Otherwise, you should be able to check it using `cosmovisor config`.

If you will be running Cosmovisor with `DAEMON_ALLOW_DOWNLOAD_BINARIES=true` at the time of the upgrade, the upgrade process is expected to take place automatically without the need to download binaries.

Otherwise, if you opt to run Cosmovisor with `DAEMON_ALLOW_DOWNLOAD_BINARIES=false`, you will need to run through the following steps:

- Download new binary from https://github.com/entrypoint-zone/testnets/releases/tag/v1.3.0 or obtain it from a reputable source.
- Apply environment variables: `source ~/.profile` (refer to [README](../README.md#from-scratch-using-cosmovisor) if you do not have this).
- Register the upgrade: `cosmovisor add-upgrade v1.2.0-to-v1.3.0 <path-to-downloaded-entrypointd-binary>`.
- From here on, the upgrade process is expected to take place automatically.

### Upgrade Instructions (without Cosmovisor)

The recommended steps to upgrade to the new version without Cosmovisor are as follows:

- Download new binary from https://github.com/entrypoint-zone/testnets/releases/tag/v1.3.0 or obtain it from a reputable source.
- At the upgrade height, once your node has stopped automatically, back up the `.entrypoint` directory, especially the `.entrypoint/data/priv_validator_state.json` file.
- Swap the old binary with the downloaded binary, and restart your node.
- From here on, the upgrade process is expected to take place automatically.

### Recovery

In the event that the upgrade does not succeed, the testnet maintainers will coordinate the recovery of the testnet. It is very important to be available and follow the instructions provided by the testnet maintainers.
