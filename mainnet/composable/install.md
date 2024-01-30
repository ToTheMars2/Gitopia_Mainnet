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
version=v6.4.3

git clone https://github.com/notional-labs/composable-centauri
cd composable-centauri
git checkout $version
make install

```

Initialization of the Binary

```
centaurid config chain-id centauri-1
centaurid init #Your_Moniker# --chain-id centauri-1
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-composable.json -O $HOME/.banksy/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-composable.json -O $HOME/.banksy/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/composable.service
[Unit]
Description=Composable daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which centaurid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
