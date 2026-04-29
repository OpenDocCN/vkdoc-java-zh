# 12. Payara Micro 特有功能

在前面的章节中，我们主要关注 Payara Micro 对 MicroProfile 标准的实现。基于标准进行编码的好处在于，我们不会受限于特定的实现，因为基于标准编写的代码可以部署到任何实现上。

在本章中，我们将介绍一些 Payara Micro 特有的功能，这些功能虽然非常有用，但仅适用于 Payara Micro，并且在不同实现之间不可移植。

## 自动集群

Payara Micro 中的集群是自动完成的；当在同一网络上启动两个 Payara Micro 实例时，它们会自动组成集群，完全无需任何配置。

如果我们不希望 Payara Micro 实例加入集群，可以使用 `--noCluster` 命令行参数启动 Payara Micro。

Payara Micro（以及 Payara Server）拥有一个内存数据网格，用于在集群中的实例之间共享数据。Payara 的数据网格基于流行的开源 Hazelcast 内存数据网格。在大多数情况下，作为将应用程序部署到 Payara Micro 的应用程序开发者，这个数据网格对我们来说是透明的；然而，它在后台被用于实现 Payara Micro 特有的集群功能，例如使应用程序作用域的 CDI Bean 在集群中共享，以及允许向同一集群中另一个 Payara Micro 实例上运行的观察者方法触发 CDI 事件。



### 集群化应用作用域 CDI Bean

在 Payara Micro 中，我们可以在网络中的节点间共享同一个应用作用域 Bean 的实例。为此，我们只需使用 `@Clustered` 注解来标注该应用作用域 Bean，如下例所示。

```
package com.ensode.clusteredcdiappscopedbeans;
import fish.payara.cluster.Clustered;
// 省略其他导入
@Clustered
@ApplicationScoped
// 集群化应用作用域 Bean 必须可序列化
public class DataCache implements Serializable {
private Map cachedValueMap = new HashMap();
public void addMapEntry(String key, String value) {
cachedValueMap.put(key, value);
}
public String retrieveValue(String key) {
return cachedValueMap.get(key);
}
public Map getCachedValueMap() {
return cachedValueMap;
}
public void setCachedValueMap(Map cachedValueMap) {
this.cachedValueMap = cachedValueMap;
}
}
```

应用作用域 CDI Bean 的一个常见用途是缓存频繁使用的数据，这样我们就不必在每次需要检索数据时都访问数据库。对于标准的应用作用域 CDI Bean，我们必须在集群的每个节点中都保留一份 Bean 的副本；保持这些实例同步并非易事。而使用 Payara 的集群化 CDI Bean，则可以在节点间共享单个实例。

从示例中可以看出，要在集群的节点间共享一个应用作用域 Bean，我们只需使用 `@Clustered` 进行注解，并让它实现 `Serializable` 接口；所有共享 Bean 实例的繁重工作都由 Payara Micro 在后台完成。

集群化应用作用域 CDI Bean 必须是可序列化的；否则，我们的代码将无法部署。

为了使用 `@Clustered` 注解，我们需要将 *payara-api* 作为 provided 依赖添加到我们的应用程序中。使用 Maven 时，我们可以通过在 *pom.xml* 的 `<dependencies>` 部分添加相应的依赖来实现。

```

fish.payara.api
payara-bom
${version.payara}
pom
import

fish.payara.api
payara-api
provided

```

当使用 Payara Maven 物料清单（BOM）时，依赖版本在 BOM 中指定；因此，我们不应在 *pom.xml* 中指定它们。

为了演示集群化应用作用域 CDI Bean，我们可以编写一个简单的 RESTful Web 服务，该服务响应 HTTP PUT 和 GET 请求，向集群化应用作用域 CDI Bean 中的 `Map` 添加或检索值。

