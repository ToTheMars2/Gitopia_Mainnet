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
version=c940d186c0d118ea017f6abc00225fdd9b26fe14

git clone https://github.com/BlockXLabs/BlockX-Genesis-Mainnet1
cd BlockX-Genesis-Mainnet1
git checkout $version
make install

```

Initialization of the Binary

```
blockxd config chain-id blockx_100-1
blockxd init #Your_Moniker# --chain-id blockx_100-1
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-blockx.json -O $HOME/.blockxd/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-blockx.json -O $HOME/.blockxd/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/blockx.service
[Unit]
Description=Blockx daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which blockxd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
