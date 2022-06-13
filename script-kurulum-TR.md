### Selamlar herkese, bugün Kujira Network'ün (scriptsiz) node testine katılacağız, keyifli okumalar dilerim.

(Nodes guru'dan türkçeleştirilmiştir)

Scriptsiz kurulum için: https://github.com/ruesandora/kujira-node-testnet-TR

# Sunucumuza baglandıktan sonra bu tek satır komut girmeniz yeterli:
```
apt install screen
screen -S node
wget -q -O kujira.sh https://databox.acadao.org/kujira.sh && chmod +x kujira.sh && sudo /bin/bash kujira.sh
//Node isminizi girin
source $HOME/.bash_profile
kujirad keys add <cüzdan-ismi>
curl -X POST https://faucet.kujira.app/<cüzdan-adresi>

journalctl -u kujirad -f -o cat
CTRL + A + D ile ana ekrana geçebilirsiniz.
```
## Validator oluşturma:

--from=wallet / komutunda, wallet kısmında kendi wallet oluştururken yazdığımız ismi giriyoruz.

```
kujirad tx staking create-validator \
--moniker="$KUJIRAD_NODENAME" \
--amount=10000000ukuji \
--gas-prices=1ukuji \
--pubkey=$(kujirad tendermint show-validator) \
--chain-id=harpoon-4 \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--min-self-delegation=1 \
--from=wallet \
--yes \
```


## Yararlı Komutlar:
```
systemctl restart kujirad
//Node yeniden başlatma

curl localhost:26657/status
//Node durumu

kujirad keys show wallet --bech val -a
//Validator adresini görme

kujirad tx staking delegate <validator-adresi> 10000000ukuji --from wallet --chain-id harpoon-3 --fees 5000ukuji
//Ek token kitleme

kujirad query staking validators --limit 2000 -o json | jq -r '.validators[] | select(.status=="BOND_STATUS_BONDED") | [.operator_address, .status, (.tokens|tonumber / pow(10; 6)), .description.moniker] | @csv' | column -t -s"," | sort -k3 -n -r
//Aktif Validatör görme
```


Teşekkürler..

