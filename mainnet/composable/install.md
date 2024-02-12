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
bin="centaurid" 
config=".banksy" 
service="composable"    
version=v6.4.3

MONIKER=""
```

#### Creating a configuration file (in the future, you will only need to use `source .composable_config` to work with my scripts) (**OPTIONAL**)

<details>

<summary>Details</summary>
centaurid composable .banksy
```
sed -i '/bin=/d' "$HOME/.composable_config"
sed -i '/config_file=/d' "$HOME/.composable_config"
sed -i '/service=/d' "$HOME/.composable_config"
sed -i '/port=/d' "$HOME/.composable_config"
sed -i '/version=/d' "$HOME/.composable_config"


echo "bin='centaurid'" >> "$HOME/.composable_config"
echo "config_file='.banksy'" >> "$HOME/.composable_config"
echo "service='composable'" >> "$HOME/.composable_config"
echo "port='266'" >> "$HOME/.composable_config"
echo "version=v6.4.3" >> "$HOME/.composable_config"
echo "chainId=gitopia" >> "$HOME/.composable_config"
source "$HOME/.composable_config"

```

</details>

#### Binary Installation

```
git clone https://github.com/notional-labs/composable-centauri
cd composable-centauri
git checkout $version
make install
```

#### Initialization of the Binary

```
$bin config chain-id gitopia
$bin init $MONIKER --chain-id gitopia
```

#### Installing Genesis and Addrbook

```
wget https://configurations.tothemars.network/genesis-mainnet-gitopia.json -O $HOME/$config/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-gitopia.json -O $HOME/$config/config/addrbook.json
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
sed -i 's/26658/'$New_Port_prefix'58/; s/26657/'$New_Port_prefix'57/; s/26656/'$New_Port_prefix'56/; s/6060/6'$New_Port_prefix'60/;' ~/$config/config/config.toml
sed -i 's/9090/'$New_Port_prefix'90/; s/8545/'$New_Port_prefix'45/; s/8546/'$New_Port_prefix'46/; s/1317/'$New_Port_prefix'17/; s/9091/'$New_Port_prefix'91/' ~/$config/config/app.toml
$bin config node http://localhost:2$(echo $New_Port_prefix)57
```

Port replacements in the configuration (**OPTIONAL**)

```
sed -i '/port=/d' "$HOME/.composable_config"
echo "port='$New_Port_prefix'" >> "$HOME/.composable_config"
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
Description=Gitopia daemon
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
Description=Gitopia daemon
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
