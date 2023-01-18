---
title: "/install/run"
linkTitle: "/install/run"
weight: 1
description: >
    安装软件
---

## 参数

1. `id`: 机器id
2. `uri`: 下载uri，可选
3. `url`: 下载url，可选
4. `dir`: 安装或解压路径，可选
4. `timeout`: 超时时间，单位秒，可选，默认300
5. `auth`: sudo、su或空值，可选
6. `user`: 账号，可选
7. `pass`: 密码，可选

## 返回值

```json
{
  "code": 0,
  "payload": 任务ID
}
```

## 错误，未找到机器

```json
{
  "code": 404,
  "msg": "client not found"
}
```

## 错误，其他运行时错误

```json
{
  "code": 500,
  "msg": 错误内容
}
```

## 说明

1. `dir`参数仅当`msi`、`exe`或压缩包时有效
2. `url`和`uri`参数必须传一个，且`uri`参数的优先级高于`url`参数
3. 目前仅支持`deb`、`rpm`、`tar`、`tar.gz`、`tar.bz2`、`zip`、`msi`和`exe`类型的安装包
