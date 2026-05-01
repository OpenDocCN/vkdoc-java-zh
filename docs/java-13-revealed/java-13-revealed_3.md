# 3. HTTP 客户端 API

在本章中，你将学习

*   什么是 HTTP 客户端 API

*   如何创建 HTTP 客户端

*   如何发起 HTTP 请求

*   如何接收和处理 HTTP 响应

*   如何使用服务器推送将未经请求的数据从服务器推送到客户端

*   如何使用 HTTP 基本认证来验证对服务器上资源的访问

*   如何创建 WebSocket 客户端端点

JDK 9 和 10 将 HTTP 客户端 API 作为孵化器模块提供。JDK 11 将其作为名为 `java.net.http` 的标准模块包含在内。其类型位于 `java.net.http` 包中。

## 什么是 HTTP 客户端 API？

Java 从非常早期的版本就支持 HTTP 客户端 API。旧版 API 支持 HTTP/1.1，它由 `java.net` 包中的几个类和接口组成。`URLConnection` 是表示通信的基类，而 `HttpURLConnection` 是表示 HTTP 通信的类。旧版 API 存在以下问题：

*   它被设计为支持多种协议，如 http、ftp、gopher 等，其中许多协议已不再使用。

*   它过于抽象且难以使用。

*   它包含许多未记录的行为。

*   它仅支持一种模式，即阻塞模式，这要求每个请求/响应都有一个线程。

*   它不支持 HTTP/2。

