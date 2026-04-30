# {1}：远程或电脑玩家名称

messages.Introduction={1} 威胁要挠你痒痒！\n{0} 对战 {1} 正在加载中...

5033CH07.qxd 6/17/05 11:50 AM 第 87 页

第 7 章 ■ 构建应用程序

**87**

在预处理阶段，翻译内容会被嵌入到实际代码中；如果你查看预处理后的代码，会发现类似清单 7-27 所示的代码。

**清单 7-27.** *预处理后的嵌入式翻译* import de.enough.polish.util.Locale;

[...]

// 获取简单翻译：

**this.menuScreen.append( "开始挠痒大战", null );**

// 获取带一个参数的翻译：

**this.menuScreen.setTitle( "欢迎 " + userName + "!" );**

// 获取带多个参数的翻译：

String[] parameters = new String[2];

parameters[0] = userName;

parameters[1] = enemyName;

**this.textField.setString( Locale.get( 1, parameters ) );** 包含零个或一个参数的翻译会直接嵌入到源代码中，因此对于这类翻译，与非本地化的应用程序相比，完全不会产生性能或大小方面的影响。只有当翻译包含多个参数时，才会实际调用 Locale 类。为了提高性能，J2ME Polish 会将原来的字符串键 messages.Introduction 转换为一个简单的整数，从而节省宝贵的字节，并确保快速检索相关资源。

定义翻译

使用 *resources/messages.txt* 文件定义默认翻译，使用 *resources/de/messages.txt* 文件定义德语翻译，使用 *resources/fr_CA/messages.txt* 文件定义加拿大法语翻译。如果你希望将所有翻译文件放在一个文件夹中，也可以使用 *resources/messages.txt*、*resources/messages_de.txt* 和 *resources/messages_fr_CA.txt* 这些文件。翻译遵循资源组合的常规层级结构，因此如果你有诺基亚特定的翻译，可以在 *resources/Nokia/messages.txt* 等文件中定义。对于每个翻译键，会使用最佳匹配的翻译。当应用程序针对诺基亚 6600 手机和德语 de 语言进行本地化时，J2ME Polish 会尝试在以下位置查找翻译：

• *resources/Nokia/6600/de/messages.txt*

• *resources/Nokia/6600/messages_de.txt*

• *resources/[组名]/de/messages.txt*（例如 *resources/Series60/de/messages.txt*）

• *resources/[组名]/messages_de.txt*（例如 *resources/Series60/messages_de.txt*）

• *resources/Nokia/de/messages.txt*

• *resources/Nokia/messages_de.txt*

5033CH07.qxd 6/17/05 11:50 AM 第 88 页

**88**

第 7 章 ■ 构建应用程序

• *resources/de/messages.txt*

• *resources/messages_de.txt*

如果仍未找到翻译，则会再次搜索相同的层级结构，但这次会使用默认的 *messages.txt* 文件，而不是更具体的 *messages_de.txt* 文件。

设置和使用本地化变量

你可以通过在相应的 *messages* 文件中定义变量来设置本地化变量。变量定义需要以 var: 或 variable: 开头，如下所示：var:VirtualCurrency=金块

你还可以在翻译中使用这些变量（当然，也可以使用普通变量），如清单 7-28 所示。

**清单 7-28.** *在 messages.txt 文件中使用变量*

