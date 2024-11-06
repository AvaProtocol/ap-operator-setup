## Introduction

This docker stack allow you to run and manage https://github.com/Layr-Labs/cerberus remote signer. Please check the repo for motivation  behind the remote signer.

## Setup

1. Change your shell current working directory to this folder.

2. Bring up docker compose

```
docker compose pull
docker compose up -d
```

3. Connect cerberus into your operator network

```
# on mainnet
docker network connect ava_ethereum cerberus

# on testnet
docker network connect ava cerberus
```

## Operator update

Now you will have cerberus running and reachable from your operator. You can edit your operator.yaml config file and add this in


```
bls_remote_signer:
  grpc_url: "127.0.0.1:50051"
  public_key: "<pub-key-from-cerberus>"
  password: "<password-from-cerberus>"
```

You can delete the  bls_private_key_store_path in the config file and delete the bls password from your env.
