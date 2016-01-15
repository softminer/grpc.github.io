#认证
gRPC 被设计成可以利用插件的形式支持多种授权机制。本文档对多种支持的授权机制提供了一个概览，并且用例子来论述对应API，最后就其扩展性作了讨论。
马上将会推出更多文档和例子。

#支持的授权机制


##SSL/TLS

gRP 集成 SSL/TLS 并对服务端授权所使用的 SSL/TLS 进行了改良，对客户端和服务端交换的所有数据进行了加密。对客户端来讲提供了可选的机制提供凭证来获得共同的授权。


##OAuth 2.0

gRPC 提供通用的机制（后续进行描述）来对请求和应答附加基于元数据的凭证。当通过 gRPC 访问 Google API 时，会为一定的授权流程提供额外的获取访问令牌的支持，这将通过以下代码例子进行展示。
*警告*：Google OAuth2 凭证应该仅用于连接 Google 的服务。把 Google 对应的 OAuth2 令牌发往非 Google 的服务会导致令牌被窃取用作冒充客户端来访问 Google 的服务。

## API
为了减少复杂性和将混乱最小化， gRPC 以一个统一的凭证对象来进行工作。
凭证可以是以下两类：

- *频道凭证*, 被附加在 `频道`上， 比如 SSL 凭证。
- *调用凭证*, 被附加在调用上(或者 C++ 里的 `客户端上下文`)。
凭证可以用`组合频道凭证`来进行组合。一个`组合频道凭证`可以将一个`频道凭证`和一个`调用凭证`关联创建一个新的`频道凭证`。结果在这个频道上的每次调用会发送组合的`调用凭证`来作为授权数据。
例如，一各`频道凭证`可以由一个`Ssl 凭证`和一个`访问令牌凭证`生成。结果是在这个频道上的每次调用都会发送对应的访问令牌。
`调用凭证`可以用 `组合凭证`来组装。组装后的 `调用凭证`应用到一个`客户端上下文`里，将触发发送这两个`调用凭证`的授权数据。

###服务端认证加密使用的 SSL/TLS 
这是个最简单的认证场景：一个客户端仅仅想认证服务器并且加密所有数据。

```cpp
// Create a default SSL ChannelCredentials object.
auto channel_creds = grpc::SslCredentials(grpc::SslCredentialsOptions());
// Create a channel using the credentials created in the previous step.
auto channel = grpc::CreateChannel(server_name, creds);
// Create a stub on the channel.
std::unique_ptr<Greeter::Stub> stub(Greeter::NewStub(channel));
// Make actual RPC calls on the stub.
grpc::Status s = stub->sayHello(&context, *request, response);
```
对于高级的用例比如改变根 CA 或使用客户端证书，可以在发送给工厂方法的 SslCredentialsOptions 参数里的相应选项进行设置。

###通过 Google 进行认证
gRPC应用可以使用一个简单的API来创建一个可以工作在不同部署场景下的凭证。


