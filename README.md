# Straightedge validator instructions
(for Ubuntu)

Minimum recommended hardware requirements: 1 vCPU; 2gb RAM; 50gb SSD

# Update Ubuntu
`sudo apt update`

`sudo apt upgrade -y`

`apt install make`

`apt install gcc`

`apt install jq`

# Install Go
`sudo rm -rf /usr/local/go`

(this removes any existing old Go installation)

`curl https://dl.google.com/go/go1.15.7.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -`

(this downloads and installs Go)

```
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
```

(these commands update environment variables to include Go)

Check to see if Go was installed:
`go version`


# Install Straightedge
`git clone https://github.com/heystraightedge/straightedge`

`cd straightedge`

`make install`

`strcli version`

(checks to see if Straightedge was installed)

# Create wallet

`strcli keys add validator`

This will create a new, password-protected w wallet named "validator". You should see something that looks like this:

```
name: validator
  type: local
  address: str1le57a2klterkxda4vpw4a3qdlk4ur4pgunpl9z
  pubkey: strpub1addwnpepqdackpms5ze5qqxaj38z8pnkeh4fhxwwjrgnk37zamvz8442tjvqxpqa574
  mnemonic: ""
  threshold: 0
  pubkeys: []


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.
```

Your account address begins with `str1`. [Ask Block Producer on Discord](https://discord.gg/Wp4qa38) to send STR to your address. You'll need STR to create your validator.

**Important!** Write down the 24 word mnemonic phrase and keep it safe. Whatever happens to your account, this mnemonic phrase can be used to recover your tokens, otherwise they will be lost forever. Do not share this mnemonic phrase with anyone or they can take all of your tokens.

# Configure Straightedge

`strd init --chain-id straightedge-2 ValidatorName`

(name your validator)

`rm ~/.strd/config/genesis.json`

(remove pre-genesis file)

`curl https://raw.githubusercontent.com/heystraightedge/mainnet/master/genesis.json -o ~/.strd/config/genesis.json`

`sed -i -e 's/persistent_peers = ""/persistent_peers = "d564ddb8017341e2bedf21487ae1d5b6d4797538@104.248.126.170:26656,346ec9481a0602ccf8d9b53138478302d0b771e9@54.36.124.100:26656,7539c53eb9893a72f2e6452ffbff4a67b9cfbec2@35.221.39.7:26656,ef29383c769d4ff7332d4c819807bb515c601067@134.122.32.31:26656"/g' ~/.strd/config/config.toml`

`sed -i -e 's/seeds = ""/seeds = "8ad635f89a1595fad6b7b0236252b89f57d62efe@45.55.55.244:26656"/g' ~/.strd/config/config.toml`

`sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "25000000000.0astr"/g' ~/.strd/config/app.toml`

(add seed nodes & persistant peers; set gas price minimum)

`sudo mkdir -p /var/log/strd && sudo touch /var/log/strd/strd.log && sudo touch /var/log/strd/strd_error.log`

(Create log files for strd)

# Sync your node

`strd start`

(begins the syncing process. BUT your node will node recover if it fails, so consider using a service for strd. A service will automatically restart strd if it fails.)

OR create a service for strd:

```
sudo tee /etc/systemd/system/strd.service > /dev/null <<'EOF'
[Unit]
Description=Straightedge daemon
After=network-online.target

[Service]
User=<your_user>
ExecStart=/home/<your_user>/go/bin/strd start
StandardOutput=file:/var/log/strd/strd.log
StandardError=file:/var/log/strd/strd_error.log
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

Check to see if your node has synced before creating a validator

`strcli status | jq .sync_info`

At first you'll see `"catching_up": true`. When this says false, you can create your validator. It could take 24 hours to catch up. In future I will try to provide a snapshot to speed this process up.

Look at `"latest_block_height"` and compare with the [Straightedge block explorer](http://explorer.straighted.ge/blocks). That should give you an idea of how close you are to having your node synchronized.


# Create validator

```
strcli tx staking create-validator \
  --amount=10000000000000000000astr \
  --broadcast-mode=block \
  --pubkey=$(strd tendermint show-validator) \
  --moniker=ValidatorName \
  --website=<yourwebsite> \
  --details="ValidatorDescription" \
  --commission-rate="0.10" \
  --commission-max-rate="0.25" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=validator \
  --chain-id=straightedge-2 \
  --fees 5000000000000000astr
```

You will need to have STR in your account to send the above transaction. The command above may need to have a different fee amount or use the gas flag. If it doesn't work, let us know.


# To do
1. common commands used by validator
2. links to other resources
