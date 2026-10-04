## 1. 



**项目结构参考**
```
project-root/
├── src/                    # 源码目录
│   ├── main/               # 主代码
│   │   ├── java/           # Java 源码
│   │   ├── resources/      # 资源文件
│   │   └── webapp/         # Web 应用资源
│   ├── test/               # 测试代码
│   │   ├── java/
│   │   └── resources/
|   ├── backend/            # 后台
│   │   ├──...
|   ├── frontend/           # 前端
│   │   ├──...
|   ├── server/             # Server
│   │   ├──...
|   ├── client/             # Client
│   │   ├──...
│   └── utils/              # 工具模块
│       └── helper.py
├── docker/                 # Docker 相关文件
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── .dockerignore
├── deploy/                 # 部署配置
│   ├── scripts/            # 部署脚本
│   │   ├── deploy.sh
│   │   └── rollback.sh
│   ├── ansible/            # Ansible 配置
│   │   └── playbook.yml
│   └── kubernetes/         # K8s 配置
│       ├── deployment.yaml
│       └── service.yaml
├── docs/                   # 文档目录
│   ├── api/                # API 文档
│   ├── guide/              # 使用指南
│   ├── changelog.md        # 更新日志
│   └── README.md
├── logs/                   # 日志目录
│   ├── app/                # 应用日志
│   ├── error/              # 错误日志
│   └── access.log          # 访问日志
├── config/                 # 配置文件
│   ├── application.yml
│   └── logback.xml
├── scripts/                # 通用脚本
│   ├── build.sh
│   └── init.sh
├── .gitignore
├── README.md
└── pom.xml / package.json  # 依赖管理文件
```






针对一个新的代码工程：
首先使用CC或者CodeX进行整体架构梳理：
**prompt**：整体分析 xxxxx 工程代码，整理出整体架构、工作流程、功能特点、使用场景、局限性、部署方式，分析结果保存为MD文件，输出到工作区根目录。


基本流程
1. 打开项目。
2. 让 Codex 总结相关文件。
3. 描述你要解决的问题。
4. 要求 Codex 给出修改计划。
5. 确认后让 Codex 修改。
6. 运行测试或本地启动验证。
7. 查看 Git diff，确认改动符合预期。
8. 再提交代码。


新产品：
1）先描述核心需求，让其进行细化
2）整理出用户及场景、功能清单、页面清单及关键流程，有含糊的地方先假设，等我确认
3）



整理架构图设计：管理后台部署在云端，操作员门户部署在局域网的某台电脑上，这台电脑同时部署SIP服务，连接在局域网的固定电话线，使用这个固定电话对外进行AI语音电话拨打回访。均通过docker部署。使用mermaid设计架构图，输出保存在docs目录。







---
*==2026==.~*