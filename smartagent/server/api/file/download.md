# /file/download

文件下载

## 参数

1. `id`: 机器id
2. `dir`: 文件路径
4. `timeout`: 超时事件，默认10分钟

## 返回值

文件内容

## 错误，未找到机器或文件

    http_code=404
    body=(client/file) not found

## 错误，超时

    http_code=408
    body=timeout

## 错误，校验码错误

    http_code=409
    body=invalid checksum

## 错误，运行时错误

    http_code=503
    body=错误内容
