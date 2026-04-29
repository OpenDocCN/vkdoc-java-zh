# 4. 上下文与依赖注入

上下文与依赖注入（CDI）是一种强大的依赖注入机制，它既是 Jakarta EE 规范的一部分，也是 MicroProfile 规范的一部分。CDI 允许我们轻松地将依赖项注入到代码中；它还具有一个事件处理机制，使我们能够松散地耦合代码的不同部分。在本章中，我们将了解如何将 CDI 集成到部署到 Payara Micro 的 MicroProfile 应用程序中。

## CDI 作用域

CDI Bean 具有*作用域*，用于表示其生命周期；Bean 的作用域指示了 Bean 何时被创建和销毁。通过使用某个作用域注解来标注 CDI Bean，可以为其指定作用域；例如，要将 CDI Bean 配置为具有请求作用域，需要使用 `@RequestScoped` 注解进行标注。

表 4-1 总结了所有 CDI 作用域。

**表 4-1**

**CDI 作用域**

| 作用域 | 注解 | 描述 |
| --- | --- | --- |
| 应用 | `@ApplicationScoped` | 在应用程序启动时初始化，在应用程序关闭时销毁。应用程序的所有用户共享同一个应用作用域的 CDI Bean 实例 |
| 会话 | `@SessionScoped` | 绑定到单个 HTTP 会话，每个用户获得一个会话作用域的 CDI Bean 实例 |
| 请求 | `@RequestScoped` | 绑定到 HTTP 请求，Bean 在每个 HTTP 请求时创建和销毁 |
| 对话 | `@ConversationScoped` | 对话作用域的 CDI Bean 的生命周期跨越两个或多个 HTTP 请求 |
| 依赖 | `@Dependent` | 依赖 CDI Bean 没有自己的作用域；相反，它们被设计为注入到另一个 CDI Bean 中并继承其作用域。例如，如果注入到请求作用域的 Bean 中，依赖 Bean 将获得请求作用域；当注入到会话作用域的 CDI Bean 中时，它将获得会话作用域，依此类推 |

我们可以通过使用上述任一作用域注解来标注，将几乎任何 Java 类转变为 CDI Bean。CDI Bean 的主要要求是：该类不能是抽象的，并且必须提供一个公共的无参构造函数。值得指出的是，会话和对话作用域更适合传统的 Web 应用程序。对于微服务架构，我们通常只使用应用、请求和依赖作用域。



### CDI Bean 的生命周期

CDI 作用域定义了 CDI Bean 的生命周期，即 Bean 何时被创建和销毁。通常，我们不会自己实例化 CDI Bean；而是将这项工作委托给运行时（在本例中为 Payara Micro）。

有时，我们需要在 Bean 初始化之后或销毁之前执行一些逻辑；为此，CDI 提供了 `@PostConstruct` 和 `@PreDestroy` 注解。

被 `@PostConstruct` 注解的方法会在 CDI Bean 初始化后由 MicroProfile 运行时调用；相反，被 `@PreDestroy` 注解的方法会在 Bean 销毁前自动调用。被 `@PostConstruct` 或 `@PreDestroy` 注解的方法必须返回 void，不接受任何参数，并且不能是静态的。

我们可以通过添加一个作用域注解，将任何 Jakarta EE RESTful Web 服务（即被 `@Path` 注解的类）转变为 CDI Bean；通常，我们会使用 `@RequestScoped` 注解，这会导致每次请求都会构造一个新的服务实例。

你也可以使用 `@ApplicationScoped` 注解来注解你的 RESTful Web 服务，这将导致同一个服务实例响应所有请求；然而，这可能会导致代码扩展性不佳。

以下示例展示了一个转变为请求作用域 CDI Bean 的 Web 服务：

```
package com.ensode.cdiscopes;
//imports omitted
@RequestScoped
@Path("cdiservice")
public class CdiService {
private static final Logger LOGGER =
Logger.getLogger(CdiService.class.getName());
@PostConstruct
public void init() {
LOGGER.log(Level.INFO, "init() method called");
}
@PreDestroy
public void cleanup() {
LOGGER.log(Level.INFO, "cleanup() method called");
}
@POST
public void handlePostRequest() {
LOGGER.log(Level.INFO, "HTTP POST request received");
}
}
```

