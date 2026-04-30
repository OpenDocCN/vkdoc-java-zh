# 例如：恭喜，您赢得了 3 个诺基亚金块！

messages.YouHaveWon=恭喜！您赢得了 {0} ${polish.Vendor}-${VirtualCurrency}！

当然，您也可以在 Java 源代码的常规预处理过程中，使用您在 *messages.txt* 文件中定义的变量：

//#= String virtualCurrency = "${VirtualCurrency}"; 使用本地化属性

某些 JAD 或 Manifest 属性也需要进行本地化，例如应用程序的描述。您可以通过在相应的 *messages* 文件中定义这些属性来实现，如清单 7-29 所示。

**清单 7-29.** *在 messages.txt 文件中定义 JAD 和 Manifest 属性*
MIDlet-Description=一个需要你挠痒敌人的游戏！

MIDlet-Name=挠痒大战

有关定义属性的更多信息，请参阅本章中的“管理 JAD 和 Manifest 属性”部分。

**处理日期和货币**

`Locale` 类也可以帮助您处理本地化内容；对于大多数功能，您还需要在 `<localization>` 元素中定义一个国家和地区。

• `static String formatDate( Calendar calendar )` 根据当前区域设置格式化日期；此方法也适用于 `Date` 和 `long` 类型。

• `static String LANGUAGE` 包含该区域设置的 ISO 语言代码。

5033CH07.qxd 6/17/05 11:50 AM 第 89 页

第 7 章 ■ 构建应用程序

**89**

• `static String COUNTRY` 包含 ISO 国家/地区代码。如果当前区域设置未使用国家/地区，则为 null。

• `static String DISPLAY_LANGUAGE` 包含本地化的语言名称；例如，德语使用 Deutsch。

• `static String DISPLAY_COUNTRY` 包含本地化的国家/地区名称；例如，德国使用 Deutschland。如果当前区域设置未使用国家/地区，则为 null。

• `static String CURRENCY_SYMBOL` 包含翻译语言的货币符号；例如，使用 $ 或 €。如果当前区域设置未使用国家/地区，则为 null。

• `static String CURRENCY_CODE` 包含所用货币的三字母代码；例如，使用 USD 或 EUR。如果当前区域设置未使用国家/地区，则为 null。

**避免常见错误**

在本地化应用程序时，您需要注意一些问题。

调整 JAR 名称

您需要记得在 *build.xml* 的 `<info>` 部分调整应用程序的 JAR 名称，以便包含区域设置；否则，只有最后一个本地化的应用程序会被实际写入 *dist* 文件夹。您可以在 `jarName` 属性中使用变量 `${polish.locale}`、`${polish.language}` 或 `${polish.country}`：

<info jarName="${polish.vendor}-${polish.name}-${polish.locale}-roadrunner.jar"

在翻译中使用引号和其他特殊字符

某些字符需要在您的翻译文件中进行 Java 编码。如果您正确地转义它们，通常是在开头使用反斜杠字符（\），那么您可以使用引号以及任何特殊字符；例如，您可以使用 \" 表示引号，\t 表示制表符，\\ 表示反斜杠。

无效的区域设置调用

`Locale.get()` 方法对其内容有些敏感。请确保始终直接给出翻译的键，而不是使用变量；否则，J2ME Polish 将无法正确嵌入翻译，您将遇到编译错误。

清单 7-30 展示了*错误*的做法。

**清单 7-30.** *无效的区域设置调用，因为键不是直接给出的*

// 永远不要这样做：

String key = "menu.StartGame";

this.menuScreen.append( Locale.get( key ), null );

5033CH07.qxd 6/17/05 11:50 AM 第 90 页

**90**

第 7 章 ■ 构建应用程序

相反，应像此示例中那样直接在调用中使用键：`this.menuScreen.append( Locale.get( "menu.StartGame" ), null );` 当您有多个参数时，需要将参数放在一个变量中；否则，J2ME Polish 再次无法正确处理该调用。清单 7-31 展示了错误的方式。

**清单 7-31.** *另一个无效的区域设置调用，因为参数是直接给出的*

// 永远不要这样做：

this.menuScreen.append( Locale.get( "game.StartMessage" ), new String[]{ userName, enemyname } );

相反，应在实际调用之前定义参数，如清单 7-32 所示。

**清单 7-32.** *更正后的区域设置调用*

// 这样是可以的：

String[] parameters = new String[]{ userName, enemyname };
this.menuScreen.append( Locale.get( "game.StartMessage" ), parameters );

■**注意** 在未来的版本中，J2ME Polish 将使用字节码处理，而不是在预处理阶段嵌入翻译，这样您就可以使用 Java 允许的任何调用方法。

**本地化 J2ME Polish GUI**

J2ME Polish GUI 使用多个文本，这些文本可以使用变量进行本地化。表 7-3 列出了可以在 *build.xml* 或任何 *messages.txt* 文件中设置的变量。

**表 7-3.** *本地化 J2ME Polish GUI*

**变量**

**默认值**

**说明**

polish.command.ok

确定

“确定”菜单项的标签。

polish.command.cancel

取消

“取消”菜单项的标签。

polish.command.select

选择

“选择”菜单项的标签，由隐式或独占列表或 ChoiceGroup 使用。

polish.command.mark

标记

“标记”菜单项的标签，用于多选列表或 ChoiceGroup。

polish.command.unmark

取消标记

“取消标记”菜单项的标签，用于多选列表或 ChoiceGroup。

polish.command.options

选项

当有多个菜单项可用时菜单的标签。

polish.command.delete

删除

“删除”菜单项的标签，由 TextField 使用。

5033CH07.qxd 6/17/05 11:50 AM 第 91 页

第 7 章 ■ 构建应用程序

**91**

**变量**

**默认值**

**说明**

polish.command.clear

清除

“清除”菜单项的标签，由 TextField 使用。

polish.title.input

输入

用于实际文本输入的原生 TextBox 的标题。仅当相应的 TextField 项没有标签时才使用此标题。当 TextField 有标签时，该标签将用作标题。

清单 7-33 展示了在 *messages.txt* 文件中定义其中一些变量的方法。

**清单 7-33.** *在 messages.txt 文件中本地化 GUI*
var:polish.command.cancel=Abbruch
var:polish.command.delete=Löschen
var:polish.title.input=Eingabe

您也可以在 *build.xml* 文件中定义 J2ME Polish GUI 的翻译。此方法仅在不需要进行完整的本地化/国际化时有用。清单 7-34 演示了如何定义一些德语翻译。

**清单 7-34.** *使用 <variables> 元素本地化 GUI*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
<deviceRequirements>
<requirement name="Identifier" value="Nokia/Series60" />
</deviceRequirements>
<build>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<variables>**
**<variable name="polish.command.cancel" value="Abbruch" />**
**<variable name="polish.command.delete" value="L&ouml;schen" />**
**<variable name="polish.title.input" value="Eingabe" />**
**</variables>**
</build>
<emulator />
</j2mepolish>

