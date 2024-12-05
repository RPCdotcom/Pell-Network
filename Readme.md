## Pell Network Node Setup 

![image](https://github.com/user-attachments/assets/274cc570-ec41-4551-9742-e4cbbec1e77d)


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