请注意，我们使用 `@RequestScoped` 注解标注了我们的类；这会将我们的 RESTful Web 服务类转变为请求作用域的 CDI Bean，并赋予我们 CDI Bean 可用的所有功能，例如拥有运行时可在 Bean 初始化或销毁时调用的回调方法。

我们创建了两个回调方法；我们使用 CDI 的 `@PostConstruct` 注解标注了 `init()` 方法，这告诉运行时需要在我们的类构造完成后立即调用此方法。此外，我们使用 `@PreDestroy` 注解标注了 `cleanup()` 方法；因此，它会在 Bean 销毁前自动被调用。

在我们的示例中，我们只是在回调方法中向日志输出了一些信息。在典型的应用程序中，这些回调方法用于执行任何必要的初始化、获取和释放资源（如数据库连接）等。

由于我们的服务是请求作用域的，每个 HTTP 请求都会创建一个新的实例；因此，对于每个 HTTP 请求，我们应该能在 Payara Micro 的输出中看到预期的日志条目。

我们的应用程序在其 Maven pom.xml 文件中配置如下：

```

fish.payara.maven.plugins
payara-micro-maven-plugin
1.4.0

${version.payara}
false

--autoBindHttp

--deploy
${project.build.directory}/$
{project.build.finalName}

/cdiscopes

```

请注意我们如何将上下文根设置为 `/cdiscopes`；因此，在向我们的服务发送 HTTP 请求时，我们需要使用这个上下文根。

我们通过常用的 Maven 目标在 Payara Micro 上运行我们的应用程序：

```
mvn war:exploded payara-micro:start
```

我们可以在 Payara Micro 的输出中看到，我们的应用程序已成功部署并正在监听请求。

```
[2021-08-30T18:21:02.818-0400] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1630362062818] [levelValue: 800] [[
Payara Micro URLs:
http://192.168.1.165:8080/cdiscopes
'cdi-scopes-1.0-SNAPSHOT' REST Endpoints:
GET  /cdiscopes/webresources/application.wadl
POST /cdiscopes/webresources/cdiservice
]]
[2021-08-30T18:21:02.818-0400] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1630362062818] [levelValue: 800] Payara Micro  5.2021.6 #badassmicrofish (build 4579) ready in 8,864 (ms)
```

现在我们可以向我们的服务发送一个请求；由于我们使用 `@Post` 注解标注了处理请求的方法，我们需要向我们的服务发送一个 HTTP POST 请求；最简单的方法是使用 curl 命令行工具。

```
curl -X POST http://localhost:8080/cdiscopes/webresources/cdiservice
```

如前所述，大多数现代操作系统都预装了 curl。

然后我们检查 Payara Micro 的输出。

```
[2021-08-30T18:21:02.818-0400] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1630362062818] [levelValue: 800] Payara Micro  5.2021.6 #badassmicrofish (build 4579) ready in 8,864 (ms)
[2021-08-30T18:36:53.102-0400] [] [INFO] [] [com.ensode.cdiscopes.CdiService] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1630363013102] [levelValue: 800] init() method called
[2021-08-30T18:36:53.102-0400] [] [INFO] [] [com.ensode.cdiscopes.CdiService] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1630363013102] [levelValue: 800] HTTP POST request received
[2021-08-30T18:36:53.111-0400] [] [INFO] [] [com.ensode.cdiscopes.CdiService] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1630363013111] [levelValue: 800] cleanup() method called
```

从输出中我们可以看到，当我们向服务发送 HTTP POST 请求时，我们的 `init()` 和 `cleanup()` 方法都按预期被调用了；由于该服务是一个请求作用域的 CDI Bean，该类在处理请求之前被构造，并在处理之后立即被销毁。Payara Micro 在 Bean 实例化后立即调用了被 `@PostConstruct` 注解的方法，并在 Bean 销毁前调用了被 `@PreDestroy` 注解的方法。换句话说，一切按预期工作。



## 依赖注入

如前所述，我们可以通过使用 CDI 作用域注解来标注几乎任何普通的 Java 对象（POJO），从而将其转换为 CDI bean。

以下类是一个应用作用域的 CDI bean：

