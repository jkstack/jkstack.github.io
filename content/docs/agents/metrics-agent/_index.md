---
title: "metrics-agent"
linkTitle: "metrics-agent"
weight: 4
description: >
    该Agent主要用于主机信息采集，包括但不限于CPU、内存、磁盘、网络、进程等。
---

项目地址: https://github.com/jkstack/metrics-agent

最新版本: ![version](https://img.shields.io/github/v/release/jkstack/metrics-agent)

## 兼容性

| 系统   | 版本号 | 内核版本 | 是否支持 |
| :----: | :---: | :-----: | ------- |
| ubuntu | 12.04 | [3.2](https://en.wikipedia.org/wiki/Ubuntu_version_history#Table_of_versions) | ✅ |
| ubuntu | 14.04 | 3.13 | ✅ |
| ubuntu | 16.04 | 4.4  | ✅ |
| ubuntu | 18.04 | 4.15 | ✅ |
| ubuntu | 20.04 | 5.4  | ✅ |
| ubuntu | 22.04 | 5.15 | ✅ |
| suse   | 10(SP4) | [2.6.16.60-0.132.1](https://www.suse.com/support/kb/doc/?id=000019587) | ❌ |
| suse   | 11(SP4) | 3.0.101-108.135.1      | ✅ |
| suse   | 12(SP5) | 4.12.14-122.130.1      | ✅ |
| suse   | 15(SP4) | 5.14.21-150400.24.18.1 | ✅ |
| redhat | 5.11 | [2.6.18-398](https://access.redhat.com/articles/3078) | ❌ |
| redhat | 6.1  | 2.6.32-754     | ✅ |
| redhat | 7.9  | 3.10.0-1160    | ✅ |
| redhat | 8.6  | 4.18.0-372.9.1 | ✅ |
| centos | 5.11 | [2.6.18-398](https://vault.centos.org/5.11/os/Source/) | ❌ |
| centos | 6.1  | [2.6.32-754](https://vault.centos.org/6.10/os/Source/SPackages/) | ✅ |
| centos | 7.9-2009 | [3.10.0-1160](https://vault.centos.org/7.9.2009/os/Source/SPackages/) | ✅ |
| centos | 8.5.2111 | [4.18.0-348](https://vault.centos.org/8.5.2111/BaseOS/Source/SPackages/) | ✅ |
| windows | XP | | ❌ |
| windows | 7 Enterprise with Service Pack 1             | | ✅ |
| windows | 7 Professional with Service Pack 1, VL Build | | ✅ |
| windows | 10 Enterprise LTSC 2021                      | | ✅ |
| windows | 2008 R2 Enterprise                           | | ✅ |
| windows | 2008 R2 Datacenter                           | | ✅ |
| windows | 2016 VL                                      | | ✅ |
| windows | 2016(Updated Feb 2018)                       | | ✅ |
| windows | 2016 Essentials                              | | ✅ |
| windows | 2019(Updated July 2020)                      | | ✅ |
| windows | 2022(updated Aug 2022)                       | | ✅ |

## 采集项

采集项分为静态数据、usage数据、进程列表数据、连接列表数据、传感器温度数据，这5类数据可单独设置采集频率。

[字段定义](https://github.com/jkstack/agent-server/blob/master/internal/api/metrics/metrics.proto)

### 静态数据

静态数据的默认采集频率为1天，该部分数据的特点是基本不太会变化，如CPU核心数、CPU型号、内存总大小等，可采集到的内容如下：

| 采集项 | 描述 |
| ------ | --- |
| host_name | 主机名 |
| uptime | 系统启动时长 |
| os_name | 操作系统类型，如linux、windows等 |
| platform_name | 操作系统名称，如debian、centos等 |
| platform_version | 操作系统版本号，如7.7.1908 |
| install | 操作系统安装时间 |
| startup | 操作系统启动时间 |
| kernel_version | 内核版本号 |
| arch | 操作系统位数，如amd64、i386等 |
| physical_cpu | 物理核心数，表示有多少块CPU |
| logical_cpu | 逻辑核心数 |
| gateway | 网关地址 |
| nameservers | dns服务器列表 |

<big>其中`cores`字段表示该主机的CPU核心列表，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| processor | 当前是第几个核心 |
| model | 核心型号，如Intel(R) Xeon(R) CPU E5-2620 v2 @ 2.10GHz |
| core | 该核心所在物理核心编号 |
| cores | 该核心所在CPU上的编号 |
| physical | 该核心所在CPU在主板上的物理编号 |
| mhz | 该核心的频率 |

<big>其中`disks`字段表示该机器的磁盘列表，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| model | 品牌型号 |
| total | 总容量 |
| type | 磁盘类型，0=硬盘；1=软盘；2=光盘 |
| partitions | 分区列表 |

<big>其中`partitions`字段表示该机器的硬盘分区信息，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| mount | linux系统为挂载路径如/run，windows系统为盘符如C: |
| type | 分区类型，如NTFS等 |
| options | 挂载选项，如rw,nosuid,nodev等 |
| total | 该分区总容量 |
| inodes | 该分区总inode数量 |

<big>其中`interfaces`字段表示该机器的网卡列表信息，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| index | 该网卡下标 |
| name | 网卡名称，如eth0 |
| mtu | 该网卡的mut配置 |
| flags | 该网卡的附加参数，如BROADCAST,RUNNING,MULTICAST等 |
| addrs | 该网卡绑定的IP列表，包含IPv4和IPv6地址 |
| mac | 该网卡的mac地址 |

<big>其中`users`字段表示该主机上的账号列表，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| name | 用户名 |
| id | 用户ID |
| gid | 用户所在组ID |

### usage数据

usage数据表示该主机的使用率数据，默认采集频率为5秒，可采集到的内容如下：

| 采集项 | 描述 |
| ----- | ---- |
| cpu_usage | CPU使用率 |
| memory_used | 内存占用字节数 |
| memory_free | 内存剩余字节数 |
| memory_available | 可用内存字节数 |
| memory_usage | 内存使用率 |
| swap_used | 已使用swap内存字节数 |
| swap_free | 剩余swap内存字节数 |
| cpu_load_1 | CPU的最近1分钟负载 |
| cpu_load_5 | CPU的最近5分钟负载 |
| cpu_load_15 | CPU的最近15分钟负载 |

<big>其中`partitions`字段表示磁盘分区当前使用率列表，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| mount | linux系统为挂载路径如/run，windows系统为盘符如C:，用于与静态数据关联 |
| used | 已使用字节数 |
| free | 未使用字节数 |
| usage | 使用率 |
| inode_used | 已使用的inode数量 |
| inode_free | 未使用的inode数量 |
| inode_usage | inode使用率 |
| read_per_second | 每秒读取字节数 |
| write_per_second | 每秒写入字节数 |
| iops_in_progress | 正在等待的IO操作数量 |

<big>其中`interfaces`字段表示网卡信息，内容如下：</big>

| 采集项 | 描述 |
| ----- | ---- |
| name | 网卡名称，如eth0，用于与静态数据关联 |
| bytes_sent | 已发送字节数 |
| bytes_recv | 已接收字节数 |
| packets_sent | 已发送数据包数量 |
| packets_recv | 已接收数据包数量 |

### 进程列表数据

该采集项用于采集主机上的进程列表信息，内容如下：

| 采集项 | 描述 |
| ----- | ---- |
| id | 进程ID |
| parent_id | 父进程ID |
| user | 所属用户 |
| cpu_usage | CPU使用率 |
| rss | 物理内存字节数 |
| vms | 虚拟内存字节数 |
| swap | swap内存字节数 |
| memory_usage | 内存使用率 |
| cmd | 启动命令行参数 |
| listen | 监听端口列表 |
| connections | 连接数 |

### 连接列表数据

该采集项用于采集主机上的连接列表信息，内容如下：

| 采集项 | 描述 |
| ----- | ---- |
| fd | 句柄编号 |
| pid | 所属进程ID |
| type | 连接类型，0=tcp IPv4；1=tcp IPv6；2=udp IPv4；3=udp IPv6；4=unix domain socket；5=unix file socket |
| local | 本地地址 |
| remote | 远程地址 |
| status | 连接状态，如ESTABLISHED等 |

### 传感器温度数据

该采集项用于采集主机上的传感器温度信息，如CPU温度、显卡温度等，内容如下：

| 采集项 | 描述 |
| ----- | ---- |
| name | 设备名称 |
| temp | 温度，单位摄氏度 |