#OP Stack Base Node
Some notes on installing a Base node with geth on a GCP for testing, with minimal instance requirements and basic configuration. For testing only.





```

<!-- Beacon RPC
https://eth-beacon-chain.drpc.org/rest/
curl -X GET "https://eth-beacon-chain.drpc.org/rest/eth/v1/beacon/genesis" -H "accept: application/json" -->


## GCP Ubuntu Configuration
### GCP Instance
Create instace: E2 (8cpu/16gbRam/4000GB regular storage)
Log in via browser SSH from console

sudo apt-get update

# Install Node
mkdir base && cd $!
git clone https://github.com/base-org/node

# Install Docker
## Certificates
## curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - # deprecated, but ok for now
OR
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

## Install Commpose
mkdir -p ~/.docker/cli-plugins/
cd ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
sudo usermod -aG docker j2abro # if you get docker permission issues. may need to logout/in to take effect

## Run docker compose
### this should be in .env file, but there seems to be an interpretation issue on ubuntu (works on mac)
export CLIENT=geth 
export HOST_DATA_DIR=./geth-data

### Run in foreground to test 
docker compose up
### Output of `node-execution-1  | INFO [01-26|20:34:19.048] Looking for peers     peercount=2 tried=180 static=0`
###  - shows that it is conneected to 2 peers.

### then test `curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
  -H "Content-Type: application/json" http://localhost:8545`

## Run in background
docker compose up -d 
### test again, with Curl



# Reference
Original Source:
 - Repo: https://github.com/base-org/node
 - Install guide: https://docs.base.org/tutorials/run-a-base-node/

 Log into Alchemy to retrieve my L1 RPC API:
`https://shape-mainnet.g.alchemy.com/v2/LZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y`

Some test queries

`curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' https://shape-mainnet.g.alchemy.com/v2/LZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y`

```
curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
  -H "Content-Type: application/json" https://shape-mainnet.g.alchemy.com/v2/LZ02nM4sVS2Bl8D9xkQvzRydKfF4pX0y
  ```