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


