# 1. 概述

在阿里云上可以获取免费证书，但有数量限制，而且不支持通配符域名，更重要的是只有3个月有效期，过期后又需要重新申请，很麻烦。

[Let's Encrypt](https://letsencrypt.org/) 是一个免费、开源的证书颁发机构，可以帮助您为网站获取SSL/TLS证书，启用HTTPS加密连接。
关于HTTPS的相关介绍，可以参见 [HTTPS](wiz://open_document?guid=445e029a-891b-46a2-8f7b-e8b309c7a6da&kbguid=&private_kbguid=db342a46-d090-11e0-85fb-00237def97cc)。
本文介绍详细的使用及安装过程。
参考：[Documentation - Let's Encrypt](https://letsencrypt.org/docs/)


# 2. 部署实施
系统环境：Ubuntu26.04 LTS X64
## 2.1 安装Certbot工具
Certbot是申请Let's Encrypt证书的官方工具。安装 Certbot可以通过 apt、pip 和 snap 三种方式安装，根据系统需要处理，一定注意要与后面的插件要一致，否则无法对应。考虑到与aliyun dns插件的兼容性问题，不要用snap方案。下面采用的是 **venv + pip 安装方案**。
```shell
# 1. 卸载 snap 版
sudo snap remove certbot
sudo snap remove certbot-dns-aliyun
snap list | grep certbot # 验证一下

# 2. 建 venv
sudo apt update 
sudo apt install -y python3-venv python3-pip libaugeas0 libssl-dev libffi-dev

sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip

# 3. 装 certbot + 阿里云插件
sudo /opt/certbot/bin/pip install certbot certbot-dns-aliyun

# 4. 做软链
sudo ln -sf /opt/certbot/bin/certbot /usr/bin/certbot

# 5. 验证
certbot --version
certbot plugins | grep aliyun
```

## 2.2 测试续期
```shell
certbot renew --dry-run

# 报错
Unsafe permissions on credentials configuration file: /etc/letsencrypt/aliyun.ini
```
Certbot 发现你的阿里云凭据文件**权限太开放**（可被其他用户读取），出于安全考虑**拒绝继续**用它做 DNS 校验。这是硬性检查，不是警告。
修复：把凭据文件权限收紧到 600，并且root为属主。
```shell
sudo chown root:root /etc/letsencrypt/aliyun.ini
sudo chmod 600 /etc/letsencrypt/aliyun.ini
ls -l /etc/letsencrypt/aliyun.ini     # 期望：-rw------- 1 root root
```

## 2.3 自动续期
1）续期service
```shell
sudo tee /etc/systemd/system/certbot.service > /dev/null <<'EOF'
# /etc/systemd/system/certbot.service
[Unit]
Description=Certbot renewal
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
ExecStart=/root/miniconda3/bin/certbot renew -q --no-self-upgrade
PrivateTmp=true
EOF
```
2）定时器（每天 2 次 + 随机延迟，官方推荐防封）
```shell
sudo tee /etc/systemd/system/certbot.timer > /dev/null <<'EOF'
[Unit]
Description=Run certbot twice daily with random delay

[Timer]
OnCalendar=*-*-* 00,12:00:00
RandomizedDelaySec=3600
Persistent=true

[Install]
WantedBy=timers.target
EOF
```
3）启用
```shell
sudo systemctl daemon-reload
sudo systemctl enable --now certbot.timer
systemctl list-timers | grep certbot
systemctl status certbot.timer  # 查看状态

sudo systemctl start certbot.service      # 立即试跑一次
journalctl -u certbot-renew.service -n 30 --no-pager
```
注意：
`certbot.service` 是 **Type=oneshot**（一次性服务），它是由 **`certbot.timer`** 来调用的，而不是通过 `enable` 启动。只需要 **enable certbot.timer** 就足够了，不需要 enable certbot.service。







---
