









**VSCode + CodeX扩展，无法启动**
启动报错：Codex could not start
The extension couldn't load its resources.
解决过程：
在VSCode中，ctrl+shift + P，输入  `Developer: Open Extension Logs Folder`，查看插件日志，找到 openai.chatgpt 文件夹，打开 Codex.log，内容如下：
```
2026-08-08 20:44:09.846 [info] Activating Codex extension
2026-08-08 20:44:09.846 [info] [CodexMcpConnection] Spawning codex app-server
2026-08-08 20:44:09.846 [info] [IpcRouter] I am the router
2026-08-08 20:44:09.846 [warning] [CodexMcpConnection] cli: message="codex_core_plugins::remote::remote_installed_plugin_sync: remote installed plugin bundle sync failed error=chatgpt authentication required for remote plugin catalog; api key auth is not supported"
2026-08-08 20:44:09.846 [info] [CodexMcpConnection] Initialize received id=1
2026-08-08 20:44:09.846 [warning] [IpcClient] Received broadcast but no handler is configured method=client-status-changed
2026-08-08 20:44:09.847 [warning] [CodexMcpConnection] cli: message="codex_core_plugins::manager: failed to warm featured plugin ids cache error=failed to send remote featured plugin request to https://chatgpt.com/backend-api/plugins/featured?platform=codex: error sending request for url (https://chatgpt.com/backend-api/plugins/featured?platform=codex)"
2026-08-08 20:44:10.326 [info] [IpcRouter] I am the router
2026-08-08 20:44:10.378 [info] [statsig-refresh-diagnostics] React root render requested windowType=extension
2026-08-08 20:44:10.485 [warning] [IpcClient] Received broadcast but no handler is configured method=client-status-changed
2026-08-08 20:44:11.309 [error] Request failed conversationId=none durationMs=572 error={"code":-32603,"message":"系统找不到指定的路径。 (os error 3)"} failureReason=null id=3e096a01-8a2b-49fd-ac4a-f47e0d8c2579 method=fs/readFile pendingCountAfter=0 priority=interactive queueWaitMs=0 source=filesystem spanId=null timeoutMs=0 traceId=null
2026-08-08 20:44:39.810 [error] [CodexWebviewProvider] Webview did not finish starting extensionVersion=26.803.41515 role=sidebar
2026-08-08 20:44:55.577 [error] Error fetching error="TypeError: fetch failed" url=https://ab.chatgpt.com/v1/initialize?k=client-sYWqzCYMRkUg4DqqiZcR5DGTNl2iD7zNJY0HoeDLzxR&st=javascript-client&sv=3.33.3&t=1786193051186&sid=3f4139f2-a58f-4ba1-9b79-527cf96f47d0&se=1
2026-08-08 20:45:05.860 [error] Error fetching error="TypeError: fetch failed" url=https://ab.chatgpt.com/v1/initialize?k=client-sYWqzCYMRkUg4DqqiZcR5DGTNl2iD7zNJY0HoeDLzxR&st=javascript-client&sv=3.33.3&t=1786193061692&sid=3f4139f2-a58f-4ba1-9b79-527cf96f47d0&se=1
2026-08-08 20:45:18.194 [error] Error fetching error="TypeError: fetch failed" url=https://ab.chatgpt.com/v1/initialize?k=client-sYWqzCYMRkUg4DqqiZcR5DGTNl2iD7zNJY0HoeDLzxR&st=javascript-client&sv=3.33.3&t=1786193073719&sid=3f4139f2-a58f-4ba1-9b79-527cf96f47d0&se=1
2026-08-08 20:45:21.931 [warning] [CodexMcpConnection] cli: message="codex_core_plugins::startup_sync: git sync failed for curated plugin sync; falling back to GitHub HTTP error=git ls-remote curated plugins repo timed out after 30s: fatal: unable to access 'https://github.com/openai/plugins.git/': schannel: server closed abruptly (missing close_notify)"
```
在VSCode的CodeX插件无法启动，查看日志发现无法连接，没有走CC-Switch代理，最终调整VSCode的settings.json配置如下：
```BASH
    "codex.apiKey": "cc-switch",
    "codex.baseUrl": "http://127.0.0.1:15721/v1",
    "http.proxyAuthorization": null,
    "http.proxy": "http://127.0.0.1:15721",
    "http.proxyStrictSSL": false
```
问题解决。
发布CSDN博客：[VSCode+CodeX扩展，无法启动-CSDN博客](https://blog.csdn.net/james506/article/details/163605963)










---
