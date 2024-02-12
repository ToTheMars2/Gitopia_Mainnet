# State sync

```
SNAP_RPC="https://composable-rpc.tothemars.network:443"
bin="centaurid"
config=".banksy"
service="composable"
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
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$config/config/config.toml
```

#### Stop the service and reset the data & Restart the service and check the log <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop $service && $bin tendermint unsafe-reset-all --home $HOME/$config --keep-addr-book
sudo systemctl restart $service
journalctl -fu $service -o cat
```
