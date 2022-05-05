# /layout/status

获取执行进度信息

## 参数

1. `task_id`: 任务ID

## 返回值

    {
        "code": 0,
        "payload": {
            "done": 是否全部完成,
            "created": 开始时间戳,
            "finished": 结束时间戳未完成时为0,
            "total_count": 总触达节点数量,
            "finished_count": 完成节点数量,
            "nodes": [
                {
                    "id": 节点ID,
                    "created": 开始时间戳,
                    "finished": 结束时间戳未完成时为0,
                    "ok": 是否成功,
                    "msg": 错误信息
                }, ...
            ]
        }
    }

## 错误，任务不存在

    {
        "code": 404,
        "msg": 错误内容
    }