```
package com.ensode.dependencyinjection;
//省略导入语句
@ApplicationScoped
public class CountryLookup {
private List countryList;
private Map countryMap;
@PostConstruct
public void init() {
countryList = new ArrayList();
countryMap = new HashMap();
countryList.add(new Country("AU", "AUSTRALIA"));
countryList.add(new Country("UK", "United Kingdom"));
countryList.add(new Country("IN", "India"));
countryList.add(new Country("US", "United States"));
countryList.add(new Country("MX", "Mexico"));
countryList.add(new Country("EG", "Egypt"));
countryList.forEach(c -> countryMap.put(
c.getAbbreviation(), c));
}
public Country getCountry(String countryAbbrev) {
return countryMap.get(countryAbbrev);
}
}
```

要将该类转换为 CDI bean，我们只需使用 `@ApplicationScoped` 对其进行标注；回想一下我们之前的讨论，应用作用域的 CDI bean 存在于我们应用程序的整个生命周期中，它们在应用程序启动时（或者更可能是在首次使用时被延迟）初始化，并在我们取消部署应用程序或关闭 Payara Micro（或我们可能使用的任何 MicroProfile 实现）时被销毁。

应用作用域的 CDI bean 通常用于缓存不经常变化的数据；这样做是为了提高性能，避免每次需要数据时都访问数据库。

我们的应用作用域托管 bean 有一个使用 `@PostConstruct` 标注的 `public void init()` 方法；该注解使得该方法在 CDI 构建 bean 时被自动调用。在我们的示例中，`init()` 方法初始化了要缓存的数据。在生产应用程序中，我们的 `init()` 方法可能会从数据库中检索数据；在这个简单的示例中，我们只是就地初始化数据。

我们的 `init()` 方法创建了一个 `HashMap`，其中键的类型为 `String`，代表国家缩写；值是名为 `Country` 的类的实例，该类包含两个属性：国家缩写和国家全名。

`Country` 类非常简单，我们就不展示其源代码了；有关我们示例应用程序的完整源代码，请参阅本书的代码下载。

我们的应用作用域 bean 还提供了一个名为 `getCountry()` 的方法，该方法接收一个包含国家缩写的 `String`，并返回 `Country` 类的相应实例。

由于我们的 `CodeLookup` 类是一个 CDI bean，它不仅可以注入到其他 CDI bean 中，还可以注入到 Payara Micro 管理的任何类中。使用 `@Path` 注解标注的类由运行时管理；因此，CDI bean 可以注入到这些类中。以下 RESTful Web 服务利用 `CodeLookup` 类根据国家缩写查找国家对象：

```
package com.ensode.dependencyinjection;
//省略导入语句
@Path("cdiservice")
public class CdiService {
@Inject
private CountryLookup countryLookup;
private static final Logger LOGGER = Logger.getLogger(CdiService.class.getName());
@GET
@Produces(MediaType.APPLICATION_JSON)
public Country handleGetRequest(
@QueryParam("countryAbbrev") String countryAbbrev) {
return countryLookup.getCountry(countryAbbrev);
}
}
```

请注意，我们的 RESTful Web 服务本身不是一个 CDI bean，但由于其生命周期由 MicroProfile 运行时管理，我们可以将 CDI bean 注入其中。在我们的示例中，我们注入了前面讨论过的 `CountryLookup` 类的一个实例。然后，我们通过使用 `@GET` 注解标注方法，实现了一个响应 HTTP GET 请求的方法；我们让该方法接受一个名为 `countryAbbrev` 的查询参数，这是通过使用 `@QueryParam` 注解标注唯一的参数来实现的。我们的方法体简单地调用了我们在应用作用域 bean 中实现的 `getCountry()` 方法，并将接收到的缩写作为参数传递；由于我们使用 `@Produces` 注解标注了该方法，表明该方法以 JSON 格式返回数据，因此该方法返回的 `Country` 实例会自动转换为 JSON 字符串，然后作为响应体返回给客户端。

我们像往常一样通过 Payara Micro Maven 插件运行服务；然后我们可以调用我们的端点，最简单的方式是使用 `curl` 命令行工具。

```
curl http://localhost:8080/dependency-injection/webresources/cdiservice?countryAbbrev=AU
```

