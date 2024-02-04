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

<pre><code><strong>Name_bin="gitopiad" 
</strong>Name_config_file=".gitopia" 
Name_service="gitopia"
Port_prefix=266

MONIKER=""
</code></pre>

#### Creating a configuration file (in the future, you will only need to use `source .gitopia_config` to work with my scripts) (Optional)

<details>

<summary>Details</summary>

```
sed -i '/Name_bin=/d' "$HOME/.gitopia_config"

sed -i '/Name_config_file=/d' "$HOME/.gitopia_config"

sed -i '/Name_service=/d' "$HOME/.gitopia_config"

sed -i '/Port_prefix=/d' "$HOME/.gitopia_config"

sed -i '/version=/d' "$HOME/.gitopia_config"


echo "Name_bin='gitopiad'" >> "$HOME/.gitopia_config"

echo "Name_config_file='.gitopia'" >> "$HOME/.gitopia_config"

echo "Name_service='gitopia'" >> "$HOME/.gitopia_config"

echo "Port_prefix='266'" >> "$HOME/.gitopia_config"

echo "version=v3.3.0" >> "$HOME/.gitopia_config"

source "$HOME/.gitopia_config"

```

</details>

#### Binary Installation

```
version=v3.3.0
```

```
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

```
wget https://configurations.tothemars.network/genesis-mainnet-gitopia.json -O $HOME/$Name_config_file/config/genesis.json
wget https://configurations.tothemars.network/addrbook-mainnet-gitopia.json -O $HOME/$Name_config_file/config/addrbook.json
```

#### Ports replacement

<pre><code><strong>New_Port_prefix=200
</strong></code></pre>

With this command you can check whether the ports \
you want to change are busy

```
sudo netstat -tulpn | sed -n '/'$New_Port_prefix'58/p; /'$New_Port_prefix'57/p; /'$New_Port_prefix'56/p; /'$New_Port_prefix'60/p; /'$New_Port_prefix'90/p; /'$New_Port_prefix'91/p;'
```

Сhanging ports

```
sed -i 's/26658/'$New_Port_prefix'58/; s/26657/'$New_Port_prefix'57/; s/26656/'$New_Port_prefix'56/; s/6060/6'$New_Port_prefix'60/;' ~/$Name_config_file/config/config.toml
sed -i 's/9090/'$New_Port_prefix'90/; s/8545/'$New_Port_prefix'45/; s/8546/'$New_Port_prefix'46/; s/1317/'$New_Port_prefix'17/; s/9091/'$New_Port_prefix'91/' ~/$Name_config_file/config/app.toml
$Name_bin config node http://localhost:2$(echo $New_Port_prefix)57
```

<details>

<summary>Using Cosmovisor Method</summary>

**Install Cosmovisor**

```
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```

**Create Cosmovisor Folders && copy Binary to Cosmovisor**

```
mkdir -p ~/$Name_config_file/cosmovisor/genesis/bin
mkdir -p ~/$Name_config_file/cosmovisor/upgrades

cp ~/go/bin/$Name_bin ~/$Name_config_file/cosmovisor/genesis/bin
```

**Creating a Service Manager**

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

<details>

<summary>Using Binary Method</summary>

**Creating a Service Manager**

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
