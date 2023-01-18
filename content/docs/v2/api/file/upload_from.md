---
title: "/file/upload_from"
linkTitle: "/file/upload_from"
weight: 3
description: >
    从指定url上传文件到某个节点
---

## 参数

1. `id`: 机器id
2. `dir`: 保存位置
3. `name`: 文件名
4. `uri`: 下载uri
5. `auth`: sudo、su或空值，可选
6. `user`: 账号，可选
7. `pass`: 密码，可选
8. `mod`: 文件权限，十进制，可选，默认0644
9. `own_user`: 文件所属用户
10. `own_group`: 文件所属分组
11. `timeout`: 超时时间，单位秒，可选，默认60

## 返回值

```json
{
  "code": 0,
  "payload": {
    "dir": 文件存放路径
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

## 错误，其他运行时错误

```json
{
  "code": 500,
  "msg": 错误内容
}
```

## 错误，上传失败

```json
{
  "code": 1,
  "msg": 错误内容
}
```

## 错误，文件内容错误

```json
{
  "code": 2,
  "msg": 错误内容
}
```
