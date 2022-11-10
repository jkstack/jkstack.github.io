---
title: "LibAgent"
linkTitle: "LibAgent"
weight: 2
description: >
    Agent标准功能封装库，包含Agent的连接、通用配置、埋点监控上报等功能的封装。
---

项目地址: https://github.com/jkstack/libagent

最新版本: ![version](https://img.shields.io/github/v/tag/jkstack/libagent)

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

## 技术实现细节

### 公共配置

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

### WebSocket连接

本地Agent与服务器端使用WebSocket协议进行RPC通信，在连接过程中双方会交换当前节点的AgentID，若本地Agent未指定AgentID时将由服务器端自动分配。

创建WebSocket链接时需要进行一次握手，握手请求由Agent端发起，[数据格式](https://github.com/jkstack/anet/blob/master/ctrl.go#L8)如下：

```go
type ComePayload struct {
	ID              string `json:"id"` // agent-id
	Name            string `json:"name"`
	Version         string `json:"version"`          // 版本号
	IP              net.IP `json:"ip"`               // ip地址
	MAC             string `json:"mac"`              // mac地址
	HostName        string `json:"host_name"`        // 主机名
	OS              string `json:"os"`               // 操作系统名称，windows或linux
	Platform        string `json:"platform"`         // 操作系统名称，debian、centos等
	PlatformVersion string `json:"platform_version"` // 操作系统版本号，如7.7.1908
	KernelVersion   string `json:"kernel_version"`   // 内核版本号，如3.10.0-1062.el7.x86_64
	Arch            string `json:"arch"`             // 操作系统位数，amd64、i386等
	CPU             string `json:"cpu"`              // CPU型号，如AMD Ryzen 7 4800U with Radeon Graphics
	CPUCore         uint64 `json:"cpu_core"`         // CPU核心数
	Memory          uint64 `json:"memory"`           // 内存大小
}
```

服务端在创建WebSocket链接时会等待本次的握手报文，超时时间为1分钟，若客户端一直未发送握手报文则将断开该链接。后续处理逻辑如下：

1. 当握手报文中未包含AgentID时，自动生成一个随机的AgentID并返回给Agent
2. 若指定AgentID在当前链接列表中已存在时，将会返回`agent id conflict`的错误
3. 返回链接成功报文

其中返回数据的报文[格式](https://github.com/jkstack/anet/blob/master/ctrl.go#L26)如下：

```go
type HandshakePayload struct {
	OK       bool     `json:"ok"`                 // 握手是否成功
	ID       string   `json:"id"`                 // 被分配的agent id
	Msg      string   `json:"msg"`                // 错误信息
	Redirect []string `json:"redirect,omitempty"` // 重定向地址
}
```

通过以上连接过程即可实现Agent端程序与服务器端程序的正常连接，并可在后续操作中实现任务的下发与调度执行。

### 断线重连

当Agent端程序发生链接断开时(如服务器端升级重启)，LibAgent将会进行断线重连，重连过程中以1s、2s、4s的间隔时间进行尝试，最多间隔4s，详情可见[start](https://github.com/jkstack/libagent/blob/master/app.go#L66)中的循环处理逻辑。

### 监控数据

Agent运行时会上报一些基础的监控信息，可通过配置文件中的`monitor.enabled`字段进行关闭，该数据由[Prometheus](https://prometheus.io/docs/concepts/data_model/)定义，可通过服务器端的`/metrics`接口获取，内容如下：

```h
# HELP agent_info 
# TYPE agent_info gauge
agent_info{agent_type="example-agent",id="example-agent",tag="cpu_usage"} 0.1730158030986786
agent_info{agent_type="example-agent",id="example-agent",tag="gc_0"} 1.0801e-05
agent_info{agent_type="example-agent",id="example-agent",tag="gc_0.25"} 1.0801e-05
agent_info{agent_type="example-agent",id="example-agent",tag="gc_0.5"} 1.6512e-05
agent_info{agent_type="example-agent",id="example-agent",tag="gc_0.75"} 1.6512e-05
agent_info{agent_type="example-agent",id="example-agent",tag="gc_1"} 1.6512e-05
agent_info{agent_type="example-agent",id="example-agent",tag="heap_in_use"} 1.9652608e+07
agent_info{agent_type="example-agent",id="example-agent",tag="in_bytes"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="in_packets"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="memory_usage"} 0.9135397672653198
agent_info{agent_type="example-agent",id="example-agent",tag="out_bytes"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="out_packets"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="read_chan_size"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="reconnect_count"} 0
agent_info{agent_type="example-agent",id="example-agent",tag="report_time"} 1.668065623e+09
agent_info{agent_type="example-agent",id="example-agent",tag="routines"} 10
agent_info{agent_type="example-agent",id="example-agent",tag="startup"} 1.668065613e+09
agent_info{agent_type="example-agent",id="example-agent",tag="threads"} 10
agent_info{agent_type="example-agent",id="example-agent",tag="write_chan_size"} 0
# HELP agent_version 
# TYPE agent_version gauge
agent_version{go_version="go1.19.3",id="example-agent",version="1.0.0"} 1
```

[埋点代码](https://github.com/jkstack/libagent/blob/master/app_report.go)，字段定义如下：

1. `startup`: 该Agent的启动时间戳
2. `report_time`: 最后一次上报时的时间戳
3. `cpu_usage`: 该Agent进程的CPU占用率（百分比）
4. `memory_usage`: 该Agent进程的内存占用率（百分比）
5. `heap_in_use`: 占用的内存字节数
6. `gc_0`: GC耗时的0分位数（秒）
7. `gc_0.25`: GC耗时的25分位数（秒）
8. `gc_0.5`: GC耗时的50分位数（秒）
9. `gc_0.75`: GC耗时的75分位数（秒）
10. `gc_1`: GC耗时的100分位数（秒）
11. `in_bytes`: 收到的数据包字节数
12. `in_packets`: 收到的数据包数量
13. `out_bytes`: 发送的数据包字节数
14. `out_packets`: 发送的数据包数量
15. `read_chan_size`: 接收队列当前长度
16. `write_chan_size`: 发送队列当前长度
17. `reconnect_count`: 重连次数
18. `threads`: 当前线程数
19. `routines`: 当前协程数
20. `go_version`: 该Agent编译的GO版本号
21. `version`: 该Agent的版本号

### linux/windows系统服务注册

目前LibAgent通过[service](https://github.com/kardianos/service)库进行Agent程序的系统服务注册与控制，目前已测试通过的操作系统列表如下：

<table>
<tr>
	<td>系统</td>
	<td>版本号</td>
	<td>内核版本</td>
</tr><tr>
	<td>ubuntu</td>
	<td>12.04</td>
	<td><a href="https://en.wikipedia.org/wiki/Ubuntu_version_history#Table_of_versions">3.2</a></td>
</tr><tr>
	<td>ubuntu</td>
	<td>14.04</td>
	<td>3.13</td>
</tr><tr>
	<td>ubuntu</td>
	<td>16.04</td>
	<td>4.4</td>
</tr><tr>
	<td>ubuntu</td>
	<td>18.04</td>
	<td>4.15</td>
</tr><tr>
	<td>ubuntu</td>
	<td>20.04</td>
	<td>5.4</td>
</tr><tr>
	<td>ubuntu</td>
	<td>22.04</td>
	<td>5.15</td>
</tr><tr>
	<td>suse</td>
	<td>11(SP4)</td>
	<td><a href="https://www.suse.com/support/kb/doc/?id=000019587">3.0.101-108.135.1</a></td>
</tr><tr>
	<td>suse</td>
	<td>12(SP5)</td>
	<td>4.12.14-122.130.1</td>
</tr><tr>
	<td>suse</td>
	<td>15(SP4)</td>
	<td>5.14.21-150400.24.18.1</td>
</tr><tr>
	<td>redhat</td>
	<td>6.1</td>
	<td><a href="https://access.redhat.com/articles/3078">2.6.32-754</a></td>
</tr><tr>
	<td>redhat</td>
	<td>7.9</td>
	<td>3.10.0-1160</td>
</tr><tr>
	<td>redhat</td>
	<td>8.6</td>
	<td>4.18.0-372.9.1</td>
</tr><tr>
	<td>centos</td>
	<td>6.1</td>
	<td><a href="https://vault.centos.org/6.10/os/Source/SPackages/">2.6.32-754</a></td>
</tr><tr>
	<td>centos</td>
	<td>7.9-2009</td>
	<td><a href="https://vault.centos.org/7.9.2009/os/Source/SPackages/">3.10.0-1160</a></td>
</tr><tr>
	<td>centos</td>
	<td>8.5.2111</td>
	<td><a href="https://vault.centos.org/8.5.2111/BaseOS/Source/SPackages/">4.18.0-348</a></td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">7 Enterprise with Service Pack 1</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">7 Professional with Service Pack 1, VL Build</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">10 Enterprise LTSC 2021</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2008 R2 Enterprise</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2008 R2 Datacenter</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2016 VL</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2016(Updated Feb 2018)</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2016 Essentials</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2019(Updated July 2020)</td>
</tr><tr>
	<td>windows</td>
	<td colspan="2">2022(updated Aug 2022)</td>
</tr>
</table>