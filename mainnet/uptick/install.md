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

git clone https://github.com/UptickNetwork/uptick.git
cd uptick
git checkout $version
make install

```

Initialization of the Binary

```
uptickd config chain-id uptick_117-1
uptickd init #Your_Moniker# --chain-id uptick_117-1
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-uptick.json -O $HOME/.uptickd/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-uptick.json -O $HOME/.uptickd/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/uptick.service
[Unit]
Description=Uptick daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which uptickd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
