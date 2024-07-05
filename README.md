# Aallora
最低系统要求:
  操作系统：UBUNTU 22.04 LTS
  CPU：至少 1/2 核心
  内存：2 至 4 GB
  存储：SSD或NVMe，至少5GB空间

准备钱包和领水：
  安装keplr钱包 : [Keplr Extension](https://chrome.google.com/webstore/detail/dmkamcknogkgcdfhhbddcghachkejeap)
  领水链接 : [[Allora Fauce](/)t](https://faucet.edgenet.allora.network)
    如果有错误，请尝试3-5次


部署 一 ：

  如果您想在单独的 VPS 上运行此节点并且您已经尝试在该单独的 VPS 上运行此节点，则需要使用下面提到的命令删除 docker 容器文件，不要在运行其他节点的 VPS 上执行这 2 个命令。
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)

  不要在运行其他节点的 VPS 上执行这 2 个命令，只需跳过“部署 一”并转到“部署 二‘

部署 二 ：

  如果你安装过allora节点，但不成功，就删除原目录  
    rm -rf allora.sh allora-chain/ basic-coin-prediction-node/    

    wget https://raw.githubusercontent.com/wuya51/Aallora/main/Allora-Worker-Node.shh && chmod +x Allora-Worker-Node.sh && ./Allora-Worker-Node.sh

  这个脚本是老外制作的，有点缺陷，你接下来要做
     
     docker stop $(docker ps -q) 

  进入目录(以你的实现目录为准)：

     cd allora-chain
     docker compose up -d  && docker start $(docker ps -aq)

   查看容器ID
     
     docker ps
     
   第一个容器ID是同步日志：

      docker logs -f 你的容器ID


上面运行你可以多窗口查看，使用screen和tmux都可以，我以tmux为例
     
     sudo apt install tmux
     tmux new -s node

     窗口已开，可以不同窗口运行你所要查看的结果

       查看节点同步情况： docker logs -f 你的容器ID

       查看是否同步完成(显示flase就同步完成):curl -so- http\://localhost:26657/status | jq .result.sync_info.catching_up
       查看同步高度： allorad status | jq .sync_info
       实时查看同步高度: watch -n1 'echo "\sync:";allorad status | jq .sync_info'




