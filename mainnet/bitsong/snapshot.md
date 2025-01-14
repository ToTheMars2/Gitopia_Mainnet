# Snapshot

### (Snapshots are taken automatically every 24 hours)

```
Name_bin="bitsongd"
Name_config_file=".bitsongd"
Name_service="bitsong-mainnet"
url_snap="https://configurations.tothemars.network/last_snapshot_bitsong.tar.lz4"
```

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop $Name_service
cp $HOME/$Name_config_file/data/priv_validator_state.json $HOME/$Name_config_file/priv_validator_state.json.backup
rm -rf $HOME/$Name_config_file/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

```
wget $url_snap --inet4-only
lz4 -c -d $(basename "$url_snap")  | tar -x -C $HOME/$config
mv $HOME/$config/priv_validator_state.json.backup $HOME/$config/data/priv_validator_state.json
```

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start $Name_service
sudo journalctl -u $Name_service -f --no-hostname -o cat
```
