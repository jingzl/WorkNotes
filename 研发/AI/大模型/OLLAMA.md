## 1. 概述
官网：[Ollama](https://ollama.com/)
GitHub：[ollama/ollama: Get up and running with Kimi-K2.6, GLM-5.2, MiniMax, DeepSeek, gpt-oss, Qwen, Gemma and other models.](https://github.com/ollama/ollama)



## 2. 部署
版本选择：


**安装 zstd**：如果系统未安装 `zstd`，在 Ubuntu/Debian 上可通过 `sudo apt install zstd` 安装，在 CentOS/Fedora 上通过 `sudo dnf install zstd` 安装。
```Shell
# 下载 ollama-linux-amd64.tar.zst，比较大，使用tmux后台处理
tmux new -d -s ollama-download 'wget https://github.com/ollama/ollama/releases/download/v0.34.0/ollama-linux-amd64.tar.zst && echo "下载完成！"'

# 解压
# 1. 先将 .zst 解压为 .tar 文件
zstd -d ollama-linux-amd64.tar.zst

# 2. 再解压 .tar 文件
tar -xf ollama-linux-amd64.tar
```

**更新现有系统中的版本**：
```shell
# 当前系统中ollama路径
which ollama
# 输出
/usr/local/bin/ollama

# 先停止当前版本
sudo systemctl stop ollama

# 将解压后的 bin 和 lib 文件夹，拷贝至 /usr 
cp ./bin -r /usr 
cp ./lib -r /usr 

# 编辑 ollama service文件，没有就创建，主要是 ExecStart 路径：
vi /etc/systemd/system/ollama.service
## 
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=$PATH"
Environment="CUDA_VISIBLE_DEVICES=0,1,2,3"
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_NUM_PARALLEL=4"
Environment="OLLAMA_MAX_LOADED_MODELS=2"
Environment="OLLAMA_KEEP_ALIVE=-1"

[Install]
WantedBy=default.target
## 

# 然后执行：
sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
```
查看版本：
```shell
ollama -v
# 输出
ollama version is 0.33.2
Warning: client version is 0.13.5
```
这个警告的原因，应该是上一个版本的信息残留，直接去 /usr/local/bin/ 路径下，删除 ollama，然后建立软链接：
```shell
ln -s /usr/bin/ollama ollama

# 再次执行 ollama -v：
ollama version is 0.33.2
# 正常
```








---