我们应该会立即在控制台上看到 RESTful Web 服务调用的输出，毫不意外，这是我们方法返回的 `Country` 类实例的 JSON 表示。

```
{"abbreviation":"AU","name":"AUSTRALIA"}
```

我们刚刚介绍了赋予该 API 名称的两个 CDI 特性；作用域注解是“上下文与依赖注入”中的“上下文”；`@Inject` 注解提供了 CDI 的“依赖注入”部分。另一个 CDI 特性是事件机制，它允许我们实现松耦合的应用程序。

## CDI 事件

CDI 事件允许我们松耦合应用程序的不同部分。我们的一个类可以触发一个事件，然后由一个或多个事件监听器处理。触发事件的类对监听器一无所知；它只是触发事件，然后 CDI 接管，调用所有事件监听器上的监听器方法。

### 触发事件

CDI 提供了 `Event` 类，用于触发事件；该类使用泛型来指示事件类型。该类型可以是任何有效的 Java 类型；例如，它可以是 `java.lang.String` 的实例，或者任何普通的 Java 对象（POJO）。以下示例说明了如何触发 CDI 事件：

```
package com.ensode.cdievents;
//省略导入语句
@Path("countryservice")
public class CountryService {
@Inject
Event countryEvent;
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
}
```

需要触发 CDI 事件的类需要注入一个 CDI 提供的 `Event` 类的实例；我们像往常一样通过 `@Inject` 注解注入此依赖。`Event` 类的参数化类型指示了正在触发的事件的类型。在这种情况下，我们使用一个表示国家的简单普通 Java 对象；这个 `Country` 类有两个属性：国家缩写和国家全名。

要实际触发事件，我们在注入的 `Event` 类实例上调用 `fire()` 方法；该方法接收一个在我们事件声明中指定的泛型类型的实例（在我们的例子中，是 `Country` 类的一个实例）。

请注意，在我们的示例代码中，完全没有提及事件的监听器。我们的 RESTful Web 服务对监听器一无所知；它不知道哪些类监听此事件，也不知道有多少个监听器，更不知道其他任何信息；它只是触发事件，然后 CDI 接管后续处理。



### 监听事件

要监听事件，CDI Bean 必须实现一个公共的 void 方法，该方法接受一个单一参数，参数类型为它要监听的事件类型（在本例中为 `Country` 类的实例）；此参数必须使用 CDI 的 `@Observes` 注解进行标注。以下代码示例说明了监听 CDI 事件的过程：

```
package com.ensode.cdievents;
//imports omitted
@ApplicationScoped
public class CountryEventLogger {
private static final Logger LOGGER =
Logger.getLogger(CountryEventLogger.class.getName());
public void logCountryEvent(@Observes Country country) {
LOGGER.log(Level.INFO, String.format(
"Event fired for the following country: %s",
country.getName()));
}
}
```

在此示例中，我们有一个应用作用域的 CDI Bean，用于监听 `Country` 类实例的事件；我们的示例只是向 Payara 日志发送一条条目，指示触发事件的国家/地区名称。

我们可以通过调用常用的 Maven 目标来运行示例：

```
mvn war:exploded payara-micro:start
```

在 Payara Micro 输出的末尾，我们看到一些关于已部署的 RESTful Web 服务端点的信息。

```
Payara Micro URLs:
http://192.168.1.165:8080/cdi-events
'cdi-events-1.0-SNAPSHOT' REST Endpoints:
GET /cdi-events/webresources/application.wadl
GET /cdi-events/webresources/countryservice
PUT /cdi-events/webresources/countryservice
]]
```

在我们的示例中，相关的端点接受 HTTP PUT 请求；我们可以通过 `curl` 命令行工具来调用它。

```
curl -X PUT http://localhost:8080/cdi-events/webresources/countryservice -H "Content-Type: application/json" --data '{"abbreviation":"TS","name":"Test"}'
```

在前面的示例中，我们传递了一个 JSON 字符串作为请求体，将一个（显然是虚构的）测试国家/地区传递给我们的 RESTful Web 服务端点。当我们的 RESTful Web 服务端点接收到请求时，由于我们作为请求体传递的 JSON 字符串符合 JSON 类的结构，它会自动使用我们传递的数据填充该类的实例。然后，我们的方法仅使用这个填充好的对象触发一个事件，接着我们的监听器被 CDI 调用，并且其监听事件的方法会自动执行。如果我们查看 Payara Micro 的输出，可以看到预期的日志文件条目。

