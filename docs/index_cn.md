#开始
欢迎进入 gRPC 的开发文档，gRPC 一开始由 google 开发，是一款语言中立、平台中立、开源的远程过程调用(RPC)系统。
本文档通过快速概述和一个简单的 Hello World 例子来向您介绍 gRPC 。你可以在本站发现更详细的教程和参考文档-文档将会越来越丰富。
##快速开始
为了直观地着手运行 gRPC，可以从你所选择的语言对应的快速开始入手，里面包含创建这个列子的安装指导、快速上手指南等更多内容。

 - [C++](https：//github.com/grpc/grpc/tree/master/examples/cpp)
 - [Java](https：//github.com/grpc/grpc-java/tree/master/examples)
 - [Go](https：//github.com/grpc/grpc-go/tree/master/examples)
 - [Python](https：//github.com/grpc/grpc/tree/master/examples/python)
 - [Ruby](https：//github.com/grpc/grpc/tree/master/examples/ruby)
 - [Node.js](https：//github.com/grpc/grpc/tree/master/examples/node)
 - [Android Java](https：//github.com/grpc/grpc-java/tree/master/examples/android)
 - [C#](https：//github.com/grpc/grpc/tree/master/examples/csharp/helloworld)
 - [Objective-C](https：//github.com/grpc/grpc/tree/master/examples/objective-c/helloworld)
 - [PHP](https：//github.com/grpc/grpc/tree/master/examples/php)

你可以从[这里](https：//github.com/grpc/grpc)找到 gRPC 的源码库。我们大多数例子都在源码库 examples 目录下。

## gRPC 是什么？
在 gRPC 里*客户端*应用可以像调用本地对象一样直接调用另一台不同的机器上*服务端*应用的方法，使得您能够更容易地创建分布式应用和服务。与许多 RPC 系统类似，gRPC 也是基于以下理念：定义一个*服务*，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个 gRPC 服务器来处理客户端调用。在客户端拥有一个*存根*能够像服务端一样的方法。

![图1](http://www.grpc.io/img/grpc_concept_diagram_00.png)

gRPC 客户端和服务端可以在多种环境中运行和交互 - 从 google 内部的服务器到你自己的笔记本，并且可以用任何 gRPC [支持的语言](#quickstart)来编写。所以，你可以很容易地用 Java 创建一个 gRPC 服务端，用 Go、Python、Ruby 来创建客户端。此外，Google 最新 API 将有 gRPC 版本的接口，使你很容易地将 Google 的功能集成到你的应用里。

### 使用 protocol buffers

gRPC 默认使用 *protocol buffers*，这是 Google 开源的一套成熟的结构数据序列化机制（当然也可以使用其他数据格式如 JSON）。正如你将在下方例子里所看到的，你用 *proto files* 创建 gRPC 服务，用 protocol buffers 消息类型来定义方法参数和返回类型。你可以在 [Protocol Buffers 文档](https：//developers.google.com/protocol-buffers/docs/overview)找到更多关于 Protocol Buffers 的资料。

#### Protocol buffers 版本
尽管 protocol buffers 对于开源用户来说已经存在了一段时间，例子内使用的却一种名叫 proto3 的新风格的 protocol buffers，它拥有轻量简化的语法、一些有用的新功能，并且支持更多新语言。当前针对 Java 和 C++ 发布了 beta 版本，针对 JavaNano（即 Android Java）发布 alpha 版本，在[protocol buffers Github 源码库里](https：//github.com/google/protobuf/releases)有 Ruby 支持， 在[golang/protobuf Github 源码库](https：//github.com/golang/protobuf)里还有针对 Go 语言的生成器， 对更多语言的支持正在开发中。 你可以在 [proto3 语言指南](https：//developers.google.com/protocol-buffers/docs/proto3)里找到更多内容， 在与当前默认版本的[发布说明](https：//github.com/google/protobuf/releases)比较，看到两者的主要不同点。更多关于 proto3 的文档很快就会出现。虽然你*可以*使用 proto2 (当前默认的 protocol buffers 版本)， 我们通常建议你在 gRPC 里使用 proto3，因为这样你可以使用 gRPC 支持全部范围的的语言，并且能避免 proto2 客户端与 proto3 服务端交互时出现的兼容性问题，反之亦然。
## 你好 gRPC!
现在你已经对 gRPC 有所了解，了解其工作机制最简单的方法是看一个简单的例子。 Hello World 将带领你创建一个简单的客户端-服务端应用，向你展示：

 - 通过一个 protocol buffers 模式，定义一个简单的带有 Hello World 方法的 RPC 服务。
 - 用你最喜欢的语言(如果可用的话)来创建一个实现了这个接口的服务端。
 - 用你最喜欢的(或者其他你愿意的)语言来访问你的服务端。
 
这个例子完整的代码在我们 GitHub 源码库的 `examples` 目录下。
我们可以使用 Git 版本系统来进行源码管理，但是除了如何安装和运行一些 Git 命令外，你没必要知道其他关于 Git 的任何事情。
需要注意的是，并不是所有 gRPC 支持的语言都可以编写我们例子的服务端代码，比如 PHP 和 Objective-C 仅支持创建客户端。
比起针对于特定语言的复杂教程，这更像是一个介绍性的例子。你可以在本站找到更有深度的教程，gRPC 支持的语言的参考文档很快就会全部开放。
### 准备
本节解释了如何在你本机上准备好例子代码的运行环境。如果你只是想读一下例子，你可以直接到下一步。

#### 安装 Git

你可以从[http://git-scm.com/download](http://git-scm.com/download)下载和安装 Git。安装好 Git 后，你应该能访问 git 命令行工具。你需要的主要命令如下：

- git clone ... ： 从远程代码库克隆一份到本机。
- git checkout ... ： 检出一个特殊分支或一个标签版本的代码来改进。

#### 安装 gRPC
针对你选择的语言构建和安装 gRPC 插件和相关工具，可以参照[快速开始](#quickstart)。 Java gRPC 除了 JDK 外不需要其他工具。
#### 获得源码

 - Java
 
Java 例子代码在 GitHub 源码库里。你可以运行如下命令克隆源码到本地：

`git clone https：//github.com/grpc/grpc-java.git`

切换当前目录到`grpc-java/examples`

`cd grpc-java/examples`

 - C++

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https：//github.com/grpc/grpc.git
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
 
获取例子：

```
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter_client
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter_server
```
切换当前目录到 `examples/helloworld`
  
 - Ruby 

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https：//github.com/grpc/grpc.git
`

切换当前目录到 `examples/ruby` ，然后使用 bundler 安装例子的包依赖：

```
$ gem install bundler # if you don't already have bundler available
$ bundle install
```


 - Node.js

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https：//github.com/grpc/grpc.git
`

切换当前目录到 `examples/node` ， 接着安装包依赖：

```
$ cd examples/node
$ npm install
```




 - C#

例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`
$ git clone https：//github.com/grpc/grpc。git
`

从 Visual Studio (或 Linux 上的 Monodevelop ) 打开 `Greeter.sln`。可以从 [C# Quickstart](/docs/installation/csharp.html) 找到平台特定的设置步骤。
  
 - Objective-C
 
例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

```
$ git clone https：//github.com/grpc/grpc.git
$ cd grpc
$ git submodule update --init
```
切换当前目录到 `examples/objective-c/helloworld` 。

- PHP
例子代码在 GitHub 源码库的 `examples` 目录。你可以运行如下命令克隆源码到本地：

`$ git clone https：//github.com/grpc/grpc.git`

切换当前目录到 `examples/php` 。
虽然我们大多数例子使用同一个 .proto 文件，但 PHP 的例子有自己的 `helloworld.proto` 文件，这是因为它依赖 proto2 语法。 PHP暂时没有针对 proto3 的支持。

### 定义服务
创建我们例子的第一步是定义一个*服务*：一个 RPC 服务通过参数和返回类型来指定可以远程调用的方法。就像你在 [概览](#protocolbuffers) 里所看到的， gRPC 通过 [protocol
buffers](https：//developers.google.com/protocol-buffers/docs/overview) 来实现。
我们使用 protocol buffers 接口定义语言来定义服务方法，用 protocol buffer 来定义参数和返回类型。客户端和服务端均使用服务定义生成的接口代码。
这里有我们服务定义的例子，在 [helloworld.proto](https：//github.com/grpc/grpc-java/tree/master/examples/src/main/proto) 里用 protocol buffers IDL 定义的。`Greeter`    服务 有一个方法 `SayHello` ，可以让服务端从远程客户端接收一个包含用户名的 `HelloRequest` 消息后，在一个 `HelloReply` 里发送回一个 `Greeter `。这是你可以在 gRPC 里指定的最简单的 RPC - 你可以在教程里找到针对你选择的语言更多类型的例子。


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

### 生成 gRPC 代码

一旦定义好服务，我们可以使用 protocol buffer 编译器 `protoc` 来生成创建应用所需的特定客户端和服务端的代码 - 你可以生成任意 gRPC 支持的语言的代码，当然 PHP 和 Objective-C 仅支持创建客户端代码。生成的代码同时包括客户端的存根和服务端要实现的抽象接口，均包含 `Greeter` 所定义的方法。

(假如你没有在系统里安装 gRPC 插件和 protoc ，并且仅仅是要看一下这个例子，你可以跳过这一步，直接到下一步来查看生成的代码。)


- Java
这个例子的构建系统也是 Java gRPC 本身构建的一部分 - 为了简单起见，我们推荐使用我们事先生成的例子代码。你可以参考 [README](https：//github.com/grpc/grpc-java/blob/master/README.md) 来看一下如何从你自己的 .proto 文件生成代码。

	这个例子事先生成的代码在 [src/generated/main](https：//github.com/grpc/grpc-java/tree/master/examples/src/generated/main)下。
以下类包含所有我们需要创建这个例子所有的代码：

	- HelloRequest.java， HelloResponse.java和其他文件包含所有 protocol buffer 用来填充、序列化和提取 `HelloRequest` 和 `HelloReply` 消息类型的代码。
	- GreeterGrpc.java， 包含 (还有其他有用的代码)：
    
    	`Greeter` 服务端需要实现的接口
 

 		```
		public static interface Greeter {
    	public void sayHello(Helloworld.HelloRequest request,
        StreamObserver&lt;Helloworld.HelloReply> responseObserver);
		}
		```
		客户端用来与 `Greeter` 服务端进行对话的 `存根` 类。就像你所看到的，异步存根也实现了 `Greeter` 接口。

		```
	public static class GreeterStub extends AbstractStub&lt;GreeterStub>
    implements Greeter {
  	...
	}
	```
	
- C++

	生成客户端和服务端接口，运行：


	```
$ make helloworld.grpc.pb.cc helloworld.pb.cc
	```
这从内部调用 protocol buffer 编译器：


	```
$ protoc -I ../../protos/ --grpc_out=. --plugin=protoc-gen-grpc=grpc_cpp_plugin ../../protos/helloworld.proto
$ protoc -I ../../protos/ --cpp_out=. ../../protos/helloworld.proto
	```

	生成：

		`helloworld.pb.h` 声明了用于填充、序列化、提取 `HelloRequest` 和 `HelloResponse` 消息类型的类，并且还有它的实现 `helloworld.pb.cc` 。
	
		`helloworld.grpc.pb.h`，声明了我们生成的服务类和它的实现 `helloworld.grpc.pb.cc` 。
  
- Python

	可以用如下命令生成客户端和服务端：

	```
	$ ./run_codegen.sh
	```
	这内部调用 protocol buffer 编译器：


	```
$ protoc -I ../../protos --python_out=. --grpc_out=. --plugin=protoc-gen-grpc=`which grpc_python_plugin` ../../protos/helloworld.proto
	```
这生成了 `helloworld_pb2.py` ，包含我们生成的客户端和服务端类，此外还有用于填充、序列化、提取 `HelloRequest` 和 `HelloResponse` 消息类型的类。

- Go

	为了生成客户端和服务端接口，运行 protocol buffer 编译器：


	```
protoc -I ../protos ../protos/helloworld.proto --go_out=plugins=grpc：helloworld
	```

	这生成了 `helloworld.pb.go` ，包含了我们生成的客户端和服务端类，此外还有用于填充、序列化、提取 `HelloRequest` 和 `HelloResponse` 消息类型的类。

- Ruby

	为了生成客户端和服务端接口，运行 protocol buffer 编译器：

	```
protoc -I ../protos --ruby_out=lib --grpc_out=lib --plugin=protoc-gen-grpc=`which grpc_ruby_plugin` ../protos/helloworld.proto
	```

	在 `lib` 目录下生成了如下文件：

	- `lib/helloworld.rb` 定义了一个 `Helloworld` 模块， 这个模块提供了用于填充、序列化、提取请求、应答消息类型的所有 protocol buffer 代码。

	- `lib/helloworld_services.rb` 用生成的客户端和服务端代码继承了 `Helloworld` 模块。

  
- Node.js

	Node.js库从运行时加载的 .proto 文件动态生成服务描述和客户端存根的定义，所以使用此语言时没必要生成任何特殊代码。而是在例子客户端和服务端里，我们 `require` gRPC 库，然后用它的 `load()` 方法：

	```
var grpc = require('grpc');
var hello_proto = grpc.load(PROTO_PATH).helloworld;
	```
  
- C#

	- 为了生成 Windows 上的代码，我们使用来自 `Google.Protobuf`  NuGet 包里的  `protoc.exe` 和来自 `Grpc.Tools`  NuGet 包里的 `Grpc.Tools` ，这两个文件都在 `tools` 目录下。
	一般你需要自己把 `Grpc.Tools` 包添加到解决方案，但在这个教程里，这一步已经为你做好了。应该在 `examples/csharp/helloworld` 下执行以下命令：

		```
  > packages\Google.Protobuf.3.0.0-alpha4\tools\protoc.exe -I../../protos --csharp_out Greeter --grpc_out Greeter --plugin=protoc-gen-grpc=packages\Grpc.Tools.0.7.0\tools\grpc_csharp_plugin.exe ../../protos/helloworld.proto
		```
	- 在 Linux 或 OS X ，我们依赖通过 Linuxbrew 或者 Homebrew 安装的 `protoc` 和 `grpc_csharp_plugin` 。从 route_guide 运行这个命令：


		```
  $ protoc -I../../protos --csharp_out Greeter --grpc_out Greeter --plugin=protoc-gen-grpc=`which grpc_csharp_plugin` ../../protos/helloworld.proto
		```
根据你的 OS 运行合适的命令，在 Greeter 目录重新生成如下文件:

		- `Greeter/Helloworld.cs` 定义了命名空间 `Helloworld` 
 它包含了所有用来填充、序列化、提取请求和应答消息类型的 protocol buffer 代码。 
		- `Greeter/HelloworldGrpc.cs`，提供了存根类和服务类，包括：
  		 - 一个 `Greeter.IGreeter` 接口，可以在定义 RootGuide 服务实现的时候来继承它。
   			- 一个 `Greeter.GreeterClient` 类，可用来访问远程的 RouteGuide 实例。
  
- Objective-c

	为了简单，我们提供了一个 [Podspec 文件](https：//github.com/grpc/grpc/blob/master/examples/objective-c/helloworld/HelloWorld.podspec)，用来使用适当的插件、输入、输出运行 protoc ，并描述如何编译生成的代码。你仅仅需要在 `examples/objective-c/route_guide` 下运行：

	```
	$ pod install
	```
然后你可以打开由 Cocoapods 创建的 XCode 工作空间，看一下生成的代码。运行命令生成：

	- `Helloworld.pbobjc.h`， 定义生成的消息类的头文件。
	- `Helloworld.pbobjc.m`，包含消息类的实现。
	- `Helloworld.pbrpc.h`，定义生成的服务类的头文件。
	- `Helloworld.pbrpc.m`，包含服务类的实现。

- PHP

	gRPC PHP 使用 [protoc-gen-php](https：//github.com/datto/protobuf-php) 工具来从 .proto 文件生成代码。你可以在 [PHP 快速开始](https：//github.com/grpc/grpc/blob/master/src/php)里找到如何安装它。为了生成 Greeter 服务的代码，运行：

	```
	protoc-gen-php -i . -o . ./helloworld.proto
	```

	生成 `helloworld.php` ，包含：

	- 所有用来填充、序列化、提取请求和应答消息类型的 protocol buffer 代码.
	- `GreeterClient` 类，可以让客户端调用在 `Greeter` 服务里的方法。 
  
### 写一个服务器

现在让我们写点代码！首先我们将创建一个服务应用来实现服务(你会记起来，我们可以是使用除了Objective-C and PHP 外的其他所有语言来实现)。在本节，我们不打算对如何创建一个服务端进行更深入地探讨 - 更详细的信息可以在你选择语言对应的教程里找到。

#### 服务实现
- Java

	[GreeterImpl.java](https：//github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldServer.java#L51) 准确地实现了 `Greeter` 服务所需要的行为。
	正如你所见，`GreeterImpl` 类通过实现 `sayHello` 方法，实现了从 [IDL](https：//github.com/grpc/grpc-java/tree/master/examples/src/main/proto) 生成的`GreeterGrpc.Greeter` 接口 。

	```
@Override
public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
  HelloReply reply = HelloReply.newBuilder().setMessage("Hello " + req.getName()).build();
  responseObserver.onNext(reply);
  responseObserver.onCompleted();
}
	```

	`sayHello` 有两个参数：

	- `HelloRequest`，请求。
	- `StreamObserver<HelloReply>`： 应答观察者，一个特殊的接口，服务器用应答来调用它。

	为了返回给客户端应答，需要完成以下调用：

	1. 用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象。
	2. 将 `HelloReply` 返回给客户端，然后表明我们已经完成了对 RPC 的处理。


- C++

	[greeter_server.cc](https：//github.com/grpc/grpc/blob/master/examples/cpp/helloworld/greeter_server.cc) 实现了 `Greeter` 服务所需要的行为。
	正如你所见，`GreeterServiceImpl` 类通过实现 `sayHello` 方法，实现了从 proto 服务定义生成的`Greeter：：Service` 接口 。

	```
class GreeterServiceImpl final ： public Greeter：：Service {
  Status SayHello(ServerContext* context, const HelloRequest* request,
                  HelloReply* reply) override {
    std：：string prefix("Hello ");
    reply->set_message(prefix + request->name());
    return Status：：OK;
  }
};
	```
在此我们实现同步版本的 `Greeter`，它提供了默认的 gRPC 服务行为（这里也有一个异步的接口，`Greeter：：AsyncService`）。

	sayHello 有三个参数：

	- `ServerContext`： RPC上下文对象。
	- `HelloRequest` ： 请求。
	- `HelloReply` ：应答。

	为了返回给客户端应答，需要完成以下调用：

	1. 用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象。
	2. 将 `Status::OK` 返回给客户端，表明我们已经完成了对 RPC 的处理。
  

- Python

	[greeter_server.py](https：//github.com/grpc/grpc/blob/master/examples/python/helloworld/greeter_server.py) 实现了 `Greeter` 服务所需要的行为。
	正如你所见，`Greeter` 类通过实现 `sayHello` 方法，实现了从 proto 服务定义生成的`helloworld_pb2.BetaGreeterServicer` 接口：

	```
 class Greeter(helloworld_pb2.BetaGreeterServicer)：

  def SayHello(self, request, context)：
    return helloworld_pb2.HelloReply(message='Hello, %s!' % request.name)
	```
	为了返回给客户端应答，需要完成以下调用：

	1.  用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象。
	2.  将 `HelloReply` 返回给客户端。

- Go

	[greeter_server/main.go](https：//github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_server/main.go) 实现了 `Greeter` 服务所需要的行为。
正如你所见，服务器有一个 `server` 结构。它通过实现 `sayHello` 方法，实现了从 proto 服务定义生成的`GreeterServer` 接口：

	```
// server is used to implement helloworld.GreeterServer.
type server struct{}
// SayHello implements helloworld.GreeterServer
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
	return &pb.HelloReply{Message： "Hello " + in.Name}, nil
}	
```
	为了返回给客户端应答，需要完成以下调用：

	1. 用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象。
	2.	将 `HelloReply` 返回给客户端。

- Ruby

	[greeter_server.rb](https：//github.com/grpc/grpc/blob/master/examples/ruby/greeter_server.rb) 实现了 `Greeter` 服务所需要的行为。
	服务器有一个 `GreeterServer ` 类，它通过实现 `sayHello` 方法，实现了从 proto 服务定义生成的`GreeterServer` 接口：

	```
class GreeterServer < Helloworld：：Greeter：：Service
  # say_hello implements the SayHello rpc method.
  def say_hello(hello_req, _unused_call)
    Helloworld：：HelloReply.new(message： "Hello #{hello_req.name}")
  end
	```
为了返回给客户端应答，需要完成以下调用：我们用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象，然后返回它。

- Node.js

	[greeter_server.js](https：//github.com/grpc/grpc/blob/master/examples/node/greeter_server.js) 实现了 `Greeter` 服务所需要的行为。
	服务器通过实现 `SayHello` 方法，实现了服务定义：

	```
function sayHello(call, callback) {
  callback(null, {message： 'Hello ' + call.request.name});
}
	```
为了返回给客户端应答并完成调用，我们填充了应答并将其传递给一个已提供了的回调，用 null 作为第一个参数来表示没有出现错误。

- C#

	[GreeterServer/Program.cs](https：//github.com/grpc/grpc/blob/master/examples/csharp/helloworld/GreeterServer/Program.cs) 实现了 `Greeter` 服务所需要的行为。
	服务器的 `GreeterImpl`类，通过实现 `sayHello` 方法，实现了生成的`IGreeter` 接口：

	```
class GreeterImpl ： Greeter.IGreeter
{
    public Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloReply { Message = "Hello " + request.Name });
    }
}
	```
为了返回给客户端应答并完成以下调用：

	1. 用激动人心的消息构建并填充一个在我们接口定义的 `HelloReply` 应答对象。
	2. 将 `HelloReply` 返回给客户端。

#### 服务端实现
需要提供一个 gRPC 服务的另一个主要功能是让这个服务实在在网络上可用。

- Java

	[HelloWorldServer.java](https：//github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldServer.java) 提供了 Java 的例子。

	```
/* The port on which the server should run */
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
}
	```

- C++

	[greeter_server.cc](https：//github.com/grpc/grpc/blob/master/examples/cpp/helloworld/greeter_server.cc)也提供了 C++ 的例子。

	```
void RunServer() {
  std：：string server_address("0.0.0.0：50051");
  GreeterServiceImpl service;
  ServerBuilder builder;
  builder.AddListeningPort(server_address, grpc：：InsecureServerCredentials());
  builder.RegisterService(&service);
  std：：unique_ptr<Server> server(builder.BuildAndStart());
  std：：cout << "Server listening on " << server_address << std：：endl;
  server->Wait();
}
	```
  
- Python
  
	[greeter_server.py](https：//github.com/grpc/grpc/blob/master/examples/python/helloworld/greeter_server.py)也提供了 Python 的例子。

	```
 server = helloworld_pb2.beta_create_Greeter_server(Greeter())
  server.add_insecure_port('[：：]：50051')
  server.start()
  try：
    while True：
      time.sleep(_ONE_DAY_IN_SECONDS)
  except KeyboardInterrupt：
    server.stop()
	```

- Go

	[greeter_server/main.go](https：//github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_server/main.go)也提供了 Go 的例子。

	```
const (
	port = "：50051"
)
...
func main() {
	lis, err ：= net.Listen("tcp", port)
	if err != nil {
		log.Fatalf("failed to listen： %v", err)
	}
	s ：= grpc.NewServer()
	pb.RegisterGreeterServer(s, &server{})
	s.Serve(lis)
}
	```

  
- Ruby

	[greeter_server.rb](https：//github.com/grpc/grpc/blob/master/examples/ruby/greeter_server.rb)也提供了 Ruby 的例子。

	```
def main
  s = GRPC：：RpcServer.new
  s.add_http2_port('0.0.0.0：50051')
  s.handle(GreeterServer)
  s.run
end
	```

  
- Node.js

	[greeter_server.js](https：//github.com/grpc/grpc/blob/maser/examples/node/greeter_server.js)也提供了 Ruby 的例子。

	```
function main() {
  var server = new Server({
    "helloworld.Greeter"： {
      sayHello： sayHello
    }
  });
  server.bind('0.0.0.0：50051');
  server.listen();
}
	```

  
- C#

	[GreeterServer/Program.cs](https：//github.com/grpc/grpc/blob/master/examples/csharp/helloworld/GreeterServer/Program.cs)也提供了 C# 的例子。

	```
Server server = new Server
{
    Services = { Greeter.BindService(new GreeterImpl()) },
    Ports = { new ServerPort("localhost", 50051, ServerCredentials.Insecure) }
};
server.Start();
	```
  
在这里我们创建了合理的 gRPC 服务器，将我们实现的 `Greeter` 服务绑定到一个端口。然后我们启动服务器：服务器现在已准备好从 `Greeter` 服务客户端接收请求。我们将在具体语言对应的文档里更深入地了解这所有的工作是怎样进行的。



### 写一个客户端

客户端的 gRPC 非常简单。在这一步，我们将用生成的代码写一个简单的客户程序来访问我们在上一节里创建的 `Greeter` 服务器。
同样，我们也不打算对如何实现一个客户端程序深入更多，我们把这些内容放到教程里。

#### 连接服务

首先我们看一下我们如何连接 `Greeter` 服务器。我们需要创建一个 gRPC 频道，指定我们要连接的主机名和服务器端口。然后我们用这个频道创建存根实例。

- Java

	```
private final ManagedChannel channel;
private final GreeterGrpc.GreeterBlockingStub blockingStub;
public HelloWorldClient(String host, int port) {
  channel = ManagedChannelBuilder.forAddress(host, port)
      .usePlaintext(true)
      .build();
  blockingStub = GreeterGrpc.newBlockingStub(channel);
}
	```

	在这个例子里，我们创建了一个阻塞的存根。这意味着 RPC 调用要等待服务器应答，将会返回一个应答或抛出一个异常。 gRPC Java 还可以有其他种类的存根，可以向服务器发出非阻塞的调用，这时应答是异步返回的。

- C++

	```
int main(int argc, char** argv) {
  GreeterClient greeter(
      grpc：：CreateChannel("localhost：50051", grpc：：InsecureCredentials(),
                          ChannelArguments()));
...
}
...
class GreeterClient {
 public：
  GreeterClient(std：：shared_ptr<ChannelInterface> channel)
      ： stub_(Greeter：：NewStub(channel)) {}
...
 private：
  std：：unique_ptr<Greeter：：Stub> stub_;
};
	```

  
- Python

	生成的 Python 代码有一个根据频道创建存根的帮助方法。
	```
channel = implementations.insecure_channel('localhost', 50051)
stub = helloworld_pb2.beta_create_Greeter_stub(channel)
...
	```
 
- Go

	```
const (
	address     = "localhost：50051"
	defaultName = "world"
)
func main() {
	// Set up a connection to the server.
	conn, err ：= grpc.Dial(address)
	if err != nil {
		log.Fatalf("did not connect： %v", err)
	}
	defer conn.Close()
	c ：= pb.NewGreeterClient(conn)
...
}
	```

	在 gRPC Go 你是使用一个特殊的 Dial() 方法来创建频道。

- Ruby

	`stub = Helloworld：：Greeter：：Stub.new('localhost：50051')`

	在 Ruby 里，我们可以在一个方法里调用从 .proto 文件里生成的存根类。

- Node.js
	`var client = new hello_proto.Greeter('localhost：50051');`
	
	在 Node.js ，我们可以在一步调用 `Greeter` 存根构造器。

- C#

	```
Channel channel = new Channel("127.0.0.1：50051", Credentials.Insecure);
var client = Greeter.NewClient(channel);
...
	```
  
- Objective-C
在 Objective-C 里，我们可以用生成的 `HLWGreeter` 类指定的初始化方法完成，这个方法需要用`NSString *` 类型表示的服务器和端口作为参数。

	```
#import <GRPCClient/GRPCCall+Tests.h>
...
static NSString * const kHostAddress = @"localhost：50051";
...
[GRPCCall useInsecureConnectionsForHost：kHostAddress];
HLWGreeter *client = [[HLWGreeter alloc] initWithHost：kHostAddress];
	```

	当用给定的 host：port 对通讯的时候，注意对 `useInsecureConnectionsForHost：` 的调用，要通知 gRPC 库使用明文 (而不是 TLS 加密的连接)。
  
- PHP

	```
$client = new helloworld\GreeterClient(
      new Grpc\BaseStub('localhost：50051', []));
	```

	在 PHP 里，我们可以使用 `GreeterClient` 类的构造器一步完成。
  



#### 调用 RPC
现在我们可以联系服务并获得一个 greeting ：

1. 我们创建并填充一个 `HelloRequest` 发送给服务。
2. 我们用请求调用存根的 `SayHello()`，如果 RPC 成功，会得到一个填充的 `HelloReply` ，从这里我们可以获得 greeting。
2. We call the stub's `SayHello()` RPC with our request and get a populated `HelloReply` if the RPC is successful, from which we can get our greeting.


- Java

	```
HelloRequest req = HelloRequest.newBuilder().setName(name).build();
HelloReply reply = blockingStub.sayHello(req);
	```

	你可以在 [HelloWorldClient.java](https：//github.com/grpc/grpc-java/blob/master/examples/src/main/java/io/grpc/examples/helloworld/HelloWorldClient.java) 里查看完整的客户端代码。

- C++

	```
 std：：string SayHello(const std：：string& user) {
    HelloRequest request;
    request.set_name(user);
    HelloReply reply;
    ClientContext context;
    Status status = stub_->SayHello(&context, request, &reply);
    if (status.ok()) {
      return reply.message();
    } else {
      return "Rpc failed";
    }
  }
	```

	你可以在 [greeter_client.cc](https：//github.com/grpc/grpc/blob/master/examples/cpp/helloworld/greeter_client.cc) 里查看完整的客户端代码。

  
 - Python
 
	```
response = stub.SayHello(helloworld_pb2.HelloRequest(name='you'), _TIMEOUT_SECONDS)
print "Greeter client received： " + response.message
	```
	你可以在 [greeter_client.py](https：//github.com/grpc/grpc/blob/master/examples/python/helloworld/greeter_client.py) 里查看完整的客户端代码。


- Go

	```
r, err ：= c.SayHello(context.Background(), &pb.HelloRequest{Name： name})
if err != nil {
		log.Fatalf("could not greet： %v", err)
}
log.Printf("Greeting： %s", r.Message)
	```

	你可以在 [greeter_client/main.go](https：//github.com/grpc/grpc-go/blob/master/examples/helloworld/greeter_client/main.go) 里查看完整的客户端代码。

- Ruby
	```
  message = stub.say_hello(Helloworld：：HelloRequest.new(name： user)).message
  p "Greeting： #{message}"
	```


	你可以在 [greeter_client.rb](https：//github.com/grpc/grpc/blob/master/examples/ruby/greeter_client.rb) 里查看完整的客户端代码。

- Node.js


	```
 client.sayHello({name： user}, function(err, response) {
    console.log('Greeting：', response.message);
  });
	```
 
 	你可以在 [/examples/node/greeter_client.js](https：//github.com/grpc/grpc/blob/master/examples/node/greeter_client.js) 里查看完整的客户端代码。 


- C#

	```
var reply = client.SayHello(new HelloRequest { Name = user });
Console.WriteLine("Greeting： " + reply.Message);
	```

 	你可以在 [GreeterClient/Program.cs](https：//github.com/grpc/grpc/blob/master/examples/csharp/helloworld/GreeterClient/Program.cs) 里查看完整的客户端代码。
  
- Objective-C

	```
HLWHelloRequest *request = [HLWHelloRequest message];
request.name = @"Objective-C";
[client sayHelloWithRequest：request handler：^(HLWHelloReply *response, NSError *error) {
  NSLog(@"%@", response.message);
}];
	```

	 你可以在 [examples/objective-c/helloworld](https：//github.com/grpc/grpc/tree/master/examples/objective-c/helloworld) 里查看完整的客户端代码。
 
- PHP

	``` 
  $request = new helloworld\HelloRequest();
  $request->setName($name);
  list($reply, $status) = $client->SayHello($request)->wait();
  $message = $reply->getMessage();
	```


 	你可以在[greeter_client.php](https：//github.com/grpc/grpc/blob/master/examples/php/greeter_client.php) 里查看完整的客户端代码。


### 试一下!
你可以尝试用同一个语言在客户端和服务端构建并运行例子。或者你可以尝试 gRPC 最有用的一个功能 - 不同的语言间的互操作性，即在不同的语言运行客户端和服务端。每个服务端和客户端使用从同一过 proto 文件生成的接口代码，则意味着任何 `Greeter` 客户端可以与任何 `Greeter` 服务端对话。

- Java
	首先运行服务端：

	你可以从 `examples` 目录构建并运行服务端。首先构建客户端和服务端：

	`
$ ../gradlew -PskipCodegen=true installDist
	`

然后运行服务端，服务端将监听 50051 ：
	`
$ ./build/install/grpc-examples/bin/hello-world-server
	`
	
- C++

	你可以从 `examples/cpp/helloworld` 目录下构建并运行服务端。首先构建客户端和服务端：

	`$ make`

	然后运行服务端，服务端将监听 50051 ：

	`$ ./greeter_server`
  
- Python
	你可以用如下命令到 `examples/python/helloworld` 下运行服务端：

	`$ ./run_server.sh`


- Go

	你可以用如下命令到 `examples/helloworld` 下运行服务端：

	`$ greeter_server &`

  
- Ruby

	你可以用如下命令到 `examples/ruby` 下运行服务端：

	`$ bundle exec ./greeter_server.rb &`

  
- Node.js
	你可以用如下命令到 `examples/node` 下运行服务端：

	`$ node ./greeter_server.js &`

- C#
	构建解决方案，然后到 `examples/csharp` ：


	```
> cd GreeterServer/bin/Debug
> GreeterServer.exe
	```

  
一旦服务器在运行，在其他的终端窗口运行客户端并确认它收到一个消息。



- Java

	你可以在 `examples` 目录下构建并运行客户端。假如你还没有构建客户端，可以使用如下命令：

	`
$ ../gradlew -PskipCodegen=true installDist
	`

	然后运行客户端：
	`
		$ ./build/install/grpc-examples/bin/hello-world-client
	`
- C++  
	你可以在 `examples/cpp/helloworld` 目录下构建并运行客户端。假如你还没有构建客户端，可以使用如下命令：

	`$ make`
	然后运行客户端：
	`$ ./greeter_client`
  
- Python

	你可以从` examples/python/helloworld `目录下用如下命令运行客户端：
	`$ ./run_client.sh`

- Go

	你可以从` examples/helloworld `目录下用如下命令运行客户端：

	`$ greeter_client`

  
- Ruby

	你可以从` examples/node `目录下用如下命令运行客户端：
	`$ bundle exec ./greeter_client.rb`

- Node.js  

	你可以从` examples/node `目录下用如下命令运行客户端：

	`$ node ./greeter_client.js`

- C#

	构建解决方案，然后从 `examples/csharp` 目录：

	```
> cd GreeterClient/bin/Debug
> GreeterClient.exe
	```
- Objective-C

	打开由Cocoapods 创建的 XCode 工作空间，运行应用，你可以在 XCode 的控制台日志里看到结果。

- PHP

	你可以从 `examples/php` 目录运行客户端 ：

	```
$ ./run_greeter_client.sh
	```

## 更多资料!

- 从[快速开始](#quickstart)找到如何安装 gRPC 并从每个语言开始 。
- 按照你喜欢的语言对应教程来学习。
- 从[ gRPC 概念](/docs/guides/concepts.html)发现更多包括 RPC 生命周期，同步、异步调用，过期时间等内容。
- 读一下 [HTTP2协议上的 gRPC](/docs/guides/wire.html) 里的详细描述 
-  [ gRPC 认证支持](/docs/guides/auth.html)里则介绍了 gRPC 对认证支持的机制和例子。


  



