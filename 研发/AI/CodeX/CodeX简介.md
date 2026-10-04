## 1. 概述
**Codex** 是 OpenAI 推出的 AI 编程助手 ，将 GPT 级别的推理能力与本地代码执行能力结合，让开发者用自然语言即可读取、修改、执行代码。
**核心特点：**
- 本地运行
- 开源免费：Apache2.0，Rust构建
- 跨平台：MacOS、Linux、Windows
- MCP支持
- 多种模式：suggest、auto-edit、full-auto
- 多智能体：并行运行多个Agent协同工作

**Codex 的三种形态：**
①Codex CLI（终端版）、②Codex IDE 插件、③Codex App（桌面版）

**在线Free版本：**
[Codex Online Free – AI Coding Agent Powered by GPT-5-Codex](https://codex.chat/chat)

## 2. 安装与配置
### 2.1 系统要求
操作系统：最低要求 macOS 12+ / Ubuntu 20.04+ / Win 11，推荐最新LTS版本
NodeJS：最低v22+
内存：推荐8+GB

### 2.2 Codex CLI 安装
推荐 npm 全局安装：
```shell
npm install -g @openai/codex

# 查看
npm list -g

# 版本
codex -V or codex --version
```

### 2.3 Codex App 桌面端安装
官网下载：[ChatGPT 中的 Codex | 专为软件工程打造的 AI 编程智能体](https://chatgpt.com/zh-Hans-CN/codex/)
但在国内下载后安装经常报错。最终的一个方式成功，打开VPN，在终端中运行：
```Shell
winget install OpenAI.Codex

# 可以安装成功
```
经过实际安装发现，通过codex启动，似乎都是终端界面，和CLI一样，看来是哪里搞错了。
在微软商店中似乎已经升级合并到 ChatGPT桌面版中了，找不到单独的Codex App了。

### 2.4 Codex IDE 插件
直接在VSCode中添加即可。


## 3. 与CC-Switch配合使用
==注意==：使用国内模型时，**Codex CLI + CC-Switch 组合**是最稳定的方案。
==**一定要关闭其他 VPN，切记切记！**==

使用CC-Switch配置国内源的多种模型来使用CodeX。CC-Switch配置好后，直接接管CLI，无需额外配置。






参考：
CodeX  
https://kcnxjau9hxy4.feishu.cn/wiki/JjnXwoVLViQ1AukjsTlcDtcLn4d


---