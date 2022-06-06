###    Selamlar herkese, bugün Kujira Network'ün (scriptsiz) node testine katılacağız, keyifli okumalar dilerim.

Kujira Türkiye telegram kanalı: https://t.me/KujiraTurkish

# Sistem gereksinimlerimiz:
```
4 cpu
4 gb ram
200gb ssd
```

# Makinemizi güncelliyoruz
```
sudo apt-get update && sudo apt-get upgrade -y
```

# Gerekli araçların yüklemesini yapıyoruz.
```
sudo apt install build-essential git unzip curl wget
```

# Go kurulumumuzu yapıyoruz: 
```
wget -O go1.18.1.linux-amd64.tar.gz https://golang.org/dl/go1.18.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.1.linux-amd64.tar.gz && rm go1.18.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```

# Githubtan dosyaları indiriyoruz
```
git clone https://github.com/Team-Kujira/core $HOME/kujira-core
```

# Kujira-core dosyasına girip kurulum yapıyoruz.
```
cd $HOME/kujira-core
make install
```

# Binary dosyanın çalıştığını kontrol ederiz. Aşşağıdaki gibi "0.3.0" çıktısı almanız gerekli.
```
kujirad version
```
# Chain id bilgimizi export ediyoruz. Değiştirmeden olduğu gibi kopyalayın.
```
export CHAIN_ID=harpoon-3
```

# Moniker name giriyoruz. Tırnak arasındaki moniker-name kısmına kendi ismimizi giriyoruz.
```
export MONIKER_NAME="moniker-name"
```

# Bu komutla birlikte çıkan bilgileri bir yere not edelim
```
kujirad init "${MONIKER_NAME}" --chain-id ${CHAIN_ID}
```

# Genesis dosyamızı indiriyoruz.
```
wget https://raw.githubusercontent.com/Team-Kujira/networks/master/testnet/harpoon-3.json -O $HOME/.kujira/config/genesis.json
```

# Addrbook dosyasını indiriyoruz.
```
wget https://raw.githubusercontent.com/Team-Kujira/networks/master/testnet/addrbook.json -O $HOME/.kujira/config/addrbook.json
```

# Screen aracımızı indirip. kujira isimli bir screen oluşturuyoruz
```
apt-get install screen
screen -S kujira
```

# Screen içinde kujira node'nu başlatıyoruz. ve sync olmasını bekliyoruz. sync olmadan validator kurmayın.
```
kujirad start
```

Sync olması için ulaşmanız gereken blok sayısı burada https://testnets.cosmosrun.info/kujira-harpoon-3/ yazıyor: 

# Sync olduğumuzu kontrol edebilmek aşşağıdaki komuttan false çıktısı almanız lazım.

```
curl http://localhost:26657/status | jq .result.sync_info.catching_up
```

# Servis dosyasını oluşturalım: 
```
echo "[Unit]
Description=Kujirad Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which kujirad) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/kujirad.service
sudo mv $HOME/kujirad.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

# Systemctl tekrar yüklüyoruz.
```
sudo systemctl daemon-reload
```

# Servisimizi aktifleştiriyoruz.
```
sudo systemctl enable kujirad
```

# Son olarak servisimizi başlatıyoruz.
```
sudo systemctl start kujirad
```

# Servisimizin durumuna bakmak için:
```
systemctl status kujirad.service
```

# Cüzdan oluşturuyoruz:
Örnek: kujirad keys add rues
```
kujirad keys add
```

# Faucetten cüzdana token talep ediyoruz. YOUR_WALLET_ADRESS Yazan yere kujira ile başlayan adresi yazıyoruz.
```
curl -X POST https://faucet.kujira.app/YOUR_WALLET_ADDRESS
```

# Son olarak validator oluşturuyoruz.
```
export PUBKEY=$( kujirad tendermint show-validator)
kujirad tx staking create-validator --moniker="${MONIKER_NAME}" \
 --amount=1000000ukuji \
        --gas-prices=1ukuji \
        --pubkey=$PUBKEY \
         --from="YOURNAME" \
        --yes \
        --node=tcp://localhost:26657 \
        --chain-id=${CHAIN_ID} \
        --commission-max-change-rate=0.01 \
        --commission-max-rate=0.20 \
        --commission-rate=0.10 \
        --min-self-delegation=1
```

Son olarak https://testnets.cosmosrun.info/kujira-harpoon-3/ kendimizi kontrol edelim.

Teşekkürler..