```
package com.ensode.clusteredcdiappscopedbeans;
// 省略导入
@Path("cachedvalueaccessor")
public class CachedValueAccessorResource {
@Inject
private DataCache dataCache;
@GET
@Produces(MediaType.TEXT_PLAIN)
public String getCachedValue(@QueryParam("key") String key) {
return dataCache.retrieveValue(key);
}
@PUT
@Consumes(MediaType.TEXT_PLAIN)
public void addCachedValue(@QueryParam("key") String key,
@QueryParam("value") String value) {
dataCache.addMapEntry(key, value);
}
}
```

请注意，使用我们的集群化应用作用域 CDI Bean 无需任何特殊操作；我们只需像往常一样通过 `@Inject` 注解注入它即可。

至此，我们有了一个简单但完整的示例，可以用来演示集群化应用作用域 CDI Bean；我们只需将应用程序打包成 WAR 文件，并将其部署到两个独立的 Payara Micro 实例上，这两个实例将自动形成一个集群。

我们可以通过命令行执行以下操作：

```
java -jar path/to/payara-micro-5.2021.10.jar \
--contextroot clusteredapplicationbeansdemo --autoBindHttp \
--deploy target/clustered-application-beans-demo-1.0-SNAPSHOT.war
```

如果我们检查 Payara Micro 的输出，可以看到我们的应用程序已部署，并且正在监听默认的 8080 HTTP 端口。

```
[2021-12-28T18:49:51.628-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640735391628] [levelValue: 800] [[
Payara Micro URLs:
http://127.0.0.1:8080/clusteredapplicationbeansdemo
'clustered-application-beans-demo-1.0-SNAPSHOT' REST Endpoints:
GET /clusteredapplicationbeansdemo/webresources/application.wadl
GET /clusteredapplicationbeansdemo/webresources/cachedvalueaccessor
PUT /clusteredapplicationbeansdemo/webresources/cachedvalueaccessor
]]
[2021-12-28T18:49:51.629-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640735391629] [levelValue: 800] Payara Micro  5 #badassmicrofish (build 879) ready in 7,708 (ms)
```

然后，我们在另一个终端窗口中使用相同的命令来部署第二个 WAR 文件副本。

在第二个 Payara Micro 实例上，我们应该会看到类似于以下的输出：

```
[2021-12-28T18:55:32.268-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640735732268] [levelValue: 800] [[
Payara Micro URLs:
http://127.0.0.1:8081/clusteredapplicationbeansdemo
'clustered-application-beans-demo-1.0-SNAPSHOT' REST Endpoints:
GET /clusteredapplicationbeansdemo/webresources/application.wadl
GET /clusteredapplicationbeansdemo/webresources/cachedvalueaccessor
PUT /clusteredapplicationbeansdemo/webresources/cachedvalueaccessor
]]
[2021-12-28T18:55:32.268-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640735732268] [levelValue: 800] Payara Micro  5 #badassmicrofish (build 879) ready in 8,235 (ms)
```

由于我们为 Payara Micro 使用了 *--autoBindHttp* 命令行参数，第二个实例会在下一个可用端口（8081）上监听 HTTP 连接。

此外，第一个 Payara Micro 实例上还会输出一些信息，告诉我们这两个实例已形成一个集群；输出内容应类似于以下内容：

```
[2021-12-28T18:55:28.239-0500] [] [INFO] [] [fish.payara.nucleus.cluster.PayaraCluster] [tid: _ThreadID=55 _ThreadName=hz.wonderful_sutherland.event-4] [timeMillis: 1640735728239] [levelValue: 800] Data Grid Instance Added f3fc7680-1fec-4544-8d9e-b8e7d20c634b at Address /192.168.1.165:6901
[2021-12-28T18:55:28.242-0500] [] [INFO] [] [fish.payara.nucleus.cluster.PayaraCluster] [tid: _ThreadID=55 _ThreadName=hz.wonderful_sutherland.event-4] [timeMillis: 1640735728242] [levelValue: 800] [[
Data Grid Status
Payara Data Grid State: DG Version: 4 DG Name: development DG Size: 2
Instances: {
DataGrid: development Name: Magnanimous-Butterfish Lite: false This: true UUID: 730a2023-0f47-4822-b2d9-d905ffcd0505 Address: /192.168.1.165:6900
DataGrid: development Lite: false This: false UUID: f3fc7680-1fec-4544-8d9e-b8e7d20c634b Address: /192.168.1.165:6901
}]]
```

