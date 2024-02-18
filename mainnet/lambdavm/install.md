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
bin="lambdavm" 
config=".lambdavm" 
service="lambdavm"    
version=v1.0.1

MONIKER=""
```

#### Creating a configuration file (in the future, you will only need to use `source .lambdavm_config` to work with my scripts) (**OPTIONAL**)

<details>

<summary>Details</summary>

```
sed -i '/bin=/d' "$HOME/.lambdavm_config"
sed -i '/config_file=/d' "$HOME/.lambdavm_config"
sed -i '/service=/d' "$HOME/.lambdavm_config"
sed -i '/port=/d' "$HOME/.lambdavm_config"
sed -i '/version=/d' "$HOME/.lambdavm_config"


echo "bin='lambdavm'" >> "$HOME/.lambdavm_config"
echo "config_file='.lambdavm'" >> "$HOME/.lambdavm_config"
echo "service='lambdavm'" >> "$HOME/.lambdavm_config"
echo "port='266'" >> "$HOME/.lambdavm_config"
echo "version=v3.3.0" >> "$HOME/.lambdavm_config"
echo "chainId=lambda_92000-1" >> "$HOME/.lambdavm_config"
source "$HOME/.lambdavm_config"

```

</details>

#### Binary Installation

```
git clone https://github.com/LambdaIM/lambdavm
cd lambdavm
git checkout $version
make install
```

#### Initialization of the Binary

```
$bin config chain-id lambda_92000-1
$bin init $MONIKER --chain-id lambda_92000-1
```

#### Installing Genesis and Addrbook

```
wget https://configurations.tothemars.network/genesis-mainnet-lambdavm.json -O $HOME/$config/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-lambdavm.json -O $HOME/$config/config/addrbook.json
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
sed -i '/port=/d' "$HOME/.lambdavm_config"
echo "port='$New_Port_prefix'" >> "$HOME/.lambdavm_config"
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
Description=Lambdavm daemon
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
Description=Lambdavm daemon
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
