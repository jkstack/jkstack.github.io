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

## 数据存储

redis:

1. `redis_addr`: redis数据库地址，可选
2. `redis_db`: redis数据库db编号，可选
3. `redis_expire`: 数据超时时间（单位秒），可选

elasticsearch:

1. `es_addr`: elasticsearch数据库地址，可选

## 返回值

```json
{
  "code": 0,
  "payload": {
    "channel_id": "20220706-00002-f230bae72f2ca269", // 任务ID
    "pid": 9655 // 进程id
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
  "msg": "错误原因"
}
```

## 说明

1. 当给定`defer_rm`参数时命令执行完毕后将会删除给定路径的文件
2. 当给定`callback`参数时将会通过`GET`的方式进行回调，同时将以下参数拼接在url内部
   - `agent_id`: 执行agent的ID
   - `pid`: 创建进程ID
3. 当给定`redis_addr`参数时表示执行的输出结果需要保存到redis中，使用`string`类型进行保存，key为返回结果中的channel_id，value内容格式如下

    ```json
    {
      "job_num": "20220706-00002-f230bae72f2ca269",  // channel_id
      "target": "agent-001",  // 执行节点ID
      "status": "SUCCESS",    // SUCCESS=执行成功；FAILURE=执行失败；PENDING=执行中
      "create_date": 0,       // 开始时间戳
      "end_date": 0,          // 结束时间戳未完成时为0
      "logs": "YWJjZA=="      // 输出内容base64编码，当超过64k数据时，内容为头64k数据加上...
    }
    ```

4. 当给定`es_addr`参数时表示执行的输出结果需要保存到elasticsearch中，其中index为exec_\<yyyymmdd\>，内容同redis中的数据，当出现跨天执行的情况时，在执行完成后将会删除PENDING状态的记录