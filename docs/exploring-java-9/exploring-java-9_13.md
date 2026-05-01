# 13. 安全性

本章涵盖 Java 9 中与安全相关的更改。

## SHA-3 哈希算法

Java 9 新增了四种 SHA-3（[`https://en.wikipedia.org/wiki/SHA-3`](https://en.wikipedia.org/wiki/SHA-3)）哈希算法用于生成消息摘要：`SHA3-224`、`SHA3-256`、`SHA3-384` 和 `SHA3-512`。清单 13-1 展示了一个如何使用 `SHA3-224` 算法生成消息摘要的示例。

```
import org.apache.commons.codec.binary.Hex;
public class SHA3 {
public static void main(final String[] args) throws NoSuchAlgorithmException {
final MessageDigest instance = MessageDigest.getInstance("SHA3-224");
final byte[] digest = instance.digest("".getBytes());
System.out.println(Hex.encodeHexString(digest));
}
}
清单 13-1.
使用 SHA-3 生成消息摘要
```



## SecureRandom

`java.security.SecureRandom` 类负责生成加密强度高的随机数。Java 内置了多种生成随机数的算法。Java 9 新增了确定性随机比特生成器（DRBG）算法。由于 DRBG 需要额外参数进行配置，现有 API 也进行了更新以支持该算法。

新的标记接口 `SecureRandomParameters` 代表了不同 `SecureRandom` 方法中使用的参数。目前只有与 DRBG 相关的参数实现了此接口。`SecureRandomSpi` 新增了一个以 `SecureRandomParameters` 为参数的构造方法。新方法 `void engineNextBytes(byte[] bytes, SecureRandomParameters params)` 可以使用 `SecureRandomParameters` 来生成随机字节。如果 `SecureRandom` 支持重新播种，则应重写 `void engineReseed(SecureRandomParameters params)` 方法以进行重新播种。最后一个新方法 `SecureRandomParameters engineGetParameters()` 返回引擎所使用的有效 `SecureRandomParameters`。

为了使用 `SecureRandomParameters` 接口，`SecureRandom` 为其静态方法 `getInstance()` 新增了三个重载，以添加额外的 `SecureRandomParameters` 参数。方法 `nextBytes()` 也新增了一个带有 `SecureRandomParameters` 参数的重载。由于 DRBG 支持重新播种，新方法 `void reseed()` 和 `void reseed(SecureRandomParameters params)` 可以从其源输入对此 `SecureRandom` 进行重新播种。最后一个新方法 `SecureRandomParameters getParameters()` 返回此 `SecureRandom` 实例的有效 `SecureRandomParameters`。

DRBG 算法有三种 `SecureRandomParameters` 的实现，用于不同阶段；参见表 13-1。

表 13-1.

`SecureRandomParameters` 实现

| 实现 | 描述 | 使用方法 |
| --- | --- | --- |
| `DrbgParameters.Instantiation` | 实例化 | `getInstance` |
| `DrbgParameters.NextBytes` | 随机比特生成 | `nextBytes` |
| `DrbgParameters.Reseed` | 重新播种 | `reseed` |

`DrbgParameters` 提供了静态方法用于创建这三种参数类型的实例。创建新的 `DrbgParameters.Instantiation` 时，你可以通过枚举 `DrbgParameters.Capability` 指定 DRBG 的最低能力要求；参见表 13-2。DRBG 能力包括是否可重新播种以及是否支持预测抗性。由于支持预测抗性的 DRBG 实现也必须支持重新播种，因此只有三个选项。

表 13-2.

`DrbgParameters.Capability` 值

| 值 | 描述 |
| --- | --- |
| `NONE` | 既不可重新播种，也不具备预测抗性 |
| `RESEED_ONLY` | 仅可重新播种 |
| `PR_AND_RESEED` | 既可重新播种，也具备预测抗性 |

创建的 DRBG `SecureRandom` 实例可能拥有比请求更多的能力。一旦创建了 `SecureRandom` 实例，你可以使用 `getParameters()` 方法获取有效的 `SecureRandomParameters`，然后检查实际能力。

清单 13-2 展示了使用 DRBG 算法生成随机数的示例。

```
public class DRBGRandom {
public static void main(final String[] args) throws NoSuchAlgorithmException {
final DrbgParameters.Instantiation instantiation =
DrbgParameters.instantiation(
256,
Capability.PR_AND_RESEED,
"HelloWorld".getBytes()
);
final SecureRandom secureRandom = SecureRandom.getInstance("DRBG", instantiation);
final DrbgParameters.NextBytes nextBytes =
DrbgParameters.nextBytes(-1, true, "Hello".getBytes());
final byte[] result = new byte[32];
secureRandom.nextBytes(result, nextBytes);
System.out.println(Hex.encodeHexString(result));
final DrbgParameters.Reseed reseed = DrbgParameters.reseed(true, null);
secureRandom.reseed(reseed);
secureRandom.nextBytes(result, nextBytes);
System.out.println(Hex.encodeHexString(result));
}
}
清单 13-2.
使用 DRBG 算法示例
```

作为实现 DRBG 的结果，哈希算法 `SHA-512/224` 和 `SHA-512/256` 以及相关的 `HmacSHA512/224` 和 `HmacSHA512/256` 也被添加了进来。

## 使用 PKCS12 作为默认密钥库

自 JDK 1.2 起，JDK 一直使用其专有的密钥库格式 JKS。JKS 密钥库只能存储私钥和受信任的公钥证书。PKCS12（[`https://en.wikipedia.org/wiki/PKCS_12`](https://en.wikipedia.org/wiki/PKCS_12)）是一种广泛支持的密钥存储格式。在 Java 9 中，默认密钥库类型从 JKS 更改为 PKCS12。现有的密钥库不会改变，应用程序无需更改即可继续运行。

## 总结

在本章中，我们讨论了新的 SHA-3 哈希算法、DRBG `SecureRandom` 算法以及使用 PKCS12 密钥库。在下一章中，我们将讨论 Java 9 中与用户界面相关的更改。

