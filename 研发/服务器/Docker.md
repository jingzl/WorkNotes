


## 阿里云ECS上Docker的安装
系统：ubuntu26.04，采用阿里云的源进行安装
```shell
# docker官方的源无法安装，采用阿里云的源安装docker

## 步骤 1：安装依赖包
# 更新软件包索引：
sudo apt-get update
# 安装依赖包以使apt能够通过HTTPS使用仓库：
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
sudo apt install -y ca-certificates curl gnupg lsb-release

## 步骤 2：添加 Docker 官方 GPG 密钥
# 创建密钥存放目录
sudo install -m 0755 -d /etc/apt/keyrings

# 下载并保存 GPG 密钥（使用阿里云镜像）
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 设置权限
sudo chmod a+r /etc/apt/keyrings/docker.gpg

## 步骤 3：添加 Docker APT 源
# 获取你的 Ubuntu 版本代号
UBUNTU_CODENAME=$(lsb_release -cs)

# 添加阿里云 Docker 源
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $UBUNTU_CODENAME stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## 步骤 4：更新源并安装 Docker
# 更新包索引
sudo apt update
# 安装 Docker 引擎，可以先查询版本
# 要替换<VERSION_STRING>为23.0.1版本的实际版本字符串，可以通过以下命令查找可用版本：
apt list -a docker-ce
# docker-ce/resolute 5:29.7.2-1~ubuntu.26.04~resolute amd64
# 选择合适版本安装
apt-get install -y docker-ce=5:29.7.2-1~ubuntu.26.04~resolute docker-ce-cli=5:29.7.2-1~ubuntu.26.04~resolute

## 步骤 5：验证安装
docker -v
# Docker version 29.7.2, build a7dcaa6

```

源配置 /etc/docker/daemon.json：
**普通版本**：
```shell
{
  "registry-mirrors": [
      "https://dockerpull.com",
      "https://dockerproxy.cn",
      "https://g2b36g87.mirror.aliyuncs.com",
      "https://docker.xuanyuan.me",
      "https://proxy.1panel.live",
      "https://docker.1ms.run",
      "https://docker.m.daocloud.io",
      "https://docker.hpcloud.cloud",
      "https://proxy.vvvv.ee",
      "https://dockerproxy.link",
      "https://docker.m.daocloud.io",
      "https://docker.jiaxin.site",
      "https://registry.cyou",
      "http://hub-mirror.c.163.com",
      "https://docker.mirrors.ustc.edu.cn",
      "https://mirrors.tuna.tsinghua.edu.cn/docker-registry"
]
}
```
**GPU版本**：
```shell
{
    "debug": true,
    "default-shm-size": "1G",
    "experimental": false,
    "max-concurrent-downloads": 10,
    "max-concurrent-uploads": 5,
    "registry-mirrors": [
	"https://docker.1ms.run",
        "https://docker.xuanyuan.me",
        "https://g2b36g87.mirror.aliyuncs.com",
        "https://mirror.gcr.io",
        "https://docker.mirrors.ustc.edu.cn",
        "https://registry.docker-cn.com",
        "https://mirror.baidubce.com",
        "https://proxy.1panel.live",
        "https://docker.1panel.top",
        "https://docker.m.daocloud.io",
        "https://docker.1ms.run",
        "https://docker.ketches.cn",
        "https://docker.rainbond.cc",
        "https://huecker.io/",
        "https://dockerhub.timeweb.cloud",
        "https://noohub.ru/",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn",
        "https://quay.nju.edu.cn",
        "https://xx4bwyg2.mirror.aliyuncs.com",
        "http://f1361db2.m.daocloud.io",
        "http://hub-mirror.c.163.com"
    ],
    "default-runtime": "nvidia",
    "runtimes": {
        "nvidia": {
            "args": [],
            "path": "nvidia-container-runtime"
        }
    }
}
```
修改完配置，需要重启：
```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```









---
