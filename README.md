
# OP Stack Base Node
Some notes on installing a Base node with geth on a GCP for testing, with minimal instance requirements and basic configuration. For testing only.

<!-- Beacon RPC
https://eth-beacon-chain.drpc.org/rest/
curl -X GET "https://eth-beacon-chain.drpc.org/rest/eth/v1/beacon/genesis" -H "accept: application/json" -->

## GCP Ubuntu Configuration
### GCP Instance
1. Create instace: E2 (8 CPU / 16 GB RAM /4000GB regular storage)
2. Log into instance

## Clone Node
Update: `sudo apt-get update`

`mkdir base && cd base`

`git clone https://github.com/j2abro/base-node`

(Original: `git clone https://github.com/base-org/node`)

Edit API Key in .env.mainnet file: `OP_NODE_L1_ETH_RPC=https://shape-mainnet.g.alchemy.com/v2/<API_KEY>`

## Install Docker

### Docker GPG Key
<!-- curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - # deprecated, but ok for now -->
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

### Install Docker
    sudo apt install docker.io

### Install Docker Compose
    mkdir -p ~/.docker/cli-plugins/
    cd ~/.docker/cli-plugins/
    curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
    chmod +x ~/.docker/cli-plugins/docker-compose
Optional: if you get permissions isssue. May need to logout/in to take effect.
    sudo groupadd docker
    sudo usermod -aG docker <USERNAME> 

### Run docker compose
This should be in .env file, but there seems to be an interpretation issue on ubuntu (works on mac)
The env file in the repo is edited around this, but as a backup try this:
    export CLIENT=geth
    export HOST_DATA_DIR=./geth-data

### Run in foreground to test 
    sudo usermod -aG docker $USER
OR
    sudo docker compose up

**Output** `node-execution-1  | INFO [01-26|20:34:19.048] Looking for peers     peercount=2 tried=180 static=0`
  - shows that it is conneected to 2 peers.

### then test 
```curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
  -H "Content-Type: application/json" http://localhost:8545```

  md2
  ```markdown
  curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
  -H "Content-Type: application/json" http://localhost:8545
  ```

## Run in background
If it runs fine, you can ctrl-c the run in background
    docker compose up -d
Test again, with Curl


# Reference
Original Source:
 - Repo: https://github.com/base-org/node
 - Install guide: https://docs.base.org/tutorials/run-a-base-node/

 Log into Alchemy to retrieve my L1 RPC API:
    https://shape-mainnet.g.alchemy.com/v2/BCDLZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y

Some test queries

    curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' https://shape-mainnet.g.alchemy.com/v2/BCDLZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y

    ```
    curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
    -H "Content-Type: application/json" https://shape-mainnet.g.alchemy.com/v2/BCDLZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y
     ```
