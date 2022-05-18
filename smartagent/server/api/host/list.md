# /host/list

查询主机列表

## 参数

1. `ids`: 需获取主机ID列表，可选，csv

## 返回值

```json
{
  "code": 0,
  "payload": [
    {
      "id": 机器ID,
      "version": agent版本号,
      "ip": ip地址,
      "mac": mac地址,
      "os": 操作系统（windows或linux）,
      "platform": 操作系统名称（centos或debian）,
      "arch": 操作系统位数（amd64或i386）
    }, ...
  ]
}
```

## 说明

1. 当不传ids参数时表示获取所有主机列表，否则获取给定ID的列表，若给定ID不存在则忽略
