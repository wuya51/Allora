<h1 align="center">Allora Network 积分程序</h1>

> - 新建Keplr钱包或者导入钱包
>
> - Allora Network [积分面板](https://app.allora.network?ref=eyJyZWZlcnJlcl9pZCI6ImI2NmYzNjA3LWYyYjEtNGZmYi1hZGI4LTM4YmJmODNjNzU0NCJ9)
>
>

<h1 align="center">价格预测工作节点</h1>

## 系统要求
![image](https://github.com/0xmoei/allora-testnet/assets/90371338/56f1e0d2-4d59-436c-a0e0-183f9a082de4)

## 安装依赖项
```console
# Install Packages
sudo apt update & sudo apt upgrade -y

sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y
```
```console
# Install Python3
sudo apt install python3
python3 --version

sudo apt install python3-pip
pip3 --version
```
```console
# Install Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version

# Install Docker-Compose
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version

# Docker Permission to user
sudo groupadd docker
sudo usermod -aG docker $USER
```
```console
# Install Go
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```

## 安装 Allorad
### 可选：仅当您之前运行过节点
删除旧文件
```console
cd $HOME && rm -rf allora-chain

git clone https://github.com/allora-network/allora-chain.git

cd allora-chain && make all

allorad version
```

## 添加钱包
* 输入密码导入助记词
```console
# 恢复钱包
allorad keys add testkey --recover

#OR

# 新建钱包
allorad keys add testkey
```

## 领水操作
> Allora Network [积分面板](https://app.allora.network?ref=eyJyZWZlcnJlcl9pZCI6ImI2NmYzNjA3LWYyYjEtNGZmYi1hZGI4LTM4YmJmODNjNzU0NCJ9) to find your Allora address
> 
> uAllo 水龙头 [领水](https://faucet.testnet-1.testnet.allora.network/)

#

## 安装并运行 Worker

### 可选：仅当您之前运行过节点
停止容器、删除旧文件
```console
cd $HOME && cd basic-coin-prediction-node
docker compose down -v
docker container prune

cd $HOME && rm -rf basic-coin-prediction-node
```

#

### 1- 下载basic-coin-prediction-node目录
```console
cd $HOME
git clone https://github.com/allora-network/basic-coin-prediction-node
cd basic-coin-prediction-node
```

### 2- 配置文件
```console
# Remove config file
rm -rf config.json

# 导入仓库里的 config.json、model.py、app.py和requirements.txt文件，覆盖原来文件
# 修改config.json 文件 ，改成自己的助记词
nano config.json
```

```
{
    "wallet": {
        "addressKeyName": "testkey",
        "addressRestoreMnemonic": "改成自己钱包助记词",
        "alloraHomeDir": "",
        "gas": "1000000",
        "gasAdjustment": 1.0,
        "nodeRpc": "https://sentries-rpc.testnet-1.testnet.allora.network/",
        "maxRetries": 1,
        "delay": 1,
        "submitTx": false
    },
    "worker": [
        {
            "topicId": 1,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        },
        {
            "topicId": 2,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        },
        {
            "topicId": 7,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        }
    ]
}
```
Ctrl+X+Y+Enter to 保存 & 退出

### 3- 运行 Worker
```console
chmod +x init.config
./init.config
```
* 如果您需要更改 config.json ，则必须在更改完成后再次重新运行此命令


```console
docker compose up -d --build
```

### 4- Check logs
Containers:
```console
docker compose ps
```

worker:
```console
docker compose logs -f worker
```
![image](https://github.com/user-attachments/assets/63ca0e84-c802-416a-a872-af6331aa776f)

inference:
```console
docker compose logs -f inference
```
![image](https://github.com/user-attachments/assets/a8133f85-b643-484d-beeb-cdfb51d6fd5c)

updater:
```console
docker compose logs -f updater

# Response:
# updater-basic-eth-pred  | UPDATING INFERENCE WORKER DATA
# updater-basic-eth-pred  | Response content is '0'
```

Check ETH price:
```console
curl http://localhost:8000/inference/ETH
```

# 积分 [面板](https://app.allora.network/points/leaderboard) 3个小时后再来看积分
# 加入TG 
- Telegram - https://t.me/new5151

