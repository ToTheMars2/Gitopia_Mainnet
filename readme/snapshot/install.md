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
version=v3.3.0

git clone https://github.com/gitopia/gitopia.git
cd gitopia
git checkout $version
make install

```

Initialization of the Binary

```
gitopiad config chain-id gitopia
gitopiad init #Your_Moniker# --chain-id gitopia
```

Installing Genesis and Addrbook&#x20;

```
// Some code
```

Creating a Service Manager

```
tee <<EOF > /dev/null /etc/systemd/system/gitopiad.service
[Unit]
Description=Gitopia daemon
After=network-online.target

[Service]
User=$User
ExecStart=$(which gitopiad) start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
