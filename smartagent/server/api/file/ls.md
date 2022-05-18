# /file/ls

列出文件

## 参数

1. `id`: 机器ID
2. `dir`: 目录

## 返回值

```json
{
  "code": 0,
  "payload": {
    "dir": 目录,
    "files": [
      {
        "name": 名称,
        "auth": 权限,
        "user": 所属用户,
        "group": 所属组,
        "size": 文件大小,
        "mod_time": 更新时间戳,
        "is_dir": 是否是目录,
        "is_link": 是否是软链
      }, ...
    ]
  }
}
```

## 错误，未找到机器或目录不存在

```json
{
  "code": 404,
  "msg": "(client/directory) not found"
}
```

## 错误，超时

```json
{
  "code": 408,
  "msg": "timeout"
}
```

## 错误，其他错误

```json
{
  "code": 500,
  "msg": 错误内容
}
```
