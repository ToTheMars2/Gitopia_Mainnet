# install

Package Installation (optional if already installed)

```
apt install jq ufw gcc curl git libssl-dev libc6-dev pkg-config make screen -y
```

Installing Go (optional if already installed)

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ToTheMars2/Update_programs/main/update_go.sh)" -- "1.21.0"
```

Binary Installation

```
version=v0.2.17

git clone https://github.com/hypersign-protocol/hid-node
cd hid-node
git checkout $version
make install

```

Initialization of the Binary

```
hid-noded config chain-id prajna-1
hid-noded init #Your_Moniker# --chain-id prajna-1
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-hypersign.json -O $HOME/.hid-node/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-hypersign.json -O $HOME/.hid-node/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/hypersign.service
[Unit]
Description=Hypersign daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which hid-noded) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