5033CH07.qxd 6/17/05 11:50 AM 第 92 页

**92**

第 7 章 ■ 构建应用程序

**集成第三方 API**

有时您需要在项目中使用第三方 API。您必须区分以源代码形式提供的 API 和仅以二进制形式提供的 API。另一种情况是所谓的可选 API，它们已预装在您的目标设备上，例如无线消息 API 或位置 API。根据 API 的类型，集成 API 需要不同的操作。

■**注意** 不要忘记在您的 IDE 中将库添加到项目的类路径中。

**集成源代码形式的第三方 API**

当第三方 API 以源代码形式提供时，您可以通过修改 *build.xml* 文件中 `<build>` 元素的 `sourceDir` 属性来集成它。考虑您正常的应用程序代码位于 *source/src* 目录，而第三方 API 的源代码位于 *source/thirdparty* 文件夹的情况。您现在可以使用 `<sources>` 元素添加第三方 API，如清单 7-35 所示。与大多数其他元素一样，您可以为您的源代码使用 `if` 和 `unless` 条件。

**清单 7-35.** *集成源代码形式的第三方 API*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
<deviceRequirements>
<requirement name="Identifier" value="Nokia/Series60" />
</deviceRequirements>
<build
**>**
**<sources>**
**<source dir="source/src" />**
**<source dir="source/thirdparty" />**
**<source dir="source/s60" if="polish.group.Series60" />**
**</sources>**
<midlet class="com.apress.roadrunner.Roadrunner" />
</build>
<emulator />
</j2mepolish>

5033CH07.qxd 6/17/05 11:50 AM 第 93 页

第 7 章 ■ 构建应用程序

**93**

■**提示** 通常您不需要指定在哪里查找源代码，因为当源代码位于 *source/src* 或 *src* 文件夹中时，J2ME Polish 会自动找到它们。

**集成二进制形式的第三方 API**

当第三方 API 仅以二进制形式提供时，您可以使用 *build.xml* 脚本中 `<build>` 部分的 `<libraries>` 元素来集成它。此元素可以指向 JAR 或 ZIP 文件，或指向包含第三方库（JAR 文件、ZIP 文件或 class 文件）的目录。当库位于 `${polish.home}/import` 文件夹中时，只需给出库的名称（而不是指定完整路径）。

