---
title: "/cmd/kill"
linkTitle: "/cmd/kill"
weight: 1
description: >
    杀死正在执行的进程
---

## 参数

1. `id`: 机器id
2. `pid`: 进程id

## 返回值

```json
{
  "code": 0
}
```

## 错误，未找到机器

```json
{
  "code": 404,
  "msg": "(client|process) not found"
}
```
