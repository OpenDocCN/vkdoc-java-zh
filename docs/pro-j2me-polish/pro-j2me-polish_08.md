# 启动画面显示的消息：

config.WelcomeMessage=欢迎！

5033CH08.qxd 6/17/05 12:06 PM 第 117 页

第 8 章 ■ 预处理

**117**

你可以通过指定`<variable>`元素的`if`或`unless`属性来有条件地设置变量。你可以在条件中使用任何预处理术语。请记住，你可以使用`and`、`or`、`xor`和`not`作为逻辑运算符，因为等效的`&&`、`||`、`^`和`!`运算符需要在*build.xml*文件中进行 XML 编码。例如，仅对支持 MIDP 2.0 配置文件的诺基亚设备使用*nokia.midp2.settings*文件：

<variable file="nokia.midp2.settings" if="(polish.Vendor == Nokia) and ➥

polish.midp2" />

■**提示** 你可以在`<variable>`元素的`file`属性中使用预处理变量和属性函数。这使你能够非常轻松地为特定设备设置变量。例如，通过以下定义为诺基亚设备包含文件*cfg/nokia.properties*，为索尼爱立信设备包含文件*cfg/sony-ericsson.properties*：<variable file="cfg/${ lowercase(polish.vendor) }.properties" />。

当你使用 J2ME Polish 对应用程序进行本地化时（如第 7 章所述），你也可以在*resources/messages.txt*文件及其本地化版本中定义变量。此处定义的任何变量都将覆盖之前的定义。变量在*messages.txt*文件中通过以`var:`开头来定义，如清单 8-17 所示。

**清单 8-17.** *在 resources/messages.txt 文件中定义变量*

