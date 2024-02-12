# Snapshot

#### Changes for configuration

```
bin="gitopiad"
config=".gitopia"
service="gitopia"
```

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop $service
cp $HOME/$config/data/priv_validator_state.json $HOME/$config/priv_validator_state.json.backup
rm -rf $HOME/$config/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
wget https://configurations.tothemars.network/snapshot_gitopia.tar.lz4 --inet4-only
lz4 -c -d snapshot_gitopia.tar.lz4  | tar -x -C $HOME/$config
mv $HOME/$config/priv_validator_state.json.backup $HOME/$config/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start $service
sudo journalctl -u $service -f --no-hostname -o cat
```
