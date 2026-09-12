## 1. 概述
**环境**：异地内网服务器，windows11 25H2，专业版，Intel i9，GeForce RTX4090D 24G显卡
**需求**：需要远程操作，运行系统，目前用向日葵，速度非常不可控，很不爽。
**方案**：目前在阿里云上有公网服务器，考虑用FRP进行端口映射。在内网windows上打开OpenSSH通过证书进行访问；另外打开远程桌面进行访问。

## 2. FRP处理
1）在内网服务器运行 FRPC，参考官网配置进行处理即可。
2）将FRPC处理为服务，自动后台运行。
首选的方案是 sc 命令
```BASH
sc.exe create frpc binPath= "C:\Programs\frp_0.71.0\frpc.exe -c C:\Programs\frp_0.71.0\frpc.toml" start= auto
```
⚠️结果报错：
```BASH
PS C:\> sc.exe start frpc 
[SC] StartService 失败 1053: 
服务没有及时响应启动或控制请求。
```
直接运行FRPC没问题，这样启动服务就报错，通过查找windows事件查看器，找到原因：
frpc作为服务启动时，Windows服务管理器（SCM）在等待frpc响应时超时了。问题在于frpc默认是前台程序，它不会向服务管理器发送"服务已启动"的信号。frpc是控制台应用程序，不是专门为Windows服务设计的程序。它启动后不会调用 `StartServiceCtrlDispatcher` 函数，所以Windows认为它没有响应。
最后解决方案：使用**nssm**（[NSSM - the Non-Sucking Service Manager](https://nssm.cc/download)），是专门解决这类问题的工具，它会在frpc和Windows服务管理器之间充当代理。
安装后，运行时根据界面提示进行相关参数设置即可，==注意要使用管理员权限打开终端==。
```Shell
.\nssm.exe install frpc
```
在弹出窗口中配置，Service name 输入服务名称，不要带特殊字符：
- **Application** -> **Path**：`C:\Programs\frp_0.71.0\frpc.exe`
- **Application** -> **Arguments**：`-c C:\Programs\frp_0.71.0\frpc.toml`
- **Startup type**：`Automatic (Delayed Start)`（延迟启动，避免开机时网络未就绪）
- **I/O** 标签页：
    - **Output (stdout)**：`C:\Programs\frp_0.71.0\logs\frpc.log`
    - **Error (stderr)**：`C:\Programs\frp_0.71.0\logs\frpc_error.log`
处理完毕后，重启服务：
``` Shell
.\nssm.exe start frpc
```
至此，FRP相关工作处理完毕，两个端口均进行了转换映射，注意不要直接将22和3389对外打开。

## 3. SSH
参考：[适用于 Windows 的 OpenSSH 服务器配置 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows-server/administration/OpenSSH/openssh-server-configuration)
1）**服务器上添加安装 OpenSSH**，并启动（已管理员权限启动终端）。
```BASH
# 确认服务在运行
Get-Service sshd
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic

# 确认22端口在监听
netstat -ano | findstr :22
```
2）**配置证书访问**，不建议用密码访问
在本地生成证书：
```BASH
ssh-keygen -t ed25519
# 生成 `C:\Users\xxx\.ssh\id_ed25519` 和 `id_ed25519.pub`
```
拷贝公钥内容追加到目标机器：
```powershell
# Windows OpenSSH 中，管理员组用户（包括 administrator）不读取 ~\.ssh\authorized_keys，而是读取：
C:\ProgramData\ssh\administrators_authorized_keys

# 假设你把 id_ed25519.pub 拷到了桌面
# 文件不存在，则直接写入：
Copy-Item "$env:USERPROFILE\Desktop\id_ed25519.pub" "C:\ProgramData\ssh\administrators_authorized_keys"

# 如果文件存在，则添加：
Get-Content "$env:USERPROFILE\Desktop\id_ed25519.pub" | Add-Content "C:\ProgramData\ssh\administrators_authorized_keys"
```
修复文件权限：
```powershell
# administrators_authorized_keys 的权限要求很严格，必须只归 SYSTEM 和 Administrators 所有，否则 sshd 会忽略该文件：
icacls "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "SYSTEM:F" /grant "BUILTIN\Administrators:F"

# 验证权限：
icacls "C:\ProgramData\ssh\administrators_authorized_keys"
# 输出应只包含 SYSTEM 和 BUILTIN\Administrators 两条。
```
重启 sshd 服务：
```powershell
Restart-Service sshd
```
3）**修改远程windows的shell终端**
Windows OpenSSH 的默认 Shell 是 cmd.exe，不是 PowerShell，这样在使用时，很多指令没有，不方便。
处理的方式就是把默认 Shell 改为 PowerShell，在远程 Windows 11 上，管理员 PowerShell 运行：
```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" `
  -Name DefaultShell `
  -Value "C:\Program Files\PowerShell\7\pwsh.exe" `
  -PropertyType String -Force
```
无需重启 sshd，重新连接即生效，之后 ls、Get-ChildItem 等都能直接用。
⚠️**问题**：结果修改后，再次远程连接时，无法连接，提示的很诡异，说密钥未在远程服务器端注册？
但明显所有的一切都是刚确认过的，只是调整了一个shell终端就出问题。
```powershell
# 检查 PowerShell 7 是否存在
Test-Path "C:\Program Files\PowerShell\7\pwsh.exe"

# 检查系统自带 PowerShell
Test-Path "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe"
```
最终发现 PowerShell 7不存在，（返回 False），改用系统自带的 PowerShell 5.1：
```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" `
  -Name DefaultShell `
  -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" `
  -PropertyType String -Force
```
重启服务并测试：
```powershell
Restart-Service sshd
```
问题解决。

## 4. RDP
在远程服务器上打开远程桌面，默认可能是关闭的：设置 → 系统 → 远程桌面 → 打开。
⚠️ Win11 家庭版不支持远程桌面服务端，需专业版/企业版
注意点：
- 确认使用的账户有密码（空密码账户默认不能远程）
- 远程唤醒前确保内网电脑未睡眠，可设置电源选项“从不睡眠”



整理了发布了CSDN博客：[记录在内网windows上开启远程SSH和RDP的过程-CSDN博客](https://blog.csdn.net/james506/article/details/164371919)

---
