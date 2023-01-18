---
title: "/host/info"
linkTitle: "/host/info"
weight: 1
description: >
    获取单个节点的基本信息
---

## 参数

1. `id`: 机器ID

## 返回值

```json
{
  "code": 0,
  "payload": {
    "hostname": 主机名,
    "os": linux或windows
  }
}
```
