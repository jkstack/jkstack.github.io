---
title: "/cmd/ps"
linkTitle: "/cmd/ps"
weight: 2
description: >
    列出当前正在执行的命令列表
---

## 参数

1. `id`: 机器ID

## 返回值

```json
{
  "code": 0,
  "payload": [
    {
      "id": 进程ID,
      "channel": 输出信息的管道ID,
      "name": 进程名称,
      "start_time": 进程创建时间戳,
      "up_time": 启动时间（秒）
    }, ...
  ]
}
```

## 错误，未找到机器

```json
{
  "code": 404,
  "msg": "client not found"
}
```
