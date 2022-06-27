# SmartAgent

SmartAgent 采用C/S架构的模型来运行，两者之间采用websocket协议保障在企业级网络策略中通信能力。为提高可扩展性，在Agent端使用多进程的方式运行多种插件，来提供业务方的扩展能力。

[实现原理](desc.md)

![架构图](imgs/framework.png)

## 常用API接口

* 获取服务端信息：[/server/info](/smartagent/server/api/server/info)
* 获取agent列表：[/host/list](/smartagent/server/api/host/list)
* 根据IP地址或MAC查找agent_id：[/host/search](/smartagent/server/api/host/search)
* 获取agent基本信息：[/host/info](/smartagent/server/api/host/info)

exec插件

* 远程执行命令：[/cmd/sync_run](/smartagent/server/api/cmd/run)

file插件

* 获取文件列表：[/file/ls](/smartagent/server/api/file/ls)
* 上传文件到agent：[/file/upload](/smartagent/server/api/file/upload)
* 从agent下载文件：[/file/download](/smartagent/server/api/file/download)

host.monitor插件

* 获取agent详细信息（需host.monitor插件）：[/hm/static](/smartagent/server/api/hm/static)