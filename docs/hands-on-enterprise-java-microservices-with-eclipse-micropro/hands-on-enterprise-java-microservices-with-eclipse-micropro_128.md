# 默认提供者

MP-RC 实现必须提供一组最低限度的提供者，包括以下内容：

*   `*/json` 类型：
    *   JSON-P，`javax.json.JsonValue`
    *   JSON-B，`javax.json.bind`
*   `*` 类型：
    *   `byte[]`
    *   `java.lang.String`
    *   `java.io.InputStream`
    *   `java.io.Reader`
*   `text/plain` 类型：
    *   `java.lang.Number 及其子类型`
    *   `int, long, float 和 double`
    *   `java.lang.Character 和 char`
    *   `java.lang.Boolean 和 boolean`



