# Namada-Gentx

#Gerekli paketleri yükleyin

```
cd $HOME
```

```
sudo apt update && sudo apt upgrade -y
```

```
sudo apt install curl tar wget clang pkg-config git make libssl-dev libclang-dev libclang-12-dev -y
```

```
sudo apt install jq build-essential bsdmainutils ncdu gcc git-core chrony liblz4-tool -y
```

```
sudo apt install original-awk uidmap dbus-user-session protobuf-compiler unzip -y
```

```
cd $HOME
```

```
  sudo apt update
```

```
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
```

```
  . $HOME/.cargo/env
```

```
  curl https://deb.nodesource.com/setup_18.x | sudo bash
```

```
  sudo apt install cargo nodejs -y < "/dev/null"
```

  ```
cargo --version
```

```
node -v
```
  
#Go'yu yükleyin. (Tek komut)

```
if ! [ -x "$(command -v go)" ]; then
  ver="1.20.5"
  cd $HOME
  wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
  sudo rm -rf /usr/local/go
  sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
  rm "go$ver.linux-amd64.tar.gz"
  echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
  source ~/.bash_profile
fi
```

```
go version
```

#Rustup & protoc'u yükleyin.

```
cd $HOME && rustup update
```

```
PROTOC_ZIP=protoc-23.3-linux-x86_64.zip
```

```
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v23.3/$PROTOC_ZIP
```

```
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc
```

```
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*'
rm -f $PROTOC_ZIP
```

```
protoc --version
```


#Değişkenleri ayarlayın.

```
sed -i '/public-testnet/d' "$HOME/.bash_profile"
sed -i '/NAMADA_TAG/d' "$HOME/.bash_profile"
sed -i '/WALLET_ADDRESS/d' "$HOME/.bash_profile"
sed -i '/CBFT/d' "$HOME/.bash_profile"
```

```
echo "export NAMADA_TAG=v0.23.2" >> ~/.bash_profile
echo "export CBFT=v0.37.2" >> ~/.bash_profile
echo "export WALLET=wallet" >> ~/.bash_profile
echo "export BASE_DIR=$HOME/.local/share/namada" >> ~/.bash_profile
```

#YOUR_MONIKER Kısmını değiştirmeyi UNUTMAYIN!***

```
echo "export VALIDATOR_ALIAS=YOUR_MONIKER" >> ~/.bash_profile
```

```
source ~/.bash_profile
```

#Namadayı ve Tendermint'i yükleyin.

```
screen -S anasayfa
```

```
cd $HOME && git clone https://github.com/anoma/namada && cd namada && git checkout $NAMADA_TAG
```

```
make build-release
```

#(Control A D tuşlarıyla çıkış yapıp biraz bekiyoruz.

```
screen -r anasayfa
```

```
cargo fix --lib -p namada_apps
```

```
cd $HOME && git clone https://github.com/cometbft/cometbft.git && cd cometbft && git checkout $CBFT
```

```
make build
```

```
cd $HOME && cp $HOME/cometbft/build/cometbft /usr/local/bin/cometbft && \
cp "$HOME/namada/target/release/namada" /usr/local/bin/namada && \
cp "$HOME/namada/target/release/namadac" /usr/local/bin/namadac && \
cp "$HOME/namada/target/release/namadan" /usr/local/bin/namadan && \
cp "$HOME/namada/target/release/namadaw" /usr/local/bin/namadaw && \
cp "$HOME/namada/target/release/namadar" /usr/local/bin/namadar
```

```
cometbft version
```

```
namada --version
```

#Systemd'yi oluşturun.

```
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=/usr/local/bin/namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl enable namadad
```


#Gentx oluşturun #PUBLIC_IP Kısmına sunucu IP adresinizi $ALIAS kısmına validator adınızı gireceksiniz

```
namada client utils init-genesis-validator --alias $ALIAS \ --max-commission-rate-change 0.01 --commission-rate 0.05 \ --net-address $PUBLIC_IP:26656
```

 #Toml dosyasının çıktısını alın
 
 ```
cat $HOME/.local/share/namada/pre-genesis/$ALIAS/validator.toml
```

https://github.com/anoma/namada-testnets
