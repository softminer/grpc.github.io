#开始
欢迎进入gRPC的开发文档，gRPC初始由google开发，是一款语言中立、平台中立、开源的远程过程调用(RPC)系统。
本文档通过快速概述和一个简单的Hello World例子来向您介绍gRPC。你可以在本站发现更详细的教程和参考文档-文档将会越来越丰富。
##快速开始
要想直观地着手运行gRPC，可以从你所选择的语言对应的快速开始入手，里面包含创建这个列子的安装指导、快速上手指南等更多内容。

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

你可以从[这里](https://github.com/grpc/grpc)找到gRPC的源码库。我们大多数例子都在源码库examples目录下。
## gRPC是什么？
在gRPC里*客户端*应用可以像调用本地对象一样直接调用另一台不同的机器上*服务端*应用的方法，使得您能够更容易地创建分布式应用和服务。与许多RPC系统类似，gRPC也是基于以下理念：定义一个*服务*，指定其能够被远程调用的方法（包含参数和返回类型）。在服务端实现这个接口，并运行一个gRPC服务器来处理客户端调用。在客户端拥有一个*存根*能够像服务端一样的方法。
![图1][1]
gRPC客户端和服务端可以在多种环境中运行和交互 - 从google内部的服务器到你自己的笔记本，并且可以用任何gRPC[支持的语言](#quickstart)来编写。所以，你可以很容易地用Java创建一个gRPC服务端，用Go、Python、Ruby来创建客户端。此外，Google最新API将有gRPC版本的接口，使你很容易地将Google的功能集成到你的应用里。

### 使用protocol buffers

gRPC默认使用*protocol buffers*，这是Google开源的一套成熟的结构数据序列化机制（当然也可以使用其他数据格式如JSON）。正如你将在下方我们的例子里所看到的，你用*proto files*创建gRPC服务，用protocol buffers 消息类型来定义方法参数和返回类型。你可以在 [Protocol Buffers文档](https://developers.google.com/protocol-buffers/docs/overview)找到更多关于Protocol Buffers的资料。

#### Protocol buffers 版本
尽管protocol buffers对于开源用户来说已经存在了一段时间，我们的例子内使用的却一种名叫proto3的新风格的protocol buffers，它拥有轻量简化的语法、一些有用的新功能，并且支持更多新语言。当前针对Java和C++发布了beta版本，针对JavaNano（即Android Java）发布alpha版本，在[protocol buffers Github源码库里](https://github.com/google/protobuf/releases)有Ruby支持， 在[golang/protobuf Github源码库](https://github.com/golang/protobuf)里还有针对Go语言的生成器, 对更多语言的支持正在开发中。 你可以在 [proto3 语言指南](https://developers.google.com/protocol-buffers/docs/proto3)里找到更多内容， 在与当前默认版本的[发布说明](https://github.com/google/protobuf/releases)比较，看到两者的主要不同点。更多关于proto3的文档很快就会出现。虽然你*可以*使用proto2(当前默认的protocol buffers版本)， 我们通常建议你在gRPC里使用proto3，因为这样你可以使用gRPC支持全部范围的的语言，并且能避免proto2客户端与proto3服务端交互时出现的兼容性问题，反之亦然。
## 你好gRPC!
现在你已经对gRPC有所了解，了解其工作机制最见简单的方法是看一个简单的例子。我们的Hello World将带领你创建一个简单的客户端-服务端应用，向你展示：

 - 通过一个protocol buffers模式，定义一个简单的带有Hello World方法的RPC服务。
 - 用你最喜欢的语言(如果可用的话)来创建一个实现了这个接口的服务端。
 - 用你最喜欢的(或者其他你愿意的)语言来访问你的服务端。
 
这个例子完整的代码在我们GitHub源码库的`examples`目录下。
我们可以使用Git版本系统来进行源码管理，但是除了如何安装和运行一些Git命令外，你没必要知道其他关于Git的任何事情。
需要注意的是，并不是所有gRPC支持的语言都可以编写我们例子的服务端代码，比如PHP和Objective-C仅支持创建客户端。
比起一个针对于特定语言的复杂教程这更像是一个介绍性的例子。你可以在本站找到更有深度的教程，gRPC支持的语言的参考文档很快就会全部开放。
### 准备
本节解释了如何在你本机上准备好例子代码的运行环境。如果你只是想读一下例子，你可以直接到[下一步](#servicedef)。

#### 安装Git

你可以从[http://git-scm.com/download](http://git-scm.com/download)下载和安装Git。一旦安装好Git后你应该能访问git命令行工具。你需要的主要命令如下：

- git clone ... : 从远程代码库克隆一份到本机。
- git checkout ... : 检出一个特殊分支或一个标签版本的代码来改进

#### 安装gRPC
针对你选择的语言构建和安装gRPC插件和相关工具，可以参照[快速开始](#quickstart)。 Java gRPC除了JDK外不需要其他工具。
#### 获得源码

 - [Java](#java_source)
 - [C++](#cpp_source)
 - [Python](#go_source)
 - [Go](#python_source)
 - [Ruby](#ruby_source)
 - [Node.js](#node_source)
 - [C#](#csharp_source)
 - [Objective-C](#objective-c_source)
 - [PHP](#php_source)
 
The example code for our Java example lives in the 
GitHub repository. Clone this repository to your local machine by running the
following command:
<pre>
git clone https://github.com/grpc/grpc-java.git
</pre>

Change your current directory to <code>grpc-java/examples</code>.
<pre>
cd grpc-java/examples
</pre>
  </div>
  <div id="cpp_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following command:

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Change your current directory to <code>examples/cpp/helloworld</code>

<pre>
$ cd examples/cpp/helloworld/
</pre>
  </div>
  <div id="python_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following command:

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Change your current directory to <code>examples/python/helloworld</code>

<pre>
$ cd examples/python/helloworld/
</pre>
</div>
  <div id="go_source">
<p>Get the example:
<pre>
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter&lowbar;client
$ go get -u github.com/grpc/grpc-go/examples/helloworld/greeter&lowbar;server
</pre>
<p>Change your current directory to <code>examples/helloworld</code>
  </div>
  <div id="ruby_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following command:</p>

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Change your current directory to <code>examples/ruby</code>. Then use bundler to install the example package's dependencies:</p>

<pre>
$ gem install bundler # if you don't already have bundler available
$ bundle install
</pre>

  </div>
  <div id="node_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following command:</p>

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Change your current directory to <code>examples/node</code>, then install this package's dependencies:</p>
<pre>
$ cd examples/node
$ npm install
</pre>

  </div>
  <div id="csharp_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following command:</p>

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Open <code>Greeter.sln</code> from Visual Studio (or Monodevelop on Linux). See the <a href="/docs/installation/csharp.html">C# Quickstart</a> for platform-specific setup.</p>
  </div>
<div id="objective-c_source">
<p>The example code for this lives in the <code>examples</code> directory in our GitHub repositories. Clone this repository to your local machine by running the following commands:</p>

<pre>
$ git clone https://github.com/grpc/grpc.git
$ cd grpc
$ git submodule update --init
</pre>

<p>Change your current directory to <code>examples/objective-c/helloworld</code>.</p>

</div>
  <div id="php_source">
<p>The example code for this lives in the <code>examples</code> in our GitHub repositories. Clone this repository to your local machine by running the following command:</p>

<pre>
$ git clone https://github.com/grpc/grpc.git
</pre>

<p>Change your current directory to <code>examples/php</code>.</p>

<p class="note">While most of our Hello World examples use the same .proto file, the PHP example has its own copy of <code>helloworld.proto</code> because it currently depends on some proto2 syntax. There is no proto3 support for PHP yet.</p>
  </div>
</div>


  [1]: http://www.grpc.io/img/grpc_concept_diagram_00.png
