# /cmd/run

执行命令

## 参数

1. `id`: 机器ID
2. `cmd`: 命令
3. `args`: 运行参数，csv，逗号用%2c%编码
4. `timeout`: 运行超时秒数，可选（默认3600）
5. `auth`: sudo、su或空值，可选
5. `user`: 账号，可选
6. `pass`: 密码，可选
7. `workdir`: 工作目录，可选
8. `env`: 环境变量，csv，逗号用%2c%编码
9. `defer_rm`: 运行完毕后删除的文件路径，可选
10. `callback`: 运行完毕后回调地址，可选

## 返回值

```json
{
  "code": 0,
  "payload": {
    "channel_id": 获取stdout输出信息的管道id,
    "pid": 进程id
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
  "msg": 原因
}
```

## 说明

1. 当给定`defer_rm`参数时命令执行完毕后将会删除给定路径的文件
2. 当给定`callback`参数时将会通过`GET`的方式进行回调，同时将`pid`参数拼接在给定url参数内