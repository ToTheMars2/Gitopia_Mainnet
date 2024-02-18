# install

#### Package Installation (optional if already installed)

```
apt install jq ufw gcc curl git libssl-dev libc6-dev pkg-config make screen -y
```

#### Installing Go (optional if already installed)

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ToTheMars2/Update_programs/main/update_go.sh)" -- "1.21.0"
```

#### Changes for configuration
```
Name_bin="blockxd"
Name_config_file=".blockx"
Name_service="blockx"
MONIKER=""
```

#### Creating a configuration file (in the future, you will only need to use `source .blockx_config` to work with my scripts) (**OPTIONAL**)

<details>

<summary>Details</summary>

```
sed -i '/bin=/d' "$HOME/.blockx_config"
sed -i '/config_file=/d' "$HOME/.blockx_config"
sed -i '/service=/d' "$HOME/.blockx_config"
sed -i '/port=/d' "$HOME/.blockx_config"
sed -i '/version=/d' "$HOME/.blockx_config"


echo "bin='blockxd'" >> "$HOME/.blockx_config"
echo "config_file='.blockx'" >> "$HOME/.blockx_config"
echo "service='blockx'" >> "$HOME/.blockx_config"
echo "port='266'" >> "$HOME/.blockx_config"
echo "version=c940d186c0d118ea017f6abc00225fdd9b26fe14" >> "$HOME/.blockx_config"
echo "chainId=blockx_100-1" >> "$HOME/.blockx_config"
source "$HOME/.blockx_config"

```

</details>

#### Binary Installation

```
version=c940d186c0d118ea017f6abc00225fdd9b26fe14

git clone https://github.com/BlockXLabs/BlockX-Genesis-Mainnet1
cd BlockX-Genesis-Mainnet1
git checkout $version
make install

```

#### Initialization of the Binary

```
$Name_bin config chain-id blockx_100-1
$Name_bin init $MONIKER --chain-id blockx_100-1
```

#### Installing Genesis and Addrbook

<pre><code>wget https://configurations.tothemars.network/genesis-mainnet-blockx.json -O $HOME/$Name_config_file/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-blockx.json -O $HOME/$Name_config_file/config/addrbook.json
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
tee <<EOF > /dev/null /etc/systemd/system/$Name_service.service
[Unit]
Description=Blockx daemon
After=network-online.target

[Service]
User=$User
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
tee <<EOF > /dev/null /etc/systemd/system/$Name_service.service
[Unit]
Description=Blockx daemon
After=network-online.target

[Service]
User=$User
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
