# LAIR3/BDK9 layer 3 Blockchain Deployment Kit v9
each of these versions is a continuation of the improvements and a fork of the kurtosis-cdk from Polygon labs for local devnet research and development with audits of continuous improvements and internal documentation from version BDK5. To date I have not made any commits to the original tree <br />
lair: a place where a wild animal, especially a fierce or dangerous one, lives<br />
pronounced: layer three Blockchain Deployment Kit<br />

A [Kurtosis](https://github.com/kurtosis-tech/kurtosis) package that deploys a private, portable, and modular Blockchain Deployment Kit layer3 BDK as devnet<br />
LAIR3-BDK5 is derived from modest improvements to Polygon-SDK and Kurtosis-CDK dual licenced with inspiration from<br /><br />
<a href="http://people.csail.mit.edu/silvio/Selected%20Scientific%20Papers/Proof%20Systems/The_Knowledge_Complexity_Of_Interactive_Proof_Systems.pdf">The Knowledge Complexity of Interactive Proof Systems</a><br /><br />


instructions are somewhat specific to Ubuntu 22.04LTS<br />

# reference links<br />
<a href="https://docs.kurtosis.com/">kurtosis-docs</a><br />
<a href="https://docs.kurtosis.com/how-to-local-eth-testnet">kurtosis ETH local testnet</a><br />
<a href="https://docs.kurtosis.com/how-to-compose-your-own-testnet">kurtosis local custom testnet</a>
<a href="https://lighthouse-book.sigmaprime.io/">lighthouse</a><br />
<a href="https://docs.polygon.technology/cdk/">polygon-cdk</a><br />
<a href="https://docs.polygon.technology/cdk/agglayer/overview/">agglayer-docs</a><br />
<a href="https://rollupjs.org/configuration-options/#output-manualchunks">manualchunks</a><br />
<a href="https://docs.timescale.com/self-hosted/latest/install/installation-docker/">pgvectorscale timescale</a><br />
<a href="https://docs.docker.com/engine/install/">docker engine install</a><br />
<a href="https://training.github.com/downloads/github-git-cheat-sheet/">github quick reference</a><br />
<a href="https://docs.kurtosis.com/how-to-local-eth-testnet">local ETH testnet</a><br />
<a href="https://docs.kurtosis.com/how-to-compose-your-own-testnet/">private local ETH testnet</a><br />

to begin install the following requirements<br />

#  go1.21.6 install on Ubuntu Linux 22.04LTS for amd64 using bash<br />
```bash
#get wget
sudo apt install wget
#wget go
wget https://go.dev/dl/go1.21.6.linux-amd64.tar.gz
#remove existing go
sudo rm -rf /usr/local/go
# possibly necessary
sudo apt remove golang $$ autoremove
#extract verbosely with force
sudo tar -xvf go1.21.6.linux-amd64.tar.gz -C /usr/local/
#add go path to ./bashrc current user with default Ubuntu 22 bash shell
echo 'export PATH="$PATH:/usr/local/go/bin"' | sudo tee -a ./bashrc
#refresh your bash shell
source $HOME/.bashrc
# verify correct version install
go version
```

# nodejs install<br />
```bash
nodejs -v
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install nodejs -y
nodejs -v
```

# docker install<br />
```bash
# docker install https://docs.docker.com/engine/install/ubuntu/
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker ps
```

# kurtosis install
https://docs.kurtosis.com/install/
```bash
echo "deb [trusted=yes] https://apt.fury.io/kurtosis-tech/ /" | sudo tee /etc/apt/sources.list.d/kurtosis.list
sudo apt update
sudo apt install kurtosis-cli
```

# <a href="https://docs.kurtosis.com/upgrade/">kurtosis-engine upgrade</a><br />
```bash
sudo apt update && sudo apt install --only-upgrade kurtosis-cli
kurtosis engine restart
```


# polygon-cli install
blockchain swiss army knife source build<br />

```bash
snap install yq
sudo apt install bc protoc
git clone https://github.com/maticnetwork/polygon-cli.git
cd polygon-cli
make install
```
# foundry install 
```bash
curl -L https://foundry.paradigm.xyz | bash
source $HOME/.bashrc
foundryup
cd BDK5
git config --global user.email "codephreak@dmg.finance"
git config --global user.name "Professor-Codephreak"
forge init --force
```
To begin make sure you have installed the above requirements including [Docker](https://docs.docker.com/get-docker/) and [Kurtosis](https://docs.kurtosis.com/install/)

run tool_check to test for version requirements

```bash
sh scripts/tool_check.sh
```

Once that is good and installed on your system, you can run the following command to locally deploy the complete BDK stack

this will take between 5 and 20 minutes depending on your hardware

```bash
kurtosis clean --all
kurtosis run --enclave bdk-v9 --args-file params.yml --image-download always .
```

![Architecture Diagram](./docs/img/starlark.png)

To begin install [Docker](https://docs.docker.com/get-docker/) and [Kurtosis](https://docs.kurtosis.com/install/).

run tool_check to test for version requirements

```bash
sh scripts/tool_check.sh
```

Once that is good and installed on your system, you can run the following command to locally deploy the complete BDK stack

this will take between 5 and 20 minutes depending on your hardware

```bash
kurtosis clean --all
kurtosis run --enclave bdk-v9 --args-file params.yml --image-download always .
```

The command above deploys a BDK stack using [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node) as the sequencer. Alternatively, to launch a CDK stack using [cdk-erigon](https://github.com/0xPolygonHermez/cdk-erigon) as a sequencer, you can run the following command.

```bash
kurtosis run --enclave bdk-v9 --args-file cdk-erigon-sequencer-params.yml --image-download always .
```

# simple L2 RPC test call.

First, you will need to figure out which port Kurtois uses for the RPC. You can get a general feel for the entire network layout by running the following command:

```bash
kurtosis enclave inspect bdk-v9
```

# view port mapping
within enclave `bdk-v9` for `zkevm-node-rpc` service and `trusted-rpc`storing the RPC URL as an environment variable:<br />

```bash
export ETH_RPC_URL="$(kurtosis port print bdk-v9 zkevm-node-rpc-001 http-rpc)"
```

That is the same environment variable that `cast` uses, so you should now be able to run this command. Note that the steps below will assume you have the [Foundry toolchain](https://book.getfoundry.sh/getting-started/installation) installed.

```bash
cast block-number
```

By default, the CDK is configured in `test` mode, which means there is some pre-funded value in the admin account with address `0xE34aaF64b29273B7D567FCFc40544c014EEe9970`.

```bash
cast balance --ether 0xE34aaF64b29273B7D567FCFc40544c014EEe9970
```

# sending transaction

```bash
export PK="0x12d7de8621a77640c9241b2595ba78ce443d05e94090365ab3bb5e19df82c625"
cast send --legacy --private-key "$PK" --value 0.01ether 0x0000000000000000000000000000000000000000
```

# multisend transactions with polygon-cli [polygon-cli](https://github.com/maticnetwork/polygon-cli) installed.

```bash
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$PK" --verbosity 700 --requests 500 --rate-limit 5 --mode t
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$PK" --verbosity 700 --requests 500 --rate-limit 10 --mode t
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$PK" --verbosity 700 --requests 500 --rate-limit 10 --mode 2
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$PK" --verbosity 700 --requests 500 --rate-limit 3  --mode uniswapv3
```
```

```bash
sudo apt install postgresql-client-common
```

foundry creates script and src folders

The LAIR3-BDK5 Layer 3 Blockchain Development Kit is designed to facilitate the deployment and management of advanced blockchain solutions specifically supporting zkEVM Rollup and Validium technologies. Merging the best of the Kurtosis SDK, polygon-cli, avalanche subnet-evm and ignite with Starlark the Blockchain Deployment Kit facilitates the creation and deployment of customized blockchain environments with enhanced observability and testing capabilities.

The primary goal is to to make blockchain deployment a sane operation accessiable to regular developers interested in dapplications development.

# set PostgreSQL master password
```bash
export POSTGRES_MASTER_PASSWORD="your_secure_password"
```

# Set Validator PostgreSQL Password

Log into PostgreSQL
```bash
sudo -u postgres psql
ALTER USER validator1 WITH PASSWORD 'new_validator1_password';
\q
```

# check postgresql installation
```bash
sudo apt install postgresql-client-common
psql -U master_user -h 127.0.0.1 -p 5432 -d master
\dx
SELECT * FROM pg_extension WHERE extname = 'vectorscale';
```

# optional upggrade PostgreSQL modify pgvectorscale as docker build using toolbox.Dockerfile
```bash
docker build -t blockchaindeploymentkit/bdk-toolbox-postgres-pgvectorscale -f docker/toolbox.Dockerfile .
docker login
docker tag blockchaindeploymentkit/bdk-toolbox-postgres-pgvectorscale blockchaindeploymentkit/bdk-toolbox-postgres-pgvectorscale
docker push blockchaindeploymentkit/bdk-toolbox-postgres-pgvectorscale
```

# remove bdk-toolbox-postgres-pgvectorscale
```bash
docker rmi blockchaindeploymentkit/bdk-toolbox-postgres-pgvectorscale
docker system prune -a
docker compose version
```

# troubleshoot ###################################################################

# kurtosis logs directory error

Create Missing Directories:
If directories /var/log/kurtosis/ and similar are missing you can manually create these directories to resolve the issue

```bash
sudo mkdir -p /var/log/kurtosis/2024/31
sudo chown -R $USER:docker /var/log/kurtosis
```
Verify Docker Permissions:
```bash
sudo chmod -R 755 /var/log/kurtosis
````
Restart Kurtosis and Docker:
```bash
kurtosis engine restart
sudo systemctl restart docker
```
requirements include but not limited to<br />

# zkevm_bridge logs ./lib/zkevm_bridge.star<br />
```bash
kurtosis service logs bdk-v9 zkevm-bridge-ui-001
```

# run the bridge UI standalone<br />
```bash
docker run -it --rm -p 8080:80 leovct/zkevm-bridge-ui:multi-network
```
generic instruction
```bash
docker run --name zkevm-bridge-ui -p 80:8080 -v /path/to/.env:/app/.env leovct/zkevm-bridge-ui:multi-network
```

# erigon standalone

```bash
docker --version
docker network create erigon-net
docker run -d --name cdk-erigon-sequencer --network erigon-net -p 8123:8123 -p 6900:6900 -p 9091:9091 -p 6060:6060 -v erigon-data:/home/erigon/data -e CDK_ERIGON_SEQUENCER=1 hermeznetwork/cdk-erigon:2.0.0-beta15 sh -c "sleep 10 && cdk-erigon --pprof=true --pprof.addr 0.0.0.0 --config /etc/cdk-erigon/config.yaml"
docker logs -f cdk-erigon-sequencer
```

# recomended diagnostics
```bash
sudo apt install netstat hardinfo
```

# run the zkevm-prover standalone
```bash
docker run -it --rm -p 50071:50071 -p 50061:50061 hermeznetwork/zkevm-prover:v6.0.2 /bin/bash
```
# ENCLAVES

# list enclaves
```bash
kurtosis enclave ls
```

# Inspect the Kurtosis enclave
```bash
kurtosis enclave inspect bdk-v9
```

# Check the logs of the failing service
```bash
kurtosis service logs bdk-v9 zkevm-bridge-ui-001
```
# Open a service shell
enter the service container to manually inspect and debug example brige-ui<br />
```bash
kurtosis service shell bdk-v9 zkevm-bridge-ui-001
```

# Environment Setup
Clone the Repository and Install Dependencies

```bash
git clone https://github.com/LAIR3/BDK5.git
cd BDK5
sh scripts/tool_check.sh
```

# Clean Up Previous Environments

```bash
kurtosis clean --all
```

# docker remove
stop all running containers
```bash
docker stop $(docker ps -aq)
```
Remove all containers:
```bash
docker rm $(docker ps -aq)
```

Remove all images:
```bash
docker rmi -f $(docker images -q)
```

Remove all volumes:
```bash
docker volume rm $(docker volume ls -q)
```
Remove all networks:
```bash
docker network rm $(docker network ls -q)
```
Prune the system:
```bash
docker system prune -a --volumes
```

```bash
docker network ls
docker network inspect NAME
```

############ START ############
# BDK-v9 as Kurtosis Enclave

```bash
kurtosis run --enclave BDK-v5 --args-file params.yml --image-download always .
```

# Inspect Enclave for Dashboard Details

```bash
kurtosis enclave ls
```

```bash
kurtosis enclave inspect bdk-v9
```

# Fetch Service Logs

```bash
kurtosis service logs bdk-v9 zkevm-agglayer-001
```

# Add Permissionless Node

```bash
yq -Y --in-place 'with_entries(if .key == "deploy_zkevm_permissionless_node" then .value = true elif .value | type == "boolean" then .value = false else . end)' params.yml
kurtosis run --enclave bdk-v9 --args-file params.yml --image-download always .
```

# Sync External Permissionless Node

```bash
cp /path/to/external/genesis.json templates/permissionless-node/genesis.json
yq -Y --in-place 'with_entries(if .key == "deploy_zkevm_permissionless_node" then .value = true elif .value | type == "boolean" then .value = false else . end)' params.yml
kurtosis run --enclave bdk-v9 --args-file params.yml --image-download always .
```

# Simple RPC Calls

```bash
export ETH_RPC_URL="$(kurtosis port print cdk-v1 zkevm-node-rpc-001 http-rpc)"
cast block-number
cast balance --ether 0xE34aaF64b29273B7D567FCFc40544c014EEe9970
```

# chain interactions
# Send Transactions

```bash
cast send --legacy --private-key 0x12d7de8621a77640c9241b2595ba78ce443d05e94090365ab3bb5e19df82c625 --value 0.01ether 0x0000000000000000000000000000000000000000
```

# Load Testing with Polygon CLI
```bash
polycli loadtest --requests 500 --legacy --rpc-url $ETH_RPC_URL --verbosity 700 --rate-limit 5 --mode t --private-key 0x12d7de8621a77640c9241b2595ba78ce443d05e94090365ab3bb5e19df82c625
```

# Observability Tools

# <a href="https://prometheus.io/">Prometheus</a>
Prometheus captures all the metrics for the running services.
Accessible via: http://127.0.0.1:49678

# Prometheus Targets

```
open http://127.0.0.1:49651/targets
```

# <a href="https://grafana.com/">Grafana</a>
Dashboards for visualizing metrics.
Accessible via: http://127.0.0.1:49701

# run grafana as a standalone
```bash
docker run -d --name=grafana-001 -p 49701:3000 grafana/grafana:latest

docker run -d --name=grafana -p 3000:3000 grafana/grafana:latest
docker start grafana
open http://localhost:49701/login
docker logs grafana
docker logs grafana-001
docker stop graphana
```

# View Grafana Dashboard

```bash
open http://127.0.0.1:49701/dashboards
open http://127.0.0.1:49701/login
```

# Panoptichain
Monitors on-chain metrics such as blocks, transactions, and smart contract calls.
Accessible via: http://127.0.0.1:49651






# notes from Polygon CDK Kurtosis Package

A [Kurtosis](https://github.com/kurtosis-tech/kurtosis) package that deploys a private, portable, and modular [Polygon CDK](https://docs.polygon.technology/cdk/) devnet over [Docker](https://www.docker.com/) or [Kubernetes](https://kubernetes.io/).

Specifically, this package will deploy:

1. A local L1 chain, fully customizable with multi-client support, using the [ethereum-package](https://github.com/ethpandaops/ethereum-package).
2. A local L2 chain, using the [Polygon Chain Development Kit](https://docs.polygon.technology/cdk/) (CDK), with customizable components such as sequencer, sequence sender, aggregator, rpc, prover, dac, etc. It will first deploy the [Polygon zkEVM smart contracts](https://github.com/0xPolygonHermez/zkevm-contracts) on the L1 chain before deploying the different components.
3. The [zkEVM bridge](https://github.com/0xPolygonHermez/zkevm-bridge-service) infrastructure to facilitate asset bridging between the L1 and L2 chains, and vice-versa.
4. The [Agglayer](https://github.com/agglayer/agglayer-go), an in-development interoperability protocol, that allows for trustless cross-chain token transfers and message-passing, as well as more complex operations between L2 chains, secured by zk proofs.
5. [Additional services](docs/additional-services.md) such as transaction spammer, monitoring tools, permissionless nodes etc.

> 🚨 This package is currently designed as a **development tool** for testing configurations and scenarios within the Polygon CDK stack. **It is not recommended for long-running or production environments such as testnets or mainnet**. If you need help, you can [reach out to the Polygon team](https://polygon.technology/interest-form) or [talk to an Implementation Partner (IP)](https://ecosystem.polygon.technology/spn/cdk/).

## Table of Contents

- [Getting Started](#getting-started)
- [Supported Configurations](#supported-configurations)
- [Advanced Use Cases](#advanced-use-cases)
- [FAQ](#faq)
- [Contact](#contact)
- [License](#license)
- [Contribution](#contribution)


## Supported Configurations

The package is flexible and supports various configurations for deploying and testing the Polygon CDK stack.

You can take a look at this [table](CDK_VERSION_MATRIX.MD) to see which versions of the CDK are meant to work together, broken by fork identifier.

The table provided illustrates the different combinations of sequencers and sequence sender/aggregator components that can be used, along with their current support status in Kurtosis.

> The team is actively working on enabling the use cases that are currently not possible.

| Stack                                 | Sequencer                                                   | Sequence Sender / Aggregator                                                                                                                                | Supported by Kurtosis?                                                                                   |
| ------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| New CDK stack                         | [cdk-erigon](https://github.com/0xPolygonHermez/cdk-erigon) | [cdk-node](https://github.com/0xPolygon/cdk)                                                                                                                | ✅                                                                                                       |
| New sequencer with new zkevm stack    | [cdk-erigon](https://github.com/0xPolygonHermez/cdk-erigon) | [zkevm-sequence-sender](https://github.com/0xPolygonHermez/zkevm-sequence-sender) + [zkevm-aggregator](https://github.com/0xPolygonHermez/zkevm-aggregator) | ❌ (WIP) - Check the [kurtosis-cdk-erigon](https://github.com/xavier-romero/kurtosis-cdk-erigon) package |
| New sequencer with legacy zkevm stack | [cdk-erigon](https://github.com/0xPolygonHermez/cdk-erigon) | [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node)                                                                                                 | ❌ (WIP)                                                                                                 |
| Legacy sequencer with new cdk stack   | [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node) | [cdk-node](https://github.com/0xPolygon/cdk)                                                                                                                | ❌ (WIP)                                                                                                 |
| Legacy sequencer with new zkevm stack | [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node) | [zkevm-sequence-sender](https://github.com/0xPolygonHermez/zkevm-sequence-sender) + [zkevm-aggregator](https://github.com/0xPolygonHermez/zkevm-aggregator) | ❌ (WIP)                                                                                                 |
| Legacy zkevm stack                    | [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node) | [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node)                                                                                                 | ✅                                                                                                       |

To understand how to configure Kurtosis for these use cases, refer to the [documentation](.github/tests/README.md) and review the test files located in the `.github/tests/` directory.

## Getting Started

### Prerequisites

To begin, you will need to install [Docker](https://docs.docker.com/get-docker/) (>= [v4.27.0](https://docs.docker.com/desktop/release-notes/#4270) for Mac users) and [Kurtosis](https://docs.kurtosis.com/install/).

- If you notice some services, such as the `zkevm-stateless-executor` or `zkevm-prover`, consistently having the status of `STOPPED`, try increasing the Docker memory allocation.

If you intend to interact with and debug the stack, you may also want to consider a few additional optional tools such as:

- [jq](https://github.com/jqlang/jq)
- [yq](https://pypi.org/project/yq/) (v3)
- [cast](https://book.getfoundry.sh/getting-started/installation)
- [polycli](https://github.com/0xPolygon/polygon-cli)

### Deploy

Once that is good and installed on your system, you can run the following command to deploy the complete CDK stack locally. This process typically takes around eight to ten minutes.

```bash
kurtosis run --enclave cdk github.com/0xPolygon/kurtosis-cdk
```

The default deployment includes [cdk-erigon](https://github.com/0xPolygonHermez/cdk-erigon) as the sequencer, and [cdk-node](https://github.com/0xPolygon/cdk) functioning as the sequence sender and aggregator. You can verify the default versions of these components and the default fork ID by reviewing input_parser.star. You can check the default versions of the deployed components and the default fork ID by looking at [input_parser.star](./input_parser.star).

To make customizations to the CDK environment, clone this repo, make any desired configuration changes, and then run:

```bash
# Delete all stop and clean all currently running enclaves
kurtosis clean --all

# Run this command from the root of the repository to start the network
kurtosis run --enclave cdk .
```

![CDK Erigon Architecture Diagram](./docs/architecture-diagram/cdk-erigon-architecture-diagram.png)

### Interact

Let's do a simple L2 RPC test call.

First, you will need to figure out which port Kurtosis is using for the RPC. You can get a general feel for the entire network layout by running the following command:

```bash
kurtosis enclave inspect cdk
```

That output, while quite useful, might also be a little overwhelming. Let's store the RPC URL in an environment variable.

> You may need to adjust the various commands slightly if you deployed the legacy [zkevm-node](https://github.com/0xPolygonHermez/zkevm-node) as the sequencer. You should target the `zkevm-node-rpc-001` service instead of `cdk-erigon-rpc-001`.

```bash
export ETH_RPC_URL="$(kurtosis port print cdk cdk-erigon-rpc-001 rpc)"
```

That is the same environment variable that `cast` uses, so you should now be able to run this command. Note that the steps below will assume you have the [Foundry toolchain](https://book.getfoundry.sh/getting-started/installation) installed.

```bash
cast block-number
```

By default, the CDK is configured in `test` mode, which means there is some pre-funded value in the admin account with address `0xE34aaF64b29273B7D567FCFc40544c014EEe9970`.

```bash
cast balance --ether 0xE34aaF64b29273B7D567FCFc40544c014EEe9970
```

Okay, let’s send some transactions...

```bash
private_key="0x12d7de8621a77640c9241b2595ba78ce443d05e94090365ab3bb5e19df82c625"
cast send --legacy --private-key "$private_key" --value 0.01ether 0x0000000000000000000000000000000000000000
```

Okay, let’s send even more transactions... Note that this step will assume you have [polygon-cli](https://github.com/maticnetwork/polygon-cli) installed.

```bash
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$private_key" --verbosity 700 --requests 50000 --rate-limit 50 --concurrency 5 --mode t
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$private_key" --verbosity 700 --requests 500 --rate-limit 10 --mode 2
polycli loadtest --rpc-url "$ETH_RPC_URL" --legacy --private-key "$private_key" --verbosity 700 --requests 500 --rate-limit 3  --mode uniswapv3
```

Pretty often, you will want to check the output from the service. Here is how you can grab some logs:

```bash
kurtosis service logs cdk agglayer --follow
```

In other cases, if you see an error, you might want to get a shell in the service to be able to poke around.

```bash
kurtosis service shell cdk contracts-001
jq . /opt/zkevm/combined.json
```

One of the most common ways to check the status of the system is to make sure that batches are going through the normal progression of [trusted, virtual, and verified](https://docs.polygon.technology/cdk/concepts/transaction-finality/):

```bash
cast rpc zkevm_batchNumber
cast rpc zkevm_virtualBatchNumber
cast rpc zkevm_verifiedBatchNumber
```

If the number of verified batches is increasing, then it means the system works properly.

To access the `zkevm-bridge` user interface, open this URL in your web browser.

```bash
open "$(kurtosis port print cdk zkevm-bridge-proxy-001 web-ui)"
```

When everything is done, you might want to clean up with this command which stops the local devnet and deletes it.

```bash
kurtosis clean --all
```

For more information about the CDK stack, visit the [Polygon Knowledge Layer](https://docs.polygon.technology/cdk/).

## Advanced Use Cases

This section features documentation specifically designed for advanced users, outlining complex operations and techniques.

- How to use CDK [ACL](docs/acl-allowlists-blocklists.md).
- How to deploy [additional services](docs/additional-services.md) alongside the CDK stack, such as transaction spammer, monitoring tools, permissionless nodes etc.
- How to [attach multiple CDK chains to the AggLayer](docs/attach-multiple-cdks.md).
- How to use the different [data availability modes](docs/data-availability-modes.md).
- How to [deploy the stack to an external L1](docs/deploy-using-sepolia.org) such as Sepolia.
- How to [deploy contracts with the deterministic deployment proxy](docs/deterministic-deployment-proxy.md).
- How to [edit the zkevm contracts](docs/edit-contracts.md).
- How to [perform an environment migration](docs/environment-migration.org) with clean copies of the databases.
- How to [iterate and debug quickly](docs/fast-iteration-cycle.md) with Kurtosis.
- How to use zkevm contracts [fork 12](docs/fork12.md).
- How to [integrate a third-party data availability committee](docs/integrate-da.md) (DAC).
- How to [migrate from fork 7 to fork 9](docs/migrate/forkid-7-to-9.md).
- How to [upgrade forks for isolated CDK chains](docs/migrate/upgrade.md).
- How to use a [native token](docs/native-token/native-token.md).
- How to [play with the network](docs/network-ops.org) to introduce latencies.
- How to [set up a permissionless zkevm node](docs/permissionless-zkevm-node.md).
- How to [resequence batches with the cdk-erigon sequencer](docs/resequence-sequencer/resequence-sequencer.md).
- How to [run a debugger](docs/running-a-debugger/running-a-debugger.org).
- How to [assign static ports](docs/static-ports/static-ports.md) to Kurtosis services.
- How to work with the [timelock](docs/timelock.org).
- How to [trigger a reorg](docs/trigger-a-reorg/trigger-a-reorg.md).
- How to [perform a trustless recovery the DAC and L1](docs/trustless-recovery-from-dac-l1.md).

## FAQ

### Q: What are the different ways to deploy this package?

<details>
<summary><b>Click to expand</b></summary>

1. Deploy the package without cloning the repository.

```bash
kurtosis run --enclave cdk github.com/0xPolygon/kurtosis-cdk
kurtosis run --enclave cdk github.com/0xPolygon/kurtosis-cdk@main
kurtosis run --enclave cdk github.com/0xPolygon/kurtosis-cdk@v0.2.15
```

2. Deploy the package with the default parameters.

```bash
kurtosis run --enclave cdk .
```

3. Deploy with the default parameters and specify on-the-fly custom arguments.

```bash
kurtosis run --enclave cdk . '{"deployment_stages": {"deploy_l1": false}}'
```

4. Deploy with a configuration file.

Check the [tests](.github/tests/) folder for sample configuration files.

```bash
kurtosis run --enclave cdk --args-file params.yml .
```

5. Do not deploy with a configuration file and specify on-the-fly custom arguments.

🚨 Avoid using this method, as Kurtosis is unable to merge parameters from two different sources (the parameters file and on-the-fly arguments).

The parameters file will not be used, and only the on-the-fly arguments will be considered.

```bash
kurtosis run --enclave cdk --args-file params.yml . '{"args": {"agglayer_image": "ghcr.io/agglayer/agglayer:latest"}}'
# similar to: kurtosis run --enclave cdk . '{"args": {"agglayer_image": "ghcr.io/agglayer/agglayer:latest"}}'
```

</details>

### Q: How do I deploy the package to Kubernetes?

<details>
<summary><b>Click to expand</b></summary>

By default your Kurtosis cluster should be `docker`. You can check this using the following command:

```bash
kurtosis cluster get
```

You can also list the available clusters.

```bash
kurtosis cluster ls
```

If you take a look at your Kurtosis configuration, it should be similar to this:

```yaml
config-version: 2
should-send-metrics: true
kurtosis-clusters:
  docker:
    type: "docker"
```

Let's say you've deployed a local [minikube](https://minikube.sigs.k8s.io/docs/) cluster. It would work the same for any type of Kubernetes cluster.

Edit the Kurtosis configuration file.

```bash
vi "$(kurtosis config path)"
```

Under `kurtosis-clusters`, you should add another entry for your local Kubernetes cluster.

```yaml
kurtosis-clusters:
  minikube: # give it the name you want
    type: "kubernetes"
    config:
      kubernetes-cluster-name: "local-01" # should be the same as your cluster name
      storage-class: "standard"
      enclave-size-in-megabytes: 10
```

Then point Kurtosis to the local Kubernetes cluster.

```bash
kurtosis cluster set minikube
```

Deploy the package to Kubernetes.

```bash
kurtosis run --enclave cdk .
```

If you want to revert back to Docker, simply use:

```bash
kurtosis cluster set docker
```

</details>

### Q: I'm trying to deploy the package and Kurtosis is complaining, what should I do?

<details>
<summary><b>Click to expand</b></summary>

Occasionally, Kurtosis deployments may run indefinitely. Typically, deployments should complete within 10 to 15 minutes. If you experience longer deployment times or if it seems stuck, check the Docker engine's memory limit and set it to 16GB if possible. If this does not resolve the issue, please refer to the troubleshooting steps provided.

> 🚨 If you're deploying the package on a mac, you may face an issue when trying to pull the [zkevm-prover](https://github.com/0xPolygonHermez/zkevm-prover) image! Kurtosis will complain by saying `Error response from daemon: no matching manifest for linux/arm64/v8 in the manifest list entries: no match for platform in manifest: not found`. Indeed, the image is meant to be used on `linux/amd64` architectures, which is a bit different from m1 macs architectures, `linux/arm64/v8`.
>
> A work-around is to pull the image by specifying the `linux/amd64` architecture before deploying the package.
>
> ```bash
> docker pull --platform linux/amd64 hermeznetwork/zkevm-prover:<tag>
> kurtosis run ...
> ```

1. Make sure the issue is related to Kurtosis itself. If you made any changes to the package, most common issues are misconfigurations of services, file artefacts, ports, etc.

2. Remove the Kurtosis enclaves.

Note: By specifying the `--all` flag, Kurtosis will also remove running enclaves.

```bash
kurtosis clean --all
```

3. Restart the Kurtosis engine.

```bash
kurtosis engine restart
```

4. Restart the Docker daemon.

</details>

### Q: How do I debug in Kurtosis?

<details>
<summary><b>Click to expand</b></summary>

Kurtosis is just a thin wrapper on top of Docker so you can use all the `docker`commands you want.

On top of that, here are some useful commands.

1. View the state of the enclave (services and endpoints).

```bash
kurtosis enclave inspect cdk
```

2. Follow the logs of a service.

Note: If you want to see all the logs of a service, you can specify the `--all` flag.

```bash
kurtosis service logs cdk cdk-erigon-sequencer-001 --follow
```

3. Execute a command inside a service.

```bash
kurtosis service exec cdk contracts-001 'cat /opt/zkevm/combined.json' | tail -n +2 | jq
```

4. Get a shell inside a service.

```bash
kurtosis service shell cdk cdk-erigon-sequencer-001
```

5. Stop or start a service.

```bash
kurtosis service stop cdk cdk-erigon-sequencer-001
kurtosis service start cdk cdk-erigon-sequencer-001
```

6. Get a specific endpoint.

```bash
kurtosis port print cdk cdk-erigon-node-001 http-rpc
```

7. Inspect a file artifact.

```bash
kurtosis files inspect cdk cdk-erigon-node-config-artifact-sequencer config.yaml
```

8. Download a file artifact.

```bash
kurtosis files download cdk cdk-erigon-node-config-artifact
```

</details>

### Q: How to lint Starlark code?

<details>
<summary><b>Click to expand</b></summary>

```bash
kurtosis lint --format .
```

</details>

### Q: How do I do x, y, z in Kurtosis?

<details>
<summary><b>Click to expand</b></summary>

Head to the Kurtosis [documentation](https://docs.kurtosis.com/).

You can also take a look at the [Starlark language specification](https://github.com/bazelbuild/starlark/blob/master/spec.md) to know if certain operations are supported.

</details>

## Contact

- For technical issues, join our [Discord](https://discord.gg/0xpolygonrnd).
- For documentation issues, raise an issue on the published live doc at [our main repo](https://github.com/0xPolygon/polygon-docs).

## License

Copyright (c) 2024 PT Services DMCC

Licensed under either:

- Apache License, Version 2.0, ([LICENSE-APACHE](./LICENSE-APACHE) or <http://www.apache.org/licenses/LICENSE-2.0>), or
- MIT license ([LICENSE-MIT](./LICENSE-MIT) or <http://opensource.org/licenses/MIT>)

as your option.

The SPDX license identifier for this project is `MIT` OR `Apache-2.0`.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any additional terms or conditions.
