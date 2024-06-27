# Ava Protocol Operator setup

This guide will walk you through the process of registering as an operator to Ava OProtocol AVS and running the Ava Protocol software.

This guide focus on running everything with-in docker container. If you don't
want to use docker container, you can follow this guide 

## Prerequisite

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

We had two directory call `holesky` and `ethereum`. To setup testnet, you will do
everything inside `hokesky` directory. For mainnet deployment, you would use
files inside `ethereum` directory.


## 2. Prepare config file and credential

Inside `holesky` directory, We will need to prepare 2 files: `.env` and `config.yaml`.

1. Make sure you are under `ethereum` or `holesky` direction, and prepare `.env` file
    ```
    cp .env.example .env
    ```

2. Then, edit it and fill in 5 things:

    Specify your operator’s keystore location and password. These are to be used to commit to your registered operator.
    ```
    - ECDSA_PRIVATE_KEY_STORE_PATH=
    - OPERATOR_ECDSA_KEY_PASSWORD=
    - BLS_PRIVATE_KEY_STORE_PATH=
    - OPERATOR_BLS_KEY_PASSWORD=
    ```

    Besides, the DB_PATH is to specify the local path to store your operator’s data for our AVS.
    ```
    - DB_PATH=
    ```

    We don't use high io so you can store on a normal volume such as gp3 wih 3000
    IOPS on EC2.

3. Next, we will create `config.yaml` file for operator:

    ```
    cp config.yaml.example config.yaml
    ```

    Only change the value of `operator_address` to your own operator wallet address.

    ```

## 2.b One-time task: Register your operator to Ava Protocol AVS

This step is only needed to be done once per operator.

```
docker compose run ap-operator register --config=/app/config.yaml
```

To check the registration status at any given time you can also do:

```
docker compose run ap-operator status --config=/app/config.yaml
```

Next, we're ready to run operator and process job.

## 3. Start to run our AVS
1. Make sure you are under `./ethereum` or `./holesky` directory.
2. Run the following command to start the operator
    ```
    docker compose pull

    docker compose up --force-recreate
    ```

    Once the operator is up and running, the output log will look like below.
    ```
    docker compose up --force-recreate
    [+] Running 1/0
    ✔ Container ap_operator  Created                                                                                                                                                          0.0s 
    Attaching to ap_operator
    ap_operator  | {"level":"info","ts":1719529804.5644045,"caller":"operator/operator.go:263","msg":"Connect to aggregator aggregator-holesky.avaprotocol.org:2206"}
    ap_operator  | {"level":"info","ts":1719529804.8751178,"caller":"operator/operator.go:307","msg":"Operator info","operatorId":[74,60,26,85,160,147,136,79,102,183,189,62,99,76,192,151,203,7,97,85,230,236,25,160,46,242,83,194,177,93,63,163],"operatorAddr":"0x2273e70Ea0F159985a9312e875839CbF242f162e","operatorG1Pubkey":"E([13980129839750270625587959504067205960106881892608925358182969477593110597180,2713793992502006479543294653290264953732656227600455037615150886215476630684])","operatorG2Pubkey":"E([10006440951214432193970386287330007898372605552301114697229665952718363326438+2917899138783614023915162275072742305856792653861495716209344717215206657922*u,20465317265628248898772842070116958367267377808142334627836040792686631701030+11895853732396257221594908719294998059804388586884333547663795174064486592588*u])"}
    ap_operator  | {"level":"info","ts":1719529805.3309655,"caller":"operator/operator.go:330","msg":"Starting operator."}
    ap_operator  | {"level":"info","ts":1719529805.3310997,"caller":"nodeapi/nodeapi.go:104","msg":"Starting node api server at address 0.0.0.0:9010"}
    ap_operator  | {"level":"info","ts":1719529805.33198,"caller":"metrics/eigenmetrics.go:81","msg":"Starting metrics server at port 0.0.0.0:9090"}
    ap_operator  | {"level":"info","ts":1719529805.3321455,"caller":"nodeapi/nodeapi.go:238","msg":"node api server running","addr":"0.0.0.0:9010"}
    ```

# FAQ

## How to update

```
git pull 

# cd into either mainnet or holesky directory
cd ethereum

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

### Testnet Operator Status Page

https://aggregator-holesky.avaprotocol.org/telemetry

### Ethereum Operator Status Page
Coming soon