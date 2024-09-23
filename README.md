# Arkeo-Node

## System Preparation:

Ensure your system is updated:

```
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y build-essential curl wget jq make gcc chrony git
```
## Update system parameters:

```
sudo su -c "echo 'fs.file-max = 65536' >> /etc/sysctl.conf"
sudo sysctl -p
```
## Install Go:

Download and install Go:

```
wget https://go.dev/dl/go1.19.2.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.19.2.linux-amd64.tar.gz
```
Update environment variables:

```
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/.go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/.go/bin:$HOME/go/bin
EOF
source $HOME/.profile
```
## Install Arkeo Binary:

Clone the repository and build the binary:

```
git clone https://github.com/arkeonetwork/arkeo
cd arkeo
git checkout ab05b124336ace257baa2cac07f7d1bfeed9ac02
make proto-gen install
```
## Node Configuration:

Initialize and configure the binary:
```
arkeod keys add <key-name>
arkeod config chain-id arkeo
arkeod init <custom_moniker> --chain-id arkeo
```
## Download the genesis file and set up seeds:

```
curl -s http://seed.arkeo.network:26657/genesis | jq '.result.genesis' > ~/.arkeo/config/genesis.json
```
## Run the Node:

Create a service file to keep the node running:
```
sudo tee /etc/systemd/system/arkeod.service > /dev/null <<EOF
[Unit]
Description=Arkeo Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which arkeod) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Enable and start the service:
```
sudo systemctl daemon-reload
sudo systemctl enable arkeod
sudo systemctl start arkeod
```
