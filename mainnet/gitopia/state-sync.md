# State sync

#### Changes for configuration
```
SNAP_RPC="https://gitopia-rpc.tothemars.network:443"
Name_bin="gitopiad"
Name_config_file=".gitopia"
Name_service="gitopia"
```

#### Get and configure the state sync information <a href="#get-and-configure-the-state-sync-information" id="get-and-configure-the-state-sync-information"></a>

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml
```

#### Stop the service and reset the data & Restart the service and check the log <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat
```