```
[2021-09-03T15:06:54.464-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1630696014464] [levelValue: 800] Event fired for the following country: Test
```

正如我们所见，作为应用程序开发者，在 CDI 应用程序中实现事件处理相当容易，因为所有繁重的工作都由 CDI 处理；我们只需要添加几个注解（`@Inject`、`@Observable`）并调用一个方法（`Event.fire()`）；其他一切都在幕后处理好了。

如果给定类型只有一个事件，我们刚才解释的方法效果很好，但如果我们需要为同一类型触发多个事件呢？事件监听器如何知道要监听哪个事件？这就是 CDI 限定符发挥作用的地方。

### 限定符

CDI 限定符是 Java 注解，它们本身使用 CDI 的 `@Qualifier` 注解进行标注。当我们需要区分同一类型但用于不同目的的对象时，我们会编写限定符。在处理 CDI 事件时，当我们需要触发同一类型的多个事件时，我们会使用限定符。

例如，假设我们需要在添加或更新国家/地区时触发一个事件；我们可以创建一个 `@Updated` 限定符，以便仅在执行这些操作之一时触发事件。

```
package com.ensode.cdievents.qualifier;
//imports omitted
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER})
public @interface Updated {
}
```

正如我们所见，我们的限定符是一个标准的 Java 注解，将在运行时处理，由 `@Retention(RetentionPolicy.RUNTIME)` 注解指定。CDI 事件的限定符在我们声明 Event 类的实例以及在任何监听器方法的相应参数上应用；因此，我们为限定符的 `@Target` 注解指定了 `ElementType.FIELD` 和 `ElementType.PARAMETER` 作为值。

假设我们需要在删除国家/地区时触发一个不同的 Country 事件；为此，我们将使用另一个限定符。

```
package com.ensode.cdievents.qualifier;
//imports omitted
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER})
public @interface Deleted {
}
```

现在我们可以应用这些限定符来区分 `Country` 类的不同类型的事件。

```
package com.ensode.cdievents;
//imports omitted
@Path("countryservice")
public class CountryService {
@Inject
private @Updated Event countryEvent;
@Inject
private @Deleted Event countryDeletedEvent;
@Inject
private CountryLookup countryLookup;
@GET
@Produces(MediaType.APPLICATION_JSON)
public Country handleGetRequest(
@QueryParam("countryAbbrev") String countryAbbrev) {
return countryLookup.getCountry(countryAbbrev);
}
@PUT
@Consumes(MediaType.APPLICATION_JSON)
public void updateCountry(Country country) {
countryEvent.fire(country);
}
@DELETE
@Consumes(MediaType.APPLICATION_JSON)
public void deleteCountry(
@QueryParam("countryAbbrev") String countryAbbrev) {
Country country=countryLookup.getCountry(countryAbbrev);
countryDeletedEvent.fire(country);
}
}
```

请注意，我们更新了 RESTful Web 服务；我们现在声明了两个 `Event<Country>` 实例；一个用于在添加或更新国家/地区时触发，另一个用于在删除国家/地区时触发；我们使用自定义的 `@Updated` 和 `@Deleted` 限定符装饰了每个实例。

除了之前存在的 `updateCountry()` 方法之外，我们还添加了一个新的 `deleteCountry()` 方法；当我们的服务收到 HTTP DELETE 请求时（由 `@DELETE` 注解指定），将调用此方法。这个新方法会触发我们添加到服务中的第二个 `Country` 事件实例，以便监听器可以在删除国家/地区时执行任何必要的逻辑。

在监听器端，我们需要在相应的监听器方法上使用相同的限定符。让我们看一个具有两个监听器方法的 CDI 事件监听器，每个方法对应一种事件类型。