回想一下，Payara Micro 的集群功能是通过内存数据网格（Hazelcast）实现的；第一个 Payara Micro 实例上的输出告诉我们，一个数据网格实例已添加到集群中。

如果你参考我们的集群化应用作用域 CDI Bean 示例，你会注意到保存缓存数据的 Map 最初是空的；我们可以通过向集群中任一 Payara Micro 实例发送 HTTP PUT 请求来向其中添加条目。例如，使用 curl 向第一个实例发送 HTTP PUT 请求：

```
$curl -XPUT 'http://localhost:8080/clusteredapplicationbeansdemo/webresources/cachedvalueaccessor?key=foo&value=bar'
```

此时，我们向集群化应用作用域 CDI Bean 中的 *Map* 添加了一个条目，键为 *foo*，值为 *bar*。由于该 Bean 是集群化的，此更改会反映到集群中的所有节点。

现在，我们可以通过向集群中的任一 Payara Micro 实例发送 HTTP GET 请求来检索刚刚添加的值；为了更有趣，我们向集群中的第二个实例发送 HTTP GET 请求，以检索刚刚在第一个实例上添加的值：

```
$ curl http://localhost:8081/clusteredapplicationbeansdemo/webresources/cachedvalueaccessor?key=foo
bar
```

curl 命令下方的文本是第二个实例的响应；正如我们所见，我们得到了预期的值，该值是通过向集群中的第一个实例发送 HTTP PUT 请求设置的。



### 远程 CDI 事件

在 Payara Micro 中，CDI 事件可以被网络中不同节点上部署的应用所观察，这些节点与触发事件的应用所在节点不同。要触发远程 CDI 事件，我们需要使用 `@Outbound` 注解被触发的事件，并使用 `@Inbound` 注解处理事件的方法中的参数。以下示例说明了如何触发远程事件；它是我们在第 4 章中看到的 CDI 事件示例的修改版本，已更新为触发远程 CDI 事件。

```
package com.ensode.cdievents;
import com.ensode.cdievents.qualifier.Deleted;
import com.ensode.cdievents.qualifier.Updated;
import fish.payara.micro.cdi.Outbound;
@Path("countryservice")
public class CountryService {
@Inject
private @Updated @Outbound
Event countryEvent;
@Inject
private @Deleted @Outbound
Event countryDeletedEvent;
@Inject
private CountryLookup countryLookup;
@GET
@Produces(MediaType.APPLICATION_JSON)
public Country handleGetRequest(@QueryParam("countryAbbrev") String countryAbbrev) {
return countryLookup.getCountry(countryAbbrev);
}
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void updateCountry(Country country) {
countryEvent.fire(country);
}
@DELETE
@Consumes(MediaType.APPLICATION_JSON)
public void deleteCountry(@QueryParam("countryAbbrev") String countryAbbrev) {
Country country=countryLookup.getCountry(countryAbbrev);
countryDeletedEvent.fire(country);
}
}
```

上述 RESTful Web 服务代码触发了两种类型的事件：一种是在国家更新时触发，另一种是在国家删除时触发。回顾第 4 章，我们可以使用限定符来区分同一类型的事件；在我们的示例中，使用了 `@Updated` 和 `@Deleted` 限定符来区分这两个事件。除了我们自定义的 `@Updated` 和 `@Deleted` 限定符之外，我们还使用 Payara 提供的 `@Outbound` 限定符对这两个事件进行了注解；该限定符用于通过网络触发事件，以便其他节点中的事件观察者方法能够处理它。事实上，要将标准 CDI 事件转换为远程 CDI 事件，我们唯一需要做的就是使用 `@Outbound` 注解 Event 实例声明。

