# /server/info

获取当前服务端信息

## 参数

无

## 返回值

```json
{
  "code": 0,
  "payload": {
    "clients": 连接客户端数量,
    "plugins": 插件数量,
    "version": 版本号,
    "created": 安装时间
  }
}
```