在清单 7-36 中，集成了二进制库 TinyLine ( *http://www.tinyline.com*)。TinyLine 为 MIDP 2.0、Nokia Series 60 和 Personal Profile J2ME 设备提供专门的库，因此您需要通过使用 `if` 或 `unless` 属性来确保包含正确的库。

**清单 7-36.** *集成 TinyLine 二进制第三方包*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
<deviceRequirements>
<requirement name="Identifier" value="Nokia/Series60" />
</deviceRequirements>
<build
>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<libraries>**
**<library file="thirdparty/tinylines60.zip"**
**if="polish.group.Series60 and not polish.midp2" />**
**<library file="thirdparty/tinylinemidp2.zip"**
**if="polish.midp2" />**
**<library file="thirdparty/tinyline.zip"**
**if="polish.midp1 and not polish.group.Series60" />**
**</libraries>**
</build>
<emulator />
</j2mepolish>

5033CH07.qxd 6/17/05 11:50 AM 第 94 页

**94**

第 7 章 ■ 构建应用程序

**集成设备 API**

许多设备提供额外的可选 API。一个流行的例子是诺基亚 UI API，它提供了额外的图形和声音功能。当设备支持特定 API 时，会在 `${polish.home}/devices.xml` 文件中通过该设备的 `JavaPackage` 能力来注明。

假设您想使用摩托罗拉电话簿 API，您只需将相应的库文件 *phonebook.jar* 或 *phonebook.zip* 复制到 `${polish.home}/import` 文件夹中。然后，对于支持电话簿 API 的设备，它将自动被使用。

J2ME Polish 已经提供了大量 API，范围从 MIDP 1.0 到特定于供应商的 API，例如诺基亚 UI API 或西门子扩展 API。

您可以修改 `${polish.home}/apis.xml` 文件来指定不同的库名称、路径或别名。第 6 章对此进行了更详细的描述。

■**警告** 您不能在不支持可选 API 和特定于供应商的 API 的设备上使用它们。因此，如果某个设备不支持无线消息 API，您就不能在为该设备构建的应用程序中使用该 API。使用预处理和 `polish.api.[api-name]` 预处理符号在您的应用程序中进行必要的调整。

**混淆应用程序**

混淆对于 J2ME 应用程序至关重要，不仅因为它使逆向工程更加困难，而且还因为应用程序的大小会减小——在某些情况下会大幅减小。混淆器通常会剥离应用程序未使用的任何类、方法和字段。在下一步中，它们会重命名所有剩余的类。由于此功能，您需要定义不会被混淆的“入口点”。J2ME Polish 会自动将所有使用 `<midlet>` 元素定义的 MIDlet 添加到入口点列表中。使用嵌套的 `<keep>` 元素，您可以定义必须保留的其他类，即使它们没有被直接使用。例如，对于所有使用 `Class.forName( String )` 机制加载的类都是如此。大多数混淆器也接受嵌套的 `<parameter>` 元素提供的额外参数。清单 7-37 演示了如何使用 ProGuard 混淆器，其中 ProGuard 的字节码优化被停用。

**清单 7-37.** *使用 ProGuard 混淆器*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
<deviceRequirements>
<requirement name="Identifier" value="Nokia/Series60" />
5033CH07.qxd 6/17/05 11:50 AM 第 95 页

第 7 章 ■ 构建应用程序

**95**

</deviceRequirements>
<build>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<obfuscator name="ProGuard" >**
**<keep class="com.apress.roadrunner.DynamicClass" />**
**<parameter name="optimize" value="false" />**
**</obfuscator>**
</build>
<emulator />
**</j2mepolish>**

**使用默认包**

当您将 MIDlet 及其相关的辅助类放入不同的包中时，您是在维护良好的架构，但您可能需要为此牺牲一些 JAR 大小。J2ME Polish 可以将您的所有类移动到空的默认包中，这可以减小 JAR 文件的大小。您可以通过将混淆器的 `useDefaultPackage` 选项设置为 `true` 来在任何混淆器上启用此选项：`<obfuscator name="ProGuard" useDefaultPackage="true" />`。

■**警告** 在代码中使用 import 语句，而不是完全限定的类名；否则，J2ME Polish 将无法正确解析导入。因此，*不要*在不导入的情况下扩展一个类。这段代码将无法工作：`class MyClass extends com.company.package.OtherClass`。但这段代码是可以的：`import com.company.package.OtherClass; class MyClass extends OtherClass`。

**组合多个混淆器**

您可以通过指定多个 `<obfuscator>` 元素来同时组合多个混淆器。当您使用 `<keep>` 子元素时，只需在其中一个 `<obfuscator>` 元素中指定即可。然后，J2ME Polish 会将一个混淆器的混淆输出用作下一个混淆器的输入。清单 7-38 将 ProGuard 混淆器与 DashO 混淆器一起使用。组合多个混淆器是否能最小化 JAR 大小取决于混淆器和您的应用程序。在某些情况下，您最终得到的 JAR 文件甚至可能比只使用一个混淆器还要大。您的结果可能会有所不同。您可以从 *http://www.preemptive.com* 获取 DashO 混淆器。

**清单 7-38.** *组合多个混淆器*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
5033CH07.qxd 6/17/05 11:50 AM 第 96 页

**96**

第 7 章 ■ 构建应用程序

/>
<deviceRequirements>
<requirement name="Identifier" value="Nokia/Series60" />
</deviceRequirements>
<build>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<obfuscator name="ProGuard" />**
**<obfuscator name="Dasho" />**
</build>
<emulator />
</j2mepolish>

■**注意** 附录列出了所有支持的混淆器。您可以通过使用 `antcall` 机制来集成不支持的混淆器，通过该机制您可以调用 *build.xml* 脚本中的另一个目标：`<obfuscator name="antcall" target="obfuscate" />`。第 13 章提供了有关此扩展机制的更多详细信息。

**调试应用程序**

J2ME Polish 对多设备、区域设置和混淆器的支持对于创建应用程序非常有用，但对于开发阶段来说有点重量级。幸运的是，您可以通过稍微调整 *build.xml* 来选择轻量级的方法。

一种实现快速构建的策略是使用条件性的 `<deviceRequirements>`、`<localization>` 和 `<obfuscation>` 元素，这些元素可以通过设置 Ant 属性来启用和禁用。

另一种策略是在编译器模式下使用 J2ME Polish。在这种情况下，J2ME Polish 仅预处理、编译并可能预验证应用程序。此方法对于将 J2ME Polish 与 IDE 或 IDE 插件（如 EclipseME）集成非常有用。

如果您想调试由 J2ME Polish 编译的应用程序，您需要启用 `<debug>` 元素，以便在 class 文件中包含调试信息，例如变量名和源代码中的行号。或者，您可以提供自己的 `<compiler>` 元素。

在以下部分中，我将详细阐述这些策略。

**使用条件**

使用条件，您可以在 *build.xml* 中禁用和启用元素。您可以使用 Ant 属性 `test` 来区分测试模式和“真实”构建模式，在真实构建模式下所有内容都被激活。清单 7-39 为您的 *build.xml* 文件提供了一个蓝图。

5033CH07.qxd 6/17/05 11:50 AM 第 97 页

第 7 章 ■ 构建应用程序

**97**

**清单 7-39.** *在 J2ME Polish 中使用条件*

<project name="roadrunner" default="j2mepolish">
<!-- 定义 J2ME Polish 的安装文件夹 -->
<property name="polish.home" location="C:\programs\J2ME-Polish" />
<!-- 定义 WTK 的安装文件夹 -->
<property name="wtk.home" location="C:\WTK22" />
<!-- 定义 J2ME Polish 任务，类路径请放在一行 -->
<taskdef name="j2mepolish"
classname="de.enough.polish.ant.PolishTask"
classpath="${polish.home}/import/enough-j2mepolish-build.jar: ${polish.home}/import/jdom.jar:
${polish.home}/import/proguard.jar"/>
<!-- 激活测试模式 -->
**<target name="test">**
**<property name="test" value="true" />**
**<property name="work.dir" value="build/test" />**
**</target>**
<!-- 正常设置 -->
**<target name="init">**
**<property name="test" value="false" />**
**<property name="work.dir" value="build/real" />**
**</target>**
<!-- 使用 J2ME Polish 开始构建 -->
<target name="j2mepolish" depends="init">
<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-${polish.locale}-app.jar"
/>
**<deviceRequirements if="test">**
<requirement name="Identifier" value="Nokia/Series60" />
</deviceRequirements>
**<deviceRequirements unless="test">**
<requirement name="JavaPlatform" value="MIDP/2+.0+" />
</deviceRequirements>
**<build workDir="${work.dir}" >**
<midlet class="com.apress.roadrunner.Roadrunner" />
<resources
dir="resources"
excludes="*.definition"
>
**<localization locales="de, en, fr_CA" unless="test" />**
**<localization locales="en" if="test" />**
5033CH07.qxd 6/17/05 11:50 AM 第 98 页

**98**

第 7 章 ■ 构建应用程序

</resources>
**<debug level="error" if="test" />**
**<obfuscator name="ProGuard" unless="test" />**
</build>
**<emulator if="test" />**
</j2mepolish>
</target>
<target name="clean">
<delete dir="build" />
<delete dir="dist" />
</target>
</project>

在此示例中，您有一个 `test` 目标，它将 Ant 属性 `test` 设置为 `true`，并将工作目录设置为 *build/test*。这是推荐的，因为您还启用了 `<debug>` 模式。`<j2mepolish>` 目标依赖于 `<init>` 目标，该目标将 `test` 属性设置为 `false` 并定义另一个工作目录。当 `<test>` 目标首先被执行时，这些属性就不能再被 `<init>` 目标更改了：

> ant test j2mepolish

元素 `<deviceRequirements>`、`<localization>`、`<debug>`、`<obfuscator>` 和 `<emulator>` 都依赖于 `test` 属性。当它设置为 `true` 时，J2ME Polish 将仅为“Nokia/Series60”设备编译应用程序，仅使用英语 `en` 区域设置，启用调试模式，跳过混淆，并在构建后启动模拟器。当 `test` 属性为 `false` 时，J2ME Polish 为所有 MIDP 2.0 设备编译应用程序；使用 `en`、`de` 和 `fr_CA` 区域设置；禁用调试模式；并混淆应用程序。

**将 J2ME Polish 用作编译器**

您可以将 J2ME Polish 用作编译器，在这种情况下，仅执行预处理、编译以及可能的预验证步骤。这对于与可用的 J2ME 插件（如 EclipseME）或支持 J2ME 的 IDE（如 NetBeans）进行交互非常有用。

编译器模式通过使用 `compilerMode`、`compilerDestDir` 和 `compilerModePreverify` 属性来激活，如清单 7-40 所示。

**清单 7-40.** *将 J2ME Polish 用作编译器*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
5033CH07.qxd 6/17/05 11:50 AM 第 99 页

第 7 章 ■ 构建应用程序

**99**

<deviceRequirements>
<requirement name="Identifier" value="Generic/midp1" />
</deviceRequirements>
**<build**
**compilerMode="true"**
**compilerModePreverify="true"**
**compilerDestDir="preverified"**
**>**
<midlet class="com.apress.roadrunner.Roadrunner" />
</build>
<emulator />
</j2mepolish>

J2ME Polish 根据所使用的 WTK 将 javac-target 设置为 1.1 或 1.2。您也可以通过指定 `<build>` 元素的 `javacTarget` 属性来直接设置目标。除非使用 `<debug>` 元素启用调试模式，否则不会添加调试信息。

如果您需要完全控制编译器设置，您也可以使用 `<compiler>` 元素，它是 `<build>` 部分的嵌套元素。清单 7-41 演示了如何使用 `<compiler>` 元素。

**清单 7-41.** *使用 <compiler> 元素*

<build
compilerMode="true"
compilerModePreverify="true"
compilerDestDir="preverified"
>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<compiler**
**compiler="jikes"**
**debug="true"**
**debuglevel="lines,vars,source"**
**optimize="true"**
**/>**
</build>

■**注意** 您可以独立于编译器模式使用 `<compiler>` 元素；它支持原始 Ant `<javac>` 任务的所有属性和元素。

5033CH07.qxd 6/17/05 11:50 AM 第 100 页

**100**

第 7 章 ■ 构建应用程序

**总结**

在本章中，您学习了如何使用 Ant 和 J2ME Polish 构建应用程序。您可以为您的应用程序配备特定资源而无需修改源代码，使用混淆器来最小化应用程序大小，本地化您的应用程序，以及集成第三方 API。

在下一章中，您将探索预处理的可能性。与打包解决方案相比，预处理是在应用程序的源代码级别上使用的。

5033CH08.qxd 6/17/05 12:06 PM 第 101 页

第 8 章

■ ■ ■

预处理

**在本章中：**

• 了解 J2ME Polish 为何对其 J2ME API 使用预处理。

• 使用指令控制预处理。

• 使用 J2ME Polish 支持的标准预处理变量。

• 在 J2ME Polish 中定义您自己的变量和符号。

• 使用属性函数转换变量值。

• 探索预处理的一些实际应用。

开发 J2ME 应用程序的最大挑战之一是调整它们以在许多不同的设备上工作。除了您在第 6 章中了解到的详细设备数据库之外，J2ME Polish 还提供了一个强大的预处理器来帮助您应对这一挑战。预处理是在不损失可移植性的情况下优化应用程序的关键。本章为您提供了必要的知识，以调整您的应用程序以适应不同的环境，规避已知问题，并利用特定于设备的选项。

**为什么需要预处理？**

通常，您需要使用特定于设备的代码来创建最佳应用程序。例如，在诺基亚的 MIDP 1.0 设备上，您可以播放声音并使用全屏模式，但前提是您使用诺基亚的 UI API。但是，当您使用此 API 时，您的应用程序将无法在其他手机上运行。那么您能做什么呢？

Java 允许您动态加载类。因此，理论上，您可以创建类的多个版本，并在应用程序运行时动态加载正确的版本。不幸的是，这会大大降低应用程序的性能，同时增加其大小。在资源受限的 J2ME 世界中，这两个结果都是不可接受的。此外，某些设备不接受任何引用未知类的应用程序，即使这些类仅通过 Java 的反射机制加载。

另一种方法是为每个目标设备，或者至少为每种设备类型，创建一个单独的项目。这解决了性能和大小问题，但给您留下了巨大的维护负担。想象一下，要在所有这些单独的项目中实现一个新功能需要做多少工作！

**101**

5033CH08.qxd 6/17/05 12:06 PM 第 102 页

**102**

第 8 章 ■ 预处理

第三种方法是使用 `static final` 变量和编译器来调整您的代码。您可以使用普通的 `if` 分支，例如 `if (Configuration.IS_NOKIA_SERIES60)` 来区分不同的配置。当 `Configuration.IS_NOKIA_SERIES60` 是一个 `final static boolean` 字段并且为 `false` 时，编译器可以在编译阶段消除后续的死代码。这里的主要问题是您需要为每个目标设备创建并包含一个 `Configuration` 类，并且需要以某种方式集成它。另一个问题是您只能在普通代码中使用 `if` 分支，因此您不能采用这种方法来导入不同的 API 或使用不同的字段等。此外，您需要确保在更改 `Configuration` 类时重新编译所有类。

一个更强大的解决方案是使用*切面*来调整您的应用程序。在面向切面编程中，您编写一个普通的应用程序，并使用切面在源代码中的特定位置（称为*连接点*）注入额外的代码。面向切面开发非常强大，允许您做一些令人惊奇的事情，比如更改类的层次结构。然而，这种新范式也非常复杂且难以学习。从好的方面来说，您最终会得到一个非常干净且易于理解的应用程序。另一方面，您需要为每个目标设备提供一组不同的切面，这意味着您需要再次拆分您的应用程序项目。

■**提示** 在面向切面开发中，您需要区分应用程序的主要目的（例如，银行应用程序）和其次要目的（例如，安全地验证其用户）。请参阅 *http://en.wikipedia.org/wiki/Aspect-oriented_programming* 以了解有关面向切面开发的更多信息。AspectJ 项目 ( *http://eclipse.org/aspectj*) 是 Java 世界的一个流行解决方案。

一个简单而有效的解决方案是*预处理*您的应用程序。预处理在编译之前更改您的应用程序代码。您可以使用预处理指令、符号和变量来控制此过程。J2ME Polish 不仅包含一个强大的预处理器，而且还提供了一种标准化的方式来查询和使用当前目标设备的能力。得益于集成的设备数据库，您可以开箱即用地访问超过 300 种不同手机的详细配置，因此您无需创建和维护自己的配置。预处理允许您调整您的应用程序以适应不同的设备和配置，而不会降低运行时性能、造成不兼容或拆分您的项目。

预处理听起来比实际复杂。清单 8-1 显示了一个使用预处理来确定如何播放声音的代码片段。

**清单 8-1.** *仅在 MMAPI 或诺基亚的 UI API 可用时播放声音*
public void playAudio() {
**//#if polish.api.mmapi || polish.midp2**
try {
Player player = Manager.createPlayer(
getClass().getResourceAsStream("/music.mid"), "audio/midi");
player.realize();
player.prefetch();
5033CH08.qxd 6/17/05 12:06 PM 第 103 页

第 8 章 ■ 预处理

**103**
player.start();
} catch (Exception e) {
//#debug error
System.out.println("Unable to start audio playback" + e);
}
**//#elif polish.api.nokia-ui**
try {
byte[] soundData = loadSoundData();
Sound sound = new Sound( soundData, Sound.FORMAT_TONE );
sound.play( 1 );
} catch (IllegalArgumentException e) {
//#debug error
System.out.println("Unable to play Nokia sound" + e );
}
**//#else**
System.out.println("No sound playback supported.");
**//#endif**
}

当设备支持 MMAPI 或 MIDP 2.0 配置文件时，您可以使用 `javax.microedition.media.Player` 进行音频播放。语句 `//#if polish.api.mmapi || polish.midp2` 测试这些要求。或者，您可以使用诺基亚的 UI API 来播放声音，因此代码在 `//#elif polish.api.nokia-ui` 语句中测试此选项。

清单 8-2 演示了如何使用 J2ME Polish 设备数据库来检查当前目标设备的能力。对于设备支持的每个 API，都会定义预处理符号 `polish.api.[api-name]`。如果设备支持 MIDP 1.0 平台，则定义 `polish.midp1` 符号。类似地，当目标设备支持 MIDP 2.0 平台时，会定义 `polish.midp2` 预处理符号。

**清单 8-2.** *为支持诺基亚 UI API 的设备预处理的代码*
public void playAudio() {
**//#if polish.api.mmapi || polish.midp2**
//# try {
//# Player player = Manager.createPlayer(
//# getClass().getResourceAsStream("/music.mid"), "audio/midi");
//# player.realize();
//# player.prefetch();
//# player.start();
//# } catch (Exception e) {
//# //#debug error
//# System.out.println("Unable to start audio playback" + e);
//# }
**//#elif polish.api.nokia-ui**
try {
byte[] soundData = loadSoundData();
Sound sound = new Sound( soundData, Sound.FORMAT_TONE );
5033CH08.qxd 6/17/05 12:06 PM 第 104 页

**104**

第 8 章 ■ 预处理
sound.play( 1 );
} catch (IllegalArgumentException e) {
//#debug error
System.out.println("Unable to play Nokia sound" + e );
}
**//#else**
//# System.out.println("No sound playback supported.");
**//#endif**
}

那么，当设备仅支持诺基亚的 UI API，而不支持 MMAPI 或 MIDP 2.0 标准时，会发生什么？在这种情况下，J2ME Polish 会注释掉所有用于不可用能力的代码。只有 `//#elif polish.api.nokia-ui` 和 `//#else` 指令之间的代码保持活动状态，如清单 8-2 所示。

顺便说一下，J2ME Polish 会自动为您调用预处理器。因此，您只需在代码中包含必要的预处理语句。其余的事情由 J2ME Polish 处理。

**预处理指令**

J2ME Polish 为您提供了强大的预处理指令来控制预处理阶段。所有指令都以标准的 Java 行注释字符（双斜杠）开头，后跟一个井号，这样它们就不会混淆您的 IDE 和 Java 编译器。表 8-1 列出了所有支持的预处理指令。以下各节将详细描述这些指令。

■**注意** 您也可以实现自己的预处理指令，如第 13 章所述。

**表 8-1.** *J2ME 预处理指令*

**指令**

**描述**

**示例**

//#ifdef

检查单个预处理符号是否已定义

//#ifdef polish.api.mmapi

//#ifndef

检查单个预处理符号是否未定义

//#ifndef polish.api.mmapi

//#else

在 //#ifdef 或 //#if 指令中分支

//#else

//#elifdef

分支并检查另一个单个预处理符号

//#elifdef polish.midp2

//#elifndef

分支并检查另一个单个预处理符号是否未定义

//#elifndef polish.midp2

//#endif

结束 //#ifdef 或 //#if 块

//#endif

5033CH08.qxd 6/17/05 12:06 PM 第 105 页

第 8 章 ■ 预处理

**105**

**指令**

**描述**

**示例**

//#if

检查多个预处理符号或比较预处理变量

//#if polish.api.mmapi || polish.midp2

//#elif

分支并检查其他预处理符号或预处理变量

//#elif polish.BitsPerPixel > 10

//#condition

包含或排除整个文件

//#condition polish.usePolishGui || polish.midp2

//#define

定义临时预处理符号或变量

//#define tmp.useSound

//#undefine

移除已定义的预处理符号或变量

//#undefine tmp.useSound

//#

注释掉 //#if 或 //#ifdef 块中的语句

//# return true;

//#=

插入预处理变量

//#= int screenWidth = ${polish.ScreenWidth};

//#debug

使用 J2ME Polish 的日志框架

//#debug error

//#foreach

使用预处理变量的多个值

//#foreach format in polish.SoundFormat

//#next

结束 #foreach 块

//#next format

//#style

为后续的项或屏幕分配 J2ME Polish CSS 样式

//#style importantText

//#include

包含代码片段

//#include ${polish.source}/includes/audioplayback.txt

//#message

在构建阶段打印消息

//#message preprocessing for ${polish.Identifier}

//#todo

在构建阶段打印消息，自动包含源文件的名称和行号

//#todo implement playSound()

最重要的指令是 `#if` 和 `#=` 指令，所以让我们先看看它们。

**分支您的代码**

If-then-else 分支是众所周知的编程结构。`#if` 指令及其各种相关指令允许您非常有效地将源代码调整到不同的手机和配置。这些调整的可能性是无穷无尽的。您可以轻松地创建量身定制的应用程序，这些应用程序使用特定于设备的 API，规避目标设备的已知问题，或根据配置更改应用程序流程。

检查单个预处理符号

使用 `#ifdef`、`#ifndef`、`#elifdef`、`#elifndef`、`#else` 和 `#endif` 指令来检查单个预处理符号。预处理符号就像一个布尔变量：要么已定义（true），要么未定义（false）。预处理符号在设备数据库中通过设备的 `<features>`（例如 `polish.hasPointerEvents`）以及间接通过设备 `<capability>` 元素（例如 `polish.CanvasSize:defined` 和 `polish.api.mmapi`）定义。您可以通过使用 *build.xml* 文件中 `<build>` 部分的 `symbols` 属性来定义您自己的项目特定符号。表 8-2 解释了这些指令的含义。

**表 8-2.** *检查单个预处理符号的指令*
**指令**

**含义**

**解释**

//#ifdef [ *符号*]

如果 [ *符号*] 已定义

当下一节应该被编译时，符号 [ *符号*] 需要被定义。

//#ifndef [ *符号*]

如果 [ *符号*] 未定义

当下一节应该被编译时，符号 [ *符号*] 必须未被定义。

//#else

否则

当相应的 #if 子句失败时，将编译以下部分（反之亦然）。

//#elifdef [ *符号*]

否则如果 [ *符号*] 已定义

当下一节应该被编译时，符号 [ *符号*] 需要被定义，并且前一部分需要为假。

//#elifndef [ *符号*]

否则如果 [ *符号*] 未定义

当下一节应该被编译时，符号 [ *符号*] 必须未被定义，并且前一部分需要为假。

//#endif

if 块结束

每个 #ifdef 和 #ifndef 块的结束。

每个 `//#ifdef` 或 `//#ifndef` 指令都需要由一个 `//#endif` 语句关闭。使用 `//#else`、`//#elifdef` 和 `//#elifndef` 指令在 `//#ifdef` 或 `//#ifndef` 块内进行分支。清单 8-3 显示了一个如何使用 `#ifdef` 指令在具有摄像头的设备上包含一个菜单项的示例。

**清单 8-3.** *仅为带有摄像头的设备包含一个额外的菜单项*
this.menuScreen = new List( "Menu", List.IMPLICIT );
this.menuScreen.append("Load Photos", null );
this.menuScreen.append("View Photos", null );
**//#ifdef polish.hasCamera**
this.menuScreen.append("Create Photo", null );
**//#endif**

检查多个符号和比较变量

通常，您需要同时检查多个符号或在预处理代码中比较变量。您可以使用 `#if`、`#elif`、`#else` 和 `#endif` 指令来完成所有这些操作。表 8-3 解释了这些指令的含义。

5033CH08.qxd 6/17/05 12:06 PM 第 107 页

第 8 章 ■ 预处理

**107**

**表 8-3.** *检查多个预处理符号和比较变量的指令*
**指令**

**含义**

**解释**

//#if [ *项*]

如果 [ *项*] 为真

如果下一节应该被编译，则指定的项必须为真。

//#else

否则

当之前的 if 子句失败时，将编译以下部分（反之亦然）。

//#elif [ *项*]

否则如果 [ *项*] 为真

如果下一节应该被编译，则指定的项需要为真，并且前一部分需要为假。

//#endif

if 块结束

每个 #if 块的结束。

您已经在清单 8-1 和 8-2 中看到了 `#if` 指令的实际应用。您也可以使用 `#if` 和 `#elif` 来检查单个预处理符号。唯一的区别是 `#ifdef` 指令在构建阶段处理速度稍快，但在当今的高速计算机上，您可能不会注意到差异。

您可以使用普通的布尔运算符（`&&`、`||`、`!` 和 `^`）和括号来评估复杂的项，如下所示：

//#if (polish.api.mmapi || polish.api.nokia-ui) && !polish.hasPointerEvents
布尔运算符的参数是符号，当它们被定义时为真，否则为假。表 8-4 解释了这些运算符。如果您愿意，也可以使用书面运算符 `and`、`or`、`not` 和 `xor` 来代替符号。如果您在 *build.xml* 文件中使用或定义此类项，这将特别方便，因为某些字符（如 `&`）需要进行 XML 编码（例如，编码为 `&amp`）。

**表 8-4.** *用于评估项的布尔运算符*
**布尔运算符**

**含义**

**解释**

&& 或 and

与

两个参数/符号都需要被定义：
true && true = true, true && false = false, false && true = false, false && false = false

|| 或 or

或

至少一个参数/符号必须被定义：
true || true = true, true || false = true, false || true = true, false || false = false

^ 或 xor

异或

有且仅有一个参数/符号必须被定义：
true ^ false = true, false ^ true = true, true ^ true = false, false ^ false = false

! 或 not

非

参数/符号必须未被定义：
! false = true, ! true = false

您也可以在 `#if` 指令中比较变量和常量，如清单 8-4 所示。

5033CH08.qxd 6/17/05 12:06 PM 第 108 页

**108**

第 8 章 ■ 预处理

**清单 8-4.** *仅在设备至少有 65,000 种颜色时显示动画*
public void startApp() {
**//#if polish.api.mmapi && (polish.BitsPerColor >= 16)**
showSplashVideo();
**//#else**
showSplashImage();
**//#endif**
}

使用 `==`、`!=`、`>`、`<`、`<=` 和 `>=` 比较器来比较变量。比较器的参数是变量或常量。一个项可以同时包含比较器和布尔运算符，前提是各部分用括号分隔。表 8-5 解释了可用的比较器。

**表 8-5.** *用于评估项的比较器*

**比较器**

**含义**

**解释**

**示例**

==

等于

左右参数必须相等。可以比较整数和字符串。

8 == 8 = true, Nokia == Nokia = true,
//#if polish.BitsPerPixel == 8,
//#if polish.vendor == Nokia

!=

不等于

左右参数必须不相等。可以比较整数和字符串。

8 != 8 = false, Nokia != Sony-Ericsson = true,
//#if polish.BitsPerPixel != 8,
//#if polish.vendor != Nokia

>

大于

左参数必须大于右参数。只能比较整数。

8 > 8 = false, 16 > 8 = true,
//#if polish.BitsPerPixel > 8

<

小于

左参数必须小于右参数。只能比较整数。

8 < 8 = false, 8 < 16 = true,
//#if polish.BitsPerPixel < 8

>=

大于或等于

左参数必须大于或等于右参数。只能比较整数。

8 >= 8 = true, 16 >= 8 = true,
//#if polish.BitsPerPixel >= 8

<=

小于或等于

左参数必须小于或等于右参数。只能比较整数。

8 <= 8 = true, 8 <= 16 = false,
//#if polish.BitsPerPixel <= 8

某些变量可能使用不同的单位。例如，`polish.HeapSize` 变量有时以千字节为单位定义，有时以兆字节为单位定义。您不能使用小于或大于比较器来比较此类变量，因为这些比较器只接受数值。在这种情况下，您可以使用属性函数。当您使用这样的函数时，您需要在函数调用前加上美元符号（$）并将其括在花括号内。以下示例使用 `bytes` 函数比较当前设备的堆大小：

//#if ${ bytes( polish.HeapSize ) } > 102400

5033CH08.qxd 6/17/05 12:06 PM 第 109 页

第 8 章 ■ 预处理

**109**

当给定的内存值是动态时，`bytes` 函数将返回 –1，因此可能需要测试两种情况：

//#if ( ${ bytes( polish.HeapSize ) } > 102400 ) or (polish.HeapSize == dynamic)
您也可以对常量使用函数，例如 `100 kb`，这通常可以提高代码的可读性：

//#if ${ bytes( polish.HeapSize ) } > ${ bytes( 100 kb ) }
您将在本章后面的“管理变量和符号”部分中找到有关使用属性函数的更多信息。

排除或包含完整的类

您可以使用 `#condition` 指令包含或排除完整的类或接口。例如，J2ME Polish 使用此功能来仅在实际使用 J2ME Polish GUI 时才包含其 GUI 包装类。另一种可能的情况是，仅当目标设备支持必要的要求时才包含音频播放器，如清单 8-5 所示。`#condition` 指令接受 `#if` 指令的所有参数，因此您可以在其中使用逻辑运算符和比较器。您可以将 `#condition` 指令放在文件中的任何位置，但推荐的位置是在第一行。

**清单 8-5.** *仅在设备可以播放声音时包含音频播放器类*

**//#condition polish.api.mmapi || polish.api.nokia-ui**
package com.company.j2me.audio;
public class AudioPlayer {
...
}

■**注意** 当您在使用 `AudioPlayer` 类之前使用 `#if` 指令检查设备是否支持音频时，您实际上并不需要 `#condition` 指令，因为混淆器会负责移除任何未使用的类。但是 `#condition` 指令强制您一致地使用这些检查；否则，您将遇到编译器错误。

**定义临时符号和变量**

您可以通过使用 `#define` 和 `#undefine` 指令在源代码中定义和移除预处理符号和变量。

5033CH08.qxd 6/17/05 12:06 PM 第 110 页

**110**

第 8 章 ■ 预处理

当您有一个复杂的预处理项时，您可以测试此项一次，然后设置一个指示该项结果的预处理符号。稍后，您只需要检查该符号。此类符号仅在其被定义的文件中有效。为了表明这些符号是临时的，您应该以 `tmp.` 开头命名它们。清单 8-6 演示了如何定义临时符号和变量。

**清单 8-6.** *在源代码中定义符号和变量*

//#if config.useFullScreen && (polish.midp2 || polish.classes.fullscreen:defined)
**//#define tmp.useFullScreen**
**//#define tmp.message = Hello fullscreen World!**
//#else
**//#define tmp.message = Hello World!**
//#endif

使用 `#undefine` 指令，您可以移除任何符号和变量。通常，这不是必需的，因为 J2ME Polish 在完成对相应源文件的预处理后会自动移除所有临时符号和变量。

■**提示** 您可以通过在 *build.xml* 文件的 `<variables>` 部分中定义它们来全局定义条件变量。使用 `if` 或 `unless` 属性来设置它们：`<variable name="config.Message" value="Hello Moto" if="polish.Vendor == Motorola" />`。

**在代码中包含变量的值**

您可以通过使用 `#=` 指令在代码中包含预处理变量的值。您可以包含您自己的变量，以及设备数据库中的任何设备能力。清单 8-7 显示了如何在您的应用程序中使用特定于设备的硬编码值。您可以通过测试预处理符号 `[variable-name]:defined` 来检查变量是否可用，例如 `//#if polish.ScreenWidth:defined`。当您包含变量时，您需要用美元符号和花括号将变量名括起来，就像 *build.xml* 文件中的 Ant 属性一样。

**清单 8-7.** *使用特定于设备的硬编码值*

//#ifdef polish.FullCanvasSize:defined
**//#= final int width = ${polish.FullCanvasWidth};**
**//#= final int height = ${polish.FullCanvasHeight};**
//#else
final int width = myCanvas.getWidth();
final int height = myCanvas.getHeight();
//#endif

5033CH08.qxd 6/17/05 12:06 PM 第 111 页

第 8 章 ■ 预处理

**111**

**单独使用多个变量值**

在设备数据库中，许多设备能力包含多个值。例如，`polish.SoundFormat`、`polish.ImageFormat` 和 `polish.VideoFormat` 能力包含多个值，用逗号分隔。您可以使用 `#foreach` 指令单独访问每个值。例如，J2ME Polish 的日志框架使用此机制来包含所有已注册的日志处理器。

在 `#foreach` 指令中，您定义一个临时的循环变量，该变量保存变量的每个值：

//#foreach format in polish.SoundFormat

您需要使用 `#next [loop-var-name]` 指令结束每个 `#foreach` 块：

//#next format

您可以遍历任何用逗号分隔其值的变量。

清单 8-8 演示了如何遍历设备支持的所有声音格式。

**清单 8-8.** *遍历目标设备的所有声音格式*
String format;
**//#foreach format in polish.SoundFormat**
format = "${ lowercase( format ) }";
System.out.println( "The audio-format " + format + " is supported." );
**//#next format**

■**警告** `#foreach` 循环内的代码片段将被复制到预处理后的源代码中，次数与值的数量相同。此复制过程有两个含义：任何变量都应在循环外部定义（但当然可以在循环内部设置和使用），并且当应用程序被调试或编译时，断点或错误可能指向错误的源代码行。如果变量未定义，则整个块将被注释掉，并且根本不会被编译。

您可以使用 `number` 属性函数来检索变量中单独值的数量。例如，当您想在 `#foreach` 循环中填充数组时，这很有用。清单 8-9 展示了一个取自 J2ME Polish 日志框架的真实示例。

**清单 8-9.** *使用 number 属性函数确定单独条目的数量*

//#if polish.log.handlers:defined
private static LogHandler[] handlers;
static {
//#= handlers = new LogHandler[ ${ number( polish.log.handlers )} ];
int i = 0;
//#foreach handler in polish.log.handlers
5033CH08.qxd 6/17/05 12:06 PM 第 112 页

**112**

第 8 章 ■ 预处理
//#= handlers[i] = new ${ classname( handler )}();
i++;
//#next handler
}
//#endif

**包含外部代码**

您可以使用 `#include` 指令包含外部代码片段。通过这种机制，您可以只创建一次常用的代码片段，而不是使用复制和粘贴来复制功能。

包含的片段是相对于 *build.xml* 文件的位置加载的。您可以使用 `polish.source` 变量来包含相对于源目录的文件：

//#include ${polish.source}/includes/standard.java

**分析预处理阶段**

预处理可能是一项复杂的工作。`#message` 和 `#todo` 指令通过在构建过程中打印消息来帮助您跟踪预处理逻辑。

您可以在消息中包含任何变量。当这些变量被定义时，它们会被其值替换。`#todo` 指令还会将源代码的名称和行号添加到输出中。清单 8-10 演示了这两个指令的用法和输出。

**清单 8-10.** *在构建阶段显示消息*

//#if polish.api.mmapi
**//#message The device ${polish.Identifier} supports the MMAPI.**
//#else
**//#todo Implement audio playback for device ${polish.Identifier}.**
//#endif

// 当您为 SE/K700 构建时的输出：
MESSAGE: The device Sony-Ericsson/K700 supports the MMAPI.

// 当您为 Nokia/7650 构建时的输出：
TODO: com.company.package.ClassName line 55: Implement audio playback for device Nokia/7650.

**隐藏语句**

隐藏语句是预处理的一种有点奇怪的应用，但有时是必要的，以免 IDE 混淆。

有时一个方法中有多个 `return` 语句。在这种情况下，您可以通过以 `//#` 开头，后跟一个空格和实际语句来注释掉除最后一个 `return` 语句之外的所有语句。当 J2ME Polish 预处理您的源代码时，它将激活正确的 `return` 语句。清单 8-11 显示了一个方法，当设备的堆大小大于 500 KB 时，它会缓存图像以便更快地检索。

5033CH08.qxd 6/17/05 12:06 PM 第 113 页

第 8 章 ■ 预处理

**113**

**清单 8-11.** *隐藏 return 语句*

public Image loadImage( String url )
throws IOException
{
//#if ${ bytes( polish.HeapSize ) } <= ${ bytes( 500 kb ) }
**//# return Image.createImage( url );**
//#else
Image image = (Image) this.cachedImages.get( url );
if (image == null) {
image = Image.createImage( url );
this.cachedImages.put( url, image );
}
**return image;**
//#endif
}

**日志记录**

J2ME Polish 包含一个日志框架，允许您在设备上查看记录的消息，并启用不同的日志级别（或完全禁用）。您可以使用 `#debug` 指令记录消息。然后，后续的 `System.out.println()` 消息将被转发到日志系统。清单 8-12 演示了日志系统的用法。第 9 章涵盖了日志框架的详细信息。

**清单 8-12.** *记录错误消息*

try {
this.optionalImage = Image.createImage( "/optional.png" );
} catch (IOException e) {
**//#debug**
**System.out.println("Unable to load optional.png" + e );**
}

**设置 CSS 样式**

您可以使用 `#style` 指令为 UI 项应用 CSS 样式。当您使用 J2ME Polish 的高级 GUI 时，您可以在 *resources/polish.css* 文件中定义此类样式。第 12 章将详细讨论整个过程。清单 8-13 显示了如何为 `StringItem` 应用样式。

**清单 8-13.** *为 UI 项设置样式*

**//#style text**
StringItem item = new StringItem( null, "Hello World!" );

5033CH08.qxd 6/17/05 12:06 PM 第 114 页

**114**

第 8 章 ■ 预处理

**嵌套指令**

您可以将指令嵌套和混合到任何级别，因此您可以在 `#if` 分支内检查特殊情况。清单 8-14 显示了一个示例。

**清单 8-14.** *嵌套指令*

//#ifdef polish.group.Series40
doSomething( 40 );
//#if polish.Identifier != Nokia/6230
doAnotherThing();
//#endif
//#elifdef polish.group.Series60
doSomething( 60 );
//#endif

**管理变量和符号**

大多数预处理指令需要在其上操作的预处理符号或变量。许多符号和变量在设备数据库中定义，但您也可以自由定义自己的变量和符号。

对于每个预处理变量，会自动定义几个符号：为每个已定义的变量设置符号 `[variable-name]:defined`，并为变量持有的每个值定义一个 `[variable-name].[value]` 符号。例如，对于虚构的变量 `config.Locations` 及其同样虚构的值 `London`、`New-York`、`Moscow`，将自动定义以下符号：

• `config.Locations:defined`
• `config.Locations.London`
• `config.Locations.New-York`
• `config.Locations.Moscow`

当变量中只有一个值时，使用相同的机制。例如，如果只为 `config.Locations` 变量设置了 `London`，则将定义预处理符号 `config.Locations:defined` 和 `config.Locations.London`。

除了定义您自己的符号和变量之外，您可能还需要在使用或比较变量之前对其进行转换。J2ME Polish 为此提供了属性函数。在本节中，您将学习如何定义和转换符号和变量。但首先，让我们看一下 J2ME Polish 设备数据库中已经定义的符号。

**使用标准预处理符号和变量**

得益于 J2ME Polish 的设备数据库，许多常用的符号和变量已经开箱即用地定义好了。每个设备特性都会产生相应的预处理符号，每个能力都由其相应的预处理变量表示。表 8-6 列出了最常用的变量和符号。（有关基于 XML 的设备数据库的详细信息，请参阅第 6 章。）

5033CH08.qxd 6/17/05 12:06 PM 第 115 页

第 8 章 ■ 预处理

**115**

**表 8-6.** *标准预处理变量和符号*
**变量**

**符号**

**解释**

polish.BitsPerPixel

polish.BitsPerPixel.4, 颜色深度：
1=单色，
polish.BitsPerPixel.16, 等。
4=16 色，8 = 256 色，12 = 4,096 色，16 = 65,536 色，18 = 262,144 色，24 = 16,777,216 色

polish.ScreenSize

polish.ScreenSize.176x208, 屏幕分辨率（以像素为单位）的宽度乘以高度，例如，176✕208
polish.ScreenWidth.176,
polish.ScreenHeight.208

polish.CanvasSize

类似 ScreenSize

MIDP 画布的宽度乘以高度

polish.FullCanvasSize

类似 ScreenSize

全屏模式下 MIDP 画布的宽度乘以高度

polish.JavaPlatform

polish.midp1 或 polish.midp2

支持的 Java 平台，例如，MIDP 1.0 或 MIDP 2.0

polish.JavaConfiguration

polish.cldc1.0 或 polish.cldc1.1

支持的 Java 配置，例如，CLDC 1.0 或 CLDC 1.1。

polish.JavaPackage

polish.api.nokia-ui, polish.api.mmapi, 支持的 API，例如，诺基亚 UI 或 MMAPI
等。

polish.JavaProtocol

polish.JavaProtocol.serial, 支持的数据交换协议*
polish.JavaProtocol.https

polish.HeapSize

最大堆大小，例如，500KB 或 1.2MB

polish.MaxJarSize

MIDlet JAR 包的最大大小，例如，100KB 或 2MB**

polish.StorageSize

所有应用程序和数据的最大大小，例如，4MB

polish.OS

polish.OS.Motorola, polish.OS.Symbian, 设备的操作系统，例如，Symbian
等。

polish.VideoFormat

polish.video.3gpp, polish.video.mpeg-4

设备支持的视频格式，例如，3gpp 和 MPEG-4

polish.SoundFormat

polish.audio.midi, polish.audio.wav

设备支持的声音格式，例如，MIDI 和 WAV

polish.Bugs

polish.Bugs.drawRgbOrigin, 已知问题的简短名称
polish.Bugs.ImageIOStreamAutoClose, 等。

**所有 MIDP 1.0 设备都支持 HTTP。所有 MIDP 2.0 设备也支持 HTTPS。*
***请注意，这些有时只是一个建议（摩托罗拉、西门子）。*

**设置符号和变量**

您可以在多个位置定义自己的变量和符号。您还可以有条件地设置变量以获得更大的灵活性。

在 *build.xml* 文件中，您可以使用 `<variables>` 元素定义变量，并使用 `<build>` 元素的 `symbols` 属性定义符号，如清单 8-15 所示。

5033CH08.qxd 6/17/05 12:06 PM 第 116 页

**116**

第 8 章 ■ 预处理

**清单 8-15.** *在 build.xml 中定义变量和符号*

<j2mepolish>
<info
license="GPL"
name="Roadrunner"
vendorName="A reader."
version="0.0.1"
jarName="${polish.vendor}-${polish.name}-roadrunner.jar"
/>
<deviceRequirements>
<requirement name="Identifier" value="Generic/midp1" />
</deviceRequirements>
<build
usePolishGui="true"
**symbols="mySymbol1, mySymbol2"**
>
<midlet class="com.apress.roadrunner.Roadrunner" />
**<variables includeAntProperties="true">**
**<variable**
**name="config.BaseUrl"**
**value="http://www.server.com"**
**/>**
**<variable**
**name="config.WelcomeMessage"**
**value="Welcome!"**
**/>**
**</variables>**
</build>
<emulator />
</j2mepolish>

`<variable>` 元素还支持 `file` 属性，用于指定包含变量定义的文件。将变量定义收集在一个文件中有时比在相当复杂的 *build.xml* 脚本中管理它们更容易。在外部文件中，您可以列出任意数量的变量，用等号分隔名称和值。您可以通过以井号开头一行来使用注释。清单 8-16 显示了一个文件的示例，该文件可以通过在 *build.xml* 文件中包含 `<variable file="filename.settings" />` 来使用。

**清单 8-16.** *在外部文件中定义变量*



