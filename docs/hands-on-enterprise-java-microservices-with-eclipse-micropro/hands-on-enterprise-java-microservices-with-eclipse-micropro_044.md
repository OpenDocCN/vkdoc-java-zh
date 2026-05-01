# 内置转换器

MicroProfile Config 实现为原始类型（`boolean`、`int`、`long`、`byte`、`float` 和 `double`）及其对应的 Java 类型（例如 `Integer`）提供了内置转换器。

它还支持在属性值中使用 `","` 作为项目分隔符的数组。如果 `","` 必须是项目的一部分，则必须使用反斜杠 `"\"` 进行转义：

```
private String[] pets = config.getValue("myPets", String[].class)
```

如果 `myPets` 属性的值是 `dog,cat,dog\\,cat`，则存储在 `pets` 中的数组元素将是 `{"dog", "cat", "dog,cat"}`。