```
package com.ensode.cdievents;
//imports omitted
@ApplicationScoped
public class CountryManager {
private static final Logger LOGGER = Logger.getLogger(CountryManager.class.getName());
@Inject
private CountryLookup countryLookup;
public void updateCountry(@Observes @Updated Country country) {
LOGGER.log(Level.INFO, String.format("Updating the following country: %s", country.getName()));
countryLookup.updateCountry(country);
}
public void deleteCountry(@Observes @Deleted Country country) {
LOGGER.log(Level.INFO, String.format("Deleting the following country: %s", country.getName()));
countryLookup.deleteCountry(country);
}
}
```



如我们所见，在监听器端，我们使用限定符来指示每个监听器方法将处理哪个事件。如果未在监听器上指定限定符，则该方法将处理与 `Country` 类对应的所有事件。

我们可以像之前一样通过 curl 向服务发送 HTTP PUT 请求：

```
curl -X PUT http://localhost:8080/cdi-events/webresources/countryservice -H "Content-Type: application/json" --data '{"abbreviation":"TS","name":"Test"}'
```

如果我们检查 Payara Micro 的输出，可以看到监听器生成的日志条目：

```
[2021-09-07T08:46:39.610-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631018799610] [levelValue: 800] Event fired for the following country: Test
[2021-09-07T08:46:39.610-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631018799610] [levelValue: 800] Updating the following country: Test
```

注意，我们之前的监听器被调用了；由于其参数没有限定符，因此对于 `Country` 类任何实例的每个事件，它都会被调用。此外，我们可以看到带有 `@Updated` 限定符的监听器方法被调用了；这是因为 HTTP PUT 请求导致服务中带有 `@PUT` 注解的 `updateCountry()` 方法被调用，该方法会触发一个带有 `@Updated` 限定符的 Country 事件。毫不意外，监听带有 `@Deleted` 限定符的 Country 事件的监听器方法没有被调用；为此，我们需要向服务发送 HTTP DELETE 请求。

```
curl  -X DELETE http://localhost:8080/cdi-events/webresources/countryservice?countryAbbrev=TS
```

当我们检查 Payara Micro 输出时，可以看到相应的事件监听器方法被调用了。

```
[2021-09-07T08:59:31.208-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631019571208] [levelValue: 800] Event fired for the following country: Test
[2021-09-07T08:59:31.209-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=76 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631019571209] [levelValue: 800] Deleting the following country: Test
```

正如预期，未限定的事件监听器方法和带有 `@Deleted` 限定符的事件方法都被调用了。

### 观察 CDI 作用域

除了允许我们通过限定符创建自定义事件类型外，CDI 还包含一些内置限定符，允许我们在 CDI 作用域初始化或销毁时实现一些逻辑。

这些限定符接受一个类参数，指示它们将监听哪个 CDI 作用域；例如，要让监听器在应用程序作用域初始化时被调用，我们可以使用类似以下的代码：

```
public void applicationScopeInitListener(@Observes @Initialized(ApplicationScoped.class) Object object){
//实际逻辑写在这里
}
```

我们需要在监听器中有一个 Object 类型的参数，仅仅是因为它需要用 `@Observes` 和 `@Initialized` 注解进行标注；我们实际上不需要在方法体中使用该参数。`@Destroyed` 和 `@BeforeDestroyed` 限定符遵循相同的模式；`@BeforeDestroyed` 会导致监听器方法在 CDI 作用域即将销毁时被调用；`@Destroyed` 则导致监听器方法在作用域销毁后立即被调用。

表 4-2 列出了每个 CDI 作用域下，用作 `@Initialized`、`@Destroyed` 和 `@BeforeDestroyed` 参数的类型。

表 4-2

CDI 作用域类

| CDI 作用域 | 类型 |
| --- | --- |
| 请求 | `RequestScoped.class` |
| 会话 | `SessionScoped.class` |
| 应用程序 | `ApplicationScoped.class` |

注意，这些类型对应于声明 CDI Bean 作用域时使用的注解。

监听应用程序作用域初始化允许我们在应用程序执行之前执行一些代码。借助此功能，可以编写具有 MicroProfile 和 Jakarta EE API 全部功能的“命令行”应用程序。一个常用的技巧是从监听器方法中使用 MicroProfile RESTful 客户端 API 编写 RESTful Web 服务客户端。以下示例说明了如何实现：