对于此示例，我们重构了应用程序，以便将事件观察者部署在单独的 WAR 文件中；这样，我们就可以将观察者部署到 Payara Micro 的不同实例上。

与第 4 章一样，我们在两个不同的 Java 类中有三个观察者方法；我们有一个 `CountryManager` 类，它有两个观察者方法：一个用于使用 `@Updated` 注解的事件，另一个用于使用 `@Deleted` 注解的事件。

```
package com.ensode.cdi.remote.event.observer;
import com.ensode.cdievents.Country;
import com.ensode.cdievents.CountryLookup;
import com.ensode.cdievents.qualifier.Deleted;
import com.ensode.cdievents.qualifier.Updated;
import fish.payara.micro.cdi.Inbound;
//additional imports omitted
@ApplicationScoped
public class CountryManager {
private static final Logger LOGGER =
Logger.getLogger(CountryManager.class.getName());
@Inject
private CountryLookup countryLookup;
public void updateCountry(
@Observes @Inbound @Updated Country country) {
LOGGER.log(Level.INFO, String.format(
"Updating the following country: %s", country.getName()));
countryLookup.updateCountry(country);
}
public void deleteCountry(
@Observes @Inbound @Deleted Country country) {
LOGGER.log(Level.INFO, String.format(
"Deleting the following country: %s", country.getName()));
countryLookup.deleteCountry(country);
}
}
```

此示例与第 4 章中相应示例的唯一区别在于，观察者方法参数中添加了 `@Inbound` 注解，这是处理通过网络触发的事件所必需的。

我们还有一个包含观察者方法的额外类；它监听任何国家事件（即，不使用像 `@Updated` 或 `@Deleted` 这样的自定义限定符），并简单地将输出发送到观察者方法的日志中。

```
package com.ensode.cdi.remote.event.observer;
import com.ensode.cdievents.Country;
import fish.payara.micro.cdi.Inbound;
//additional imports omitted
@ApplicationScoped
public class CountryEventLogger {
private static final Logger LOGGER = Logger.getLogger(CountryEventLogger.class.getName());
public void logCountryEvent(
@Observes @Inbound Country country) {
LOGGER.log(Level.INFO, String.format(
"Event fired for the following country: %s",
country.getName()));
}
}
```

毫不意外，要让这个观察者方法监听远程事件，我们唯一需要做的就是使用 `@Inbound` 注解其参数。

至此，我们已准备好测试重构后的 CDI 事件代码；我们将包含触发事件代码的 WAR 文件部署到监听 HTTP 端口 8080 的 Payara Micro 实例上。Payara Micro 的输出会告诉我们可用于向 RESTful Web 服务发送 HTTP 请求的端点。

```
Payara Micro URLs:
http://127.0.0.1:8080/cdi-remote-events
'cdi-remote-events-1.0-SNAPSHOT' REST Endpoints:
GET    /cdi-remote-events/webresources/application.wadl
DELETE /cdi-remote-events/webresources/countryservice
GET    /cdi-remote-events/webresources/countryservice
PUT    /cdi-remote-events/webresources/countryservice
]]
```

然后，我们将包含事件观察者的第二个 WAR 文件部署到监听端口 8081 的第二个 Payara Micro 实例上。

```
[2021-12-29T18:54:07.730-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640822047730] [levelValue: 800] [[
Payara Micro URLs:
http://127.0.0.1:8081/cdi-remote-events-observer
]]
```

由于我们没有在第二个 WAR 文件中打包任何 RESTful Web 服务（只有 CDI Bean），因此第二个 Payara Micro 实例的输出中没有显示任何端点。

