# Ollo-gayd

```
sudo apt update && sudo apt upgrade -y
```
```
ver="1.19.1"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```
```
git clone https://github.com/OLLO-Station/ollo
cd ollo
make install
```
```
which ollod
```
```
CHAIN="ollo-testnet-0"
MONIKER="<YOUR moniker>" 
WALLET="wallet"
```
```
echo 'export CHAIN='${CHAIN} >> $HOME/.bash_profile
echo 'export MONIKER='${MONIKER} >> $HOME/.bash_profile
echo 'export WALLET='${WALLET} >> $HOME/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
```
```
ollod config chain-id ollo-testnet-0
ollod config keyring-backend test
```
```
ollod init $MONIKER --chain-id $CHAIN
```
```
ollod keys add $WALLET
```
### or this
```
ollod keys add $WALLET --recover
```
```
ADDR=$(ollod keys show $WALLET -a)
echo $ADDR
VALOPER=$(ollod keys show $WALLET --bech val -a)
echo $VALOPER
```
```
echo 'export ADDR='${ADDR} >> $HOME/.bash_profile
echo 'export VALOPER='${VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
ollod keys list
```
```
cd $HOME/.ollo/config/
rm genesis.json
```
```
curl -O https://raw.githubusercontent.com/OLLO-Station/ollo/networks/ollo-testnet-0/genesis.json
mv genesis.json ~/.ollo/config/genesis.json
```
```
wget -O $HOME/.ollo/config/addrbook.json "https://raw.githubusercontent.com/OllO-Station/ollo/master/networks/ollo-testnet-0/addrbook.json"
```
```
cd ~/.ollo/config
```
```
sed -i '/\[api\]/,+3 s/enable = false/enable = true/' app.toml
```
```
PEERS=06658ccd5c119578fb662633234a2ef154881b94@18.144.61.148
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.ollo/config/config.toml
```
### In order to create validator first you need to fund your wallet with testnet tokens.
```
https://discord.com/channels/983996378533482557/1018399842256556034
```
### use this command for fauset:  !request YOUR_WALLET_ADDRESS
### for check ur balance 
```
ollod query bank balances $WALLET
```
```
sudo tee /etc/systemd/system/ollod.service > /dev/null <<EOF
[Unit]
Description=ollod
After=network-online.target

[Service]
User=$USER
ExecStart=$(which ollod) start --home $HOME/.ollo
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable ollod
sudo systemctl restart ollod && sudo journalctl -u ollod -f -o cat
```

## Create Validator
```
ollod tx staking create-validator \
      --amount="1000000utollo" \          
      --pubkey=$(ollod tendermint show-validator) \
      --moniker="$MONIKER" \
      --chain-id="$CHAIN" \
      --commission-rate="0.15" \
      --commission-max-rate="0.20" \
      --commission-max-change-rate="0.01" \
      --min-self-delegation="1" \
      --gas="auto" \
      --gas-adjustment="1.5" \
      --gas-prices="0.025utollo" \
      --from="$WALLET" \
      --website="<SOME SITE>" \
      --details="<SOME DETAILS>" \
      --identity="<KEYBASE ID>" \
      -y
```
### If u need change default ports u can change this strings for what u need 
#### config.toml
#### string 15: proxy_app = "tcp://127.0.0.1:26658"
#### string 91: laddr = "tcp://127.0.0.1:26657"
#### string 167: pprof_laddr = "localhost:6060"
#### string 175: laddr = "tcp://0.0.0.0:26656"
#### string 392: prometheus_listen_addr = ":26660"
#### app.toml
#### string 165: address = "0.0.0.0:9090"
#### string 178: address = "0.0.0.0:9091"
#### client.toml
#### string 15: node = "tcp://localhost:26657"

#### or u can do this 
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:60858\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:60857\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:6067\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:60856\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":60860\"%" $HOME/.ollo/config/config.toml
```
```
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:9790\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:9791\"%" $HOME/.ollo/config/app.toml
```
```
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:60857\"%" $HOME/.ollo/config/client.toml
```
# And ofc u can use this best system monitoring!
```
https://github.com/madnoder/Monitoring-Bot-HAQQ/blob/main/README.md
```