```cpp
auto creds = grpc::GoogleDefaultCredentials();
// Create a channel, stub and make RPC calls (same as in the previous example)
auto channel = grpc::CreateChannel(server_name, creds);
std::unique_ptr<Greeter::Stub> stub(Greeter::NewStub(channel));
grpc::Status s = stub->sayHello(&context, *request, response);
```
这个应用使用的频道凭证对象就像 [Google 计算引擎 
(GCE)](https://cloud.google.com/compute/)里运行的应用一样使用服务账号。在前面的案例里，服务账号的密钥从环境变量 `GOOGLE_APPLICATION_CREDENTIALS` 对应的文件里加载。这些密钥被用来生成承载令牌附加在在相应频道的每次 RPC 调用里。
对于 GCE 里运行的应用，可以在虚拟机设置的时候为其配置一个默认的服务账号和相应的 OAuth2 范围。在运行时，这个凭证被用来与认证系统通讯来获取 OAuth2 访问令牌并且把令牌用作在相应的频道上的 RPC 调用。

### 扩展 gRPC 支持其他的认证机制

相应的凭证插件 API 允许开发者开发自己的凭证插件。

- `MetadataCredentialsPlugin` 抽象类包含需要被开发者创建的子类实现的纯虚方法
  `GetMetadata`。
- `MetadataCredentialsFromPlugin` 方法可以从 `MetadataCredentialsPlugin` 创建一个
`调用者凭证`。
这类有个简单的凭证插件例子，是通过在自定义头了设置一个认证票据。

```cpp
class MyCustomAuthenticator : public grpc::MetadataCredentialsPlugin {
 public:
  MyCustomAuthenticator(const grpc::string& ticket) : ticket_(ticket) {}

  grpc::Status GetMetadata(
      grpc::string_ref service_url, grpc::string_ref method_name,
      const grpc::AuthContext& channel_auth_context,
      std::multimap<grpc::string, grpc::string>* metadata) override {
    metadata->insert(std::make_pair("x-custom-auth-ticket", ticket_));
    return grpc::Status::OK;
  }

 private:
  grpc::string ticket_;
};

auto call_creds = grpc::MetadataCredentialsFromPlugin(
    std::unique_ptr<grpc::MetadataCredentialsPlugin>(
        new MyCustomAuthenticator("super-secret-ticket")));
```
更深层次的集成可以通过在将 gRPC 的凭证实现以插件的形式集成进核心层。gRPC 内部也允许用其他加密机制来替换 SSL/TLS 。


## 例子

这些授权机制将会在所有 gRPC 支持的语言里提供。以下的一些节里展示了上文提到的认证和授权在每种语言里如何实现：很快将会推出更多语言的支持。


###通过 SSL/TLS 进行服务端授权和加密(Ruby)

```ruby
# Base case - No encryption
stub = Helloworld::Greeter::Stub.new('localhost:50051', :this_channel_is_insecure)
...

# With server authentication SSL/TLS
creds = GRPC::Core::Credentials.new(load_certs)  # load_certs typically loads a CA roots file
stub = Helloworld::Greeter::Stub.new('localhost:50051', creds)
```

###通过 SSL/TLS 进行服务端授权和加密 (C#)

```csharp
// Base case - No encryption/authentication
var channel = new Channel("localhost:50051", ChannelCredentials.Insecure);
var client = new Greeter.GreeterClient(channel);
...

// With server authentication SSL/TLS
var channelCredentials = new SslCredentials(File.ReadAllText("roots.pem"));  // Load a custom roots file.
var channel = new Channel("myservice.example.com", channelCredentials);
var client = new Greeter.GreeterClient(channel);
```

###通过 SSL/TLS 进行服务端授权和加密 (Python)

```python
from grpc.beta import implementations
import helloworld_pb2

# Base case - No encryption
channel = implementations.insecure_channel('localhost', 50051)
stub = helloworld_pb2.beta_create_Greeter_stub(channel)
...

# With server authentication SSL/TLS
creds = implementations.ssl_channel_credentials(open('roots.pem').read(), None, None)
channel = implementations.secure_channel('localhost', 50051, creds)
stub = helloworld_pb2.beta_create_Greeter_stub(channel)
```

###通过 Google 进行授权 (Ruby)

####基本案例 - 无加密/授权

```ruby
stub = Helloworld::Greeter::Stub.new('localhost:50051', :this_channel_is_insecure)
```

####用无限制凭证进行授权 (推荐途径)

```ruby
require 'googleauth'  # from http://www.rubydoc.info/gems/googleauth/0.1.0
...
ssl_creds = GRPC::Core::ChannelCredentials.new(load_certs)  # load_certs typically loads a CA roots file
authentication = Google::Auth.get_application_default()
call_creds = GRPC::Core::CallCredentials.new(authentication.updater_proc)
combined_creds = ssl_creds.compose(call_creds)
stub = Helloworld::Greeter::Stub.new('greeter.googleapis.com', combined_creds)
```

####用 OAuth2 令牌进行认证(传统途径)

```ruby
require 'googleauth'  # from http://www.rubydoc.info/gems/googleauth/0.1.0
...
ssl_creds = GRPC::Core::ChannelCredentials.new(load_certs)  # load_certs typically loads a CA roots file
scope = 'https://www.googleapis.com/auth/grpc-testing'
authentication = Google::Auth.get_application_default(scope)
call_creds = GRPC::Core::CallCredentials.new(authentication.updater_proc)
combined_creds = ssl_creds.compose(call_creds)
stub = Helloworld::Greeter::Stub.new('greeter.googleapis.com', combined_creds)
```


###通过 Google 进行授权 (Node.js)

####基本案例 - 无加密/授权

```js
var stub = new helloworld.Greeter('localhost:50051', grpc.credentials.createInsecure());
```

####用无限制凭证进行授权 (推荐途径)

```js
// Authenticating with Google
var GoogleAuth = require('google-auth-library'); // from https://www.npmjs.com/package/google-auth-library
...
var ssl_creds = grpc.credentials.createSsl(root_certs);
(new GoogleAuth()).getApplicationDefault(function(err, auth) {
  var call_creds = grpc.credentials.createFromGoogleCredential(auth);
  var combined_creds = grpc.credentials.combineChannelCredentials(ssl_creds, call_creds);
  var stub = new helloworld.Greeter('greeter.googleapis.com', combined_credentials);
});
```
####用 OAuth2 令牌进行认证(传统途径)

```js
var GoogleAuth = require('google-auth-library'); // from https://www.npmjs.com/package/google-auth-library
...
var ssl_creds = grpc.Credentials.createSsl(root_certs); // load_certs typically loads a CA roots file
var scope = 'https://www.googleapis.com/auth/grpc-testing';
(new GoogleAuth()).getApplicationDefault(function(err, auth) {
  if (auth.createScopeRequired()) {
    auth = auth.createScoped(scope);
  }
  var call_creds = grpc.credentials.createFromGoogleCredential(auth);
  var combined_creds = grpc.credentials.combineChannelCredentials(ssl_creds, call_creds);
  var stub = new helloworld.Greeter('greeter.googleapis.com', combined_credentials);
});
```

###通过 Google 进行授权 (C#)

####基本案例 - 无加密/授权
```csharp
var channel = new Channel("localhost:50051", ChannelCredentials.Insecure);
var client = new Greeter.GreeterClient(channel);
...
```

####用无限制凭证进行授权 (推荐途径)
```csharp
using Grpc.Auth;  // from Grpc.Auth NuGet package
...
// Loads Google Application Default Credentials with publicly trusted roots.
var channelCredentials = await GoogleGrpcCredentials.GetApplicationDefaultAsync();

var channel = new Channel("greeter.googleapis.com", channelCredentials);
var client = new Greeter.GreeterClient(channel);
...
```

####用 OAuth2 令牌进行认证(传统途径)
```csharp
using Grpc.Auth;  // from Grpc.Auth NuGet package
...
string scope = "https://www.googleapis.com/auth/grpc-testing";
var googleCredential = await GoogleCredential.GetApplicationDefaultAsync();
if (googleCredential.IsCreateScopedRequired)
{
    googleCredential = googleCredential.CreateScoped(new[] { scope });
}
var channel = new Channel("greeter.googleapis.com", googleCredential.ToChannelCredentials());
var client = new Greeter.GreeterClient(channel);
...
```

####授权一个 gRPC 调用
```csharp
var channel = new Channel("greeter.googleapis.com", new SslCredentials());  // Use publicly trusted roots.
var client = new Greeter.GreeterClient(channel);
...
var googleCredential = await GoogleCredential.GetApplicationDefaultAsync();
var result = client.SayHello(request, new CallOptions(credentials: googleCredential.ToCallCredentials()));
...

```

###通过 Google 进行授权 (PHP)

####基本案例 - 无加密/授权

```php
$client = new helloworld\GreeterClient('localhost:50051', [
    'credentials' => Grpc\ChannelCredentials::createInsecure(),
]);
...
```
####用无限制凭证进行授权 (推荐途径)
####Authenticate using scopeless credentials (recommended approach)

```php
function updateAuthMetadataCallback($context)
{
    $auth_credentials = ApplicationDefaultCredentials::getCredentials();
    return $auth_credentials->updateMetadata($metadata = [], $context->service_url);
}
$channel_credentials = Grpc\ChannelCredentials::createComposite(
    Grpc\ChannelCredentials::createSsl(file_get_contents('roots.pem')),
    Grpc\CallCredentials::createFromPlugin('updateAuthMetadataCallback')
);
$opts = [
  'credentials' => $channel_credentials
];
$client = new helloworld\GreeterClient('greeter.googleapis.com', $opts);
````

####用 OAuth2 令牌进行认证(传统途径)

```php
// the environment variable "GOOGLE_APPLICATION_CREDENTIALS" needs to be set
$scope = "https://www.googleapis.com/auth/grpc-testing";
$auth = Google\Auth\ApplicationDefaultCredentials::getCredentials($scope);
$opts = [
  'credentials' => Grpc\Credentials::createSsl(file_get_contents('roots.pem'));
  'update_metadata' => $auth->getUpdateMetadataFunc(),
];
$client = new helloworld\GreeterClient('greeter.googleapis.com', $opts);
```

###通过 Google 进行授权 (Python)

####基本案例 - 无加密/授权

```python
channel = implementations.insecure_channel('localhost', 50051)
stub = helloworld_pb2.beta_create_Greeter_stub(channel)
...
```
###用 OAuth2 令牌进行认证(传统途径)
```python
transport_creds = implementations.ssl_channel_credentials(open('roots.pem').read(), None, None)
def oauth2token_credentials(context, callback):
  try:
    credentials = oauth2client.client.GoogleCredentials.get_application_default()
    scoped_credentials = credentials.create_scoped([scope])
  except Exception as error:
    callback([], error)
    return
  callback([('authorization', 'Bearer %s' % scoped_credentials.get_access_token().access_token)], None)

auth_creds = implementations.metadata_plugin_credentials(oauth2token_credentials)
channel_creds = implementations.composite_channel_credentials(transport_creds, auth_creds)
channel = implementations.secure_channel('localhost', 50051, channel_creds)

stub = helloworld_pb2.beta_create_Greeter_stub(channel)
```
