---
title: "/cmd/pty"
linkTitle: "/cmd/pty"
weight: 3
description: >
    获取脚本输出内容
---

## 参数

1. `id`: 机器ID
2. `pid`: 进程ID

## 返回值

    http_code=200
    body=内容

## pid不存在

    http_code=404
    body=process not found

## 错误，其他异常

    http_code=500
    body=错误内容