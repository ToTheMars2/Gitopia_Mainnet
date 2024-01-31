
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
version=v0.34.0

git clone https://github.com/lavanet/lava
cd lava
git checkout $version
make install

```

Initialization of the Binary

```
lavad config chain-id lava-testnet-2
lavad init #Your_Moniker# --chain-id lava-testnet-2
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-lava.json -O $HOME/.lava/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-lava.json -O $HOME/.lava/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/lava.service
[Unit]
Description=Lava daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which lavad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
