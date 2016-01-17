#开始
欢迎进入 gRPC 的开发文档，gRPC 一开始由 google 开发，是一款语言中立、平台中立、开源的远程过程调用(RPC)系统。
本文档通过快速概述和一个简单的 Hello World 例子来向您介绍 gRPC 。你可以在本站发现更详细的教程和参考文档-文档将会越来越丰富。
##快速开始
要想直观地着手运行 gRPC，可以从你所选择的语言对应的快速开始入手，里面包含创建这个列子的安装指导、快速上手指南等更多内容。

 - [C++](https://github.com/grpc/grpc/tree/master/examples/cpp)
 - [Java](https://github.com/grpc/grpc-java/tree/master/examples)
 - [Go](https://github.com/grpc/grpc-go/tree/master/examples)
 - [Python](https://github.com/grpc/grpc/tree/master/examples/python)
 - [Ruby](https://github.com/grpc/grpc/tree/master/examples/ruby)
 - [Node.js](https://github.com/grpc/grpc/tree/master/examples/node)
 - [Android Java](https://github.com/grpc/grpc-java/tree/master/examples/android)
 - [C#](https://github.com/grpc/grpc/tree/master/examples/csharp/helloworld)
 - [Objective-C](https://github.com/grpc/grpc/tree/master/examples/objective-c/helloworld)
 - [PHP](https://github.com/grpc/grpc/tree/master/examples/php)

你可以从[这里](https://github.com/grpc/grpc)找到 gRPC 的源码库。我们大多数例子都在源码库 examples 目录下。
## gRPC 是什么？
在 gRPC 里*客户端*应用可以像调用本地对象一样直接调用另一台不同的机器上*服务端*应用的方法，使得您能够更容易地创建分布式应用和服务。与许多 RPC 系统类似，gRPC 也是基于以下理念：定义一个*服务*，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个 gRPC 服务器来处理客户端调用。在客户端拥有一个*存根*能够像服务端一样的方法。
![图1][1]
gRPC 客户端和服务端可以在多种环境中运行和交互 - 从 google 内部的服务器到你自己的笔记本，并且可以用任何 gRPC [支持的语言](#quickstart)来编写。所以，你可以很容易地用 Java 创建一个 gRPC 服务端，用 Go、Python、Ruby 来创建客户端。此外，Google 最新 API 将有 gRPC 版本的接口，使你很容易地将 Google 的功能集成到你的应用里。

### 使用 protocol buffers

gRPC 默认使用 *protocol buffers*，这是 Google 开源的一套成熟的结构数据序列化机制（当然也可以使用其他数据格式如 JSON）。正如你将在下方我们的例子里所看到的，你用 *proto files* 创建 gRPC 服务，用 protocol buffers 消息类型来定义方法参数和返回类型。你可以在 [Protocol Buffers 文档](https://developers.google.com/protocol-buffers/docs/overview)找到更多关于 Protocol Buffers 的资料。

#### Protocol buffers 版本
尽管 protocol buffers 对于开源用户来说已经存在了一段时间，我们的例子内使用的却一种名叫 proto3 的新风格的 protocol buffers，它拥有轻量简化的语法、一些有用的新功能，并且支持更多新语言。当前针对 Java 和 C++ 发布了 beta 版本，针对 JavaNano（即 Android Java）发布 alpha 版本，在[protocol buffers Github 源码库里](https://github.com/google/protobuf/releases)有 Ruby 支持， 在[golang/protobuf Github 源码库](https://github.com/golang/protobuf)里还有针对 Go 语言的生成器, 对更多语言的支持正在开发中。 你可以在 [proto3 语言指南](https://developers.google.com/protocol-buffers/docs/proto3)里找到更多内容， 在与当前默认版本的[发布说明](https://github.com/google/protobuf/releases)比较，看到两者的主要不同点。更多关于 proto3 的文档很快就会出现。虽然你*可以*使用 proto2 (当前默认的 protocol buffers 版本)， 我们通常建议你在 gRPC 里使用 proto3，因为这样你可以使用 gRPC 支持全部范围的的语言，并且能避免 proto2 客户端与 proto3 服务端交互时出现的兼容性问题，反之亦然。
## 你好 gRPC!
现在你已经对 gRPC 有所了解，了解其工作机制最见简单的方法是看一个简单的例子。我们的 Hello World 将带领你创建一个简单的客户端-服务端应用，向你展示：

 - 通过一个 protocol buffers 模式，定义一个简单的带有 Hello World 方法的 RPC 服务。
 - 用你最喜欢的语言(如果可用的话)来创建一个实现了这个接口的服务端。
 - 用你最喜欢的(或者其他你愿意的)语言来访问你的服务端。
 
这个例子完整的代码在我们 GitHub 源码库的 `examples` 目录下。
我们可以使用 Git 版本系统来进行源码管理，但是除了如何安装和运行一些 Git 命令外，你没必要知道其他关于 Git 的任何事情。
需要注意的是，并不是所有 gRPC 支持的语言都可以编写我们例子的服务端代码，比如 PHP 和 Objective-C 仅支持创建客户端。
比起一个针对于特定语言的复杂教程这更像是一个介绍性的例子。你可以在本站找到更有深度的教程，gRPC 支持的语言的参考文档很快就会全部开放。
### 准备
本节解释了如何在你本机上准备好例子代码的运行环境。如果你只是想读一下例子，你可以直接到[下一步](#servicedef)。

#### 安装 Git

你可以从[http://git-scm.com/download](http://git-scm.com/download)下载和安装 Git。一旦安装好 Git 后你应该能访问 git 命令行工具。你需要的主要命令如下：

- git clone ... : 从远程代码库克隆一份到本机。
- git checkout ... : 检出一个特殊分支或一个标签版本的代码来改进

#### 安装 gRPC
针对你选择的语言构建和安装 gRPC 插件和相关工具，可以参照[快速开始](#quickstart)。 Java gRPC 除了 JDK 外不需要其他工具。
#### 获得源码






 - Java
 
Java 例子代码在 GitHub 源码库里。你可以运行如下命令克隆源码到本地：

`git clone https://github.com/grpc/grpc-java.git`

切换当前目录到`grpc-java/examples`

`cd grpc-java/examples`

 - C++

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
`

切换当前目录到 `examples/cpp/helloworld`

`
$ cd examples/cpp/helloworld/
`
  
 
 - Python 

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
`

切换当前目录到 `examples/python/helloworld`

`
$ cd examples/python/helloworld/
`

 - Go
 
获取例子:
`
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter&lowbar;client
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter&lowbar;server
`
切换当前目录到 `examples/helloworld`
  
 - Ruby 

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
`

切换当前目录到 `examples/ruby` 。Then use bundler to install the example package's dependencies:

`
$ gem install bundler # if you don't already have bundler available
$ bundle install
`


 - Node.js

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
`

切换当前目录到 `examples/node` ， 接着安装包依赖：`
$ cd examples/node
$ npm install
`




 - C#

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
`

Open `Greeter.sln` from Visual Studio (or Monodevelop on Linux). See the <a href="/docs/installation/csharp.html">C# Quickstart</a> for platform-specific setup.
  
 - Objective-C
 
例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https://github.com/grpc/grpc.git
$ cd grpc
$ git submodule update --init
`

- PHP


切换当前目录到 `examples/objective-c/helloworld`
例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`$ git clone https://github.com/grpc/grpc.git`


切换当前目录到 `examples/php`
虽然我们大多数例子使用同一个 .proto 文件，但 PHP 的例子有自己的 `helloworld.proto` 文件，这是因为它依赖 proto2 语法。 PHP暂时没有 proto3 的支持。

### 定义服务
创建我们例子的第一步是定义一个*服务*：一个 RPC 服务通过参数和返回类型来指定可以远程调用的方法。就像你在 [概览](#protocolbuffers) 里所看到的， gRPC 通过 [protocol
buffers](https://developers.google.com/protocol-buffers/docs/overview) 来实现。
我们使用 protocol buffers 接口定义语言来定义服务方法，用 protocol buffer 来定义参数和返回类型。客户端和服务端均使用服务定义生成的接口代码。
这里有我们服务定义的例子，在 [helloworld.proto](https://github.com/grpc/grpc-java/tree/master/examples/src/main/proto) 里用 protocol buffers IDL 定义的。`Greeter`    服务 有一个方法 `SayHello` ，可以让服务端从远程客户端接收一个包含用户名的 `HelloRequest` 消息后，在一个 `HelloReply` 发送回一个招呼。这是你可以在 gRPC 里指定的最简单的 RPC - 你可以在教程里找到针对你选择的语言更多类型的例子。


```proto
syntax = "proto3";

option java_package = "io.grpc.examples";

package helloworld;

// The greeter service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}

```

<!--=================================-->

### 生成 gRPC 代码

一旦定义好服务，我们可以使用 protocol buffer 编译器 `protoc` 来生成创建应用所需的特殊客户端和服务端的代码 - 你可以生成任意 gRPC 支持的语言的代码，当然 PHP 和 Objective-C 仅支持创建客户端代码。生成的代码同时包括客户端的存根和服务端要实现的抽象接口，均包含 `Greeter` 所定义的方法。

(假如你没有在系统里安装 gRPC 插件和 protoc ，并且仅仅是要读一下这个例子，你可以跳过这一步，直接到下一步来查看生成的代码。)


- Java
这个例子的构建系统也是 Java gRPC 本身的构建的一部分 - 为了简单我们推荐使用我们事先生成的例子代码。你可以参考 [README](https://github.com/grpc/grpc-java/blob/master/README.md)来看一下如何从你自己的 .proto 文件生成代码。

这个例子事先生成的代码在 [src/generated/main ]（https://github.com/grpc/grpc-java/tree/master/examples/src/generated/main）下。
以下类包含所有我们需要创建这个例子所有的代码：

HelloRequest.java, HelloResponse.java和其他
  others which have all the protocol buffer code to populate, serialize, and
  retrieve our <code>HelloRequest</code> and <code>HelloReply</code> message types
<li><code>GreeterGrpc.java</code>, which contains (along with some other useful code):
    <ul><li>an interface for <code>Greeter</code> servers to implement

    `
public static interface Greeter {
    public void sayHello(Helloworld.HelloRequest request,
        StreamObserver&lt;Helloworld.HelloReply> responseObserver);
}
    `

    <li> <em>stub</em> classes that clients can use to talk to a <code>Greeter</code> server. As you can see, the async stub also implements the <code>Greeter</code> interface.

  `
public static class GreeterStub extends AbstractStub&lt;GreeterStub>
    implements Greeter {
  ...
}
  `
</ul>
</ul>
  
  <div id="cpp_generate">
To generate the client and server side interfaces, run:

`
$ make helloworld.grpc.pb.cc helloworld.pb.cc
`

Which internally invokes the protocol buffer compiler as:

`
$ protoc -I ../../protos/ --grpc_out=. --plugin=protoc-gen-grpc=grpc_cpp_plugin ../../protos/helloworld.proto
$ protoc -I ../../protos/ --cpp_out=. ../../protos/helloworld.proto
`

This generates:
<ul><li><code>helloworld.pb.h</code>, which declares classes for populating, serializing, and retrieving our <code>HelloRequest</code> and <code>HelloResponse</code> message types, and its implementation <code>helloworld.pb.cc</code>.
<li><code>helloworld.grpc.pb.h</code>, which declares our generated service classes, and its implementation <code>helloworld.grpc.pb.cc</code>.
</ul>
  

To generate the client and server side interfaces:

`
$ ./run_codegen.sh
`

Which internally invokes the protocol buffer compiler as:

`$ protoc -I ../../protos --python_out=. --grpc_out=. --plugin=protoc-gen-grpc=&#96;which grpc&lowbar;python&lowbar;plugin&#96; ../../protos/helloworld.proto`

This generates <code>helloworld_pb2.py</code>, which contains our generated client and server classes, as well as classes for populating, serializing, and retrieving our <code>HelloRequest</code> and <code>HelloResponse</code> message types.


To generate the client and server side interfaces, run the protocol buffer compiler:

```
protoc -I ../protos ../protos/helloworld.proto --go_out=plugins=grpc:helloworld
```

This generates `helloworld.pb.go`, which contains our generated client and server code, as well as code for populating, serializing, and retrieving our `HelloRequest` and `HelloResponse` message types.

  
  <div id="ruby_generate">
To generate the client and server side interfaces, run the protocol buffer compiler:

```
protoc -I ../protos --ruby_out=lib --grpc_out=lib --plugin=protoc-gen-grpc=`which grpc_ruby_plugin` ../protos/helloworld.proto
```

This generates the following files in the <code>lib</code> directory:

- `lib/helloworld.rb` defines a module `Helloworld`, which provides all the protocol buffer code to populate, serialize, and retrieve our request and response message types.
- `lib/helloworld_services.rb` extends the `Helloworld` module with our generated client and server classes.

  
  <div id="node_generate">
The Node.js library dynamically generates service descriptors and client stub definitions from .proto files loaded at runtime, so there's no need to generate any special code when using this language. Instead, in our example server and client we `require` the gRPC library, then use its `load()` method:

```
var grpc = require('grpc');
var hello_proto = grpc.load(PROTO_PATH).helloworld;
```
  
  <div id="csharp_generate">

- To generate the code on Windows, we use `protoc.exe` from the `Google.Protobuf` NuGet package and `grpc_csharp_plugin.exe` from the `Grpc.Tools` NuGet package (both under the `tools` directory).
Normally you would need to add the `Grpc.Tools` package to the solution yourself, but in this tutorial it has been already done for you. The following command should be run from the `examples/csharp/helloworld` directory: 

  ```
  > packages\Google.Protobuf.3.0.0-alpha4\tools\protoc.exe -I../../protos --csharp_out Greeter --grpc_out Greeter --plugin=protoc-gen-grpc=packages\Grpc.Tools.0.7.0\tools\grpc_csharp_plugin.exe ../../protos/helloworld.proto
  ```

- On Linux or OS X, we rely on `protoc` and `grpc_csharp_plugin` being installed by Linuxbrew/Homebrew. Run this command from the route_guide directory:

  ```
  $ protoc -I../../protos --csharp_out Greeter --grpc_out Greeter --plugin=protoc-gen-grpc=`which grpc_csharp_plugin` ../../protos/helloworld.proto
  ```

Running the appropriate command for your OS regenerates the following files in the Greeter directory:

- `Greeter/Helloworld.cs` defines a namespace `Helloworld`
  - This contains all the protocol buffer code to populate, serialize, and retrieve our request and response message types
- `Greeter/HelloworldGrpc.cs`, provides stub and service classes, including:
   - an interface `Greeter.IGreeter` to inherit from when defining RouteGuide service implementations
   - a class `Greeter.GreeterClient` that can be used to access remote RouteGuide instances


  
<div id="objective-c_generate">
For simplicity, we've provided a [Podspec file](https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/objective-c/helloworld/HelloWorld.podspec) that runs protoc for you with the appropriate plugin, input, and output, and describes how to compile the generated files. You just need to run in `examples/objective-c/route_guide`:

```
$ pod install
```

You can then open the XCode workspace created by Cocoapods to see the generated code. Running the command generates:

- `Helloworld.pbobjc.h`, the header which declares your generated message classes.
- `Helloworld.pbobjc.m`, which contains the implementation of your message classes.
- `Helloworld.pbrpc.h`, the header which declares your generated service classes.
- `Helloworld.pbrpc.m`, which contains the implementation of your service classes.



  <div id="php_generate">
gRPC PHP uses the [protoc-gen-php](https://github.com/datto/protobuf-php) tool to generate code from .proto files. You can find out how to install this in the [PHP Quickstart](https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/src/php). To generate the code for our Greeter service, run:

```
protoc-gen-php -i . -o . ./helloworld.proto
```

This generates `helloworld.php`, which contains:

- All the protocol buffer code to populate, serialize, and retrieve our request and response message types.
- A class called `GreeterClient` that lets clients call the methods defined in the `Greeter` service.


  



<!--=================================-->
<a name="server"></a>
### 写一个服务端

Now let's write some code! First we'll create a server application to implement
our service (which, you'll remember, we can do in all gRPC languages except Objective-C and PHP). We're not going to go into a lot of detail about how
to create a server in this section - more detailed information will be in the
tutorial for your chosen language.

#### 服务实现

<div class="tabs">
  <ul>
    <li><a href="#java_service">Java</a></li>
    <li><a href="#cpp_service">C++</a></li>
    <li><a href="#python_service">Python</a></li>
    <li><a href="#go_service">Go</a></li>
    <li><a href="#ruby_service">Ruby</a></li>
    <li><a href="#node_service">Node.js</a></li>
    <li><a href="#csharp_service">C#</a></li>
  </ul>
  <div id="java_service">
<a href="https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldServer.java#L51">GreeterImpl.java</a>
actually implements our <code>Greeter</code> service's required behaviour.

As you can see, the class <code>GreeterImpl</code> implements the interface
<code>GreeterGrpc.Greeter</code> that we <a href="#generating">generated</a> from our proto
<a href="https://github.com/grpc/grpc-java/tree/master/examples/src/main/proto">IDL</a> by implementing the method <code>sayHello</code>:
`
@Override
public void sayHello(HelloRequest req, StreamObserver&lt;HelloReply&gt; responseObserver) {
  HelloReply reply = HelloReply.newBuilder().setMessage("Hello " + req.getName()).build();
  responseObserver.onNext(reply);
  responseObserver.onCompleted();
}
`
<ul>
<li><code>sayHello</code> takes two parameters:

<ul>
<li><code>HelloRequest</code>: the request</li>
<li><code>StreamObserver&lt;HelloReply&gt;</code>: a response observer, which is
a special interface for the server to call with its response</li>
</ul></li>
</ul>

To return our response to the client and complete the call:

<ol>
<li>We construct and populate a <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition.</li>
<li>We return the <code>HelloReply</code> to the client and then specify that we've finished dealing with the RPC.</li>
</ol>
  
  <div id="cpp_service">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/cpp/helloworld/greeter_server.cc">greeter_server.cc</a>
implements our <code>Greeter</code> service's required behaviour.

As you can see, the class <code>GreeterServiceImpl</code> implements the interface
<code>Greeter::Service</code> that we <a href="#generating">generated</a> from our proto
service definition by implementing the method <code>sayHello</code>:
`
class GreeterServiceImpl final : public Greeter::Service {
  Status SayHello(ServerContext* context, const HelloRequest* request,
                  HelloReply* reply) override {
    std::string prefix("Hello ");
    reply->set_message(prefix + request->name());
    return Status::OK;
  }
};
`

In this case we're implementing the synchronous version of <code>Greeter</code>, which provides our default gRPC server behaviour (there's also an asynchronous interface, <code>Greeter::AsyncService</code>).
<ul>
<li><code>sayHello</code> takes three parameters:

<ul>
<li><code>ServerContext</code>: a context object for this RPC.
<li><code>HelloRequest</code>: the request</li>
<li><code>HelloReply</code>: the response</li>
</ul></li>
</ul>

To return our response to the client and complete the call:

<ol>
<li>We populate the provided <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition.</li>
<li>We return <code>Status::OK</code> to specify that we've finished dealing with the RPC.</li>
</ol>
  
  <div id="python_service">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/python/helloworld/greeter_server.py">greeter_server.py</a> implements our <code>Greeter</code> service's required behaviour.

As you can see, the class <code>Greeter</code> implements the interface
<code>helloworld_pb2.BetaGreeterServicer</code> that we <a href="#generating">generated</a> from our proto
service definition by implementing the method <code>SayHello</code>:
`class Greeter(helloworld_pb2.BetaGreeterServicer):

  def SayHello(self, request, context):
    return helloworld_pb2.HelloReply(message='Hello, %s!' % request.name)
`
To return our response to the client and complete the call:
<ul>
<li>We construct and populate a <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition.</li>
<li>We return the <code>HelloReply</code> to the client.</li>
</ul>

  <div id="go_service">
<a href="https://github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_server/main.go">greeter_server/main.go</a> implements our <code>Greeter</code> service's required behaviour.
As you can see, our server has a <code>server</code> struct type. This implements the <code>GreeterServer</code> interface that we <a href="#generating">generated</a> from our proto
service definition by implementing the method <code>SayHello</code>:
`// server is used to implement helloworld.GreeterServer.
type server struct{}

// SayHello implements helloworld.GreeterServer
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
	return &pb.HelloReply{Message: "Hello " + in.Name}, nil
}
`
To return our response to the client and complete the call:
<ul>
<li>We construct and populate a <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition.</li>
<li>We return the <code>HelloReply</code> to the client.</li>
</ul>

  
  <div id="ruby_service">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/ruby/greeter_server.rb">greeter&lowbar;server.rb</a> implements our <code>Greeter</code> service's required behaviour.
Our server has a <code>GreeterServer</code> class, which implements the <code>GreeterServer</code> interface that we <a href="#generating">generated</a> from our proto
service definition by implementing the method <code>SayHello</code>:
`class GreeterServer < Helloworld::Greeter::Service
  # say_hello implements the SayHello rpc method.
  def say_hello(hello_req, _unused_call)
    Helloworld::HelloReply.new(message: "Hello #{hello_req.name}")
  end
`
To return our response to the client and complete the call, we construct and populate a <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition, then return.

  
  <div id="node_service">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/node/greeter_server.js">greeter&lowbar;server.js</a> implements our <code>Greeter</code> service's required behaviour.
Our server implements the <code>Greeter</code>service from our
service definition by implementing the method <code>SayHello</code>:
`
function sayHello(call, callback) {
  callback(null, {message: 'Hello ' + call.request.name});
}
`
To return our response to the client and complete the call, we populate our response and pass it to the provided callback, with a null first parameter to indicate that there is no error.

  
  <div id="csharp_service">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/csharp/helloworld/GreeterServer/Program.cs">GreeterServer/Program.cs</a> implements our <code>Greeter</code> service's required behaviour.
Our server has a <code>GreeterImpl</code> class, which implements the <code>IGreeter</code> interface that we <a href="#generating">generated</a> from our proto
service definition by implementing the method <code>SayHello</code>:
`
class GreeterImpl : Greeter.IGreeter
{
    public Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloReply { Message = "Hello " + request.Name });
    }
}
`

To return our response to the client and complete the call:
<ul>
<li>We construct and populate a <code>HelloReply</code> response object with our exciting
message, as specified in our interface definition.</li>
<li>We return the <code>HelloReply</code> to the client.</li>
</ul>





#### 服务端实现

The other main feature required to provide a gRPC service is making the service
implementation available from the network.

<div class="tabs">
  <ul>
    <li><a href="#java_server">Java</a></li>
    <li><a href="#cpp_server">C++</a></li>
    <li><a href="#python_server">Python</a></li>
    <li><a href="#go_server">Go</a></li>
    <li><a href="#ruby_server">Ruby</a></li>
    <li><a href="#node_server">Node.js</a></li>
    <li><a href="#csharp_server">C#</a></li>
  </ul>
  <div id="java_server">
<a href="https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldServer.java">HelloWorldServer.java</a>
provides this for our Java example.
`/* The port on which the server should run */
private int port = 50051;
private Server server;

private void start() throws Exception {
  server = ServerBuilder.forPort(port)
      .addService(GreeterGrpc.bindService(new GreeterImpl()))
      .build()
      .start();
  logger.info("Server started, listening on " + port);
  Runtime.getRuntime().addShutdownHook(new Thread() {
    @Override
    public void run() {
      // Use stderr here since the logger may has been reset by its JVM shutdown hook.
      System.err.println("*** shutting down gRPC server since JVM is shutting down");
      HelloWorldServer.this.stop();
      System.err.println("*** server shut down");
    }
  });
}`

  
  <div id="cpp_server">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/cpp/helloworld/greeter_server.cc">greeter_server.cc</a>
also provides this for our C++ example.
`void RunServer() {
  std::string server_address("0.0.0.0:50051");
  GreeterServiceImpl service;

  ServerBuilder builder;
  builder.AddListeningPort(server_address, grpc::InsecureServerCredentials());
  builder.RegisterService(&service);
  std::unique_ptr<Server> server(builder.BuildAndStart());
  std::cout << "Server listening on " << server_address << std::endl;
  server->Wait();
}

`
  
  <div id="python_server">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/python/helloworld/greeter_server.py">greeter_server.py</a>
also provides this for our Python example.
`  server = helloworld_pb2.beta_create_Greeter_server(Greeter())
  server.add_insecure_port('[::]:50051')
  server.start()
  try:
    while True:
      time.sleep(_ONE_DAY_IN_SECONDS)
  except KeyboardInterrupt:
    server.stop()
`

  <div id="go_server">
<a href="https://github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_server/main.go">greeter_server/main.go</a> also provides this for our Go example.
`
const (
	port = ":50051"
)
...

func main() {
	lis, err := net.Listen("tcp", port)
	if err != nil {
		log.Fatalf("failed to listen: %v", err)
	}
	s := grpc.NewServer()
	pb.RegisterGreeterServer(s, &server{})
	s.Serve(lis)
}
`

  
  <div id="ruby_server">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/ruby/greeter_server.rb">greeter&lowbar;server.rb</a> also provides this for our Ruby example.
`
def main
  s = GRPC::RpcServer.new
  s.add&lowbar;http2&lowbar;port('0.0.0.0:50051')
  s.handle(GreeterServer)
  s.run
end
`

  
  <div id="node_server">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/node/greeter_server.js">greeter&lowbar;server.js</a> also provides this for our Node.js example.
`
function main() {
  var server = new Server({
    "helloworld.Greeter": {
      sayHello: sayHello
    }
  });
  server.bind('0.0.0.0:50051');
  server.listen();
}
`

  
  <div id="csharp_server">
<a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/csharp/helloworld/GreeterServer/Program.cs">GreeterServer/Program.cs</a> also provides this for our C# example.
`Server server = new Server
{
    Services = { Greeter.BindService(new GreeterImpl()) },
    Ports = { new ServerPort("localhost", 50051, ServerCredentials.Insecure) }
};
server.Start();
`
  


Here we create an appropriate gRPC server, binding the `Greeter` service
implementation that we created to a port. Then we start the server running: the server is now ready to receive
requests from `Greeter` service clients on our specified port. We'll cover
how all this works in a bit more detail in our language-specific documentation.

<!--=================================-->

### 写一个客户端

Client-side gRPC is pretty simple. In this step, we'll use the generated code
to write a simple client that can access the `Greeter` server we created
in the [previous section](#server).

Again, we're not going to go into much detail about how to implement a client;
we'll leave that for the tutorial.

#### Connecting to the service

First let's look at how we connect to the `Greeter` server. First we need
to create a gRPC channel, specifying the hostname and port of the server we
want to connect to. Then we use the channel to construct the stub instance.


<div class="tabs">
  <ul>
    <li><a href="#java_connect">Java</a></li>
    <li><a href="#cpp_connect">C++</a></li>
    <li><a href="#python_connect">Python</a></li>
    <li><a href="#go_connect">Go</a></li>
    <li><a href="#ruby_connect">Ruby</a></li>
    <li><a href="#node_connect">Node.js</a></li>
    <li><a href="#csharp_connect">C#</a></li>
    <li><a href="#objective-c_connect">Objective-C</a></li>
    <li><a href="#php_connect">PHP</a></li>
  </ul>
  <div id="java_connect">
`
private final ManagedChannel channel;
private final GreeterGrpc.GreeterBlockingStub blockingStub;

public HelloWorldClient(String host, int port) {
  channel = ManagedChannelBuilder.forAddress(host, port)
      .usePlaintext(true)
      .build();
  blockingStub = GreeterGrpc.newBlockingStub(channel);
}
`

In this case, we create a blocking stub. This means that the RPC call waits
for the server to respond, and will either return a response or raise an
exception. gRPC Java has other kinds of stubs that make non-blocking calls
to the server, where the response is returned asynchronously.
  
  <div id="cpp_connect">
`int main(int argc, char** argv) {
  GreeterClient greeter(
      grpc::CreateChannel("localhost:50051", grpc::InsecureCredentials(),
                          ChannelArguments()));
...
}

...

class GreeterClient {
 public:
  GreeterClient(std::shared_ptr<ChannelInterface> channel)
      : stub_(Greeter::NewStub(channel)) {}
...
 private:
  std::unique&lowbar;ptr<Greeter::Stub> stub_;
};

  
  <div id="python_connect">
The generated Python code has a helper function that creates a stub given a channel.
`channel = implementations.insecure_channel('localhost', 50051)
stub = helloworld_pb2.beta_create_Greeter_stub(channel)
...
`
  
  <div id="go_connect">
`const (
	address     = "localhost:50051"
	defaultName = "world"
)

func main() {
	// Set up a connection to the server.
	conn, err := grpc.Dial(address)
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()
	c := pb.NewGreeterClient(conn)
...
}`

In gRPC Go you use a special <code>Dial()</code> function to create the channel.

  
  <div id="ruby_connect">
`stub = Helloworld::Greeter::Stub.new('localhost:50051')`
In Ruby, we can do this in a single method call using the <code>Stub</code> class generated from our .proto.
  
  <div id="node_connect">
`var client = new hello_proto.Greeter('localhost:50051');`
In Node.js, we can do this in a single step by calling the <code>Greeter</code> stub constructor.

  
  <div id="csharp_connect">
`Channel channel = new Channel("127.0.0.1:50051", Credentials.Insecure);
var client = Greeter.NewClient(channel);
...
`
  
  <div id="objective-c_connect">

In Objective-C, we can do this in a single step using our generated `HLWGreeter` class's designated initializer, which expects a `NSString *` with the server address and port.

`
#import <GRPCClient/GRPCCall+Tests.h>
...
static NSString * const kHostAddress = @"localhost:50051";
...
[GRPCCall useInsecureConnectionsForHost:kHostAddress];
HLWGreeter *client = [[HLWGreeter alloc] initWithHost:kHostAddress];
`

Notice the call to `useInsecureConnectionsForHost:`, which tells the gRPC library to use cleartext (instead of TLS-encrypted connections) when communicating with the given host:port pair. 

  
  <div id="php_connect">

```
$client = new helloworld\GreeterClient(
      new Grpc\BaseStub('localhost:50051', []));
```
In PHP, we can do this in a single step using the `GreeterClient` class's constructor.

  



#### 调用 RPC

Now we can contact the service and obtain a greeting:

1. We construct and fill in a `HelloRequest` to send to the service.
2. We call the stub's `SayHello()` RPC with our request and get a populated `HelloReply` if the RPC is successful, from which we can get our greeting.

<div class="tabs">
  <ul>
    <li><a href="#java_call">Java</a></li>
    <li><a href="#cpp_call">C++</a></li>
    <li><a href="#python_call">Python</a></li>
    <li><a href="#go_call">Go</a></li>
    <li><a href="#ruby_call">Ruby</a></li>
    <li><a href="#node_call">Node.js</a></li>
    <li><a href="#csharp_call">C#</a></li>
    <li><a href="#objective-c_call">Objective-C</a></li>
    <li><a href="#php_call">PHP</a></li>
  </ul>
  <div id="java_call">
`
HelloRequest req = HelloRequest.newBuilder().setName(name).build();
HelloReply reply = blockingStub.sayHello(req);
`

You can see the complete client code in
<a href="https://github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldClient.java">HelloWorldClient.java</a>.

  <div id="cpp_call">
`  std::string SayHello(const std::string& user) {
    HelloRequest request;
    request.set&lowbar;name(user);
    HelloReply reply;
    ClientContext context;
    Status status = stub_->SayHello(&context, request, &reply);
    if (status.ok()) {
      return reply.message();
    } else {
      return "Rpc failed";
    }
  }`

You can see the complete client code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/cpp/helloworld/greeter_client.cc">greeter_client.cc</a>.
  
  <div id="python_call">
`response = stub.SayHello(helloworld_pb2.HelloRequest(name='you'), _TIMEOUT_SECONDS)
print "Greeter client received: " + response.message
`
You can see the complete client code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/python/helloworld/greeter_client.py">greeter_client.py</a>.


  <div id="go_call">
`r, err := c.SayHello(context.Background(), &pb.HelloRequest{Name: name})
if err != nil {
		log.Fatalf("could not greet: %v", err)
}
log.Printf("Greeting: %s", r.Message)`
You can see the complete client code in <a href="https://github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_client/main.go">greeter_client/main.go</a>.

  
  <div id="ruby_call">
`
  message = stub.say&lowbar;hello(Helloworld::HelloRequest.new(name: user)).message
  p "Greeting: #{message}"
`
You can see the complete client code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/ruby/greeter_client.rb">greeter_client.rb</a>.

  
  <div id="node_call">
`  client.sayHello({name: user}, function(err, response) {
    console.log('Greeting:', response.message);
  });`
You can see the complete client code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/node/greeter_client.js">greeter_client.js</a>.

  
  <div id="csharp_call">
`var reply = client.SayHello(new HelloRequest { Name = user });
Console.WriteLine("Greeting: " + reply.Message);`
You can see the complete example code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/csharp/helloworld/GreeterClient/Program.cs">GreeterClient/Program.cs</a>.

  
  <div id="objective-c_call">
`
HLWHelloRequest *request = [HLWHelloRequest message];
request.name = @"Objective-C";
[client sayHelloWithRequest:request handler:^(HLWHelloReply *response, NSError *error) {
  NSLog(@"%@", response.message);
}];`
You can see the complete example code in <a href="https://github.com/grpc/grpc/tree/{{ site.data.config.grpc_release_branch }}/examples/objective-c/helloworld">examples/objective-c/helloworld</a>.

  
  <div id="php_call">
`  $request = new helloworld\HelloRequest();
  $request->setName($name);
  list($reply, $status) = $client->SayHello($request)->wait();
  $message = $reply->getMessage();`

You can see the complete client code in <a href="https://github.com/grpc/grpc/blob/{{ site.data.config.grpc_release_branch }}/examples/php/greeter_client.php">greeter_client.php</a>.

  


<!--=================================-->
<a name="run"></a>
### 试一下!

You can try building and running our example using the same language on both the client and server side. Or you can try out one of gRPC's most useful features - interoperability
between code in different languages - and run a server and client in different languages. Each service
and client uses interface code generated from the same proto, which means that any `Greeter` client can talk to any `Greeter` server.

First run the server:

<div class="tabs">
  <ul>
    <li><a href="#java_runserver">Java</a></li>
    <li><a href="#cpp_runserver">C++</a></li>
    <li><a href="#python_runserver">Python</a></li>
    <li><a href="#go_runserver">Go</a></li>
    <li><a href="#ruby_runserver">Ruby</a></li>
    <li><a href="#node_runserver">Node.js</a></li>
    <li><a href="#csharp_runserver">C#</a></li>
  </ul>
  <div id="java_runserver">
You can build and run the server from the <code>examples</code> folder. First
build the client and server.

`
$ ../gradlew -PskipCodegen=true installDist
`

Then run the server, which will listen on port 50051:
`
$ ./build/install/grpc-examples/bin/hello-world-server
`


You can build and run the server from the <code>examples/cpp/helloworld</code> folder. First build the client and server.
`$ make`
Then run the server, which will listen on port 50051:
`$ ./greeter_server`
  
  <div id="python_runserver">
You can run the server from <code>examples/python/helloworld</code> using:
`$ ./run_server.sh`


  <div id="go_runserver">
You can run the server from <code>examples/helloworld</code> using:
`$ greeter_server &`

  
  <div id="ruby_runserver">
You can run the server from <code>examples/ruby</code> using:
`$ bundle exec ./greeter_server.rb &`

  
  <div id="node_runserver">
You can run the server from <code>examples/node</code> using:
`$ node ./greeter_server.js &`

  
  <div id="csharp_runserver">
Build the solution. Then from <code>examples/csharp</code>:

```
> cd GreeterServer/bin/Debug
> GreeterServer.exe
```

  


Once the server is running, in another terminal window run the client and confirm that it receives a message.

<div class="tabs">
  <ul>
    <li><a href="#java_runclient">Java</a></li>
    <li><a href="#cpp_runclient">C++</a></li>
    <li><a href="#python_runclient">Python</a></li>
    <li><a href="#go_runclient">Go</a></li>
    <li><a href="#ruby_runclient">Ruby</a></li>
    <li><a href="#node_runclient">Node.js</a></li>
    <li><a href="#csharp_runclient">C#</a></li>
    <li><a href="#objective-c_runclient">Objective-C</a></li>
    <li><a href="#php_runclient">PHP</a></li>
  </ul>
  <div id="java_runclient">
You can build and run the client from the <code>examples</code> folder. If
you haven't already built the client, build it using:

`
$ ../gradlew -PskipCodegen=true installDist
`

Then run the client:
`
$ ./build/install/grpc-examples/bin/hello-world-client
`
  
  <div id="cpp_runclient">
You can build and run the client from the <code>examples/cpp/helloworld</code> folder. If you haven't already built the client, build it using:
`$ make`
Then run the client:
`$ ./greeter_client`
  
  <div id="python_runclient">
You can run the client from <code>examples/python/helloworld</code> using:
`$ ./run_client.sh`


  <div id="go_runclient">
You can run the client from <code>examples/helloworld</code> using:
`$ greeter_client`

  
  <div id="ruby_runclient">
You can run the client from <code>examples/ruby</code> using:
`$ bundle exec ./greeter_client.rb`

  
  <div id="node_runclient">
You can run the client from <code>examples/node</code> using:
`$ node ./greeter_client.js`


  <div id="csharp_runclient">
Build the solution. Then from <code>examples/csharp</code>:

```
> cd GreeterClient/bin/Debug
> GreeterClient.exe
```


打开由Cocoapods 创建的 XCode 工作空间，运行应用。你可以在 XCode 的控制台日志里看到结果。



你可以从 <code>examples/php</code> 允许客户端 :

```
$ ./run_greeter_client.sh
```




<!--=========================================================================-->
## 更多资料!

- 找到如何安装 gRPC 并从每个语言开始 [快速开始](#quickstart).
- 按照你喜欢的语言对应教程来学习。
- 发现更多[ gRPC 概念](/docs/guides/concepts.html)，包括 RPC 生命周期，同步、异步调用，过期时间等等。
- 读一下 [HTTP2协议上的 gRPC](/docs/guides/wire.html) 里的详细描述 
-  [gRPC 认证支持](/docs/guides/auth.html)里介绍了 gRPC 对认证支持的机制和例子。


  


[1]: http://www.grpc.io/img/grpc_concept_diagram_00.png
