# Snapshot

```
Name_bin="gitopiad"
Name_config_file=".gitopia"
$="gitopiad"
```

#### Stop the service and reset the data <a href="#stop-the-service-and-reset-the-data" id="stop-the-service-and-reset-the-data"></a>

```
sudo systemctl stop $Name_service
cp $HOME/$Name_config_file/data/priv_validator_state.json $HOME/$Name_config_file/priv_validator_state.json.backup
rm -rf $HOME/$Name_config_file/data
```

#### Download latest snapshot <a href="#download-latest-snapshot" id="download-latest-snapshot"></a>

<pre><code><strong>curl -L https://configurations.tothemars.network/snapshot_gitopia.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.$Name_config_file
</strong>mv $HOME/$Name_config_file/priv_validator_state.json.backup $HOME/$Name_config_file/data/priv_validator_state.json
</code></pre>

#### Restart the service and check the log <a href="#restart-the-service-and-check-the-log" id="restart-the-service-and-check-the-log"></a>

```
sudo systemctl start $Name_service
sudo journalctl -u $Name_service -f --no-hostname -o cat
```
