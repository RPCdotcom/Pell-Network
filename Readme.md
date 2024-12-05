## Pell Network Node Setup 

![image](https://github.com/user-attachments/assets/274cc570-ec41-4551-9742-e4cbbec1e77d)

##### Pell Node İçin Gereksinimler.

- CPUS: 4 Çekirdek
- Memory: 8 GB RAM
- Data Disk: 500+ GB Disk


Sistem paketlerini güncellemek ve yükseltmek için aşağıdaki komutu çalıştırın:

```bash
sudo apt update -y && sudo apt upgrade -y
```
## 2. Gerekli paketleri kurun:

```bash
sudo apt install htop ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev jq gcc screen unzip lz4 -y
```
## 3. Docker'ı Kur : 

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
docker version
```

## 4. Docker Compose'u Kur : 

```bash
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/$VER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## 5. Docker Kullanıcı İzinleri

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

## 6. Docker Giriş : 

```bash
docker login -u pellnetwork
```

- Key : 

```bash
dckr_pat_caHIV_ijk654lnOJ-6vvedJfLNc
```

## 7. İmageleri İndireceğiz : 

```bash
docker pull docker.io/pellnetwork/pellnode:v1.0.11-ignite-testnet
```

## 8. Node'u Başlatalım : 

- Düz Hali Budur : 

![image](https://github.com/user-attachments/assets/4ca54882-49df-43d5-bfff-a5564a980913)

- Biz burada <your_local_path> kısmına kendi sunucumuzdaki dizine göre /root/.pellcored olarak ayarladık - iç dış - bilgilerin kaydedilmesi için içeriyide yazmak gerek.

- Monikeri ayarlayın - <your_node_name> kısmı misal MONIKER="RPCdotcom"

- Portlara biz ellemedik - değiştirmek size kalmış.


```bash
docker run -d --name=pell-validator \
    -v /root/.pellcored:/root/.pellcored \
    -e MONIKER="<your_node_name>" \
    -p 26656:26656 \
    -p 26660:26660 \
    -d \
    --entrypoint /usr/local/bin/start-pellcored.sh \
    docker.io/pellnetwork/pellnode:v1.0.11-ignite-testnet
```

## 9. Container İçine Girelim : 
```bash
docker exec -it pell-validator /bin/bash
```

#### Burada kendinizi sanki Cosmos node kurar gibi hissedin lakin herşey container içerisinde - bilgilerde sunucunuzda root dizinin altında .pellcored dizininde bulunuyor.

## 10 . Cüzdan Oluşturma : 
```bash
pellcored keys add cüzdanismi
```

#### Cüzdan alın - kayıt edin - Proje ekibinden token istemeniz lazım. Tokeni Proje veriyor.

##### Cüzdana Gelip Gelmediğini Kontrol Etmek için : 

```bash
pellcored query bank balances pellcuzdanadresin
```

## Sunucu Ağ İle Senkron Olmasını Mecbur Bekleyeceğiz : 
```bash
docker logs -f pell-validator -n 150
```
- Loglarınızda blocklar gözükecektir. Misal "11:19PM INF indexed block events height=25748 module=txindex server=node"  sunucum 25748. blockta.

- Güncel block takip : https://testnet.pell.explorers.guru/ 

![image](https://github.com/user-attachments/assets/29bfac58-5b3d-4037-9f9b-285a094f6b62)

## 11. Validator Olusturma : 

- Değiştirmeniz gereklenleri biliyorsunuz - Moniker - İdentitiy Keybase yada Uport - Website - Security - Details - From cüzdan adı 

```bash

cat > ./validator.json << EOF
{
	"pubkey": $(pellcored tendermint show-validator),
	"amount": "1000000000000000000apell",
	"moniker": "<your_node_name>",
	"identity": "optional identity signature (ex. UPort or Keybase)",
	"website": "validator's (optional) website",
	"security": "validator's (optional) security contact email",
	"details": "validator's (optional) details",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}
EOF


pellcored tx staking create-validator ./validator.json \
--chain-id=ignite_186-1 \
--fees=0.000001pell \
--gas=1000000 \
--from=<your_node_name>

```

###### Doğrulayıcınızın oluşturulduğunu doğrulamak için doğrulayıcınızın mevcut durumunu gösteren bu komutu çalıştırın.

- <your_node_name> kısmını düzenleyip yapıştırın.

```bash
pellcored query staking validator $(pellcored keys show <your_node_name> --bech val -a)

```
