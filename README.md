# Ava Protocol Operator setup

This guide will walk you through the process of registering as an operator to Ava OProtocol AVS and running the Ava Protocol software.

This guide focus on running everything with-in docker container. If you don't
want to use docker container, you can follow this guide 
## Pre-requirement

An operator need to be onboard and setup their own operator with EigenLayer,
following the [official
document](https://docs.eigenlayer.xyz/eigenlayer/operator-guides/operator-introduction)

## Software/Hardware Requirement

- Operating System: Linux, MacOS
- CPU: x64/arm. 
- vCPUs: 1
- Memory: 1GiB
- Storage: 100GB
- EC2 Equivalent: c6gd.medium, m6g.medium, c7a.medium, c6g.large
- Expected Network Utilization:
    - Total download bandwidth usage: 1 Mbps
    - Upload bandwidth usage: 1 Mbps

- Incoming Ports: 9090
- Outgoing Ports: 2206

If your cloud providers support Arm CPU, we suggest to use Arm because it's more cost effective.

# Operator Setup

## 1. Clone this repository

```
git clone git@github.com:AvaProtocol/ap-operator-setup.git

cd ap-operator-setup
```

We had two directory call `holesky` and `mainnet`. To setup testnet, you will do
everything inside `hokesky` directory. For mainnet deployment, you would use
files inside `mainnet` directory.


## 2. Prepare config file and credential

Inside `holesky` directory, We will need to prepare 2 files:

- env
- config.yaml

```
cd holesky
cp .env.example .env
```

Then edit it and fill in 5 things:

- ECDSA_PRIVATE_KEY_STORE_PATH=<path-to-your-ecdsa-private-key>
- BLS_PRIVATE_KEY_STORE_PATH=<path-to-your-bls-private-key>
- OPERATOR_BLS_KEY_PASSWORD=<bls-key-password>
- OPERATOR_ECDSA_KEY_PASSWORD=<ecdsa-key-password>
- DB_PATH=<path-to-a-directory-to-hold-operator-volume>

We don't use high io so you can store on a normal volume such as gp3 wih 3000
IOPS on EC2.

Next, we will create `config.yaml` file for operator:

```
cp config.yaml.example config.yaml
```

Then update your `operator_address`.

## 3. Register your operator to AP AVS

This step is only needed to be done once per operator.

```
docker compose run ap-operator register --config=/app/config.yaml
```

To check the registration status at any given time you can also do:

```
docker compose run ap-operator register --config=/app/config.yaml
```

Next, we're ready to run operator and process job.

## 3. Starting up operator

```
cd holesky
docker compose pull

docker compose up --force-recreate
```

## Mainnet setup

The process is similarly but you would setup and run operator in the `mainnet`
directory.


# FAQ

## How to update

```
git pull 

# for testnet
cd holesky
# for mainnet
cd mainnet

# then issue a pull command to fetch latest image
docker compose pull

# finally restart with the new container
docker compose up --force-recreate
```

## How to check that my operator is working

When your operator connects to aggregator, it will reports telemetry which we
can check to ensure your operator is working properly

Beside, you will see some log indicate that the operator is working and
processing task that our aggregator asked it to do.

You can also visit the telemetry dashboard

### Testnet

https://aggregator-holesky.avaprotocol.org/telemetry
