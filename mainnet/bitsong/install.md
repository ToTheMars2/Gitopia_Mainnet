# install

#### Package Installation (optional if already installed)

```
sudo apt install jq ufw gcc curl git libssl-dev libc6-dev pkg-config make screen net-tools -y
```

#### Installing Go (optional if already installed)

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ToTheMars2/Update_programs/main/update_go.sh)" -- "1.21.0"
```

#### Changes for configuration

```
bin="bitsongd" 
config=".bitsongd" 
service="bitsong-mainnet"    
version=v0.17.0

MONIKER=""
```

#### Creating a configuration file (in the future, you will only need to use `source .bitsong_config` to work with my scripts) (**OPTIONAL**)

<details>

<summary>Details</summary>

```
sed -i '/bin=/d' "$HOME/.bitsong_config"
sed -i '/config_file=/d' "$HOME/.bitsong_config"
sed -i '/service=/d' "$HOME/.bitsong_config"
sed -i '/port=/d' "$HOME/.bitsong_config"
sed -i '/version=/d' "$HOME/.bitsong_config"


echo "bin='bitsongd'" >> "$HOME/.bitsong_config"
echo "config_file='.bitsongd'" >> "$HOME/.bitsong_config"
echo "service='bitsong-mainnet'" >> "$HOME/.bitsong_config"
echo "port='266'" >> "$HOME/.bitsong_config"
echo "version=v0.17.0" >> "$HOME/.bitsong_config"
echo "chainId=bitsong-2b" >> "$HOME/.bitsong_config"
source "$HOME/.bitsong_config"

```

</details>

#### Binary Installation

```
git clone https://github.com/bitsongofficial/go-bitsong
cd bitsong
git checkout $version
make install
```

#### Initialization of the Binary

```
$bin config chain-id bitsong-2b
$bin init $MONIKER --chain-id bitsong-2b
```

#### Installing Genesis and Addrbook

```
wget https://configurations.tothemars.network/genesis-mainnet-bitsong.json -O $HOME/$config/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-bitsong.json -O $HOME/$config/config/addrbook.json
```

#### Ports replacement

```
New_Port_prefix=200
```

With this command you can check whether the ports \
you want to change are busy

```
sudo netstat -tulpn | sed -n '/'$New_Port_prefix'58/p; /'$New_Port_prefix'57/p; /'$New_Port_prefix'56/p; /'$New_Port_prefix'60/p; /'$New_Port_prefix'90/p; /'$New_Port_prefix'91/p;'
```

Сhanging ports

```
sed -i 's/26658/'$New_Port_prefix'58/; s/26657/'$New_Port_prefix'57/; s/26656/'$New_Port_prefix'56/; s/6060/'$New_Port_prefix'60/;' ~/$config/config/config.toml
sed -i 's/9090/'$New_Port_prefix'90/; s/8545/'$New_Port_prefix'45/; s/8546/'$New_Port_prefix'46/; s/1317/'$New_Port_prefix'17/; s/9091/'$New_Port_prefix'91/' ~/$config/config/app.toml
$bin config node http://localhost:2$(echo $New_Port_prefix)57
```

Port replacements in the configuration (**OPTIONAL**)

```
sed -i '/port=/d' "$HOME/.bitsong_config"
echo "port='$New_Port_prefix'" >> "$HOME/.bitsong_config"
```

<details>

<summary>Using Cosmovisor Method</summary>

**Install Cosmovisor**

```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```

**Create Cosmovisor Folders && copy Binary to Cosmovisor**

```
mkdir -p ~/$config/cosmovisor/genesis/bin
mkdir -p ~/$config/cosmovisor/upgrades

cp ~/go/bin/$bin ~/$config/cosmovisor/genesis/bin
```

**Creating a Service Manager**

```
sudo tee <<EOF > /dev/null /etc/systemd/system/$service.service
[Unit]
Description=Bitsong daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=$bin"
Environment="DAEMON_HOME=$(echo $HOME)/$config"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"


[Install]
WantedBy=multi-user.target
EOF
```

</details>

<details>

<summary>Using Binary Method</summary>

**Creating a Service Manager**

```
sudo tee <<EOF > /dev/null /etc/systemd/system/$service.service
[Unit]
Description=Bitsong daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which $bin) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

</details>

For quick synchronization, you can use **State Sync** or **Snatshot**

