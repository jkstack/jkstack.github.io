# /file/upload

文件上传

## 参数

1. `id`: 机器id
2. `dir`: 保存位置
3. `auth`: sudo、su或空值，可选
4. `user`: 账号，可选
5. `pass`: 密码，可选
6. `mod`: 文件权限，十进制，可选，默认0644
7. `own_user`: 文件所属用户
8. `own_group`: 文件所属分组
9. `file`: http标准文件上传字段
10. `timeout`: 超时时间，单位秒，可选，默认60
11. `md5`: 文件md5，可选

## 返回值

```json
{
  "code": 0,
  "payload": null
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
