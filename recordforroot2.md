1, Init the root machine

balanced bx2-2x8
2 vCPUs, 8G RAM, 4 Gbps

root-server

start init the server:
IP 169.59.166.16
 >>> ssh -i C:\Users\zhang\.ssh\id_rsa_1 root@169.59.166.16
 
 friend01 169.59.161.251
 ssh -i C:\Users\zhang\.ssh\id_rsa_1 root@169.59.161.251

1) init the env
sudo apt-get update
sudo apt-get install libssl-dev libcurl4-openssl-dev libprotobuf-dev
sudo apt-get install build-essential python

2) download and install the intel SGX

mkdir download
cd download/
wget https://download.01.org/intel-sgx/sgx-linux/2.7.1/distro/ubuntu16.04-server/sgx_linux_x64_sdk_2.7.101.3.bin
chmod 777 sgx_linux_x64_sdk_2.7.101.3.bin
sudo ./sgx_linux_x64_sdk_2.7.101.3.bin

source /root/download/sgxsdk/environment

vim /etc/profile
export PATH=$PATH:/root/download/sgxsdk/environment
source /etc/profile

3) test the SGX install
cd /root/download/sgxsdk/SampleCode/SampleEnclave

Makefile:
SGX_SDK ?= /root/download/sgxsdk
SGX_MODE ?= SIM
SGX_ARCH ?= x64
SGX_DEBUG ?= 1

Sudo make
./app

test finished...


4) install and build go env
wget https://golang.org/dl/go1.15.5.linux-amd64.tar.gz
sudo tar -xzf go1.15.5.linux-amd64.tar.gz -C /usr/bin

vi /etc/profile

export GOPATH=/goWorkPlace                                                                                   export GOROOT=/usr/bin/go                                                                                     export PATH=$PATH:$GOROOT/bin

source /etc/profile


test: go version

5) install geth env
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum

git clone https://github.com/ethereum/go-ethereum.git
cd go-ethereum
make geth

test 
>>> geth version

2, build the geth cluster
git clone https://github.com/siriuswatch/advancednetworkingproject.git

1) init the first block
geth --datadir /root/eth/data0/ init genesis.json

2) start the first block

geth --datadir /root/eth/data0/ \
--networkid 45 \
--identity "rootnode" \
--port 30303 \
--rpc \
--rpcport 8545 \
--rpccorsdomain "*" \
--nodiscover \
--allow-insecure-unlock \
--verbosity 4 \
console 2>> eth.log

geth --datadir /root/eth/data0/ \
--networkid 45 \
--identity "friend01" \
--port 30303 \
--rpc \
--rpcport 8545 \
--rpccorsdomain "*" \
--nodiscover \
--allow-insecure-unlock \
--verbosity 4 \
console 2>> eth.log

3) The agreement:

var bytecode = "0x60806040526000805534801561001457600080fd5b5061015a806100246000396000f3fe608060405234801561001057600080fd5b506004361061004c5760003560e01c8063188b85b41461005157806360fe47b1146100935780636d4ce63c146100c1578063f446c1d0146100df575b600080fd5b61007d6004803603602081101561006757600080fd5b81019080803590602001909291905050506100fd565b6040518082815260200191505060405180910390f35b6100bf600480360360208110156100a957600080fd5b810190808035906020019092919050505061010b565b005b6100c9610115565b6040518082815260200191505060405180910390f35b6100e761011e565b6040518082815260200191505060405180910390f35b600081600054019050919050565b8060008190555050565b60008054905090565b6000548156fea2646970667358221220f01bbfb0dfc6584cd14d7a303b00244721619619d07bacb960d496128c20b0ea64736f6c63430007040033"

var abi = JSON.parse('[{\"inputs\":[],\"name\":\"A\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"get\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"a\",\"type\":\"uint256\"}],\"name\":\"set\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"b\",\"type\":\"uint256\"}],\"name\":\"sum\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"}]')

test = web3.eth.contract(abi)