2015 年 5 月，互联网工程任务组（IETF）发布了 HTTP/2 的规范。有关 HTTP/2 规范的完整文本，请参阅网页 [`https://tools.ietf.org/html/rfc7540`](https://tools.ietf.org/html/rfc7540)。HTTP/2 并未修改应用层语义。也就是说，你在应用程序中了解并一直使用的 HTTP 协议内容没有改变。它采用了一种更高效的方式来准备数据包并将其在客户端和服务器之间通过线路发送。你之前了解的关于 HTTP 的所有内容，例如 HTTP 标头、方法、状态码、URL 等，都保持不变。HTTP/2 试图解决 HTTP/1.1 连接面临的许多与性能相关的问题：

*   HTTP/2 支持二进制数据交换，而不是 HTTP/1.1 支持的文本数据。

*   HTTP/2 支持多路复用和并发，这意味着在单个 TCP 连接的两个方向上可以同时进行多个数据交换，并且可以乱序接收对请求的响应。这消除了对等点之间建立多个连接的开销，而这在使用 HTTP/1.1 时通常是必需的。在 HTTP/1.1 中，必须按照发送请求的顺序接收响应，这被称为*队头*阻塞。HTTP/2 通过在同一 TCP 连接上启用多路复用解决了队头阻塞问题。

*   客户端可以建议请求的优先级，服务器在确定响应优先级时可能会遵循该建议。

*   HTTP 标头被压缩，这显著减小了标头大小，从而降低了延迟。

*   它允许将资源从服务器未经请求地推送到客户端，这称为服务器推送。

JDK 11 中的 HTTP 客户端 API 没有更新现有的 HTTP/1.1 API，而是提供了一个同时支持 HTTP/1.1 和 HTTP/2 的新 API。其目的是 HTTP 客户端 API 最终将取代 `URLConnection` 类提供的旧 API。新 API 还包含用于使用 WebSocket 协议开发客户端应用程序的类和接口。有关完整的 WebSocket 协议规范，请参阅网页 [`https://tools.ietf.org/html/rfc6455`](https://tools.ietf.org/html/rfc6455)。与旧版 API 相比，新的 HTTP 客户端 API 具有多项优势：

*   它很简单。对于大多数常见情况，它易于学习和使用。

*   它使用 Java 9 中引入的 Flow API，通过响应式流处理请求和响应体。

*   它支持服务器推送，允许服务器将资源推送到客户端，而无需客户端显式请求这些资源。它使得与服务器建立 WebSocket 通信变得简单。

*   它支持 HTTP/2 和 HTTPS/TLS 协议。

*   它可以在同步（阻塞）和异步（非阻塞）模式下工作。



## HTTP 客户端 API 的组成部分

一次典型的 HTTP 通信包含一个请求和一个响应。客户端创建一个请求，该请求由 URL、标头和正文组成。请求被发送到由 URL 指示的服务器。服务器用一个响应来回应，该响应由状态码、标头和正文组成。HTTP 客户端 API 提供了表示 HTTP 请求和响应的类。

HTTP 客户端 API 规模很小。它包含不到 20 种类型，其中四种是主要类型。其他类型在你使用这四种类型之一时会用到。这个新的 API 也使用了旧版 HTTP API 中的一些类型。新的 API 位于 `java.net.http` 模块的 `java.net.http` 包中。如果你想在你的模块中使用 HTTP 客户端 API，你的模块需要读取 `java.net.http` 模块。

HTTP 客户端 API 中的四种主要类型如下：

*   `HttpClient` 类
*   `HttpRequest` 类
*   `HttpResponse<T>` 接口
*   `WebSocket` 接口

`HttpClient` 充当一个容器，用于存储可用于多个 HTTP 请求的配置，而不是为每个请求单独设置它们。

`HttpRequest` 表示一个可以发送到服务器的 HTTP 请求。一旦你创建了一个 `HttpRequest`，它就是不可变的，并且可以重复使用以多次发送相同的请求。

`HttpResponse` 表示一个 HTTP 响应。你不需要创建 `HttpResponse`；相反，你将它作为服务器对 `HttpRequest` 的响应来接收。

`WebSocket` 表示一个 WebSocket 客户端。你可以使用 Java EE 7 WebSocket API 或使用其他几种编程语言（如 Python、NodeJS 等）来创建 WebSocket 服务器。本章不会向你展示如何创建 WebSocket 服务器。

### 提示

`HttpClient` 是不可变的。存储在 `HttpClient` 中的一些配置在构建 HTTP 请求时可能会被覆盖。你不需要创建 `HttpResponse`；它会作为你发送给服务器的 HTTP 请求的一部分返回给你。

`HttpClient`、`HttpRequest` 和 `WebSocket` 的实例是使用构建器创建的。它们每个都包含一个名为 `Builder` 的嵌套接口，用于构建该类型的实例。

HTTP 客户端 API 使用起来非常简单，你甚至可以用一条语句读取 HTTP 资源！下面的语句使用 `GET` HTTP 请求将 URL [`http://httpbin.org/ip`](http://httpbin.org/ip) 的内容作为字符串读取，并将响应正文打印到标准输出：

```
HttpClient.newHttpClient()
.sendAsync(HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.build(), BodyHandlers.ofString())
.thenApply(HttpResponse::body)
.thenAccept(System.out::println)
.join();
```

如果这条语句看起来令人生畏，请不要担心。我会在将其分解为单独的语句，然后再重新组合成一条语句后，解释它的每个部分。在这一点上，你只需要知道新的 HTTP 客户端 API 非常简单易用，你只需编写一条语句就可以读取 HTTP 资源。

## 设置示例

本章中的许多示例都涉及与 Web 服务器交互。我没有为所有示例创建一个 Web 应用程序，而是使用了 `httpbin.org`，它支持多种类型的 HTTP 和 HTTPS 通信以用于演示目的。如果你想在任何其他 Web 服务器上尝试这些示例，请随意将示例中的 URI 替换为你自己的。请注意，`httpbin.org` 网站不是我的。它由 Kenneth Reitz 维护。我只是在本章的示例中使用这个网站。还有另一个网站 [`http://postman-echo.com`](http://postman-echo.com) 提供了与 `httpbin.org` 类似的功能。

所有 HTTP 客户端程序示例的代码都在 `jdojo.http.client` 模块中，其声明如清单 3-1 所示。

```
// module-info.java
module jdojo.http.client {
requires java.net.http;
exports com.jdojo.http.client;
}
清单 3-1
一个名为 jdojo.http.client 的模块
```

## 快速示例

让我们来看一个快速但完整的示例，你将向 Web 服务器发送一个 HTTP 请求，并打印响应的状态码和正文。HTTP 通信中的典型步骤如下：

*   创建一个 `HttpClient` 来存储 HTTP 配置信息。
*   创建一个 `HttpRequest` 并用要发送到服务器的信息填充它，例如 URI、标头、HTTP 方法、请求正文等。
*   使用 `HttpClient` 将请求发送到服务器。
*   从服务器接收一个 `HttpResponse<T>` 作为响应。
*   处理 HTTP 响应，例如打印状态码和正文。

你可以使用 `HttpClient` 类的 `newHttpClient()` 静态方法创建一个具有默认配置的 `HttpClient`：

```
// 创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
```

`HttpRequest` 类的 `newBuilder()` 静态方法返回一个构建器来创建 `HttpRequest`。你需要调用构建器的方法来设置请求的各个部分。最后，你需要调用 `build()` 方法，该方法返回一个 `HttpRequest`：

```
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.build();
```

`HttpRequest.Builder` 使用的默认请求 HTTP 方法是 GET。你也可以通过在构建器上调用 `GET()` 方法来显式设置它：

```
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.GET()
.build();
```

调用 `HttpClient` 的 `send()` 方法将请求发送到服务器，该方法同步发送请求——它会阻塞直到收到响应。在发送请求时，你需要指定一个正文处理器来处理响应正文。`HttpResponse.BodyHandlers` 类的工厂方法提供了几个内置的正文处理器。例如，`BodyHandlers.ofString()` 静态方法返回一个正文处理器，用于将响应正文作为 `String` 处理。`send()` 方法返回一个 `HttpResponse<T>`，其中类型参数 `T` 是正文处理器的类型。如果正文处理器是 `String` 类型，则 `send()` 方法返回 `HttpResponse<String>`。`HttpResponse` 的 `statusCode()` 方法以 `int` 形式返回 HTTP 状态码，其 `body()` 方法返回响应正文。`send()` 方法会抛出已检查异常 `IOException` 和 `InterruptedException`，因此你需要在代码中处理这些异常。以下代码片段展示了如何将请求发送到服务器并将其正文作为 `String` 处理：

```
// 将请求发送到服务器，并将响应正文作为 String 处理
HttpResponse response
= client.send(request, BodyHandlers.ofString());
// 获取响应状态和正文
int statusCode = response.statusCode();
String body = response.body();
```

清单 3-2 包含了向服务器发送请求并处理响应的完整程序。URL `http://httpbin/ip` 以 JSON 格式返回请求者的 IP 地址。该程序将 JSON 数据作为 `String` 读取并打印到标准输出。状态码 200 表示成功。你可能会得到不同的输出。


```java
// PrintMyIP.java
package com.jdojo.http.client;
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
public class PrintMyIP {
public static void main(String[] args) {
// 创建使用默认配置的 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 创建 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.build();
try {
// 向服务器发送请求
HttpResponse response
= client.send(request, BodyHandlers.ofString());
// 获取响应状态码和响应体
int statusCode = response.statusCode();
String body = response.body();
// 打印响应状态码和响应体
System.out.println("响应状态码: " + statusCode);
System.out.println("响应体:\n" + body);
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
}
响应状态码: 200
响应体:
{
"origin": "75.76.172.37, 75.76.172.37"
}
清单 3-2
向服务器发送 HTTP 请求并处理响应
```

## 创建 HTTP 客户端

发送到服务器的 HTTP 请求需要进行配置，以便服务器知道要使用哪个身份验证器、SSL 配置详情、要使用的 Cookie 管理器、代理信息、如果服务器重定向请求时的重定向策略等。`HttpClient` 类的实例存储这些特定于请求的配置，并且它们可以重用于多个请求。你可以针对每个请求覆盖其中的一些配置。你需要使用 `HttpClient` 向服务器发送 HTTP 请求。一个 `HttpClient` 包含以下用于所有 HTTP 请求的信息：身份验证器、连接超时、Cookie 管理器、执行器、重定向策略、请求优先级、代理选择器、SSL 上下文、SSL 参数和 HTTP 版本。

*身份验证器*是 `java.net.Authenticator` 类的一个实例。它用于 HTTP 身份验证。默认情况下不使用身份验证器。HTTP 客户端 API 内置支持 HTTP 基本身份验证。

如果 `HttpClient` 在指定的连接超时时间内无法建立新连接，其 `send()` 方法会抛出 `HttpConnectTimeoutException`，而 `sendAsync()` 方法会以 `HttpConnectTimeoutException` 异常完成。如果不需要新连接（例如重用已建立的连接），则连接超时值无效。

*Cookie 处理器*用于处理 HTTP Cookie。它是 `java.net.CookieHandler` 类的一个实例。默认情况下不使用 Cookie 处理器。

*执行器*是 `java.util.concurrent.Executor` 接口的一个实例，用于发送和接收异步 HTTP 请求和响应。如果未指定，则会提供默认执行器。

*重定向策略*是 `HttpClient.Redirect` 枚举的常量之一，用于指定如何处理服务器发出的重定向。默认值为 `NEVER`，这意味着永远不会跟随服务器发出的重定向。

*请求优先级*是 HTTP/2 请求的默认优先级，取值范围为 1 到 256（含）。这是给服务器的一个提示，用于优先处理请求。值越高表示优先级越高。

*代理选择器*是 `java.net.ProxySelector` 类的一个实例，用于选择要使用的代理服务器。默认情况下不使用代理服务器。

*SSL 上下文*是 `javax.net.ssl.SSLContext` 类的一个实例，它提供了安全套接字协议的实现。提供了一个默认的 `SSLContext`，当你不需要指定协议或不需要客户端身份验证时，它可以正常工作。

*SSL 参数*是 SSL/TLS/DTLS 连接的参数。它们存储在 `javax.net.ssl.SSLParameters` 类的实例中。

HTTP 版本是 HTTP 的版本，可以是 1.1 或 2。它被指定为 `HttpClient.Version` 枚举的常量之一：`HTTP_1_1` 和 `HTTP_2`。它尽可能请求特定的 HTTP 协议版本。默认值为 `HTTP_2`。也就是说，如果你没有在 `HttpClient` 中设置 HTTP 版本，默认情况下它将尝试使用 HTTP/2。如果服务器不支持 HTTP/2，它将降级到 HTTP/1.1。

### 提示

`HttpClient` 是不可变的。在构建 HTTP 请求时，`HttpClient` 中存储的一些配置可能会被覆盖。

`HttpClient` 类是抽象的，你不能直接创建它的实例。有两种方法可以创建 `HttpClient` 类的实例：

*   使用 `HttpClient` 类的 `newHttpClient()` 静态方法
*   使用 `HttpClient.Builder` 接口的 `build()` 方法

以下代码片段获取一个包含默认配置的 `HttpClient`：

```
// 获取使用默认配置的 HttpClient
HttpClient defaultClient = HttpClient.newHttpClient();
```

你也可以使用 `HttpClient.Builder` 接口创建 `HttpClient`。`HttpClient.newBuilder()` 静态方法返回 `HttpClient.Builder` 接口的一个新实例。`HttpClient.Builder` 接口为每个配置值提供了一个设置方法。配置值作为方法的参数指定，并且该方法返回构建器对象本身的引用，因此你可以链式调用多个方法。最后，调用 `build()` 方法，该方法返回一个配置好的 `HttpClient` 对象。以下语句创建了一个 `HttpClient`，其重定向策略设置为 `ALWAYS`，HTTP 版本设置为 1.1，连接超时设置为 2 秒：

```
// 创建使用自定义配置的 HttpClient
HttpClient client = HttpClient.newBuilder()
.followRedirects(ALWAYS)
.version(HTTP_1_1)
.connectTimeout(Duration.ofSeconds(2))
.build();
```

`HttpClient` 类包含一个对应于每个配置设置的方法，该方法返回该配置的值：

*   `Optional<Authenticator> authenticator()`
*   `Optional<Duration> connectTimeout()`
*   `Optional<CookieHandler> cookieHandler()`
*   `Optional<Executor> executor()`
*   `HttpClient.Redirect followRedirects()`
*   `Optional<ProxySelector> proxy()`
*   `SSLContext sslContext()`
*   `SSLParameters sslParameters()`
*   `HttpClient.Version version()`

`HttpClient` 类中没有 setter 方法，因为该类是不可变的。你不能单独使用 `HttpClient`。在使用 `HttpClient` 向服务器发送请求之前，你需要有一个 `HttpRequest`。我将在下一节中解释 `HttpRequest` 类。`HttpClient` 类包含以下三个向服务器发送请求的方法：

*   `<T> HttpResponse<T> send(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler)`
*   `<T> CompletableFuture<HttpResponse<T>> sendAsync(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler)`
*   `<T> CompletableFuture<HttpResponse<T>> sendAsync(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler, HttpResponse.PushPromiseHandler<T> pushPromiseHandler)`

`send()` 方法同步发送请求，而 `sendAsync()` 方法异步发送请求。`sendAsync()` 的第二个版本允许你异步发送请求并处理服务器推送响应。我将在后续章节中更详细地介绍这些方法。


## 处理 HTTP 请求

客户端应用程序通过 HTTP 请求与 Web 服务器通信。它向服务器发送请求，服务器则返回响应。`HttpRequest` 类的实例代表一个请求。以下是处理请求所需执行的步骤：

*   获取一个 HTTP 请求构建器。

*   设置请求的参数。

*   从构建器创建 HTTP 请求。

*   将 HTTP 请求发送到服务器——同步或异步。

*   处理来自服务器的响应。

### 获取 HTTP 请求构建器

你需要使用一个构建器对象（即 `HttpRequest.Builder` 接口的实例）来创建 `HttpRequest`。你可以通过 `HttpRequest` 类的以下 `static` 方法之一来获取 `HttpRequest.Builder`：

*   `HttpRequest.Builder newBuilder()`

*   `HttpRequest.Builder newBuilder(URI uri)`

以下代码片段展示了如何使用这些方法来获取 `HttpRequest.Builder`：

```
// 为 httpbin.org 创建一个 URI
URI uri = URI.create("http://httpbin.org");
// 获取一个带有 URI 的构建器
HttpRequest.Builder builder1 = HttpRequest.newBuilder(uri);
// 获取一个此时未指定 URI 的构建器
HttpRequest.Builder builder2 = HttpRequest.newBuilder();
```

### 设置 HTTP 请求参数

一旦你有了 HTTP 请求构建器，就可以使用构建器的方法来设置请求的参数，例如请求方法、主体和标头。以下各节将通过示例展示如何在 `HttpRequest` 中设置不同类型的参数。

### 设置请求方法和主体发布器

HTTP 支持以下类型的方法：GET、POST、PUT、DELETE、PATCH、HEAD、OPTIONS、CONNECT 和 TRACE。`HttpRequest.Builder` 接口提供了四种方法来直接设置同名的 HTTP 方法：

*   `HttpRequest.Builder GET()`

*   `HttpRequest.Builder POST`​`(HttpRequest.BodyPublisher bodyPublisher)`

*   `HttpRequest.Builder PUT`​`(HttpRequest.BodyPublisher bodyPublisher)`

*   `HttpRequest.Builder DELETE()`

某些 HTTP 方法（如 POST 和 PUT）要求你将数据作为请求主体传递。某些 HTTP 方法（如 GET 和 DELETE）不支持请求主体。这反映在前面列表中的方法声明中。

请求主体表示为 `HttpRequest.BodyPublisher` 接口的一个实例。`BodyPublisher` 接口扩展了 `Flow.Publisher<ByteBuffer>`，其工作是将作为请求主体传递的 Java 对象转换为适合发送请求主体的字节缓冲区流。无需担心从头开始创建 `BodyPublisher`。对于常见用例，可以通过 `HttpRequest.BodyPublishers` 静态类提供预定义的主体发布器。

表 3-1 列出了 `HttpRequest.BodyPublishers` 类中的工厂方法。表中的所有方法都返回一个 `HttpRequest.BodyPublisher`。该类还提供了一个 `fromPublisher()` 方法，它是 `Flow.Publisher` 和 `HttpRequest.BodyPublisher` 之间的适配器。该方法接受一个 `Flow.Publisher` 并返回一个 `HttpRequest.BodyPublisher`。当你想要使用现有的 `Flow.Publisher` 作为 `HttpRequest.BodyPublisher` 时，可以使用此方法。

表 3-1

HttpRequest.BodyPublishers 类中返回 HttpRequest.BodyPublisher 实例的工厂方法列表

| 方法 | 描述 |
| --- | --- |
| `noBody()` | 返回一个不发送请求主体的请求主体发布器。当你没有要发送给服务器的请求主体，但用于发送请求的方法要求你指定一个主体发布器时，使用此方法获取主体发布器。 |
| `ofByteArray(byte[] buf)` | 返回一个将指定字节数组作为请求主体发送的请求主体发布器。 |
| `ofByteArray(byte[] buf, int offset, int length)` | 返回一个将指定字节数组中从 `offset` 开始的 `length` 个字节作为请求主体发送的请求主体发布器。 |
| `ofByteArrays(Iterable<byte[]> iterable)` | 返回一个将 `Iterable` 中的字节数组作为请求主体发送的请求主体发布器。 |
| `ofFile(Path path)` | 返回一个将 `path` 指定文件中的数据作为请求主体发送的请求主体发布器。如果指定的 `path` 不存在，则抛出 `FileNotFoundException`。 |
| `ofInputStream` `(Supplier<? extends InputStream> streamSupplier)` | 返回一个通过从 `InputStream` 读取来发送请求主体的请求主体发布器。注意使用 `Supplier` 作为参数而不是 `InputStream`。使用 supplier 可以在重新发送请求时重用。如果 `Supplier` 返回 `null`，则请求失败。 |
| `ofString(String body)` | 返回一个请求主体发布器，其请求主体是使用 UTF8 字符集转换为字节的指定 `String`。 |
| `ofString(String s, Charset charset)` | 返回一个请求主体发布器，其请求主体是使用指定 `charset` 转换为字节的指定 `String`。 |

以下代码片段展示了如何使用 HTTP POST 方法将表单数据发布到服务器：

```
// 将请求主体设置为字符串
String requestBody = "first_name=Kishori&last_name=Sharan";
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.header("Content-Type", "application/x-www-form-urlencoded")
.POST(BodyPublishers.ofString(requestBody))
.build();
```

该表单包含两个字段——`first_name` 和 `last_name`。我特意为这些字段设置了简单的值（Kishori 和 Sharan），这些值不需要 URL 编码。否则，你需要使用 `java.net.URLEncoder` 类的 `encode()` 方法在将字段值发送到服务器之前对其进行编码。请注意，如果你要发布表单数据，则必须在请求中将 `"Content-Type"` 标头设置为 `"application/x-www-form-urlencoded"`。

`HttpRequest.Builder` 接口提供了一个名为 `method()` 的方法，你可以使用它为请求设置任何类型的 HTTP 方法。其声明如下：

```
HttpRequest.Builder method(String method, HttpRequest.BodyPublisher bodyPublisher)
```

`method` 参数的值与 HTTP 方法名称相同，例如 `"POST"`、`"OPTIONS"`、`"HEAD"` 等。目前，HTTP 客户端 API 不支持 CONNECT HTTP 方法。以下语句使用 `method()` 方法配合 HTTP OPTIONS 方法来创建一个 `HttpRequest`：

```
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.method("OPTIONS", BodyPublishers.noBody())
.build();
```

通常，你不会使用 OPTIONS 方法发送请求主体。请注意，我们使用 `BodyPublishers.noBody()` 工厂方法来获取一个 `HttpRequest.BodyPublisher`，它不发送任何请求主体。你可以使用 `method()` 方法重写前面创建 `HttpRequest` 以发布表单数据的示例，如下所示：

```
// 将请求主体设置为字符串
String requestBody = "first_name=Kishori&last_name=Sharan";
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.header("Content-Type", "application/x-www-form-urlencoded")
.method("POST", BodyPublishers.ofString(requestBody))
.build();
```



### 覆盖 HTTP 版本

`HttpRequest` 通过 `HttpClient` 发送到服务器。在构建 `HttpRequest` 时，你可以通过其 `HttpRequest.Builder` 对象，使用 `version()` 方法设置 HTTP 版本值，该方法将覆盖发送此请求时 `HttpClient` 中设置的 HTTP 版本。以下代码片段为请求将版本设置为 HTTP 1.1，覆盖了 `HttpClient` 中 HTTP 2 的默认值。回顾一下，是否使用 HTTP 2 取决于服务器。如果服务器不支持 HTTP 2，HTTP 通信将降级为使用 HTTP 1.1。

```
// 使用此 HttpClient 发送的所有请求都将使用 HTTP 2
// 除非被请求覆盖
HttpClient client = HttpClient.newHttpClient();
// 获取一个使用 HTTP 1.1 的 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.version(HTTP_1_1)
.build();
// client 对象使用 HTTP 2，而 request 对象将其覆盖为 HTTP 1.1。
// 以下语句将使用 request 对象中的 HTTP 1.1 发送请求。
HttpResponse response =
client.send(request, BodyHandlers.ofString());
```

### 设置超时

你可以使用 `HttpRequest.Builder` 实例的 `timeout(Duration timeout)` 方法为请求指定超时时间。如果在指定的超时时间内未收到响应，则会抛出 `HttpTimeoutException`。如果你没有设置请求超时，请求可能会永远阻塞。`httpbin.org/delay/{delay}` URL 会在延迟 URL 的 `{delay}` 部分指定的时间后发回响应。以下 `HttpRequest` 将始终因 `HttpTimeoutException` 而失败，因为服务器在 5 秒后发送响应，而我们将请求超时设置为 2 秒：

```
// 一个发送时会失败的 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/delay/5"))
.timeout(Duration.ofSeconds(2))
.build();
```

### 设置 Expect Continue 标头

HTTP 请求可能包含一个名为 `Expect` 且值为 `"100-Continue"` 的标头。如果设置了此标头，客户端仅向服务器发送标头（不带请求体），服务器应发回错误响应或 `100-Continue` 响应。收到 `100-Continue` 响应后，客户端将请求体发送到服务器。客户端使用此技术在实际发送请求体之前，根据请求标头检查服务器是否可以处理该请求。通常，当请求体很大，并且你不想在发送大量请求体后才发现请求无法完成时，会执行此操作。

默认情况下不设置 `Expect` 标头。你需要调用请求构建器的 `expectContinue(true)` 方法来启用此功能。以下代码片段向你展示了如何使用它：

```
// 在请求中启用 Expect=100-Continue 标头
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.header("Content-Type", "application/x-www-form-urlencoded")
.POST(BodyPublishers.ofString("name=Kishori"))
.expectContinue(true)
.build();
```

当你发送此请求时，客户端和服务器之间内部将进行两次交换。第一次请求不会发送请求体。在响应中收到 HTTP 100 状态码后，请求体将作为第二次交换的一部分发送。但是，你只会看到一个响应。

调用请求构建器的 `header("Expect", "100-Continue")` 方法不会启用此功能。你必须使用 `expectContinue(true)` 方法来启用它。

### 设置请求标头

HTTP 请求中的标头是一个名称-值对。一个请求中可以有多个标头。你可以使用 `HttpRequest.Builder` 类的 `header()`、`headers()` 和 `setHeader()` 方法向请求添加标头。如果标头尚不存在，`header()` 和 `headers()` 方法会添加标头。如果标头已添加，这些方法不执行任何操作。`setHeader()` 方法会替换已存在的标头；否则，它会添加一个新标头。

`header()` 和 `setHeader()` 方法允许你一次添加/设置一个标头，而 `headers()` 方法允许你添加多个标头。`headers()` 方法接受一个可变参数，该参数应按顺序包含名称-值对。以下代码片段展示了如何使用这三种方法为请求设置标头。所有三个请求都包含相同的标头。

```
// 使用 header() 方法
HttpRequest request1 = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.header("Content-Type", "application/x-www-form-urlencoded")
.header("Accept", "text/plain")
.POST(BodyPublishers.ofString("name=Kishori"))
.build();
// 使用 headers() 方法
HttpRequest request2 = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.headers("Content-Type", "application/x-www-form-urlencoded",
"Accept", "text/plain")
.POST(BodyPublishers.ofString("name=Kishori"))
.build();
// 使用 setHeader() 方法
HttpRequest request3 = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/post"))
.setHeader("Content-Type", "application/x-www-form-urlencoded")
.setHeader("Accept", "text/plain")
.POST(BodyPublishers.ofString("name=Kishori"))
.build();
```

### 创建 HTTP 请求

在前面的章节中，你学习了如何创建 `HttpRequest.Builder` 以及如何使用它来设置 HTTP 请求的不同属性。创建 `HttpRequest` 只需在 `HttpRequest.Builder` 上调用 `build()` 方法。以下代码片段创建了一个使用 HTTP GET 方法的 `HttpRequest`：

```
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.GET()
.build();
```

请注意，创建 `HttpRequest` 对象并不会将请求发送到服务器。你需要调用 `HttpClient` 的 `send()` 或 `sendAsync()` 方法将请求发送到服务器。我将在下一节中解释如何向服务器发送请求。

## 处理 HTTP 响应

一旦你有了 `HttpRequest`，就可以将请求发送到服务器并同步或异步接收响应。`HttpResponse<T>` 接口的实例表示从服务器接收到的响应，其中类型参数 `T` 指示响应体的类型，例如 `String`、`byte[]` 或 `Path`。你可以使用 `HttpClient` 类的以下方法之一来发送 HTTP 请求并接收 HTTP 响应：

*   `<T> HttpResponse<T> send`​`(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler) throws IOException, InterruptedException`

*   `<T> CompletableFuture<HttpResponse<T>> sendAsync`​`(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler)`

*   `<T> CompletableFuture<HttpResponse<T>> sendAsync`​`(HttpRequest request, HttpResponse.BodyHandler<T> responseBodyHandler, HttpResponse.PushPromiseHandler<T> pushPromiseHandler)`

`send()` 方法是同步的。也就是说，它会阻塞直到完全接收到响应。`sendAsync()` 方法异步处理响应。它会立即返回一个 `CompletableFuture<HttpResponse<T>>`，当响应准备好被处理时，该对象完成。你将在后续章节中看到这两种类型的示例。



### 处理响应状态码和标头

`HttpResponse` 包含状态码、标头和响应体。有时，在从服务器接收到状态码和标头后，但在接收到响应体之前，`HttpResponse` 就已经可用。响应在其主体完全可用之前的可用性取决于您提供的响应体处理器。例如，如果您提供一个 `String` 响应体处理器，则 `HttpResponse` 会在整个响应体被接收并转换为 `String` 后可用。如果您提供一个将响应体作为行流处理的响应体处理器，则 `HttpResponse` 将在接收到状态码和标头后可用。

`HttpResponse` 接口的 `statusCode()` 方法以 `int` 形式返回响应的状态码。

`HttpResponse` 接口的 `headers()` 方法以 `HttpHeaders` 类的实例形式返回响应的标头。`HttpHeaders` 类包含以下方法，可以方便地按名称检索标头值，或将所有标头作为 `Map<String,List<String>>` 检索：

*   `List<String> allValues`​`(String name)`

*   `Optional<String> firstValue`​`(String name)`

*   `OptionalLong firstValueAsLong`​`(String name)`

*   `Map<String,`​`List<String>> map()`

清单 3-3 包含一个完整的程序，用于向 URL [`http://httpbin.org`](http://httpbin.org) 发送 `HEAD` 请求。它会打印接收到的响应的状态码和标头。您可能会得到不同的输出。

```
// PrintHeaders.java
package com.jdojo.http.client;
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpRequest.BodyPublishers;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
public class PrintHeaders {
public static void main(String[] args) {
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org"))
.method("HEAD", BodyPublishers.noBody())
.build();
try {
// 向服务器发送请求
HttpResponse response
= client.send(request, BodyHandlers.discarding());
// 打印响应状态码和标头
System.out.println("Status Code:" + response.statusCode());
System.out.println("Response Headers:");
response.headers()
.map()
.entrySet()
.forEach(System.out::println);
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
}
Status Code:200
Response Headers:
access-control-allow-credentials=[true]
access-control-allow-origin=[*]
connection=[keep-alive]
content-length=[9593]
content-type=[text/html; charset=utf-8]
date=[Tue, 23 Jul 2019 18:01:12 GMT]
referrer-policy=[no-referrer-when-downgrade]
server=[nginx]
x-content-type-options=[nosniff]
x-frame-options=[DENY]
x-xss-protection=[1; mode=block]
清单 3-3
处理 HTTP 响应的状态码和标头
```

让我们看看请求和响应中的重要部分。创建 `HttpRequest` 的语句是：

```
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org"))
.method("HEAD", BodyPublishers.noBody())
.build();
```

注意使用了 HEAD HTTP 方法和 `method()` 方法。您只对接收响应中的标头感兴趣，因此使用了 HEAD 作为 HTTP 方法。您没有发送任何请求体，因此使用了 `BodyPublishers.noBody()` 指示的不发布任何主体的发布器。

以下语句将请求发送到服务器：

```
HttpResponse response
= client.send(request, BodyHandlers.discarding());
```

这里，您为响应体使用了从 `BodyHandlers.discarding()` 返回的响应体处理器。此响应体处理器简单地丢弃响应体，您可以在 `HttpResponse<Void>` 中 `send()` 方法返回值的类型参数 `Void` 中看到这一点。`response` 对象的 `body()` 方法的返回类型是 `Void`，调用 `response.body()` 将返回 `null`。

### 处理响应体

处理 HTTP 响应的主体是一个两步过程：

*   当您使用 `HttpClient` 类的 `send()` 或 `sendAsync()` 方法发送请求时，您需要指定一个响应体处理器，它是 `HttpResponse.BodyHandler<T>` 接口的一个实例。

*   当接收到响应状态码和标头时，会调用响应体处理器的 `apply(HttpResponse.ResponseInfo responseInfo)` 方法。`ResponseInfo` 包含响应状态码、标头和 HTTP 协议版本。`apply()` 方法返回一个 `HttpResponse.BodySubscriber<T>` 接口的实例，该实例读取响应体并将读取的数据转换为类型 `T`。`HttpResponse.BodySubscriber<T>` 接口扩展了 `Flow.Subscriber<List<ByteBuffer>>`，这意味着主体订阅者是 Flow API 中的一个订阅者，其工作是消费响应体并将其作为高级 Java 对象提供给您。

不必担心处理响应体的这些细节。`HttpResponse.BodyHandler<T>` 提供了多种实现。您可以使用 `HttpResponse.BodyHandlers` 类的以下 `static` 工厂方法之一，为不同的类型参数 `T` 获取其实例：

*   `<T> HttpResponse.BodyHandler<T> buffering`​`(HttpResponse.BodyHandler<T> downstreamHandler, int bufferSize)`

*   `HttpResponse.BodyHandler<Void> discarding()`

*   `HttpResponse.BodyHandler<byte[]> ofByteArray()`

*   `HttpResponse.BodyHandler<Void> ofByteArrayConsumer`​`(Consumer<Optional<byte[]>> consumer`

*   `HttpResponse.BodyHandler<Path> ofFile`​`(Path file)`

*   `HttpResponse.BodyHandler<Path> ofFile`​`(Path file, OpenOption... openOptions)`

*   `HttpResponse.BodyHandler<Path> ofFileDownload`​`(Path directory, OpenOption... openOptions)`

*   `HttpResponse.BodyHandler<InputStream> ofInputStream()`

*   `HttpResponse.BodyHandler<Stream<String>> ofLines()`

*   `HttpResponse.BodyHandler<Flow.Publisher<List<ByteBuffer>>> ofPublisher()`

*   `HttpResponse.BodyHandler<String> ofString()`

*   `HttpResponse.BodyHandler<String> ofString`​`(Charset charset)`

*   `<U> HttpResponse.BodyHandler<U> replacing`​`(U value)`

这些方法的签名足够直观，可以告诉您它们处理哪种类型的响应体。例如，如果您想将响应体作为 `String` 获取，请使用 `ofString()` 方法来获取主体处理器。`discarding()` 方法返回一个主体处理器，它将丢弃响应体。`replacing(U value)` 方法返回一个主体处理器，它丢弃原始响应体并返回指定的 `value` 作为主体。`ofLines()` 方法返回一个主体处理器，它将响应体作为行流返回。`ofFile()` 方法返回一个主体处理器，它将响应体保存到由指定 `Path` 表示的指定文件中。

`HttpResponse<T>` 接口的 `body()` 方法返回类型为 `T` 的响应体。

### 提示

`HttpResponse.BodyHandlers` 类包含 `fromSubscriber()` 和 `fromLineSubscriber()` 方法。它们充当 `Flow.Subscriber<T>` 和 `HttpResponse.BodyHandler<T>` 之间的适配器。它们允许您将 `Flow.Subscriber<T>` 转换为 `HttpResponse.BodyHandler<T>`。

您已经看到了将响应体作为 `String` 处理的示例。

后续章节将探讨处理响应体的更多选项，例如将响应体保存到文件、将其作为附件下载到文件。目前，没有内置的主体处理器可以将响应体作为 JSON 对象处理。您需要使用 Java 库，例如 Jackson JSON Parser，来处理 JSON 响应体。我们的几个示例返回 JSON 响应体。我只是将它们作为 `String` 处理。



#### 将响应体保存到文件

在本节中，我将演示如何将响应体保存到文件中。你将使用 [`http://httpbin.org/image/jpeg`](http://httpbin.org/image/jpeg) 这个地址，它会返回一个内容类型为 `image/jpeg` 的图片。假设你想将图片内容（位于响应体中）保存到当前目录下名为 `myimage.jpeg` 的文件中。你可以通过使用 `HttpResponse.BodyHandlers` 类的 `ofFile()` 方法返回的处理器来实现。响应体将是一个指向已保存文件的 `Path` 对象。以下代码片段展示了这部分逻辑：

```
// 创建一个 Path 对象，用于将图片保存到当前目录下名为 myimage.jpeg 的文件中
Path filePath = Paths.get("myimage.jpeg");
// 向服务器发送请求
HttpResponse response
= client.send(request, BodyHandlers.ofFile(filePath));
// 获取响应状态码和响应体。此处，响应体是保存了响应内容的文件路径
int statusCode = response.statusCode();
Path savedFilePath = response.body();
```

清单 3-4 包含了一个完整的程序，用于将响应体保存到文件中。你可能会得到不同的输出，因为它会打印已保存文件的路径，而该路径取决于当前目录。

```
// SaveInFile.java
package com.jdojo.http.client;
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import java.nio.file.Path;
import java.nio.file.Paths;
public class SaveInFile {
public static void main(String[] args) {
// 使用默认配置创建 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 使用 GET HTTP 方法
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/image/jpeg"))
.build();
try {
// 创建一个 Path 对象，用于将图片保存到当前目录下名为 myimage.jpeg 的文件中
Path filePath = Paths.get("myimage.jpeg");
// 向服务器发送请求
HttpResponse response
= client.send(request, BodyHandlers.ofFile(filePath));
// 获取响应状态码和响应体
int statusCode = response.statusCode();
Path savedFilePath = response.body();
// 打印响应状态码和响应体
System.out.println("响应状态码: " + statusCode);
System.out.println("响应体已保存至 "
+ savedFilePath.toAbsolutePath());
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
}
响应状态码: 200
响应体已保存至 C:\Java13Revealed\myimage.jpeg
清单 3-4
将响应体保存到文件
```

#### 将响应体作为附件下载

在本节中，我将演示如何将响应体作为附件下载并保存到文件中。要将文件作为附件下载，响应需要包含一个名为 `Content-Disposition` 的标头，该标头包含两个值——第一个是 `attachment`，第二个是 `filename` 及其文件名。该标头格式如下：

```
Content-Disposition: attachment; filename="myimage.jpeg"
```

`HttpResponse.BodyHandlers.ofFileDownload()` 方法返回一个处理器，用于读取 `Content-Disposition` 响应标头。如果标头值中包含 `attachment` 和 `filename`，该处理器会下载响应体，并将内容保存到 `filename` 参数指定的文件中。你需要指定下载文件保存的目录。如果响应不包含具有上述值的 `Content-Disposition` 标头，则 `ofFileDownload()` 方法返回的处理器将无法正常工作。

对于此示例，我在互联网上未找到能返回包含待下载文件的 `Content-Disposition` 响应标头的 URL。你将使用 [`http://httpbin.org/response-headers`](http://httpbin.org/response-headers) 这个 URL，它会返回该 URL 查询字符串中指定的响应标头。对于此示例，你需要在 URL 的查询字符串中指定包含预期值的 `Content-Disposition`。服务器将返回一个包含 JSON 文档和 `Content-Disposition` 响应标头的响应，该标头与你请求 URL 的查询字符串中指定的一致。查询字符串需要进行 URL 编码。未进行 URL 编码时，URL 如下所示：

```
http://httpbin.org/response-headers?Content-Disposition=attachment; filename=test.json
```

对 `Content-Disposition` 查询字符串参数的值进行编码后，URL 如下所示：

```
http://httpbin.org/response-headers?Content-Disposition=attachment%3B+filename%3Dtest.json
```

清单 3-5 包含了一个完整的程序，用于演示如何下载文件。程序重要部分的讨论位于输出之后。你可能会得到不同的输出。



```
// DownloadFile.java
package com.jdojo.http.client;
import java.io.IOException;
import java.net.URI;
import java.net.URLEncoder;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import static java.nio.charset.StandardCharsets.UTF_8;
import java.nio.file.Path;
import java.nio.file.Paths;
import static java.nio.file.StandardOpenOption.CREATE;
import static java.nio.file.StandardOpenOption.WRITE;
public class DownloadFile {
public static void main(String[] args) {
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
try {
// 通过编码参数值准备查询字符串
String paramName = "Content-Disposition";
String paramValue = "attachment; filename=test.json";
String encodedParamValue
= URLEncoder.encode(paramValue, UTF_8);
// 准备带有查询字符串的 URI
String uriStr = "http://httpbin.org/response-headers"
+ "?" + paramName + "=" + encodedParamValue;
// 创建一个 HttpRequest
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create(uriStr))
.header("Accept", "application/json")
.build();
// 为下载目录创建一个 Path，此处为当前目录
Path dirPath = Paths.get(".");
// 向服务器发送请求
HttpResponse response = client.send(request,
BodyHandlers.ofFileDownload(dirPath, CREATE, WRITE));
// 获取响应状态和响应体。
// 响应体是已下载文件的 Path。
int statusCode = response.statusCode();
Path filePath = response.body();
// 打印响应状态和响应体
System.out.println("Response Status: " + statusCode);
System.out.println("File downloaded at "
+ filePath.toAbsolutePath().normalize());
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
}
Response Status: 200
File downloaded at D:\Java13Revealed\test.json
Listing 3-5
Downloading an Attachment in a Response Body
```

`ofFileDownload()` 方法接受两个参数——第一个参数是一个 `Path`，指向文件将被下载到的目录。`Content-Disposition` 头中的 `filename` 参数可能指定一个文件路径而非文件名。出于安全原因，`filename` 参数中指定的文件路径不会被使用。只有文件路径的最后一部分被用作文件名。以下语句创建了一个表示当前目录的 `Path`：

```
Path dirPath = Paths.get(".");
```

当你创建 body handler 时，需要将选项作为 `OpenOption` 类型的可变参数传递给 `ofFileDownload()` 方法的第二个参数。在此示例中，你指定了 `CREATE` 和 `WRITE` 作为这些选项。它们将允许 body handler 创建一个文件（如果该文件不存在）来存储下载的内容，并且该文件将以写入模式打开。程序中的以下语句实现了这一点：

```
HttpResponse response = client.send(request,
BodyHandlers.ofFileDownload(dirPath, CREATE, WRITE));
```

从响应体返回的 `Path` 并未规范化。例如，它包含的文件路径为 `C:\Java13Revealed\.\test.json`。请注意文件路径中的点。该路径是有效的，但未规范化。在打印文件路径之前，程序会获取其绝对路径并进行规范化，以打印出类似 `C:\Java13Revealed\test.json` 的文件路径。程序中的以下代码片段执行此任务：

```
System.out.println("File downloaded at "
+ filePath.toAbsolutePath().normalize());
```

#### 跟随服务器重定向

当你向服务器发送请求时，通常会被重定向到另一个 URL。重定向通过响应状态码指示。状态码为 3xx 表示重定向。例如，如果你收到状态码 301，表示该 URL 已永久移动。在这种情况下，响应将包含一个名为 `Location` 的头，其值包含资源已移动到的新的 URL。例如，当你请求 [`http://google.com`](http://google.com) 的页面时，你会收到状态码为 301 的响应，并且响应头中的 `Location` 设置为 [`www.google.com`](http://www.google.com)。

一个 URL 可能会将你重定向到另一个 URL，新的 URL 又可能将你重定向到另一个，依此类推。有时，设计不佳的服务器上的 URL 可能会让你陷入无限的重定向循环。大多数浏览器设置了一个默认限制，即最多跟随 20 次重定向。

有时，服务器可能会将你重定向到另一个 URL，仅仅是为了强制你使用安全协议。例如，[`www.abc.com`](http://www.abc.com) 可能会将你重定向到 [`www.abc.com`](https://www.abc.com)。然而，从 HTTPS 重定向到 HTTP 并非正常做法。

浏览器会自动为你处理重定向。HTTP Client API 默认不跟随重定向。重定向策略在 `HttpClient` 中设置，并指定为 `HttpClient.Redirect` 枚举中的以下值之一：

*   `ALWAYS`

*   `NEVER`

*   `NORMAL`

`ALWAYS` 允许在所有情况下进行重定向。`NEVER` 是默认的重定向策略，不允许重定向。`NORMAL` 的行为与 `ALWAYS` 相同，但拒绝从 HTTPS 到 HTTP 的重定向。



### 提示

你可以使用 `HttpClient.Builder` 接口的 `followRedirects()` 方法来设置 `HttpClient` 的重定向策略。

清单 3-6 包含一个完整的程序，用于演示重定向策略的使用。程序输出后附有相关讨论。你得到的输出可能有所不同。

```
// FollowRedirection.java
package com.jdojo.http.client;
import java.io.IOException;
import java.net.URI;
import java.net.URLEncoder;
import java.net.http.HttpClient;
import static java.net.http.HttpClient.Redirect.NORMAL;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import static java.nio.charset.StandardCharsets.UTF_8;
public class FollowRedirection {
public static void main(String[] args) {
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 新 URL 的位置
String location = "http://httpbin.org/ip";
// 准备一个会将请求重定向到新位置的 URL
String uriStr = "http://httpbin.org/redirect-to?"
+ "url=" + URLEncoder.encode(location, UTF_8)
+ "&status_code=301";
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create(uriStr))
.build();
// 使用默认的 NEVER 重定向策略发送请求。
// 期望收到状态码为 301 的响应。
printResponse(client, request);
System.out.println("-----------------------------------");
// 创建一个重定向策略为 NORMAL 的 HttpClient
client = HttpClient.newBuilder()
.followRedirects(NORMAL)
.build();
// 使用 NORMAL 重定向策略发送请求
// 期望收到状态码为 200 的响应
printResponse(client, request);
}
public static void printResponse(HttpClient client,
HttpRequest request) {
try {
HttpResponse response
= client.send(request, BodyHandlers.ofString());
// 获取响应状态码和响应体
int statusCode = response.statusCode();
String body = response.body();
String locationHeader = response.headers()
.firstValue("Location")
.orElse("");
System.out.println("状态码: " + statusCode);
System.out.println("Location 头: " + locationHeader);
System.out.println("响应体:\n" + body);
} catch (IOException | InterruptedException e) {
e.printStackTrace();
}
}
}
状态码: 301
Location 头: http://httpbin.org/ip
响应体:

状态码: 200
Location 头:
响应体:
{
"origin": "23.131.128.26, 23.131.128.26"
}
清单 3-6
通过在 HttpClient 中设置重定向策略来跟随服务器重定向
```

该程序使用了 [`http://httpbin.org/redirect-to`](http://httpbin.org/redirect-to) 这个 URL。该 URL 会重定向到 `uri` 查询字符串参数中指定的新 URL，并返回 `status_code` 查询字符串参数中指定的响应状态码。你使用了 [`http://httpbin.org/ip`](http://httpbin.org/ip) 作为重定向 URL，并将状态码设为 301。同一个请求被发送了两次。第一次，你使用 `NEVER` 作为重定向策略发送请求，这是 `HttpClient` 的默认策略。你收到了预期的响应，状态码为 301，并且 `Location` 头被设置为我们请求中设置的重定向 URL。第二次，你将 `HttpClient` 的重定向策略设置为 `NORMAL`，这允许 `HttpClient` 跟随重定向。重定向后，你从 [`http://httpbin.org/ip`](http://httpbin.org/ip) URL 收到了响应，该响应返回了发起请求的客户端的 IP 地址。

#### 发起异步请求

当你使用 `HttpClient` 的 `send()` 方法时，调用会被阻塞，直到收到响应。你可能出于多种原因不希望这样做。HTTP 请求的异步处理能带来很多好处，例如提高性能和响应能力。你的程序可以在等待响应时执行其他操作。你可能需要向多个服务器发送相同的请求，并希望在收到所有或部分服务器的响应后恢复处理。通过 HTTP 请求的异步处理，可以非常容易地实现这一点。

使用 `HttpClient` 类的 `sendAsync()` 方法可以向服务器异步发送请求。该方法返回一个 `CompletableFuture<HttpResponse<T>>`，其中 `T` 是响应体类型。例如，如果你使用一个将响应体处理为 `String` 的 body handler，该方法将返回一个 `CompletableFuture<HttpResponse<String>>`。以下代码片段异步地将响应体打印到标准输出：

```
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 使用 HTTP GET 方法
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.build();
// 打印响应体
client.sendAsync(request, BodyHandlers.ofString())
.thenApply(HttpResponse::body)
.thenAccept(System.out::println);
```

这段代码片段中，除了对 `thenApply()` 和 `thenAccept()` 方法的调用之外，并无特别之处。有关这些方法的更多详细信息，请参考 `java.util.concurrent.CompletableFuture<T>` 类的文档。`thenApply()` 方法接受一个 `Function<T,R>` 作为参数，该函数接受类型为 `HttpResponse<String>` 的响应对象作为参数，并返回一个 `String` 类型的响应体。`thenAccept()` 方法打印响应体，该响应体是从上一个完成阶段传递过来的。`sendAsync()` 方法使用由 `Executor` 提供的独立线程来处理请求。你也可以通过 `HttpClient.Builder` 接口的 `executor()` 方法配置 `HttpClient`，从而使用你自己的 `Executor`。

`sendAsync()` 方法会立即返回，请求和响应稍后在独立的线程中处理。如果你将前面的语句作为 `main()` 方法中的最后一条语句，你的程序可能看不到任何输出。当你的请求被发送、响应在其他守护线程中被处理时，`"main"` 这个非守护线程已经终止，这反过来会导致你的程序终止。你可以通过调用 `get()` 或 `join()` 方法来解决这个问题，这两个方法会返回最后一个完成阶段的结果。两者的区别在于，`get()` 方法会抛出受检异常，而 `join()` 方法会抛出非受检异常。以下代码片段解决了程序不输出任何内容的问题：

```
// 打印响应体
client.sendAsync(request, BodyHandlers.ofString())
.thenApply(HttpResponse::body)
.thenAccept(System.out::println)
.join();
```

清单 3-7 包含一个完整的程序，用于异步处理请求及其响应。你得到的输出可能有所不同。


```markdown

```
// AsynchronousRequest.java
package com.jdojo.http.client;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
public class AsynchronousRequest {
public static void main(String[] args) {
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 使用 HTTP GET 方法
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://httpbin.org/ip"))
.build();
// 异步发送请求并打印响应体
client.sendAsync(request, BodyHandlers.ofString())
.thenApply(HttpResponse::body)
.thenAccept(System.out::println)
.join();
}
}
{
"origin": "23.131.128.26, 23.131.128.26"
}
清单 3-7
异步处理 HTTP 请求和响应
```

如果你知道如何使用 `CompletableFuture<T>` 类，异步处理请求能让你在处理流程上拥有高度的灵活性。假设你想异步处理一个请求，并在响应可用时操作 `HttpResponse` 对象。以下代码片段展示了如何实现这一点：

```
// 异步处理请求并等待响应
HttpResponse response
= client.sendAsync(request, BodyHandlers.ofString())
.join();
/* 在此处使用 response 变量访问响应详情 */
```

你还可以异步处理多个并发请求。假设你想使用以下三个 URL 从 [`http://httpbin.org`](http://httpbin.org) 下载三张图片：

*   [`http://httpbin.org/image/jpeg`](http://httpbin.org/image/jpeg)

*   [`http://httpbin.org/image/png`](http://httpbin.org/image/png)

*   [`http://httpbin.org/image/svg`](http://httpbin.org/image/svg)

首先，将三个 URL 及其对应的 HTTP 请求存储在一个列表中，如下所示：

```
// 将所有 URI 收集到一个 List 中
List uriList = List.of(
URI.create("http://httpbin.org/image/jpeg"),
URI.create("http://httpbin.org/image/png"),
URI.create("http://httpbin.org/image/svg"));
// 为每个 URI 构建请求并存储到 List 中
List requestList = uriList.stream()
.map(HttpRequest::newBuilder)
.map(reqBuilder -> reqBuilder.build())
.collect(toList());
```

以下代码片段异步发送所有请求，并将下载的文件保存到当前目录：

```
// 异步发送所有请求并等待它们完成
CompletableFuture.allOf(
requestList.stream()
.map(req -> {
String[] elems = req.uri().getPath().split("/");
String fileName = "image." + elems[elems.length - 1];
return client.sendAsync(req,
BodyHandlers.ofFile(Paths.get(fileName)));
}).toArray(CompletableFuture[]::new)
).thenRun(() -> System.out.println("所有下载已完成。"))
.join();
```

这虽然是一条语句，但初看可能令人望而生畏。让我们将其分解为逻辑部分。`CompletableFuture<T>` 类的 `allOf()` 方法接受 `CompletableFuture<?>` 类型的可变参数，并返回一个 `CompletableFuture<Void>`。当所有给定的 `CompletableFuture` 完成时，返回的 `CompletableFuture<Void>` 也随之完成。你的首要目标是创建一个 `CompletableFuture<HttpResponse<Path>>` 数组，其中包含发送到服务器的所有异步请求。前面代码片段中的以下部分实现了这一点：

```
requestList.stream()
.map(req -> {
String[] elems = req.uri().getPath().split("/");
String fileName = "image." + elems[elems.length - 1];
return client.sendAsync(req,
BodyHandlers.ofFile(Paths.get(fileName)));
}).toArray(CompletableFuture[]::new)
```

该代码通过调用 `HttpResponse.BodyHandlers` 类的 `ofFile()` 方法获取一个 `BodyHandler<Path>`。你需要为每个下载的图片传递一个唯一的文件名。我提取了 URL 的最后一部分，例如 `jpeg`、`png` 和 `svg`，并将其用作名为 `image` 的文件的扩展名。因此，`jpeg` 文件将存储在当前目录的 `image.jpeg` 中，`png` 文件将存储在 `image.png` 中，以此类推。

`allOf()` 方法返回一个 `CompletableFuture<Void>`，当所有提交的任务完成时，该对象也随之完成。代码在其上调用 `thenRun()` 方法来打印一条消息，表明所有下载已完成。最后，你需要调用 `join()` 方法，以确保在程序退出之前所有响应都已处理完毕。清单 3-8 包含了完整的程序。

```
// MultipleRequests.java
package com.jdojo.http.client;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse.BodyHandlers;
import java.nio.file.Paths;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import static java.util.stream.Collectors.toList;
public class MultipleRequests {
public static void main(String[] args) {
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 将所有 URI 收集到一个 List 中
List uriList = List.of(
URI.create("http://httpbin.org/image/jpeg"),
URI.create("http://httpbin.org/image/png"),
URI.create("http://httpbin.org/image/svg"));
// 为每个 URI 构建请求并存储到 List 中
List requestList = uriList.stream()
.map(HttpRequest::newBuilder)
.map(reqBuilder -> reqBuilder.build())
.collect(toList());
// 异步发送所有请求并等待它们完成
CompletableFuture.allOf(
requestList.stream()
.map(req -> {
String[] elems =
req.uri().getPath().split("/");
String fileName =
"image." + elems[elems.length - 1];
return client.sendAsync(req,
BodyHandlers.ofFile(Paths.get(fileName)));
}).toArray(CompletableFuture[]::new)
).thenRun(() -> System.out.println("所有下载已完成。"))
.join();
}
}
所有下载已完成。
清单 3-8
异步处理多个 HTTP 请求和响应
```

```


#### 服务器推送

服务器推送是 HTTP/2 支持的一项功能，服务器通过该功能主动向客户端发送额外的资源。客户端发送一个请求。服务器通过检查客户端的初始请求，判断出客户端除了初始请求的响应外，还需要额外的资源。服务器会响应初始请求，并同时发送额外的响应。服务器推送的典型用例是在浏览器中显示网页。一个网页由多个资源组成，例如样式表、JavaScript 代码、图片、嵌入式字体等。如果你请求一个网页，支持 HTTP/2 的服务器可能会向你发送该网页的 HTML 页面以及该网页所需的所有其他资源。这样，你只需发出一个请求就能获得多个资源。如果没有服务器推送，浏览器通常需要多次往返才能获取一个网页的所有资源。在 HTTP/2 之前，有几种变通方法用来避免这些往返。

Java 中的 HTTP Client API 支持服务器推送。当你使用 `HttpClient` 发出请求时，服务器可能会以推送承诺的形式向你发送额外的响应。你可以选择接受或拒绝推送承诺。服务器会为每个推送承诺合成一个 `HttpRequest` 和一个 `HttpResponse`。你必须提供一个推送承诺处理器，每当 `HttpClient` 收到推送承诺时，该处理器就会被调用。

服务器推送涉及服务器推送内容以及客户端消费这些内容。浏览器会为你处理服务器推送。在 Java 程序中，你需要编写逻辑来处理服务器推送——最重要的是，你需要提前知道服务器将向你的客户端推送何种资源，这样你才能在代码中知道如何处理它们。

你需要有一个向客户端发送服务器推送的服务器。在本例中，我将使用一个 URL 为 [`www.angular.io`](http://www.angular.io) 的网页。你可以在 [`https://http2.netray.io/data.php`](https://http2.netray.io/data.php) 找到使用服务器推送的域名列表。

要处理服务器推送，你需要使用 `HttpClient` 类中接受 `HttpResponse.PushPromiseHandler<T>` 的 `sendAsync()` 版本来发送请求。该方法的声明如下：

```
CompletableFuture> sendAsync (HttpRequest request, HttpResponse.BodyHandler responseBodyHandler, HttpResponse.PushPromiseHandler pushPromiseHandler)
```

### 提示

服务器推送是服务器主动向客户端推送资源的操作。HTTP Client API 将一次服务器推送称为*推送承诺*。也就是说，如果响应一个请求，服务器推送了五个额外的资源，那么你将收到五个推送承诺。

推送承诺处理器是 `HttpResponse.PushPromiseHandler<T>` 接口的一个实例。你需要为该接口的 `applyPushPromise()` 方法提供实现。该方法声明如下：

```
applyPushPromise (HttpRequest initiatingRequest,
HttpRequest pushPromiseRequest,
Function, CompletableFuture>> acceptor)
```

推送承诺处理器的 `applyPushPromise()` 方法会为从服务器接收到的每个推送承诺调用一次。该方法接受三个参数——第一个是你发送给服务器的初始请求，第二个是接收到的服务器推送的合成请求。第三个参数是一个名为 acceptor 的函数`<T,R>`。如果你想接受推送承诺，你需要调用 `acceptor` 上的 `apply()` 方法，并向其传递一个 `HttpReponse.BodyHandler<T>`，该处理器将用于处理该推送承诺的响应体。`apply()` 方法返回一个 `CompletableFuture<HttpResponse<T>>`，当它完成时，会将推送承诺作为一个 `HttpResponse<T>` 对象提供。

清单 3-9 包含一个推送承诺处理器的简单实现，用于将推送承诺的响应体作为 `String` 处理。你将在下一个示例中使用它。当客户端收到推送承诺时，其 `applyPushPromise()` 方法将被调用。我让这个方法非常简单，它打印推送承诺的合成请求的 URI。它通过调用 acceptor 函数的 `apply()` 方法来接受推送承诺。请注意，推送承诺中的响应将在稍后处理。当响应体可用时，从 `apply()` 方法返回的 `CompletableFuture` 就完成了。代码调用 `CompletableFuture` 上的 `thenAccept()` 方法来打印推送承诺请求 URI 和响应状态码。在实际应用中，`apply()` 方法内的代码将以更复杂的方式处理响应。

```
// ServerPushPromiseHandler.java
package com.jdojo.http.client;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.concurrent.CompletableFuture;
import java.util.function.Function;
import static java.lang.System.out;
public class ServerPushPromiseHandler implements
HttpResponse.PushPromiseHandler {
@Override
public void applyPushPromise(HttpRequest initiatingRequest,
HttpRequest pushPromiseRequest,
Function,
CompletableFuture>> acceptor) {
// 打印推送承诺请求及其初始请求的 URI
out.printf("收到推送承诺请求 URI: %s%n"
+ "推送承诺初始请求 URI: %s%n%n",
pushPromiseRequest.uri(),
initiatingRequest.uri());
// 通过调用传入此方法的函数上的 apply() 方法来接受推送承诺
acceptor.apply(HttpResponse.BodyHandlers.ofString())
.thenAccept(response -> {
// 打印推送承诺响应详情
out.printf("%n 已处理推送承诺请求 URI: %s%n"
+ "推送响应状态码: %d%n",
pushPromiseRequest.uri(),
response.statusCode());
});
}
}
清单 3-9
PushPromiseHandler 的一个实现
```

清单 3-10 包含一个测试服务器推送的程序。我使用 [`www.angular.io`](http://www.angular.io) URL 发送一个 HTTP GET 请求，并期望服务器推送。如果此 URL 对你无效，请将其替换为另一个提供服务器推送功能的 URL。你可能会得到不同的输出。



```
// ServerPush.java
package com.jdojo.http.client;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import static java.net.http.HttpClient.Redirect.ALWAYS;
public class ServerPush {
public static void main(String[] args) {
// 创建一个允许重定向的 HttpClient
HttpClient client = HttpClient.newBuilder()
.followRedirects(ALWAYS)
.build();
// 构建一个请求
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("http://www.angular.io"))
.build();
// 异步发送请求，允许服务器推送
HttpResponse response = client.sendAsync(request,
BodyHandlers.ofString(),
new ServerPushPromiseHandler()
).join();
// 打印响应详情
System.out.printf("发起请求的 URI: %s%n"
+ "响应的请求 URI: %s%n"
+ "响应状态码: %d%n",
request.uri(),
response.request().uri(),
response.statusCode());
try {
// 等待五秒钟，让推送承诺完成
// 如果看不到与推送承诺响应相关的输出，请增加等待时间，例如增加到 10 秒。
Thread.sleep(5000);
} catch (InterruptedException e) {
e.printStackTrace();
}
}
}
收到推送承诺请求 URI: https://angular.io/generated/navigation.json
推送承诺发起请求 URI: http://www.angular.io
收到推送承诺请求 URI: https://angular.io/generated/docs/index.json
推送承诺发起请求 URI: http://www.angular.io
发起请求的 URI: http://www.angular.io
响应的请求 URI: https://angular.io/
响应状态码: 200
已处理推送承诺请求 URI: https://angular.io/generated/docs/index.json
推送响应状态码: 200
已处理推送承诺请求 URI: https://angular.io/generated/navigation.json
推送响应状态码: 200
清单 3-10
一个测试服务器推送的程序
```

在输出中有几个值得注意的点：

*   `HttpClient` 被配置为始终遵循服务器发出的重定向。

*   你创建了一个 URI 为 [`www.angular.io`](http://www.angular.io) 的 `HttpRequest`。这是你的发起 URI。

*   服务器将发起请求重定向到 [`https://angular.io/`](https://angular.io/)。服务器推送的两个资源的主机是 [`https://angular.io`](https://angular.io)。在服务器重定向期间发生了两件事——协议从 HTTP 变为 HTTPS，主机从 [`www.angular.io`](http://www.angular.io) 变为 `angular.io`。端口从 80（HTTP）变为 443（HTTPS）是由协议变更隐含的。

*   我们发起请求和推送承诺请求的主机和端口不同。我们仍然决定无条件接受推送承诺。

*   我将在下一个示例中使用这些观察结果，在该示例中我将改进处理服务器推送的逻辑。

除了两个新部分之外，你已熟悉此程序中的大部分代码。第一部分是如何向服务器发送请求，第二部分是让主线程等待 5 秒。相关代码如下：

```
// 异步发送请求，允许服务器推送
HttpResponse response = client.sendAsync(request,
BodyHandlers.ofString(),
new ServerPushPromiseHandler()
).join();
```

请注意，你正在使用 `HttpClient` 类的 `sendAsync()` 方法的一个版本，该版本接受一个 `PushPromiseHandler` 作为第三个参数。你正在传递一个 `ServerPushPromiseHandler` 类的实例作为推送承诺处理器。当 `join()` 方法返回时，初始请求的响应体已经可用。此时，你已经收到了推送承诺请求，但不一定收到了它们的响应。推送承诺的响应及其响应体将在稍后某个时间可用。在我们的程序中，我们没有等待推送承诺响应可用的逻辑。我任意选择使用以下语句让主线程在 `main()` 方法末尾等待 5 秒：

```
Thread.sleep(5000);
```

如果你看到输出的前四行，但没有看到最后四行，则需要增加此等待时间。

### 提示

推送承诺处理器被调用来接受或拒绝一个推送承诺，直到发起服务器推送的请求的响应体被完全接收。在响应体被完全接收后从服务器收到的任何推送承诺都将被拒绝。

让我们改进之前的服务器推送示例。我不喜欢使用 `Thread.sleep()` 方法来让推送承诺完成。我们不得不这样做，是因为我们没有保存清单 3-9 中推送承诺处理器的 `applyPushPromise()` 方法内部从 `apply()` 方法返回的 `CompletableFuture`。缺陷在于我们实现推送承诺处理器的方式。有个好消息！`HttpResponse.PushPromiseHandler<T>` 接口包含一个 `static of()` 方法，它允许你创建一个推送承诺处理器，该处理器会将所有推送承诺请求和推送承诺响应的 `CompletableFuture` 存储在一个并发映射中。其声明如下：

```
HttpResponse.PushPromiseHandler of (Function> pushPromiseHandler,
ConcurrentMap>> pushPromisesMap)
```

不要被这个方法庞大的声明所吓倒。它使用起来非常简单。第一个参数是一个函数，它接受推送承诺请求并返回一个响应体处理器。每收到一个推送承诺，就会调用一次此函数。

第二个参数是一个并发映射，用于累积所有推送承诺。其键是推送承诺请求，值是代表推送承诺响应的 `CompletableFuture`。一旦你收到发起请求的响应体，就可以等待此并发映射中的所有值完成，之后，你可以确信你已经完全收到了所有推送承诺的响应体。以下代码片段创建了一个并发映射：

```
// 创建一个并发映射来累积所有推送承诺
ConcurrentMap>>
pushPromisesMap = new ConcurrentHashMap();
```

以下代码片段创建了一个推送承诺处理器：

```
// 创建一个推送承诺处理器
PushPromiseHandler pushPromiseHandler
= PushPromiseHandler.of(pushPromiseRequest -> {
out.printf("收到推送承诺请求 URI: %s%n"
+ "推送承诺发起请求 URI: %s%n%n",
pushPromiseRequest.uri(),
request.uri());
return BodyHandlers.ofString();
}, pushPromisesMap);
```

`of()` 方法的第一个参数是一个函数，它打印推送承诺详情并返回一个 `String` 类型的响应体处理器。第二个参数是你希望在其中累积所有推送承诺的并发映射。

当你以这种方式创建推送承诺处理器时，你无法控制接受和拒绝推送承诺。推送承诺处理器将使用以下规则来接受和拒绝推送承诺：

*   如果并发映射中已经存在一个键等于收到的推送承诺请求的条目，则该推送请求将被拒绝/取消。

*   如果推送请求与其发起请求的来源不同，则该推送请求将被拒绝/取消。

*   否则，推送请求将被接受。

第二条规则需要稍作解释。如果你的发起请求 URI 是 `http://host/path`，则推送请求必须来自 `http://host`。推送承诺处理器会比较发起请求和推送请求的主机和端口。如果两者不匹配，则推送承诺被拒绝。



让我们回顾一下清单 3-10 中之前的服务器推送示例。发起请求的 URI 是 [`www.angular.io`](http://www.angular.io)，而推送承诺请求的 URI 类似于 `https://angular.io/<path>`。这两个请求的端口（http 与 https）和主机（[`www.angular.io`](https://www.angular.io) 与 `angular.io`）都不匹配。如果你使用由 `HttpResponse.PushPromiseHandler<T>` 接口的静态 `of()` 方法创建的推送承诺处理器，推送承诺将被拒绝。在下一个示例中，你将使用 [`https://angular.io`](https://angular.io) 作为发起请求的 URI，以避免推送承诺被自动拒绝的问题。

清单 3-11 包含了完整的代码，演示了如何使用由 `HttpResponse.PushPromiseHandler<T>` 接口的 `static of()` 方法创建的推送承诺处理器。代码中有大量注释来解释每一步。请注意，在这个示例中，`main()` 方法的末尾没有调用 `Thread.sleep()`！

```
// ImprovedServerPush.java
package com.jdojo.http.client;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
import java.net.http.HttpResponse.PushPromiseHandler;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;
import static java.lang.System.out;
public class ImprovedServerPush {
public static void main(String[] args) throws InterruptedException {
// 创建一个使用默认配置的 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 构建一个请求
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("https://angular.io"))
.build();
// 创建一个并发映射来累积所有推送承诺
ConcurrentMap>>
pushPromisesMap  = new ConcurrentHashMap();
// 创建一个推送承诺处理器
PushPromiseHandler pushPromiseHandler
= PushPromiseHandler.of(pushPromiseRequest -> {
out.printf("收到推送承诺请求 URI: %s%n"
+ "推送承诺发起请求 URI: %s%n%n",
pushPromiseRequest.uri(),
request.uri());
return BodyHandlers.ofString();
}, pushPromisesMap);
// 异步发送请求，允许服务器推送
HttpResponse response = client.sendAsync(request,
BodyHandlers.ofString(),
pushPromiseHandler
).join();
// 打印响应详情
out.printf("发起请求 URI: %s%n"
+ "响应的请求 URI: %s%n"
+ "响应状态码: %d%n",
request.uri(),
response.request().uri(),
response.statusCode());
// 此时所有推送承诺都已存入 pushPromisesMap。
// 但它们的响应可能尚未可用。
CompletableFuture[] allPushPromises
= pushPromisesMap.values()
.toArray(CompletableFuture[]::new);
// 等待所有推送承诺的响应体可用
CompletableFuture.allOf(allPushPromises)
.join();
// 打印推送承诺的响应详情
pushPromisesMap.values()
.stream()
.map(CompletableFuture::join)
.forEach(pushResponse -> {
out.printf("%n 已处理的推送承诺请求 URI: %s%n"
+ "推送响应状态码: %d%n",
pushResponse.request().uri(),
pushResponse.statusCode());
});
}
}
收到推送承诺请求 URI: https://angular.io/generated/navigation.json
推送承诺发起请求 URI: https://angular.io
收到推送承诺请求 URI: https://angular.io/generated/docs/index.json
推送承诺发起请求 URI: https://angular.io
发起请求 URI: https://angular.io
响应的请求 URI: https://angular.io
响应状态码: 200
已处理的推送承诺请求 URI: https://angular.io/generated/docs/index.json
推送响应状态码: 200
已处理的推送承诺请求 URI: https://angular.io/generated/navigation.json
推送响应状态码: 200
清单 3-11
测试服务器推送程序的改进版本
```

## 使用 HTTP 基本认证

服务器可能需要认证才能允许访问请求的资源。HTTP 提供了一种质询-响应类型的认证框架。客户端向服务器请求一个资源。服务器向客户端发出质询，要求提供特定类型的认证。客户端向服务器提供认证信息。服务器在验证认证信息后，允许或拒绝对请求资源的访问。认证有多种类型，例如基本认证、Bearer 认证、摘要认证等。

HTTP 客户端 API 为基本认证提供了内置支持。在这种认证方式中，服务器向客户端响应一个 401（未授权）HTTP 状态码和一个名为 `WWW-Authenticate` 的响应头，其中包含至少一个质询。通常，客户端会在名为 `Authorization` 的请求头中响应一个 base64 编码的用户名和密码，其形式可能如下：

```
Authorization: Basic a2lzaG9yaTpzaGFyYW4=
```

让我们看一个基本认证的示例。你将使用以下 URL，该 URL 会向客户端发出基本认证的质询：

```
https://httpbin.org/basic-auth/[username]/[password]
```

这里，`[username]` 是用户名，`[password]` 是认证所需的密码。当服务器发出质询时，你需要提供相同的用户名和密码才能使认证成功。在示例中，我使用了：

```
https://httpbin.org/basic-auth/kishori/sharan
```

如果你在浏览器中输入此 URL，系统会提示你输入用户名和密码。你需要输入 `kishori` 作为用户名，`sharan` 作为密码。要使用不同的用户名/密码，请相应地更改此 URL。

`java.net.Authenticator` 类的实例用于通过用户名/密码提供基本认证。你需要继承 `Authenticator` 并重写 `getPasswordAuthentication()` 方法。该方法返回一个 `PasswordAuthentication` 类的实例，该类只是用户名和密码的持有者。你将使用 `HttpClient.Builder` 接口的 `authenticator()` 方法将此认证器的实例设置到 `HttpClient` 对象中。当 `HttpClient` 需要对请求进行认证时，它将调用你的认证器的 `getPasswordAuthentication()` 方法，并将 `PasswordAuthentication` 中返回的用户名/密码发送给服务器。

清单 3-12 包含了一个名为 `BasicAuthenticator` 的类的代码。你通过提供用户名和密码来创建其实例。当需要认证时，其 `getPasswordAuthentication()` 方法会打印一条消息，并在 `PasswordAuthentication` 中返回用户名和密码。通常，在提供用户名/密码之前，你会检查请求的认证信息，例如网站名称。如果用户需要提供此信息，你可能希望用一个对话框提示用户输入用户名/密码。我让这个示例保持简单，直接返回提供的用户名/密码。

```
// BasicAuthenticator.java
package com.jdojo.http.client;
import java.net.Authenticator;
import java.net.PasswordAuthentication;
public class BasicAuthenticator extends Authenticator {
private final String userName;
private final char[] password;
public BasicAuthenticator(String userName, char[] password) {
this.userName = userName;
this.password = password;
}
@Override
protected PasswordAuthentication getPasswordAuthentication() {
System.out.println(this.getRequestingURL()
+ " 正在请求认证: "
+ this.getRequestingPrompt());
return new PasswordAuthentication(userName, password);
}
}
清单 3-12
一个基本的 HTTP 认证器
```

清单 3-13 包含了一个完整的程序，用于演示基本认证。你可能会得到不同的输出。程序重要部分的讨论在输出之后。



```
// BasicAuthenticationTest.java
package com.jdojo.http.client;
import java.net.Authenticator;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;
public class BasicAuthenticationTest {
public static void main(String[] args) {
// 设置默认认证器
String username = "kishori";
char[] password = new char[]{'s', 'h', 'a', 'r', 'a', 'n'};
Authenticator.setDefault(
new BasicAuthenticator(username, password));
// 创建一个带有认证器的 HttpClient
HttpClient client = HttpClient.newBuilder()
.authenticator(Authenticator.getDefault())
.build();
// 使用 GET HTTP 方法
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("https://httpbin.org/basic-auth/"
+ username + "/" + new String(password)))
.build();
// 异步发送请求到服务器
HttpResponse response
= client.sendAsync(request, BodyHandlers.ofString())
.join();
// 打印响应状态和正文
System.out.println("响应状态: " + response.statusCode());
System.out.println("响应正文:\n" + response.body());
// 打印前一个响应的详细信息
response.previousResponse()
.ifPresent(previousResopnse -> {
System.out.println("前一个响应状态: "
+ previousResopnse.statusCode());
System.out.println("前一个响应正文:\n"
+ previousResopnse.body());
System.out.println("前一个响应头:");
previousResopnse.headers()
.map()
.entrySet()
.forEach(System.out::println);
});
}
}
https://httpbin.org/basic-auth/kishori/sharan 正在请求认证: Fake Realm
响应状态: 200
响应正文:
{
"authenticated": true,
"user": "kishori"
}
前一个响应状态: 401
前一个响应正文:
null
前一个响应头:
access-control-allow-credentials=[true]
access-control-allow-origin=[∗]
connection=[keep-alive]
content-length=[0]
date=[Sun, 28 Jul 2019 00:05:49 GMT]
referrer-policy=[no-referrer-when-downgrade]
server=[nginx]
www-authenticate=[Basic realm="Fake Realm"]
x-content-type-options=[nosniff]
x-frame-options=[DENY]
x-xss-protection=[1; mode=block]
清单 3-13
测试基本 HTTP 认证
```

该程序创建了一个`BasicAuthenticator`类的实例，并向其传递`kishori/sharan`作为用户名/密码。你通过调用`Authenticator`类的`setDefault()`方法设置默认认证器，并通过调用其`getDefault()`方法获取它。你在`HttpClient`实例中设置认证器。程序中使用了此逻辑：

```
// 设置默认认证器
String username = "kishori";
char[] password = new char[]{'s', 'h', 'a', 'r', 'a', 'n'};
Authenticator.setDefault(new BasicAuthenticator(username, password));
// 创建一个带有认证器的 HttpClient
HttpClient client = HttpClient.newBuilder()
.authenticator(Authenticator.getDefault())
.build();
```

输出的第一行表明认证器已被使用。程序打印了响应状态码和正文。在基本认证的情况下，第一个请求会收到一个状态码为 401、无正文且包含`WWW-Authenticate`头的响应。如果完成一个请求涉及多个响应，你可以使用`HttpResponse<T>`接口的`previousResponse()`方法获取前一个响应的引用，该方法返回一个`Optional<HttpResponse<T>>`。输出的最后部分显示服务器曾以 401 状态码和`WWW-Authenticate`头进行响应。

你也可以使用 HTTP 客户端 API 进行 Bearer HTTP 认证。如果你收到一个 401 状态码和包含`Bearer`值的`WWW-Authenticate`头，你可以简单地重新发送一个带有承载令牌的`Authorization`头的请求。以下代码片段创建了一个带有`my-secret-token`承载令牌的请求：

```
// 在 Authorization 头中准备一个带有承载令牌的请求
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create("https://httpbin.org/bearer"))
.header("Authorization", "Bearer my-secret-token")
.build();
```

你可以编写一个程序将此请求发送到服务器并打印响应详细信息，作为练习。

## 使用 WebSocket

WebSocket 协议在两个端点（一个客户端端点和一个服务器端点）之间提供双向通信。术语*端点*指代使用 WebSocket 协议的连接中的任意一方。客户端端点发起连接，服务器端点接受连接。该连接是双向的，这意味着服务器端点可以自行向客户端端点推送消息。在此上下文中，你还会遇到另一个术语，称为*对端*。对端就是连接的另一端。例如，对于客户端端点，服务器端点是对端；对于服务器端点，客户端端点是对端。WebSocket*会话*表示一个端点与单个对端之间的一系列交互。

WebSocket 协议可以分为三个部分：

*   打开握手

*   数据交换

*   关闭握手

客户端向服务器发起打开握手。握手过程使用 HTTP 协议，并附带一个升级到 WebSocket 协议的请求。服务器以升级响应来回应打开握手。一旦握手成功，客户端和服务器便开始交换消息。消息交换可以由客户端或服务器中的任一方发起。最后，任一端点都可以发送关闭握手；对端以关闭握手作为响应。一旦关闭握手成功，WebSocket 即被关闭。

HTTP 客户端 API 支持创建 WebSocket 客户端端点。要有一个使用 WebSocket 协议的完整示例，你需要同时拥有服务器端点和客户端端点。以下章节将解释如何创建客户端端点以与服务器端点交互的 WebSocket 细节。你将使用一个已构建好的服务器端点，该端点可在网上获取，如下一节所述。

### WebSocket 服务器端点

HTTP 客户端 API 提供对创建 WebSocket 客户端的支持，但不支持创建服务器。如何创建 WebSocket 服务器端点超出了本书的范围。我将在示例中使用一个免费可用的服务器端点。我将使用的 WebSocket 服务器端点是`ws://demos.kaazing.com/echo`。有关此端点的更多详细信息，请访问[`www.websocket.org/echo.html`](http://www.websocket.org/echo.html)。你也可以使用此网页测试该端点。此端点充当回显服务器。当你发送一条消息时，它会将消息回显给你。



### 创建客户端端点

开发 WebSocket 客户端端点涉及使用 `HttpClient` 类和 `WebSocket` 接口，它们都属于 HTTP 客户端 API。`WebSocket` 接口包含以下两个嵌套的 `static` 接口：

*   `WebSocket.Builder`

*   `WebSocket.Listener`

`WebSocket` 接口的实例代表一个 WebSocket 客户端端点。构建器（`WebSocket.Builder` 接口的实例）用于创建 `WebSocket` 实例。`HttpClient` 类的 `newWebSocketBuilder()` 方法返回一个 `WebSocket.Builder`。

当客户端端点发生事件时，例如，打开握手完成、消息到达、关闭握手等，通知会被发送到监听器（`WebSocket.Listener` 接口的实例）。该接口为每种通知类型都包含一个 `default` 方法。你需要创建一个实现此接口的类，并仅为那些你感兴趣接收通知的事件对应的方法提供实现。在创建 `WebSocket` 实例时，你需要指定一个监听器。

当你向对等端发送关闭消息时，可以指定一个关闭状态码。`WebSocket` 接口包含一个 `int` 类型的 `NORMAL_CLOSURE` 常量。它代表 WebSocket `Close` 消息的状态码 (1000)，表示连接已正常关闭。这意味着建立连接的目的已经达成。

回顾一下，WebSocket 提供了客户端和服务器之间的双向通信。该 API 提供了两种类型的方法——一种用于向服务器发送消息，另一种用于从服务器接收消息。你可以使用 `WebSocket` 实例的 `sendXxx()` 方法向服务器发送消息。当从服务器接收到消息时，会调用 `WebSocket.Listener` 实例的某个 `onXxx()` 方法。以下各节将详细描述设置客户端端点的所有步骤。

#### 创建监听器

监听器是 `WebSocket.Listener` 接口的一个实例。创建监听器涉及创建一个实现此接口的类。该接口中的所有方法都是 `default` 方法。监听器的所有方法都会传入与之关联的 `WebSocket` 的引用。你可以将监听器中的方法分为以下两类：

*   连接打开和错误报告方法

*   消息接收方法

以下两个方法属于第一类：

*   `void onOpen`​`(WebSocket webSocket)`

*   `void onError`​`(WebSocket, Throwable error)`

当 WebSocket 连接建立时，会调用其监听器的 `onOpen()` 方法。这是监听器中被调用的第一个方法，且仅被调用一次。通常，你会打印一条连接已建立的信息性消息，并请求接收更多消息。该方法的默认实现会请求再接收一条消息。使用 `WebSocket` 接口的 `request(long n)` 方法来请求消息。

```
// 允许再接收一条消息
webSocket.request(1);
```

请注意，消息可以分部分接收。对 `WebSocket` 调用 `request(n)` 意味着接收消息的方法可以被调用 `n` 次。假设一条文本消息分三部分传递。调用 `request(1)` 将只允许调用 `onText()` 方法一次。通常，你会在 `onOpen()` 方法中以及当你接收到消息时（例如在 `onBinary()` 和 `onText()` 方法中）调用 `request(1)`。

### 提示

如果服务器发送的消息数量超过请求的数量，消息会在 TCP 连接上排队，并可能最终通过 TCP 流量控制强制发送方停止发送更多消息。在适当的时间使用适当的参数值调用 `request(long n)` 方法非常重要，这样你的监听器才能持续接收来自服务器的消息。一个常见的错误是在监听器中重写 `onOpen()` 方法，却没有调用 `webSocket.request(1)` 方法，这会阻止你接收来自服务器的消息。

当发生错误时，会调用 `onError()` 方法。在调用 `onError()` 方法之后，监听器上的其他方法都不会再被调用。第二个 `error` 参数包含错误详情。如果此方法抛出异常，其行为是未定义的。

以下方法属于第二类——消息接收方法。当从服务器接收到特定类型的消息时，会调用这些方法：

*   `CompletionStage<?> onBinary`​`(WebSocket webSocket, ByteBuffer data, boolean last)`

*   `CompletionStage<?> onText`​`(WebSocket webSocket, CharSequence data, boolean last)`

*   `CompletionStage<?> onPing`​`(WebSocket webSocket, ByteBuffer message)`

*   `CompletionStage<?> onPong`​`(WebSocket webSocket, ByteBuffer message)`

*   `CompletionStage<?> onClose`​`(WebSocket webSocket, int statusCode, String reason)`

当端点从对等端接收到关闭消息时，会调用 `onClose()` 方法。这是对监听器的最后一次通知。从此方法抛出的异常会被忽略。默认实现不执行任何操作。通常，你需要向对等端发送一条关闭消息以完成关闭握手。

当此端点从对等端接收到 `Ping` 消息时，会调用 `onPing()` 方法。客户端和服务器端点都可以发送 `Ping` 消息。`WebSocket` 接口的默认实现会向对等端发送一条 `Pong` 消息作为对 `Ping` 消息的响应，且内容相同。因此，当你创建监听器时，通常不需要为 `onPing()` 方法提供实现。

当端点从对等端接收到 `Pong` 消息时，会调用 `onPong()` 方法。`Pong` 消息通常作为对先前发送的 `Ping` 消息的响应而接收。端点也可能接收到未经请求的 `Pong` 消息。`onPong()` 方法的默认实现会在监听器上再请求一条消息，并且不执行其他操作。

当从对等端分别接收到二进制消息和文本消息时，会调用 `onBinary()` 和 `onText()` 方法。二进制和文本消息可以分部分发送。这些方法的第三个 `boolean` 参数指示它是否是消息的最后一部分。值为 `false` 表示它不是最后一部分，稍后将接收到消息的更多部分。值为 `true` 表示它是消息的最后一部分。在这些方法内部，当最后一个参数为 `false` 时，你累积消息部分；当所有部分都接收到（由 `last` 参数的 `true` 值指示）时，处理消息。

请注意，当接收到消息时调用的方法会返回一个 `CompletionStage<?>`。返回的 `CompletionStage` 的完成向 `WebSocket` 指示消息已被处理，并且它可以回收用于传递消息的资源。从这些方法返回 `null` 表示消息处理已完成。

让我们构建一个 WebSocket 监听器，你将在后续章节中使用它来创建一个 `WebSocket`。清单 3-14 包含了我们 `WebSocket` 监听器的完整代码。其组件的描述紧随代码之后。



```
// WebSocketEchoListener.java
package com.jdojo.http.client;
import java.net.http.WebSocket;
import java.nio.ByteBuffer;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.CompletionStage;
import static java.util.stream.Collectors.joining;
public final class WebSocketEchoListener implements WebSocket.Listener {
// 部分文本消息将累积在此列表中
private final List textMessages = new ArrayList();
// 当发生错误或 WebSocket 关闭时，此 CompletableFuture 将完成。参见 onError() 和 onClose() 方法。
CompletableFuture closeStatus = new CompletableFuture();
@Override
public void onOpen(WebSocket webSocket) {
System.out.println("已连接...");
// 请求接收下一条消息
webSocket.request(1);
}
@Override
public CompletionStage onText(WebSocket webSocket,
CharSequence data, boolean last) {
// 请求接收下一条消息
webSocket.request(1);
// 累积消息
textMessages.add(data.toString());
if (last) {
// 收到消息的最后一部分。让我们打印它。
String wholeMessage = textMessages.stream()
.collect(joining(""));
System.out.println("收到: " + wholeMessage);
// 清空累积的消息，以便将来累积接收到的部分消息
textMessages.clear();
}
// 返回 null 表示消息处理完成
return null;
}
@Override
public CompletionStage onPong (WebSocket webSocket,
ByteBuffer message) {
// 请求接收下一条消息
webSocket.request(1);
// 解码消息并打印
String data = new String(message.array());
System.out.println("收到一个 Pong: " + data);
// 返回 null 表示消息处理完成
return null;
}
@Override
public void onError(WebSocket webSocket, Throwable error) {
// 异常地完成 CompletableFuture，以指示 WebSocket 连接因错误而关闭
closeStatus.completeExceptionally(error);
}
@Override
public CompletionStage onClose(WebSocket webSocket,
int statusCode, String reason) {
// 准备关闭描述
String closeDescription = "以状态码 " + statusCode
+ " 关闭，原因: " + reason;
// 正常完成 CompletableFuture，以指示 WebSocket 连接已关闭。
closeStatus.complete(closeDescription);
return null;
}
public CompletableFuture closeStatus() {
return this.closeStatus;
}
}
清单 3-14
WebSocket.Listener 接口的一个实现
```

我们的监听器监听文本消息，这些消息可以分部分接收。`textMessages` 实例变量用于累积部分消息。它在 `onText()` 方法内部使用。当 `onText()` 方法接收到消息的最后一部分时，它会将 `textMessages` 实例变量中的所有部分消息拼接起来，打印出来，并清空列表，以便将来累积新消息的部分内容。

该监听器声明了另一个名为 `closeStatus` 的实例变量：

```
CompletableFuture closeStatus = new CompletableFuture();
```

当我创建一个 `WebSocket` 客户端程序时，我希望知道这个监听器何时收到最后一条消息。当监听器的 `onError()` 或 `onClose()` 方法被调用时，即表示收到了最后一条消息。`onError()` 和 `onClose()` 方法分别异常地或正常地完成 `closeStatus CompletableFuture`。`closeStatus()` 方法简单地返回这个 `CompletableFuture`。其预期用途是在客户端程序中等待 `WebSocket` 关闭：

```
// 为我们的 WebSocket 创建一个监听器
WebSocketEchoListener listener = new WebSocketEchoListener();
/* 使用此监听器创建一个 WebSocket，并在此处与服务器交换消息
*/
// 等待 WebSocket 关闭
listener.closeStatus()
.thenAccept(System.out::println)
.exceptionally(e -> {
e.printStackTrace();
return null;
})
.join();
```

`onOpen()` 方法打印一条信息性消息并请求接收下一条消息。请注意，如果移除对 `webSocket.request(1)` 方法的调用，你将完全无法接收任何消息。

`onText()` 方法请求接收下一条消息并累积该消息。如果这是消息的最后一部分，它会将完整消息打印到标准输出，并清空 `textMessages` 实例变量，该变量将在将来 `onText()` 方法被调用时用于累积下一条文本消息。它返回 `null` 以表示消息处理完成。

`onPong()` 方法请求接收下一条消息，打印该消息，并返回 `null` 以表示消息处理完成。

`onError()` 方法异常地完成 `closeStatus CompletableFuture`。

`onClose()` 方法正常地完成 `closeStatus CompletableFuture`，并附带一个包含状态码和关闭连接原因的描述。状态码 1000 表示正常关闭。

#### 构建客户端端点

你需要构建一个充当客户端端点的 `WebSocket` 接口实例。该实例用于连接服务器端点并与之交换消息。`WebSocket` 实例是使用 `WebSocket.Builder` 构建的。你需要使用 `HttpClient` 类的 `newWebSocketBuilder()` 方法来获取一个构建器。

一旦你有了构建器，就可以调用 `WebSocket.Builder` 的以下方法来配置和构建 `WebSocket`：

*   `WebSocket.Builder connectTimeout`​`(Duration timeout)`

*   `WebSocket.Builder header`​`(String name, String value)`

*   `WebSocket.Builder subprotocols`​`(String mostPreferred, String... lesserPreferred)`

*   `CompletableFuture<WebSocket> buildAsync`​`(URI uri, WebSocket.Listener listener)`

`connectTimeout()` 方法允许你为打开握手指定超时时间。如果在指定时间内打开握手未完成，则 `WebSocket.Builder` 的 `buildAsync()` 方法返回的 `CompletableFuture` 将异常完成，并抛出 `HttpTimeoutException`。

你可以使用 `header()` 方法为打开握手添加任何自定义头。

你可以使用 `subprotocols()` 方法在打开握手期间请求给定的子协议——服务器将只选择其中一个。子协议由应用程序定义。客户端和服务器需要就特定的子协议及其细节达成一致。

最后，调用 `WebSocket.Builder` 接口的 `buildAsync()` 方法来构建端点。第一个参数是服务器端点的 URI。第二个参数是一个 WebSocket 监听器。它返回一个 `CompletableFuture<WebSocket>`，当此端点连接到服务器端点时正常完成；当发生错误时异常完成。

以下代码片段展示了如何构建和连接客户端端点。请注意，服务器的 URI 以 `ws` 开头，这表示 WebSocket 协议。

```
// 使用默认配置创建一个 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 为我们的 WebSocket 创建一个监听器
WebSocketEchoListener listener = new WebSocketEchoListener();
// 创建一个 WebSocket
WebSocket webSocket = client.newWebSocketBuilder()
.buildAsync(URI.create("ws://demos.kaazing.com/echo"), listener)
.join();
```



#### 向对等端发送消息

一旦客户端端点连接到对等端，双方即可交换消息。`WebSocket` 接口的实例代表一个客户端端点，该接口包含以下用于向对等端发送消息的方法：

*   `CompletableFuture<WebSocket> sendBinary`​`(ByteBuffer data, boolean last)`

*   `CompletableFuture<WebSocket> sendText`​`(CharSequence data, boolean last)`

*   `CompletableFuture<WebSocket> sendPing`​`(ByteBuffer message)`

*   `CompletableFuture<WebSocket> sendPong`​`(ByteBuffer message)`

*   `CompletableFuture<WebSocket> sendClose`​`(int statusCode, String reason)`

`sendText()` 方法用于向对等端发送文本消息。如果第二个参数为 `false`，则表示这是一条部分消息的一部分。如果第二个参数为 `true`，则表示部分消息的最后一部分。如果之前没有发送过部分消息，则第二个参数为 `true` 表示这是一条完整消息。

`sendBinary()` 方法向对等端发送二进制消息。

`sendPing()` 和 `sendPong()` 方法分别向对等端发送 `Ping` 和 `Pong` 消息。

`sendClose()` 方法向对等端发送 `Close` 消息。你可以发送 `Close` 消息作为对等端发起的关闭握手的一部分，也可以发送它来发起与对等端的关闭握手。

### 提示

如果你想突然关闭 WebSocket，请使用 `WebSocket` 接口的 `abort()` 方法。

### 运行 WebSocket 程序

现在是时候看看 WebSocket 客户端和服务器交换消息了。清单 3-15 包含一个完整的程序。它创建一个 WebSocket，与位于 `ws://demos.kaazing.com/echo` 的服务器发送/接收消息，然后关闭 WebSocket。输出显示了交互过程。该程序包含大量注释来解释逻辑。

```
// WebSocketClient.java
package com.jdojo.http.client;
import static java.lang.System.out;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import static java.net.http.WebSocket.NORMAL_CLOSURE;
import java.nio.ByteBuffer;
public class WebSocketClient {
public static void main(String[] args) throws InterruptedException {
// 使用默认配置创建 HttpClient
HttpClient client = HttpClient.newHttpClient();
// 为我们的 WebSocket 创建一个监听器
WebSocketEchoListener listener = new WebSocketEchoListener();
// 创建一个 WebSocket
WebSocket webSocket = client.newWebSocketBuilder()
.buildAsync(URI.create("ws://demos.kaazing.com/echo"),
listener)
.join();
// 向服务器发送一条消息。sendText() 中的第二个参数为 true
// 表示该消息是完整的。
String helloMessage = "Hello, there.";
webSocket.sendText(helloMessage, true)
.thenRun(() -> out.println("Sent: " + helloMessage))
.join();
// 向服务器发送一条 Ping 消息
String pingMessage = "Just checking...";
webSocket.sendPing(ByteBuffer.wrap(pingMessage.getBytes()))
.thenRun(() -> out.println("Sent Ping: " + pingMessage))
.join();
// 向服务器发送一条 Close 消息。在此之后，你无法再
// 向服务器发送任何消息。服务器会向你发送一条 Close 消息作为回应。
webSocket.sendClose(NORMAL_CLOSURE, "Done")
.thenRun(() -> out.println("Sent Close"))
.join();
// 等待 WebSocket 监听器关闭。回想一下，当监听器的
// onClose() 或 onError() 方法被调用时，closeStatus()
// 方法返回的 CompletableFuture 就会完成。
listener.closeStatus()
.thenAccept(System.out::println)
.exceptionally(e -> {
e.printStackTrace();
return null;
})
.join();
}
}
Connected...
Sent: Hello, there.
Sent Ping: Just checking...
Sent Close
Received: Hello, there.
Received a Pong: Just checking...
Closed with status 1000 and reason: Done
清单 3-15
演示 WebSocket 客户端/服务器交互的程序
```

清单 3-15 中的程序使用以下代码片段向服务器发送了一条文本消息：

```
// 向服务器发送一条消息。sendText() 中的第二个参数为 true
// 表示该消息是完整的。
String helloMessage = "Hello, there.";
webSocket.sendText(helloMessage, true)
.thenRun(() -> out.println("Sent: " + helloMessage))
.join();
```

我希望你修改代码，将 `false` 作为第二个参数传递给 `sendText()` 方法。修改后的代码看起来像这样：

```
String helloMessage = "Hello, there.";
webSocket.sendText(helloMessage, false)
.thenRun(() -> out.println("Sent: " + helloMessage))
.join();
```

现在再次运行清单 3-15 中的程序。你观察到了什么？你应该会发现问候消息已发送到服务器，但你看不到任何关于收到相同消息回显的输出。你的输出应该看起来像这样：

```
Connected...
Sent: Hello, there.
Sent Ping: Just checking...
Sent Close
Received a Pong: Just checking...
Closed with status 1000 and reason: Done
```

我们的服务器是一个回显服务器。无论你向服务器发送什么，你都会收到相同的消息回显。`sendText()` 方法的第二个参数为 `false` 意味着这是一条部分消息。因此，服务器也以部分消息的形式将此消息发送回来。我们的 WebSocket 监听器的 `onText()` 方法被调用时，第三个参数被设置为 `false`。回想一下，我们的 `onText()` 方法（参考清单 3-14）仅在第三个参数设置为 `true`（表示这是消息的最后一部分）时才打印消息。问候消息被监听器接收到了，但从未被打印出来，因为它被标记为部分消息。除非你向服务器发送另一条文本消息并将其标记为消息的最后一部分，否则你不会在输出中看到问候消息。你可以通过从监听器的 `onText()` 方法打印一条消息来验证消息已被接收。你可以通过在发送问候消息作为部分消息后添加以下语句来解决此问题：

```
// 发送一个空字符串，将其标记为消息的最后一部分
webSocket.sendText("", true)
.join();
```

或者，你可以分两部分发送问候消息——第一部分是部分消息，第二部分是最终消息，如下所示：

```
webSocket.sendText("Hello, ", false)
.join();
webSocket.sendText("there.", true)
.thenRun(() -> out.println("Sent: Hello, there."))
.join();
```



## 摘要

Java 11 将 HTTP 客户端 API 作为标准特性引入，使您能够在 Java 应用程序中处理 HTTP 请求和响应。该 API 提供了用于开发带身份验证的 WebSocket 客户端端点的类和接口。该 API 位于 `java.net.http` 包中，该包属于 `java.net.http` 模块。

`HttpClient` 和 `HttpRequest` 类以及 `HttpResponse<T>` 和 `WebSocket` 接口是 HTTP 客户端 API 的核心。这些类型的实例通过构建器创建。`HttpClient` 类是不可变的。`HttpClient` 类的实例存储可重用于多个 HTTP 请求的 HTTP 连接配置。`HttpRequest` 类的实例表示一个 HTTP 请求。`HttpResponse` 接口的实例表示从服务器接收到的 HTTP 响应。您可以同步或异步地发送和接收 HTTP 请求和响应。

`WebSocket` 接口的实例表示一个 WebSocket 客户端端点。与 WebSocket 服务器端点的通信是异步完成的。WebSocket API 是基于事件的。您需要为 WebSocket 客户端端点指定一个监听器，该监听器是 `WebSocket.Listener` 静态接口的实例。当端点上发生事件时，监听器会通过调用其相应方法得到通知，例如，当与对等方的打开握手成功完成时，会通过调用监听器的 `onOpen()` 方法来通知监听器。该 API 支持与对等方交换文本和二进制消息。消息可以分部分交换。

