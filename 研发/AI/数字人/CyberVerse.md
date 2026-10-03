
# 1.概述
**GitHub**：[Lynpoint/CyberVerse: Self hosted, real-time digital human agent platform. Build voice-first AI agents with WebRTC, persona memory, tools, RAG, and optional digital-human video.](https://github.com/Lynpoint/CyberVerse)



# 2.部署
## 2.1 在WSL2上部署
系统环境：win11，RTX4090D，已安装完驱动。
先把系统的APT源，调整为国内源：清华源、阿里云源等。

安装依赖：
```shell
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential cmake git curl wget pkg-config libssl-dev \ zlib1g-dev protobuf-compiler unzip tmux htop netcat-openbsd

sudo apt install -y ffmpeg libopus-dev libopusfile-dev libsoxr-dev libvpx-dev libsndfile1-dev

```
安装conda：
```shell


eval "$($HOME/miniconda3/bin/conda shell.bash hook)" && conda init



conda create -n cyberverse python=3.10 -y && conda activate cyberverse

```


## 2.2 Docker编译及部署



















---
