<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/169664551-39020c2e-fa95-483b-916b-c52ce4cb907c.png">
</p>

# Price Oracle Script
This is a simple oracle script that fetchs market prices of different token pairs from the CoinGecko. Sei team will add multiple price sources in this script so that Sei can decentralize the oracle prices.

Price feeder usage:
```
usage: price_feeder.py [-h] [--binary BINARY] [--node NODE] [--salt SALT] [--interval INTERVAL] [--valoper VALOPER] key password chain_id coins

positional arguments:
  key                  Your wallet (key) name
  password             The keychain password
  chain_id             Chain id
  coins                The coins to use

optional arguments:
  -h, --help           show this help message and exit
  --binary BINARY      Your seid binary path
  --node NODE          The node to contact
  --salt SALT          The salt to use
  --interval INTERVAL  How long time to sleep between price checks
  --valoper VALOPER    Validator address if using separate feeder account
```

> Example: `python3 -u price_feeder.py my_wallet_name my_wallet_password atlantic-1 cosmos,usd-coin --node http://localhost:12657`

## Install coingecko api
```
git clone https://github.com/man-c/pycoingecko.git
cd pycoingecko
python3 setup.py install
```

## Set coins to fetch
```
COINS='cosmos','usd-coin'
```

## Create service
```
sudo tee /etc/systemd/system/price_feeder.service > /dev/null <<EOF
[Unit]
Description=sei
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/sei-chain/scripts/oracle
ExecStart=python3 -u price_feeder.py $WALLET 0 $SEI_CHAIN_ID $COINS --node http://localhost:${SEI_PORT}657
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Run service
```
sudo systemctl daemon-reload
sudo systemctl enable price_feeder
sudo systemctl restart price_feeder
```

## Check service logs
```
journalctl -fu price_feeder
```

Successful logs should look like:
```
submitting price feeds  1usei,10.67uatom,1.001uusdc 1usei,10.67uatom,1.001uusdc
sleep for 3...
submitting price feeds  1usei,10.67uatom,1.001uusdc 1usei,10.67uatom,1.001uusdc
submitting price feeds  1usei,10.67uatom,1.001uusdc 1usei,10.67uatom,1.001uusdc
sleep for 3...
```

Also to make sure price feeder is sending transactions you can check your wallet transaction history in explorer

![image](https://user-images.githubusercontent.com/50621007/183050984-b17e6879-e7ff-42f4-885c-5115b7101f35.png)
