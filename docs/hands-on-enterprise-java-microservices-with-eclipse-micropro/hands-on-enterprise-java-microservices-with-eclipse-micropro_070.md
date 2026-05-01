# MP-JWT API 的高级描述

MP-JWT 项目在 `org.eclipse.microprofile.jwt` 包命名空间下引入了以下 API 接口和类：

*   `JsonWebToken`：这是一个 `java.security.Principal` 接口扩展，通过 get 风格的访问器提供对必需声明集的访问，以及对 JWT 中任何声明的通用访问。
*   `Claims`：这是一个枚举工具类，封装了所有标准的 JWT 相关声明，以及从 `JsonWebToken#getClaim(String)` 方法返回的声明的描述和所需的 Java 类型。
*   `Claim`：这是一个限定符注解，用于表示 `ClaimValue` 的注入点。
*   `ClaimValue<T>`：这是一个 `java.security.Principal` 接口扩展...