```
package com.ensode.cdievents.client;
//导入已省略
@ApplicationScoped
public class CountryServiceClient {
private static final Logger LOGGER = Logger.getLogger(CountryServiceClient.class.getName());
@Inject
@RestClient
private CountryService cdiService;
public void updateCountries(
@Observes @Initialized(ApplicationScoped.class) Object o) {
Country testCountry = new Country("TS", "Test");
Country albanya = new Country("AL", "Albanya");
Country albania = new Country("AL", "Albania");
cdiService.updateCountry(testCountry);
cdiService.deleteCountry(testCountry.getAbbreviation());
cdiService.updateCountry(albanya);
cdiService.updateCountry(albania);
}
}
```

在这个例子中，我们有一个应用程序作用域的 Bean，其中包含一个带有 `@Observes` 和 `@Initialized(ApplicationScoped.class)` 注解的监听器方法，这会导致该方法在应用程序作用域初始化时被调用。由于我们的类是应用程序作用域的 CDI Bean，我们可以通过 `@Inject` 和 `@RestClient` 注解将 RESTful Web 服务客户端接口注入其中。

你可能还记得我们之前讨论 MicroProfile RESTful 客户端 API 时提到，RESTful 客户端接口是一个 Java 接口，其方法签名与相应 RESTful Web 服务的方法签名相匹配。

在我们的客户端中，我们简单地初始化一个 POJO 传递给客户端，该 POJO 由 MicroProfile 运行时转换为 JSON 字符串，并作为请求体传递给我们的服务，或者将任何必需的查询或路径参数传递给我们的方法。

我们可以像往常一样，借助 Maven WAR 和 Payara Micro 插件运行我们的客户端：

```
mvn war:exploded payara-micro:start
```

运行客户端后，我们可以在 Payara Micro 的服务输出中看到预期的结果。



```
[2021-09-08T10:18:37.083-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1631110717083] [levelValue: 800] 为以下国家触发事件：Test
[2021-09-08T10:18:37.084-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1631110717084] [levelValue: 800] 正在更新以下国家：Test
[2021-09-08T10:18:37.117-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=77 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631110717117] [levelValue: 800] 为以下国家触发事件：Test
[2021-09-08T10:18:37.118-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=77 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631110717118] [levelValue: 800] 正在删除以下国家：Test
[2021-09-08T10:18:37.125-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1631110717125] [levelValue: 800] 为以下国家触发事件：Albanya
[2021-09-08T10:18:37.126-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=78 _ThreadName=http-thread-pool::http-listener(2)] [timeMillis: 1631110717126] [levelValue: 800] 正在更新以下国家：Albanya
[2021-09-08T10:18:37.132-0400] [] [INFO] [] [com.ensode.cdievents.CountryEventLogger] [tid: _ThreadID=77 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631110717132] [levelValue: 800] 为以下国家触发事件：Albania
[2021-09-08T10:18:37.132-0400] [] [INFO] [] [com.ensode.cdievents.CountryManager] [tid: _ThreadID=77 _ThreadName=http-thread-pool::http-listener(1)] [timeMillis: 1631110717132] [levelValue: 800] 正在更新以下国家：Albania
```

以这种方式编写“命令行”RESTful Web 服务客户端，是一种简洁的测试 Web 服务的方法，无需我们自行构造合适的 JSON 字符串传递给服务，而是让 MicroProfile 替我们完成繁琐的工作。

## 总结

本章介绍了在 Payara Micro MicroProfile 和 Jakarta EE 应用程序中可用的上下文与依赖注入 API。我们了解了如何为 CDI 托管 Bean 分配作用域，以及如何在 Bean 初始化或销毁时执行逻辑。

此外，我们还介绍了如何通过 `@Inject` 注解，将 CDI Bean 轻松注入到 Payara Micro 管理的任何对象中，例如 RESTful Web 服务或其他 CDI Bean。

我们还解释了 CDI 事件处理机制，该机制允许我们编写松耦合的应用程序。我们介绍了如何使用 CDI 事件在 CDI 作用域初始化或销毁时编写应用程序逻辑，然后利用这一新知识实现了一个巧妙技巧：在 WAR 文件部署后立即执行逻辑。接着，我们进一步运用这一知识，使用 MicroProfile RESTful Web 服务 API 开发了命令行 RESTful Web 服务客户端。

