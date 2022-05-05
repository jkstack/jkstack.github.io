# /cmd/kill

杀死正在执行的进程

## 参数

1. `id`: 机器id
2. `pid`: 进程id

## 返回值

    {
        "code": 0
    }

## 错误，未找到机器

    {
        "code": 404,
        "msg": "(client|process) not found"
    }
