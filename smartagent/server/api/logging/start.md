# /logging/start

启动采集任务

## 参数

1. `pid`: 项目ID

## 返回值

```json
{
  "code": 0
}
```

## 错误，项目已启动

```json
{
  "code": 1,
  "msg": 错误内容
}
```

## 错误，任务不存在

```json
{
  "code": 404,
  "msg": 错误内容
}
```
