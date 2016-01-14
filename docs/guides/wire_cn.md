#HTTP2协议上的gRPC

本文档作为gRPC在HTTP2草案17框架上的实现的详细描述，假设你已经熟悉HTTP2的规范。产品规则采用的是[ABNF语法](http://tools.ietf.org/html/rfc5234)

## 大纲

以下是gRPC请求和应答消息流中一般的消息顺序：

* 请求 → 请求头 *有定界符的消息 EOS
* 应答 → 应答头 *有定界符的消息 EOS
* 应答 → (应答头 *有定界符的消息 跟踪信息) / 仅仅跟踪时


## 请求



* 请求 → 请求头 *界定的消息 EOS
请求头是通过头+联系帧方式以HTTP2头来发送的。

* **请求头** → 调用定义 *自定义元数据

* **调用定义** → 方法模式路径TE [Authority] [超时] [内容类型] [消息类型] [消息编码] [接受消息类型] [用户代理]

* **方法** →  “:method POST”

* **模式** → “:scheme ”  (“http” / “https”)

* **路径** → “:path”  {_暴漏的API对应的方法路径_}

* **Authority** → “:authority” {_权限所对应的虚拟主机名_}

* **TE** → “te” “trailers”  # 用来检测不兼容的代理

* **超时** → “grpc-timeout” 超时时间值 超时时间单位

* **超时时间值** → {_至少8位数字正整数的ASCII码字符串_}

* **超时时间单位** → 时 / 分 / 秒 / 毫秒 / 微秒 / 纳秒
* **时** → “H”

* **分** → “M”

* **秒** → “S”

* **毫秒** → “m”

* **微秒** → “u”

* **纳秒** → “n”

* **内容类型** → “content-type” “application/grpc” [(“+proto” / “+json” / {_自定义_})]

* **内容编码** → “gzip” / “deflate” / “snappy” / {_自定义_}

* **消息编码** → “grpc-encoding” Content-Coding

* **接受消息编码** → “grpc-accept-encoding” Content-Coding *("," Content-Coding)

* **用户代理** → “user-agent” {_结构化的用户代理字符串_}

* **消息类型** → “grpc-message-type” {_消息模式的类型名_}

* **自定义数据** → 二进制头 / ASCII码头

* **二进制头** → {以“-bin”结尾小写的头名称的ASCII码 } {_以base64进行编码的值_}

* **ASCII码头** → {小写头名称的ASCII码} {_值_}




HTTP2需要一个以“:”开始的保留的头在其他头之前。额外的实现应该在保留头后面马上传送**超时**，并且应该在发送**自定义元数据**前发送**调用定义**头。
如果没有设置**超时**，服务端会认为是无限时长的超时。客户端实现可以根据发布需要自由地发送一个默认最小超时时间。
**自定义元数据**是应用层定义的任意的键值对集合。除了HTTP2头部总长度的传输限制外，唯一的约束就是以“grpc-”开始的头名称是为将来使用保留的。

注意HTTP2并不允许任意字节序列来作为头值，所以二进制的头值必须使用Base64来编码，参见[https://tools.ietf.org/html/rfc4648#section-4](https://tools.ietf.org/html/rfc4648#section-4)。 实现必须接受填充的和非填充的值，并且发出非填充的值。应用以“-bin”结尾的名称来定义二进制头。运行时库在头被发送和接收时，用这个后缀来检测二进制头并且正确地进行Base64编码和解码。

 **界定的消息**的重复序列通过数据帧来进行传输。

* **界定的消息** → 压缩标志 消息长度 消息

* **压缩标志** → 0 / 1   # 编码为1byte的无符号整数

* **消息长度** → {_消息长度_}  # 编码为4byte的无符号整数

* **消息** → *{二进制字节}




 **压缩标志** 值为1 表示**消息**的二进制序列通过**消息编码**头声明的机制进行压缩，为0表示消息的字节码没有进行编码。压缩上下文不在消息编辑间维护，声明必须为流中的每个消息创建一个新的上下文。假如 **压缩标志** 被遗漏了，那么**压缩标志** 必须为0。

对请求来讲，**EOS** (end-of-stream)以最后接收到的数据帧END_STREAM出现END_STREAM标志为准。
在**请求流**需要关闭但是没有数据继续发送的话，实现必须发送包含这个标志的空数据帧。


##应答

* **应答** → (应答头 界定的消息 跟踪信息) / 仅仅跟踪

* **应答头** → HTTP状态 [消息编码] [消息接受编码] 内容类型 *自定义元数据

* **仅仅跟踪** → HTTP状态 内容类型 跟踪消息

* **跟踪消息** → 状态 [状态消息] *自定义元数据

* **HTTP状态** → “:status 200”

* **状态** → “grpc-status” <状态码的ASCII字符串>

* **状态消息** → “grpc-message” <状态描述文本对应的ASCII字符串>




**应答头** 和 **仅仅跟踪** 分别在一个HTTP2头帧块里发送。大多数应答期望既有头又有跟踪消息，但是调用允许**仅仅跟踪**生成一个立即的错误。假如状态码是OK的话，则必须在**跟踪消息**里发送状态。
对于应答来讲，通过在最后一个接收的包含跟踪信息的头帧里提供一个END_STREAM标志来表明流结束。

实现应当会让中断的部署在应答里发送一个非200的HTTP状态码和一系列非GRPC内容类型并且省略**状态**和**状态消息**。
当发生这种情况时实现应当合成**状态**和**状态消息**来扩散到应用层。


## 例子

单项调用HTTP2帧序列例子





**请求**




```

HEADERS (flags = END_HEADERS)

:method = POST

:scheme = http

:path = /google.pubsub.v2.PublisherService/CreateTopic

:authority = pubsub.googleapis.com

grpc-timeout = 1S

content-type = application/grpc+proto

grpc-encoding = gzip

authorization = Bearer y235.wef315yfh138vh31hv93hv8h3v




DATA (flags = END_STREAM)

<Delimited Message>

```

**应答**




```

HEADERS (flags = END_HEADERS)

:status = 200

grpc-encoding = gzip




DATA

<Delimited Message>




HEADERS (flags = END_STREAM, END_HEADERS)

grpc-status = 0 # OK

trace-proto-bin = jher831yy13JHy3hc

```

## 用户代理

当协议不需要一个用户代理时，建议客户端提供一个结构化的用户代理字符串来对要调用的库、版本和平台提供一个基本的描述来帮助在异质的环境里进行问题诊断。以下结构推荐给库开发者:

```

User-Agent → “grpc-” Language ?(“-” Variant) “/” Version ?( “ (“  *(AdditionalProperty “;”) “)” )

```

例如




```

grpc-java/1.2.3

grpc-ruby/1.2.3

grpc-ruby-jruby/1.3.4

grpc-java-android/0.9.1 (gingerbread/1.2.4; nexus5; tmobile)

```

## HTTP2传输映射




### 流识别

所有的GPC调用需要定义指定一个内部ID。我们将在这个模式里使用HTTP2流ID来作为调用标识。注意：这些ID在一个打开的HTTP2会话里是前后关联的，在一个处理多个HTTP2会话的进程里不是唯一的，也不能被用作GUID。 

### 数据帧
数据帧边界与**界定消息**的边界无关，实现不应假设它们的一致性。


### 错误

当应用或运行时在PRC出现错误时，应当在**跟踪消息**里包含**状态**和**状态消息**。

在有些情况下可能消息流的帧已经中断，RPC运行时会选择使用**RST_STREAM**帧来给对方表示这种状态。RPC运行时声明应当将RST_STREAM解释为流的完全关闭，并且将错误传播到应用层。
以下为从RST_STREAM错误码到GRPC的错误码的映射：

| HTTP2 编码       |GRPC 编码         |
| -------------     |:-------------:|
| NO_ERROR(0)   | INTERNAL -一个显式的GRPC OK状态应当被发出，但是这个也许在某些场景里会被侵略性地使用|
| PROTOCOL_ERROR(1)|INTERNAL |
| INTERNAL_ERROR(2)| INTERNAL | 
| FLOW_CONTROL_ERROR(3)| INTERNAL | 
| SETTINGS_TIMEOUT(4)| INTERNAL | 
| STREAM_CLOSED| 无映射，因为没有打开的流来传播。实现应记录。 | 
| FRAME_SIZE_ERROR| INTERNAL | 
| REFUSED_STREAM|UNAVAILABLE-表示不作处理，请求可能在他处重试。 | 
| CANCEL(8)|当是由客户端发出时映射为调用取消，当是由服务端发出时映射为CANCELLED。注意服务端在需要取消调用时应仅仅使用这个机制，但是有效荷载字节顺序是不完整的| 
| COMPRESSION_ERROR| INTERNAL | 
| CONNECT_ERROR| INTERNAL | 
| ENHANCE_YOUR_CALM| RESOURCE_EXHAUSTED...并且运行时提供有额外的错误详情，表示耗尽资源是带宽| 
| INADEQUATE_SECURITY| PERMISSION_DENIED... 并且有额外的信息表明许可被拒绝，因为对调用来说协议不够安全| 

### 安全
HTTP2规范当使用TLS时强制使用TLS 1.2及以上的版本，并且在部署上对允许的密码施加一些额外的限制以避免已知的比如需要SNI支持的问题。并且期待HTTP2与专有的传输安全机制相结合，这些传输机制的规格说明不能提供有意义的建议。

###连接管理

#### GOAWAY帧

服务端发出这种帧给客户端表示服务端在相关的连接上不再接受任何新流。这种帧包含服务端最后成功接受的流的ID。客户端应该认为任何在最后成功的流后面初始的任意流为UNAVAILABLE，并且在别处重试这些调用。客户端可以自由地在已经接受的流上继续工作直到它们完成或者连接中断。
服务端应该在终止连接前发送GOAWAY帧，以可靠地通知客户端哪些工作已经被服务端接受并执行。

####PING帧
客户端和服务端均可以发送一个PING帧，对方必须精确回显它们所接收到的信息。这可以被用来确认连接仍然是活动的，并且能够提供估计端对端延迟估计的方法。假如服务端初始的PING在最后期限仍然没有收到运行时所期待的应答的话，所有未完成的调用将会被取消的状态关闭。一个客户端期满的初始的PING则会导致所有的调用被用不可用的状态关闭。注意PING的频率高度依赖于网络环境，实现可以根据网络和应用需要，自由地调整PING频率。



####连接失败
假如客户端检测到连接失败，所有的调用都会关闭并有一个不可用的状态。而服务端则会关闭打开的调用并有一个取消的状态。








## 附录 A - Protobuf上的GRPC 



用protobuf定义的服务接口可以通过protoc得代码生成扩展映射成GRPC，以下定义了所用的映射：








* **路径** → / 服务名 / {_方法名_}

* **服务名** → ?( {_proto 包名_} "." ) {_服务名_}

* **消息类型** → {_全路径proto消息名_}

* **内容类型** → "application/grpc+proto"







