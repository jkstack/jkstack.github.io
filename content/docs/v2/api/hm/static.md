---
title: "/hm/static"
linkTitle: "/hm/static"
weight: 1
description: >
    获取主机的静态监控数据
---

## 参数

1. `id`: 机器ID

## 返回值

```json
{
  "code": 0,
  "payload": {
    "timestamp": 上报时间戳,
    "host_name": 主机名,
    "uptime": 启动时长,
    "os_name": 系统类型，linux、windows,
    "platform": 系统名称，如debian、centos,
    "platform_version": 系统版本号,
    "kernel_arch": 内核类型，如i386、amd64,
    "kernel_version": 内核版本，如3.10.0-1062.el7.x86_64,
    "physical_core": 物理核心数,
    "logical_core": 逻辑核心数,
    "cores": [
      {
        "processor": 该核心编号,
        "model": 名称，如Intel(R) Xeon(R) CPU E5-2620 v2 @ 2.10GHz,
        "core": 所在物理核上的编号,
        "cores": 某块CPU上的编号,
        "physical": 物理CPU编号
      }, ...
    ]
    "physical_memory": 物理内存大小,
    "swap_memory": swap内存大小,
    "disks": [
      {
        "name": 名称，linux为挂载路径如/run，windows为盘符如C:,
        "type": 文件系统类型，如NTFS,
        "options": [附加参数，如rw，nosuid，nodev, ...],
        "total": 总容量,
        "inodes": inode数量
      }, ...
    ],
    "intfs": [
      {
        "index": 编号,
        "name": 名称,
        "mtu": mtu,
        "flags": [附加信息, ...],
        "mac": mac地址,
        "addrs": [ip地址, ...],
      }, ...
    ]
  }
}
```

## 错误，未找到机器

```json
{
  "code": 404,
  "msg": "client not found"
}
```

## 错误，超时

```json
{
  "code": 408,
  "msg": "timeout"
}
```
