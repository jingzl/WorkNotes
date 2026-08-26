## 1. 概述







## 2. 服务及工具：
### 2.1 SIP服务
FreeSwitch、Asterisk、Kamailio等，SIP服务器，它只能处理基于IP的SIP信令。
- **FreeSWITCH**是一个开源的电话软交换平台，支持实时音视频通信及会议，支持SIP及WebRTC等多种通信协议。其主要开发语言是C，某些模块使用了C++，以MPL1.1发布。[FreeSwitch](FreeSwitch.md)
- [h9-tec/Call-center-AI-local](https://github.com/h9-tec/Call-center-AI-local)


### 2.2 SIP中继服务
要拨打真实手机号，必须通过一个**SIP中继（SIP Trunk）** 服务商，该服务商能将SIP通话转换为传统电话网信号。
Twilio、国内的云通讯平台等

https://www.twilio.com/
[容联云,全球智能通讯云服务商](https://www.yuntongxun.com/)


### 2.3 常用工具
- [MicroSIP Downloads - Installer and Portable Version](https://www.microsip.org/downloads)，在window上运行时，务必要使用管理员运行，避免无法创建账户并连接。









## 3. 开发
1）建立空的项目，并在git上保存。
2）建立docs目录，并编写需求及版本规划，里面详细描述第一阶段需求，包含整体架构考虑、功能需求、部署需求、其他需求等。
3）让CC针对需求进行分析，对整体架构进行设计。
4）











---
*==2026.~==*