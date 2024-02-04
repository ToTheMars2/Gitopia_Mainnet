# install

#### Package Installation (optional if already installed)

```
sudo apt install jq ufw gcc curl git libssl-dev libc6-dev pkg-config make screen -y
```

#### Installing Go (optional if already installed)

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ToTheMars2/Update_programs/main/update_go.sh)" -- "1.21.0"
```

#### Changes for configuration
```
Name_bin="gitopiad"
Name_config_file=".gitopia"
Name_service="gitopia"
MONIKER=""
```

#### Binary Installation

```
version=v3.3.0

git clone https://github.com/gitopia/gitopia.git
cd gitopia
git checkout $version
make install

```

#### Initialization of the Binary

```
$Name_bin config chain-id gitopia
$Name_bin init $MONIKER --chain-id gitopia
```

#### Installing Genesis and Addrbook

<pre><code>wget https://configurations.tothemars.network/genesis-mainnet-gitopia.json -O $HOME/$Name_config_file/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-gitopia.json -O $HOME/$Name_config_file/config/addrbook.json
</code></pre>


<details>
  <summary><b>Using Cosmovisor Method</b></summary>

#### Install Cosmovisor
```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```

#### Create Cosmovisor Folders && copy Binary to Cosmovisor
```
mkdir -p ~/$Name_config_file/cosmovisor/genesis/bin
mkdir -p ~/$Name_config_file/cosmovisor/upgrades

cp ~/go/bin/$Name_bin ~/$Name_config_file/cosmovisor/genesis/bin
```

#### Creating a Service Manager

```
sudo tee <<EOF > /dev/null /etc/systemd/system/$Name_service.service
[Unit]
Description=Gitopia daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=$Name_bin"
Environment="DAEMON_HOME=$(echo $HOME)/$Name_config_file"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"


[Install]
WantedBy=multi-user.target
EOF
```
</details>
<details open>
  <summary><b>Using Binary Method</b></summary>

#### Creating a Service Manager

```
sudo tee <<EOF > /dev/null /etc/systemd/system/$Name_service.service
[Unit]
Description=Gitopia daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which $Name_bin) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

</details>

For quick synchronization, you can use **State Sync** or **Snatshot**