至此，我们已准备好测试代码；我们可以向第一个 Payara Micro 实例发送一个 HTTP DELETE 请求，这将导致它触发一个远程 CDI 事件；我们可以通过如下 curl 命令来执行此操作：

```
$ curl -XDELETE http://localhost:8080/cdi-remote-events/webresources/countryservice?countryAbbrev=AU
```

此时，如果我们检查 Payara Micro 的输出，应该会看到由相应的 CDI 远程事件观察者方法添加的日志条目。

```
[2021-12-29T18:59:21.152-0500] [] [INFO] [] [com.ensode.cdi.remote.event.observer.CountryEventLogger] [tid: _ThreadID=121 _ThreadName=concurrent/__defaultManagedExecutorService-managedThreadFactory-Thread-9] [timeMillis: 1640822361152] [levelValue: 800] Event fired for the following country: Australia
[2021-12-29T18:59:21.156-0500] [] [INFO] [] [com.ensode.cdi.remote.event.observer.CountryManager] [tid: _ThreadID=121 _ThreadName=concurrent/__defaultManagedExecutorService-managedThreadFactory-Thread-9] [timeMillis: 1640822361156] [levelValue: 800] Deleting the following country: Australia
```

正如我们的示例所示，在 Payara Micro 中触发远程 CDI 事件几乎不需要我们做什么工作；我们只需使用 `@Outbound` 注解要触发的事件，并使用 `@Inbound` 注解相应的事件观察者方法参数；所有繁重的工作都由 Payara Micro 完成，并且对我们应用程序开发人员来说是透明的。

## Uber JAR

Uber JAR 是可执行的 JAR 文件，它捆绑了我们的应用程序代码和 Payara Micro 运行时。创建 Uber JAR 可以潜在地简化我们应用程序的执行；我们所要做的就是运行可执行的 WAR 文件，而无需向 Payara Micro 传递任何参数。



### 创建 Uber JAR 文件

我们可以通过向 Payara Micro 传递 *--outputUberJar* 命令行选项来创建 Uber JAR 文件，如下例所示：

```
java -jar path/to/payara-micro/5.2021.10/payara-micro-5.2021.10.jar --deploy target/cdi-remote-events-1.0-SNAPSHOT.war --autoBindHttp --outputUberJar cdiremoteevents.jar
```

*--outputUberJar* 命令行选项接受一个参数，用于指定要创建的 JAR 文件的名称。

我们传递给 Payara Micro 的大多数命令行参数都会被记录下来，这样在执行 JAR 文件时就无需再次指定；在我们的示例中，生成的 JAR 文件将默认启用 *--autoBindHttp* 选项来执行 Payara Micro。一个值得注意的例外是，`--contextRoot` 命令行参数不会被记录；生成的 JAR 文件的上下文根始终是已部署 WAR 文件的名称（在我们的示例中为 *cdi-remote-events-1.0-SNAPSHOT.war*）。

上述命令会在当前目录下生成一个我们指定名称的 JAR 文件；要执行它，我们只需像运行任何可执行 JAR 文件一样运行它：

```
java -jar cdiremoteevents.jar
```

我们应该会在终端窗口中看到 Payara Micro 的常规输出：

```
[2021-12-31T09:30:37.148-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640961037148] [levelValue: 800] [[
Payara Micro URLs:
http://192.168.1.165:8080/cdi-remote-events-1.0-SNAPSHOT
'cdi-remote-events-1.0-SNAPSHOT' REST Endpoints:
GET    /cdi-remote-events-1.0-SNAPSHOT/webresources/application.wadl
DELETE /cdi-remote-events-1.0-SNAPSHOT/webresources/countryservice
GET    /cdi-remote-events-1.0-SNAPSHOT/webresources/countryservice
PUT    /cdi-remote-events-1.0-SNAPSHOT/webresources/countryservice
]]
[2021-12-31T09:30:37.148-0500] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1640961037148] [levelValue: 800] Payara Micro  5 #badassmicrofish (build 879) ready in 8,995 (ms)
```

