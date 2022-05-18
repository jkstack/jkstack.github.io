# /cmd/status

获取进程运行完成时的状态码

## 参数

1. `id`: 机器ID
2. `pid`: 进程ID

## 返回值

```json
{
  "code": 0,
  "payload": {
    "id": 进程ID,
    "channel": 该进程的channel id,
    "created": 进程创建时间,
    "running": 该进程是否在执行中,
    "code": 返回码（仅当running=false时有效）
  }
}
```

## 错误，未找到机器或进程

```json
{
  "code": 404,
  "msg": 错误内容
}
```
