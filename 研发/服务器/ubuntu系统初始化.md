
1）修改主机名
```shell
sudo hostnamectl set-hostname xxx(new-hostname)

# 验证修改
hostnamectl
```

2）更新索引及Swap区
```shell
# 更新软件包索引：
sudo apt-get update
sudo apt update
sudo apt upgrade

## 针对内存偏小的设备，设置swap分区：
## 调整 swap 分区
## 建立4GB的swap分区
1）查看
df -h # 查看磁盘空间，确保 / 目录下有足够可用空间
free -h # 查看分区情况 如果没有，则创建
2）创建
sudo fallocate -l 4G /swapfile
# 设置权限 Swap 文件只能由 root 用户读写，普通用户不可访问
sudo chmod 600 /swapfile
# 格式化为 Swap
sudo mkswap /swapfile
3）启用
# 启用 Swap
sudo swapon /swapfile
# 设置开机自动挂载 (永久生效)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
# 验证是否成功
free -h
4）优化
Ubuntu 默认的 Swappiness 值是 60，意味着内存用到 40% 左右就开始积极使用 Swap。对于只有 2GB 内存的服务器，建议调低这个值（例如 10），让系统尽量优先使用物理内存，实在不够用了再用 Swap，这样系统响应会更快。
# 永久生效（修改配置文件）
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

3）调整默认端口
关于ICMP、RDP等端口，修改ssh默认端口

4）采用证书方式登录









---
