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
version=v1.0.1

git clone https://github.com/LambdaIM/lambdavm
cd lambdavm
git checkout $version
make install

```

Initialization of the Binary

```
lambdavm config chain-id lambda_92000-1
lambdavm init #Your_Moniker# --chain-id lambda_92000-1
```

Installing Genesis and Addrbook

<pre><code><strong>wget https://configurations.tothemars.network/genesis-mainnet-lambdavm.json -O $HOME/.lambdavm/config/genesis.json
</strong>wget https://configurations.tothemars.network/addrbook-mainnet-lambdavm.json -O $HOME/.lambdavm/config/addrbook.json
</code></pre>

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/lambdavm.service
[Unit]
Description=Lambdavm daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which lambdavm) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

For quick synchronization, you can use **State Sync** or **Snatshot**