如果我们想更改上下文根，可以重命名 WAR 文件，使其具有我们想要的上下文根名称；例如，将 *cdi-remote-events-1.0-SNAPSHOT.war* 重命名为 *cdiremoteevents.war* 将导致应用程序的上下文根变为 */cdiremoteevents*。

## 嵌入 Payara Micro

我们可以将 Payara Micro 嵌入到其他 Java 应用程序中。这样做允许我们通过 Payara Micro 的 Java API 以编程方式启动/停止/部署应用程序等。以下示例说明了如何执行此操作：

```
package com.ensode.embeddedpayaramicroexample;
import fish.payara.micro.BootstrapException;
import fish.payara.micro.PayaraMicro;
import java.io.File;
public class Main {
public void startPayaraMicro() throws BootstrapException {
File warFile = new File("/path/to/some.war");
PayaraMicro.getInstance().
setHttpAutoBind(true).
bootStrap().deploy("applicationname",
"contextroot", warFile);
}
public static void main(String[] args)
throws BootstrapException {
new Main().startPayaraMicro();
}
}
```

`PayaraMicro` 类是一个单例；它有一个静态的 `getInstance()` 方法，该方法返回 `PayaraMicro` 实例；我们可以通过调用 setter 和 getter 方法来以编程方式配置 Payara Micro；例如，在我们的示例中，我们调用了 `setHttpAutoBind(true)`，这相当于在命令行运行时向 Payara Micro 传递 *--autoBind* 命令行参数。对于熟悉从命令行运行 Payara Micro 的人来说，配置 Payara Micro 的 API 非常直观；请参阅 [`https://javadoc.io/doc/fish.payara.extras/payara-micro/latest/index.html`](https://javadoc.io/doc/fish.payara.extras/payara-micro/latest/index.html) 上的官方 Payara Micro API JavaDoc；特别值得关注的是 `fish.payara.micro` 包，尤其是 `PayaraMicro` 类和 `PayaraMicroRuntime` 接口。

一旦我们配置了嵌入的 Payara Micro 实例，我们就调用它的 `bootstrap()` 方法，该方法返回一个 `PayaraMicroRuntime` 接口的实现；要部署我们的 WAR 文件，我们调用它的 `deploy()` 方法，该方法接受三个参数：一个定义我们应用程序名称的 `String`，一个定义 WAR 文件上下文根的 `String`，以及一个代表我们 WAR 文件的 `java.io.File` 实例。

然后，我们可以像运行任何其他独立的 Java 应用程序一样运行我们的应用程序。对于 Maven 项目，最简单的方法是使用 Exec Maven 插件，它会自动从我们的项目依赖项中设置类路径；例如：

```
mvn exec:java -Dexec.mainClass="com.ensode.embeddedpayaramicroexample.Main"
```

将运行我们的应用程序，该应用程序又会启动一个 Payara Micro 实例；然后我们可以像往常一样向这个 Payara Micro 实例发送 HTTP 请求。

## 总结

在本章中，我们介绍了 Payara Micro 超越 Jakarta EE 和 MicroProfile 标准的功能。我们解释了 Payara Micro 实例如何自动集群。我们还介绍了如何在集群节点之间共享应用程序作用域的 CDI Bean，以及如何通过网络将 CDI 事件发送到部署在集群中其他 Payara Micro 实例上的监听器。

此外，我们还介绍了如何创建所谓的 Uber JAR 文件，这些文件是可执行的 JAR 文件，其中包含我们的应用程序代码和 Payara Micro 运行时，允许我们通过运行一个可执行的 JAR 文件来启动 Payara Micro 并部署我们的代码。

然后，我们介绍了如何将 Payara Micro 嵌入到我们的 Java 应用程序中，以及如何配置和部署我们的应用程序并以编程方式启动 Payara Micro。

