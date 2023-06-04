# Gitopia



![image](https://github.com/ToTheMars2/Gitopia_Mainnet/assets/109024799/107eb340-16bf-4cd7-8a9f-174c7f60cdac)

* [Installing Node: A Step-by-Step Guide](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#installing-node-a-step-by-step-guide)
* [Public Api](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#public-api)
* [State Sync](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#state-sync)
* [Snapshot](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#snapshot)

# Installing Node: A Step-by-Step Guide

Package Installation (optional if already installed)
```
apt install jq ufw gcc curl git libssl-dev libc6-dev pkg-config make screen -y
```

Installing Go (optional if already installed)
```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.19.9.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
### First stage: this involves installing the binary and initialization.

Binary Installation
```
version=v2.1.0

git clone https://github.com/gitopia/gitopia.git
cd gitopia
git checkout $version
make install

```
Initialization of the Binary
```
gitopiad config chain-id gitopia
gitopiad init #Your_Moniker# --chain-id gitopia
```


### Second Stage: Uploading Genesis and (Addrbook or Peers)
Installing Genesis
```
wget http://176.36.248.34/Gitopia_Mainnet_genesis.json -O $HOME/.gitopia/config/genesis.json
```

Inserting Peers or Addrbook:
<details>
<summary><b>Peers</b></summary>
  
```
peers="4cf66531681c92f15c95c25bd1bff524f9dca35e@65.109.154.181:26656,b2f764694d52e09793d68259d584ece0c194b6fe@65.108.229.93:26656,082e95b5d5351e68dcfb24dff802f9064cfd5a4c@65.109.92.241:51056,a94aec7233f9fec2b2de4b5c9dab6ad979820b3d@65.109.104.118:60756,a0ebd1e5845148c47451452047c7c99621da195e@65.109.96.93:60556,4adfa5889675e1e91ea4459e15ff4a0ba53e7828@65.108.224.156:19656,12f6b84a23b054a6591c647c2a4456c40af65cce@5.9.147.22:24657,88497ab3bbbcc1e8545771f45020e738bcce590f@95.165.89.222:24136,abca18ed112719b4f0a23932797dba2733f0fd44@23.88.5.169:25656,976d95adec7f0d7fda4464df019fa538fa0bb4ce@144.76.97.251:44656,ffd761a9e0d86609de6dae5935f99451694051a9@34.28.130.17:26656,5b2df98ad73a0a81a5bd31da4489a9236a7d7a99@65.21.91.160:26867,712dd67b7abe08577d394e90a4930492c8f7d2ee@65.108.124.219:41656"

sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.gitopia/config/config.toml

```
</details>

<details open>
<summary><b>Addrbook</b></summary>
  
```
wget http://176.36.248.34/Gitopia_Mainnet_addrbook.json -O $HOME/.gitopia/config/addrbook.json

```
</details>

### Third Stage: Obtaining the Current State of the Blockchain through [Snapshot](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#snapshot) or [State Sync](https://github.com/ToTheMars2/Gitopia_Mainnet/blob/main/README.md#state-sync) (Optional)


### Fourth Stage: Launching via a Service Manager
Creating a Service Manager
```
User=root
full_path=/root/go/bin

tee <<EOF > /dev/null /etc/systemd/system/gitopiad.service
[Unit]
Description=Gitopia daemon
After=network-online.target

[Service]
User=$User
ExecStart=$full_path/gitopiad start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

Starting the Service Manager
```
sudo systemctl daemon-reload && sudo systemctl enable gitopiad
sudo systemctl restart gitopiad && sudo journalctl -u gitopiad -f -o cat
```

**#################################################################################################################**

# Public Api

RPC Endpoin
```
http://176.36.248.34:23457
```

LCD (Rest) API Endpoint
```
http://176.36.248.34:1317
```
GRPC Endpoint
```
http://176.36.248.34:9340
```

# State Sync
```
SNAP_RPC="http://176.36.248.34:23457"
Name_bin="gitopiad"
Name_config_file=".gitopia"
Name_service="gitopiad"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml
```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat
```

# Snapshot (soon)
