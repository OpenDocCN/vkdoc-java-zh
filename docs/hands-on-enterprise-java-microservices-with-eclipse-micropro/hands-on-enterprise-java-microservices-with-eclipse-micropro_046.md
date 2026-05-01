# 自定义转换器

如果您的应用程序定义的 Java 类型不提供自动转换器所涵盖的这三种情况中的任何一种，MicroProfile Config 仍然可以使用扩展了 `org.eclipse.microprofile.config.spi.Converter` 接口的自定义转换器来提供转换，该接口定义如下：

```
public interface Converter<T> {
    /**
     * 将字符串值配置为指定类型
     * @param value 属性值的字符串表示形式。
     * @return 转换后的值或 null
     *
     * @throws IllegalArgumentException 如果无法将值转换为指定类型。
     */
    T convert(String value);
```

您必须编写一个 `org.eclipse.microprofile.config.spi.Converter` 的实现，然后将其名称添加到 `/META-INF/services/org.eclipse.microprofile.config.spi.Converter ...`



