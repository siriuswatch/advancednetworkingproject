1, Init the root machine

balanced bx2-2x8
2 vCPUs, 8G RAM, 4 Gbps

root-server

start init the server:
IP 169.59.166.16
 >>> ssh -i C:\Users\zhang\.ssh\id_rsa_1 root@169.59.166.16

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
--verbosity 4 \
console 2>> eth.log
