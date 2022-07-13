# /cmd/sync_run

执行命令

## 参数

1. `id`: 机器ID
2. `cmd`: 命令
3. `args`: 运行参数，csv，逗号用%2c%编码
4. `timeout`: 运行超时秒数，可选（默认60，最大60）
5. `auth`: sudo、su或空值，可选
5. `user`: 账号，可选
6. `pass`: 密码，可选
7. `workdir`: 工作目录，可选
8. `env`: 环境变量，csv，逗号用%2c%编码
9. `defer_rm`: 运行完毕后删除的文件路径，可选

## 返回值

```json
{
  "code": 0,
  "payload": {
    "code": 0, // 返回码（-65535表示执行失败或超时）
    "data": "aGVsbG8gd29ybGQ=", // 返回内容经过base64编码
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

## 错误，命令执行失败

```json
{
  "code": 1,
  "msg": "错误原因"
}
```

## 说明

1. 当给定`defer_rm`参数时当命令执行完毕后将会执行对应操作