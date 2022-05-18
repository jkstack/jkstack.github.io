# /host/search

根据IP地址或MAC地址查找主机

## 参数

1. `keyword`: IP地址或MAC地址

## 返回值

```json
{
  "code": 0,
  "payload": 主机ID
}
```

## 错误，未找到

```json
{
  "code": 404,
  "msg": "client not found"
}
```
