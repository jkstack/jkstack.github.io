# 实现原理

smartagent底层通过websocket协议进行服务端与客户端的RPC通信，同时通过内部的心跳机制来进行节点保活，从宏观上看agent的处理逻辑如下

![agent_main](imgs/agent_main.png)

1. smartagent底层使用websocket协议与服务端进行连接，在连接过程中含有一次握手的过程，握手过程中agent会上报配置文件中的agent_id信息与当前操作系统信息，服务端在收到握手信息后会通过[manifest.json](https://github.com/jkstack/scaffolding/blob/master/manifest.json)中的描述信息来分发对应操作系统的插件
2. 握手过程中若发现当前上报的agent_id已存在（当前活跃连接中已包含该agent_id的agent连接）则会返回握手失败消息并告知id冲突
3. executor为插件执行器，在非windows操作系统下支持插件切换到其他用户身份运行
4. smartagent使用[containerd/cgroups](https://github.com/containerd/cgroups)库来限制linux系统下的资源使用，目前仅支持cgroups v1

## Msg结构

Msg结构为smartagent底层通信的核心数据结构，其结构如下

    type Msg struct {
        Type      TypeName    `json:"type"`             // 消息类型，详见消息类型章节
        Important bool        `json:"-"`                // 是否是关键消息
        TaskID    string      `json:"tid,omitempty"`    // 任务ID
        Plugin    *PluginInfo `json:"plugin,omitempty"` // 所需调用的插件信息
        ErrorMsg  string      `json:"errmsg,omitempty"` // 错误消息的详情
        // ctrl
        Come      *ComePayload      `json:"come,omitempty"`      // 握手请求
        Handshake *HandshakePayload `json:"handshake,omitempty"` // 握手返回结果
        ...
    }

1. [type](https://github.com/jkstack/anet/blob/master/types.go)字段是一个整数类型的字段，用于表明该消息的类型，目前已定义区段如下

        0~9: 系统保留，用于服务端到agent通信
        10+: 各插件使用
2. tid字段用于表明该消息的id
3. plugin字段用于表明该消息所需使用的插件信息，其中包含插件的版本号等，当agent收到的msg非系统消息且不包含plugin信息时该消息将被丢弃

        type PluginInfo struct {
            Name    string         `json:"name"`    // 插件名称
            Version string         `json:"version"` // 插件版本号
            MD5     [md5.Size]byte `json:"md5"`     // 插件文件的md5
            URI     string         `json:"uri"`     // 插件下载的uri
        }
4. 在Msg结构的设计时，应尽量缩短字段名并增加omitempty关键字来忽略控制，以此来减少数据传输时的数据量

## executor与插件化

executor是smartagent中的插件执行器，目前实现了以下功能：

1. 下载并管理插件的可执行文件，当某个插件的低版本所有进程退出时进行旧版本文件清理
2. 插件运行时参数配置信息生成，插件的运行参数将会被写入到临时文件内，插件运行参数通过临时文件尽心传递，在运行完毕后该临时文件将会被删除
3. 插件运行时的日志将会通过stderr进行输出，executor在收集到日志后将会在每一行前面加上时间
4. 插件返回数据可通过stdout进行输出，每一次插件运行允许输出多条数据，输出的格式如下

        <length(4字节)><crc32(4字节)><payload>
5. 插件的基本实现原理如下

    ![plugin](imgs/plugin.png)
