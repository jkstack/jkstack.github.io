---
title: "LibAgent"
linkTitle: "LibAgent"
weight: 2
description: >
    Agent标准功能封装库，包含Agent的连接、通用配置、埋点监控上报等功能的封装。
---

项目地址: https://github.com/jkstack/libagent

## 流程图

![流程图](../agents/assets/%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

## Agent启动过程

1. 加载配置文件
2. 初始化logging模块，设置rotate等参数
3. 初始化limit模块
   - 若当前系统为linux操作系统则通过cgroups进行资源限制
   - 若当前系统为windows操作系统则通过go语言的[runtime](https://pkg.go.dev/runtime)库进行CPU和内存的资源限制
4. 启动monitor模块，定时上报监控数据
5. 创建WebSocket连接，交换AgentID和当前操作系统信息
6. 启动异步读/写协程处理从服务端收到的任务
7. 启动keepalive模块，定时发送心跳

## 公共配置

精鲲Agent产品在经过了若干个版本的迭代后，提炼出了一些每一类Agent都会有的公共配置信息如下：

```python
id         = <agent-id>
server     = <server-addr>
log.target = stdout,file  # log写入目标，目前仅支持stdout和file
log.dir    = <dir>        # log文件保存路径
log.size   = 10M          # log文件滚动生成时的文件大小
log.rotate = 7            # log文件滚动生成时的保留数量
monitor.enabled    = true # 是否开启监控信息上报
monitor.interval   = 30s  # 监控数据上报频率
limit.cpu_quota    = 100  # CPU最大使用率，100表示一个CPU核心
limit.memory_limit = 512M # 内存最大使用容量
# 以下配置项的内容为一个json数组，其中的字段含义如下：
#   dev: 磁盘设备号，可通过lsblk命令查询
#   read_bytes:  每秒读取字节数
#   write_bytes: 每秒写入字节数
#   read_iops:   每秒读取次数
#   write_iops:  每秒写入次数
# 注意: 该配置项只支持linux操作系统，且支持cgroups时有效
limit.disk_limit = [{"dev":"8:0","read_bytes":"10M","write_bytes":"10M","read_iops":1000,"write_iops":1000}]
```

## WebSocket连接

本地Agent与服务器端使用WebSocket协议进行RPC通信，在连接过程中双方会交换当前节点的AgentID，若本地Agent未指定AgentID时将由服务器端自动分配。