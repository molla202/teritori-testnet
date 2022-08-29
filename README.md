<h1 align="center">Teritori Testnet </h1>

### Teritori Network'ün testnetine katılacağız. 



![image](https://user-images.githubusercontent.com/101149671/179496277-2037ebfb-a375-4979-a4ac-bcfcacff059c.png)

# Sistem gereksinimleri:



```
2 CPU
2 RAM
160 GB SSD
```
# Makinemizi kuruyoruz:
```
apt update && apt upgrade -y 
```

```
apt install build-essential git curl gcc make jq -y
```

# Go kurulum:
```
wget -c https://go.dev/dl/go1.18.3.linux-amd64.tar.gz && rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz && rm -rf go1.18.3.linux-amd64.tar.gz
```

```
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
```

# Bu komutu girdikten sonra: "*Should return go version go1.18.3 linux/amd64*" yazıyorsa sıkıntı yoktur
```
go version
```

# Zinciri oluşturuyoruz:
```
git clone https://github.com/TERITORI/teritori-chain && cd teritori-chain && git checkout teritori-testnet-v2 && make install
```

# Kurulum gerçekleştiğni teyit etmek için (*Should return  teritori-testnet-v2-0f4e5cb1d529fa18971664891a9e8e4c114456c6*) yazıyorsa sıkıntı yok:
```
teritorid version
```

# Ağı başlatalım (<> işaretlerini kaldıralım)
```
teritorid init <VALİDATOR İSMİNİZ> --chain-id teritori-testnet-v2
```

# Yapılandırma dosyası: 
```
sed -i.bak 's/persistent_peers =.*/persistent_peers = "0b42fd287d3bb0a20230e30d54b4b8facc412c53@176.9.149.15:26656,2371b28f366a61637ac76c2577264f79f0965447@176.9.19.162:26656,2f394edda96be07bf92b0b503d8be13d1b9cc39f@5.9.40.222:26656"/' $HOME/.teritorid/config/config.toml
```

# Genesis dosyasını indirelim:
```
wget -O ~/.teritorid/config/genesis.json https://raw.githubusercontent.com/TERITORI/teritori-chain/main/testnet/teritori-testnet-v2/genesis.json
```

# Servis dosyası:
```
tee <<EOF >/dev/null /etc/systemd/system/teritorid.service
[Unit]
Description=Teritori Cosmos daemon
After=network-online.target

[Service]
User=root
ExecStart=/root/go/bin/teritorid start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

# Servisten sonra bu komutları:

```
systemctl enable teritorid
systemctl daemon-reload
systemctl restart teritorid
```

# Şimdi servis dosyasının içine giriyoruz:
```
sudo nano /etc/systemd/system/teritorid.service
```



# En üste altta ki komudu ekliyoruz, en alta EOF yazıyoruz:
```
tee <<EOF >/dev/null /etc/systemd/system/teritorid.service
```

# Bu şekilde gözükecek:

Not: CTRL+X+Y+ENTER yapıp kaydediyoruz



# Şimdi addrbook ekliyoruz:
```
sudo systemctl stop teritorid
rm $HOME/.teritorid/config/addrbook.json
wget -O $HOME/.teritorid/config/addrbook.json https://raw.githubusercontent.com/StakeTake/guidecosmos/main/teritori/teritori-testnet-v2/addrbook.json
sudo systemctl restart teritorid
```
# Daha sonra logları kontrol ediyoruz ve eşleşmeye başlıyor. (başlamazsa 4-5 dakika bekleyin)
```
journalctl -u teritorid.service -f -n 100
```

# Eşleşmeye başlarken cüzdan oluşturalım (<> kalkacak):
```
teritorid keys add <molla202>
```
# Test tokeni için discorda gidelim: https://discord.gg/6VAMb9Ch
```
$request Cüzdan adresi
```

# Bu komutu ara ara kullanın en altta false yazınca validator oluşturalım:
```
teritorid status 2>&1 | jq .SyncInfo
```

# Eşleşince Validator oluşturma Moniker (validator ısmı) ve from (cüzdan) kısımlarını düzenleyin:
```
teritorid tx staking create-validator --chain-id teritori-testnet-v2 --commission-rate 0.1 --commission-max-rate 0.1 --commission-max-change-rate 0.1 --min-self-delegation "900000" --amount 900000utori --pubkey $(teritorid tendermint show-validator) --moniker "molla202" --from molla202 --fees 555utori
```


