# 2. Selenium 基础：设置与浏览器命令

本章将开启一次技术探索之旅，旨在设置并掌握 Java、Eclipse IDE 和 Selenium WebDriver 的集成——这些是现代自动化测试工具库中的必备工具。你的重点是配置一个强大的开发环境，使你能够充分利用浏览器自动化的潜力来测试 Web 应用程序。

你将从安装 Java 开始，用这种强大的编程语言为你的测试脚本奠定基础。接下来，你将深入研究 Eclipse IDE，优化你的工作区以实现无缝开发和测试协同。安装的最后一步是集成 Selenium WebDriver，解锁你以无与伦比的精度编程控制 Web 浏览器并模拟用户交互的能力。

本章探讨了浏览器窗口的策略性操作，调整其大小和位置以模拟各种用户环境。此能力对于评估响应式 Web 设计并确保跨不同设备的兼容性至关重要。你将应对加载网页的挑战，强调通过 HTTP 和 HTTPS 协议进行安全连接的重要性，并探索 Selenium WebDriver 的导航命令以复制复杂的用户旅程。本章旨在提升你的技术熟练度，并为你提供实施全面自动化测试策略所需的知识。

## 在你的机器上设置 Java

在你的机器上设置 Java 涉及一系列步骤，包括下载 Java 开发工具包 (JDK)、安装它以及配置环境变量。让我们逐步进行。

### 步骤 1：下载 Java 开发工具包 (JDK)

1.  **访问 Oracle 官方网站。** 导航到 Oracle JDK 下载页面 ([`https.//www.oracle.com/java/technologies/downloads/#java11`](http://www.oracle.com/java/technologies/downloads/%2523java11))。该网站提供最新版本的 JDK。

2.  **选择合适的 JDK 版本。** 选择最适合你测试项目需求的 JDK 版本。虽然通常建议大多数用户使用最新版本，但特定项目可能需要特定版本。

3.  **选择操作系统。** 选择并点击与你操作系统（无论是 Windows、macOS 还是 Linux）相对应的下载链接。

4.  **接受许可协议。** 接受 Oracle Java SE 的 Oracle 技术网络许可协议。

5.  **下载文件。** 点击可执行文件的下载链接，无论是适用于 Windows 的 .exe、适用于 macOS 的 .dmg 还是适用于 Linux 的 .tar.gz。



### 第 2 步：安装 JDK

1.  **运行安装程序。**
    *   **Windows**：双击下载的 .exe 文件，并按照安装说明进行操作。你可以选择希望安装 JDK 的目录。

    *   **macOS（手动安装）**：打开 .dmg 文件并按照说明操作，通常涉及将 JDK 拖入“应用程序”文件夹。

    *   **macOS（使用 Homebrew）**：无需手动下载和安装 JDK，而是使用 Homebrew 这个包管理器来简化流程。打开终端并执行以下命令。

    ```
    brew install openjdk
    ```

    安装后，Homebrew 可能会提示你链接 JDK。命令可能类似于以下内容。

    ```
    sudo ln -sfn /usr/local/opt/openjdk/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk.jdk
    ```

    此步骤确保 JDK 在你的 macOS 系统中被正确识别。
    *   **Linux**：通过图形文件管理器或使用命令行 **tar -xzf [文件名]** 将 .tar.gz 文件解压到你想要的位置。这会将 JDK 解压到你选择的目录中。

2.  **按照安装步骤操作。** 除非你有特定的偏好或要求，否则在安装过程中通常可以保持默认设置。

### 第 3 步：设置环境变量

在 Windows 中，执行以下操作。

1.  右键单击“此电脑”，选择“属性”，然后选择“高级系统设置”，打开“系统属性”。

2.  单击“环境变量”按钮。

3.  在“系统变量”中，单击“新建”创建一个新的 `JAVA_HOME` 变量，并将其值设置为你的 JDK 安装路径（例如，**C:\Program Files\Java\jdk-11**）。

4.  在“系统变量”中找到 `Path` 变量，单击“编辑”并添加一个新条目：**%JAVA_HOME%\bin**。

在 macOS 和 Linux 中，执行以下操作。

1.  打开终端，使用 **vi** 或 **vim** 编辑你的 shell 配置文件，例如位于主目录下的 **.bash_profile**、**.bashrc** 或 **.zshrc**。要打开文件，请键入 **vi ~/.bashrc** 或 **vim ~/.bashrc**（或替换为适合你 shell 的相应文件）。

2.  添加一行来导出 `JAVA_HOME` 变量，例如 **export JAVA_HOME=/usr/lib/jvm/jdk-11**。

3.  使用 **export PATH=$JAVA_HOME/bin:$PATH** 将 Java bin 目录添加到你的 `PATH` 变量中。此步骤确保 Java 二进制文件可以从命令行访问。

4.  保存文件并退出 **vi** 或 **vim**。如果你使用的是 **vi**，可以通过键入 **:wq** 并按 Enter 键来保存并退出。对于使用 **vim** 的用户，保存并退出的命令相同，即 **:wq**。

5.  保存文件并重新加载你的配置文件，例如，通过运行 **source ~/.bashrc** 或与你编辑的 shell 配置文件相对应的命令。

### 第 4 步：验证安装

1.  打开命令行界面。
    *   **Windows**：打开命令提示符。

    *   **macOS/Linux**：打开终端。

2.  检查 Java 版本。
    1.  键入 **java -version** 并按 Enter 键。这应该会显示已安装的 Java 版本，确认 Java 已成功安装并且路径在你的机器上设置正确。

    2.  键入 **javac -version** 以确保 Java 编译器已安装并可运行。

### 第 5 步：必要时进行更新

定期检查 JDK 的更新，以确保你拥有最新的安全修复和性能改进。

完成这些步骤后，你将在机器上建立一个功能完整的 Java 开发环境。此设置对于开发 Java 应用程序至关重要，并且是运行和编写 Selenium 自动化脚本的先决条件。

## 安装 Eclipse IDE

安装 Java 后，下一步是安装 Eclipse IDE。以下步骤说明了如何操作。

### 第 1 步：下载 Eclipse

1.  打开你的网页浏览器，访问 [Eclipse 官方网站](https://www.eclipse.org/downloads/) ([`www.eclipse.org/downloads/`](http://www.eclipse.org/downloads/))。

2.  单击“**安装你喜欢的桌面 IDE 包**”的下载按钮，这将带你进入下一页进行下载，或者你也可以通过单击“下载包”链接，根据需求选择包。

3.  下载最新的可用版本，该版本非常适合 Java 开发。

### 第 2 步：运行 Eclipse 安装程序

1.  找到下载的文件（通常在“下载”文件夹中）。

2.  运行安装程序。
    *   在 Windows 中，它是一个 .exe 文件。

    *   在 macOS 中，它是一个 .dmg 文件。

    *   在 Linux 中，它通常是一个 .tar.gz 文件。

### 第 3 步：安装 Eclipse

1.  当安装程序打开时，选择“Eclipse IDE for Java Developers”。

2.  安装程序会提示你选择一个安装文件夹。你可以保留默认设置，或选择你希望安装 Eclipse 的其他位置。

3.  单击“安装”继续。

### 第 4 步：完成安装

等待安装完成。这可能需要几分钟时间。

### 第 5 步：启动 Eclipse

1.  Eclipse 安装完成后，你可以直接从安装程序启动它，或者通过在你选择的目录或桌面上创建的快捷方式启动。

2.  首次启动时，Eclipse 会要求你选择一个工作空间目录。这是你所有项目存储的位置。

### 第 6 步：配置 Eclipse（可选）

Eclipse 启动后，你可能希望根据自己的偏好进行配置。这可以包括设置代码样式、字体和颜色，或通过 Eclipse Marketplace 安装额外的插件。

### 第 7 步：创建一个 Java 项目进行测试

让我们创建一个新的 Java 项目，以确保一切设置正确。

1.  转到文件 **➤** 新建 **➤** Java 项目。

2.  如果你可以输入项目名称并设置项目，则你的 Eclipse 安装已准备好进行 Java 开发。

注意

Maven 是专业 IT 环境中的首选工具，尤其是在测试自动化中，因为它具有项目管理和构建自动化能力。Apache Maven 的设置，包括其安装和环境配置，将在第 12 章中讨论。

就是这样！你已经成功地在你的机器上安装了 Eclipse IDE，可以开始处理 Java 项目了。具体步骤可能因操作系统而异，但整体流程大致相同。

## Selenium 安装

成功安装 Java 和 Eclipse IDE 后，在开始你的自动化测试项目之前，你必须安装 Selenium WebDriver。以下步骤可确保 Selenium WebDriver 与 Eclipse IDE 的顺利安装。



### 步骤 1：下载 Selenium WebDriver

1.  打开网页浏览器，访问 Selenium 官方网站（[`https://www.selenium.dev/downloads/`](https://www.selenium.dev/downloads/)）。

2.  滚动至 **Selenium 客户端与 WebDriver 语言绑定** 部分，下载 Selenium WebDriver Java 客户端。下载文件是一个包含 Selenium Java 库和驱动程序的 ZIP 压缩包。

表 2-1 列出了各 WebDriver 及其下载地址。

表 2-1

WebDriver 列表

| WebDriver | 支持的浏览器 | 下载地址 |
| --- | --- | --- |
| ChromeDriver | Google Chrome | [`https://sites.google.com/chromium.org/driver/`](https://sites.google.com/chromium.org/driver/) |
| GeckoDriver | Mozilla Firefox | [`https://github.com/mozilla/geckodriver/releases`](https://github.com/mozilla/geckodriver/releases) |
| EdgeDriver | Microsoft Edge | [`https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/`](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/) |
| SafariDriver | Apple Safari | [`https://developer.apple.com/documentation/webkit/testing_with_webdriver_in_safari`](https://developer.apple.com/documentation/webkit/testing_with_webdriver_in_safari) |
| OperaDriver | Opera | [`https://github.com/operasoftware/operachromiumdriver/releases`](https://github.com/operasoftware/operachromiumdriver/releases) |
| IEDriverServer | Internet Explorer | [`https://www.selenium.dev/downloads/`](https://www.selenium.dev/downloads/) |

注意

Internet Explorer 的支持正在逐步淘汰，转而支持 Edge。IEDriverServer 列在 Selenium 下载页面的“先前版本”部分。

此表涵盖了最流行的网页浏览器。每个 WebDriver 都允许 Selenium 与相应的浏览器进行交互，从而实现自动化测试。Safari WebDriver 已预装在浏览器中，但您可能需要启用它才能进行自动化操作。

### 步骤 2：在 Eclipse 中创建新的 Java 项目

1.  在 Eclipse 中，依次点击 **文件** **➤** **新建** **➤** **Java 项目**。

2.  为项目命名，例如 **SeleniumTestProject**，然后点击 **完成**。

### 步骤 3：将 Selenium JAR 包添加到项目

1.  将下载的 ZIP 文件解压到计算机上的一个文件夹中。

2.  在 Eclipse 中，右键点击项目资源管理器中的项目（**SeleniumTestProject**），然后选择 **属性**。

3.  在属性窗口中，导航到左侧的 **Java 构建路径**。

4.  在 **库** 选项卡中，点击 `添加` **外部 JAR...**。

5.  导航到您解压 Selenium WebDriver 的文件夹，选择 **client-combined-x.x.x.jar** 和 **libs** 文件夹中的所有 JAR 文件。

6.  点击 **打开**，然后点击 **应用并关闭**。

### 步骤 4：验证安装

让我们创建一个简单的测试脚本来验证 Selenium WebDriver 是否安装正确。

1.  右键点击项目的 src 文件夹，依次选择 **新建 ➤ 类**，为其命名（例如 **SeleniumTest**），然后点击 **完成**。

2.  在新创建的类文件中，编写一个简单的 Selenium WebDriver 测试。以下是一个示例脚本，用于在网页浏览器中打开 Google。

    ```
    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.chrome.ChromeDriver;
    public class SeleniumTest {
    public static void main(String[] args) {
    // 设置 Chrome 驱动程序可执行文件的路径
    System.setProperty("webdriver.chrome.driver", "path/to/chromedriver");
    // 初始化 Chrome 驱动程序实例
    WebDriver driver = new ChromeDriver();
    // 在浏览器中打开 Google
    driver.get("https://www.google.com");
    // 关闭浏览器
    driver.quit();
    }
    }
    ```

    在上述脚本中，将 “path/to/chromedriver” 替换为您计算机上 ChromeDriver 可执行文件的路径。

### 步骤 5：运行测试脚本

要运行该脚本，请在 Eclipse 中右键点击它，然后选择 **运行方式 ➤ Java 应用程序**。

如果一切设置正确，此脚本应在 Chrome 浏览器中打开一个 Google 网页，然后将其关闭。

您已成功将 Selenium WebDriver 添加到 Eclipse 项目中，并准备好编写自动化测试！请记得从 Selenium 网站下载您打算使用的浏览器对应的特定 WebDriver（Google Chrome 使用 ChromeDriver，Firefox 使用 GeckoDriver 等）。

## 浏览器命令

使用 Selenium WebDriver 打开浏览器是自动化 Web 应用程序测试的基础步骤。Selenium WebDriver 提供了一个与网页浏览器交互的接口，允许您以编程方式控制浏览器会话。这包括打开浏览器、导航网页以及执行各种用户操作。以下指南和 Java 代码片段演示了如何使用 Selenium WebDriver 打开网页浏览器（Google Chrome、Firefox 和 Microsoft Edge）。

首先，请确保您的机器上已安装并设置好以下先决条件。

*   Java 开发工具包 (JDK)

*   Selenium WebDriver 库

*   特定于浏览器的 WebDriver 可执行文件（Google Chrome 使用 ChromeDriver，Firefox 使用 GeckoDriver，Microsoft Edge 使用 EdgeDriver）

## 使用 Java 代码打开 Selenium WebDriver

以下 Java 程序演示了如何使用 Selenium WebDriver 打开网页浏览器。此示例假定 Selenium WebDriver 已集成到您的项目中，并且您已准备好相应的 WebDriver 可执行文件。

```
import org.openqa.selenium.WebDriver;
// 取消注释您将要使用的浏览器的导入语句
import org.openqa.selenium.chrome.ChromeDriver;
// import org.openqa.selenium.firefox.FirefoxDriver;
// import org.openqa.selenium.edge.EdgeDriver;
public class OpenBrowserExample {
public static void main(String[] args) {
// 取消注释以下某个部分以使用相应的 WebDriver 和浏览器。
// 对于 Google Chrome：
System.setProperty("webdriver.chrome.driver", "path/to/chromedriver");
WebDriver driver = new ChromeDriver();
driver.get("https://www.google.com");
System.out.println("Google Chrome 启动成功。");
driver.quit(); // 关闭浏览器
// 对于 Firefox：
// System.setProperty("webdriver.gecko.driver", "path/to/geckodriver");
// WebDriver driver = new FirefoxDriver();
// driver.get("https://www.google.com");
// System.out.println("Firefox 启动成功。");
// driver.quit(); // 关闭浏览器
// 对于 Microsoft Edge：
WebDriver firefoxDriver = new FirefoxDriver();
firefoxDriver.get("https://www.google.com");
System.out.println("Firefox 启动成功。");
// 打开 Microsoft Edge
// System.setProperty("webdriver.edge.driver", "path/to/edgedriver");
// WebDriver driver = new EdgeDriver();
// driver.get("https://www.google.com");
// System.out.println("Microsoft Edge 启动成功。");
// driver.quit(); // 关闭浏览器
}
}
```

在此程序中，请执行以下操作。

1.  设置 ChromeDriver、GeckoDriver 和 EdgeDriver 的系统属性，以告知 Selenium 浏览器特定 WebDriver 可执行文件的位置。

2.  实例化 **ChromeDriver**、**FirefoxDriver** 和 **EdgeDriver** 对象，这些对象分别用于打开 Google Chrome、Firefox 和 Microsoft Edge 浏览器。（为简单起见，此处全面展示了所有浏览器；您可以选择任意一个。）

3.  在每个浏览器中使用 **get()** 方法导航至 [`https://www.google.com`](https://www.google.com)，这标志着测试的开始。

注意

请务必将 **“path/to/chromedriver”、“path/to/geckodriver”** 和 **“path/to/edgedriver”** 替换为您下载的 WebDriver 可执行文件的实际路径。

这个简单但基础的程序展示了 Selenium WebDriver 如何使您能够启动和控制浏览器会话，为更复杂的自动化 Web 测试奠定基础。

## 打开在线或离线网页

在使用 Selenium WebDriver 自动化 Web 测试时，您通常需要打开网页来验证被测应用程序的行为。Selenium 允许您无缝地打开在线和离线网页。



### 在线网页

要打开一个在线网页，请使用 `get()` 方法并传入你想要访问页面的完整 URL。该 URL 可以是 HTTP 或 HTTPS 协议。

```
driver.get("https://www.apress.com");
```

网页使用两种协议作为其地址。

*   **HTTP（超文本传输协议）** 是访问网页的基础非安全方式。在测试不涉及敏感用户信息的内容时，它非常有用。

*   **HTTPS（HTTP 安全版）** 是一种使用 SSL/TLS 加密来保护数据的安全版本。在测试处理敏感数据的页面时，使用 HTTPS 至关重要，这能确保通信是加密且安全的。

在测试过程中遇到 HTTP 和 HTTPS 不匹配的情况，可能会导致安全警告、浏览器阻止混合内容，或将敏感数据暴露于漏洞之中。你必须确保你的 Web 应用在必要时使用 HTTPS，尤其是对于传输敏感信息的页面。

### 离线网页

你也可以使用 WebDriver 通过 `file:///` 协议打开本地 HTML 文件，从而允许你离线测试网页。

```
// 打开本地 HTML 文件进行测试
driver.get("file:///path/to/your/localfile.html");
```

此方法在 UI 测试的初始阶段，或者不需要网络连接的场景中特别有用。它使你无需 Web 服务器即可测试静态网页或组件。

让我们扩展你的指南，加入使用 Selenium WebDriver 中的 **close** 和 **quit** 命令来管理浏览器会话的关键方面。这些命令至关重要，因为它们能确保你优雅地结束测试会话，而不会留下未释放的资源。

## 理解 close 和 quit 命令

在你启动并使用浏览器会话进行测试之后，正确地终止这些会话至关重要。Selenium WebDriver 为此提供了两个命令：**close** 和 **quit**。

### close 命令

此命令会关闭当前处于焦点的浏览器窗口或标签页。

```
chromeDriver.close(); // 关闭当前的 Chrome 窗口
firefoxDriver.close(); // 关闭当前的 Firefox 窗口
edgeDriver.close(); // 关闭当前的 Edge 窗口
```

当你调用 `close` 命令时，WebDriver 会关闭当前处于活动焦点的窗口或标签页。如果 WebDriver 启动的会话中打开了多个标签页或窗口，则只会关闭当前活动的那一个。当你的测试涉及处理多个标签页或窗口，并且需要作为测试流程的一部分有选择地关闭它们时，此命令特别有用。

### quit 命令

此方法会关闭与会话关联的所有窗口，并安全地结束整个 WebDriver 会话。

```
chromeDriver.quit(); // 退出 Chrome 会话，关闭所有关联的窗口
firefoxDriver.quit(); // 退出 Firefox 会话，关闭所有关联的窗口
edgeDriver.quit(); // 退出 Edge 会话，关闭所有关联的窗口
```

**quit** 命令是你在完成测试后进行清理的首选方法。它确保 WebDriver 打开的所有浏览器窗口都被关闭，并且 WebDriver 会话被终止。这对于释放资源以及确保测试结束后没有遗留的浏览器进程在运行至关重要。

## 设置浏览器大小

调整浏览器大小对于使用 Selenium WebDriver 进行自动化测试至关重要。它允许你模拟 Web 应用在不同屏幕尺寸下的行为，确保在各种设备上提供一致的用户体验。

### 最大化浏览器窗口

最大化浏览器窗口是你执行的最常见操作之一。它确保你的应用在全屏环境下进行测试，类似于许多用户通常查看它的方式。

```
driver.manage().window().maximize();
```

通过最大化浏览器窗口，你可以确保测试覆盖了 Web 应用在屏幕提供的最大视口下的布局和功能。这对于捕捉可能仅在全屏视图下出现的 UI 问题特别有用。

### 最小化浏览器窗口

**minimize()** 方法允许你在测试期间以编程方式最小化浏览器窗口。此操作模拟用户点击浏览器窗口上的最小化按钮，有效地将其缩小到任务栏或程序坞而不关闭它。

```
driver.manage().window().minimize();
```

通过调用 **minimize()**，你不仅是在测试应用的视觉方面，也是在确保任何应在后台运行的进程都能正确执行。例如，你可以使用此方法来验证即使浏览器不在焦点中，音频是否继续播放或数据是否继续加载。

### 设置特定的浏览器窗口大小

在某些情况下，你需要以特定的分辨率测试你的应用。这时，将浏览器大小设置为精确尺寸就变得非常有价值，它允许你模拟各种设备。

```
import org.openqa.selenium.Dimension;
// 将浏览器大小设置为 1024x768
driver.manage().window().setSize(new Dimension(1024, 768));
```

指定浏览器窗口大小有助于你有效地测试响应式设计。通过模拟平板电脑、手机或台式机的屏幕尺寸，你可以确保 Web 应用能正确适应不同的分辨率。此功能对于验证应用 UI 的响应性和流畅性至关重要。

### 使用全屏模式

进入全屏模式是我们可以配置浏览器窗口的另一种方式。此模式与最大化不同，因为它会隐藏浏览器的界面元素（如地址栏和标签页），提供更沉浸式的视图。

```
driver.manage().window().fullscreen();
```

使用全屏模式来模拟你的应用在沉浸式环境中被查看的场景，类似于提供视频内容或游戏的 Web 应用。这允许你确保应用的 UI 和功能即使在无浏览器界面的环境下也能无缝运行。

## 使用 Selenium WebDriver 设置浏览器位置

设置浏览器位置允许你将浏览器窗口移动到屏幕上的指定位置。

```
import org.openqa.selenium.Point;
// 将浏览器移动到屏幕的左上角
driver.manage().window().setPosition(new Point(0, 0));
```

通过指定你希望浏览器窗口所在的 **Point**，你可以确保应用在你预期的精确屏幕环境中进行测试。当你想要测试应用在不同屏幕位置或显示器配置下的行为时，这特别有用。



## 使用坐标设置窗口大小

要使用坐标设置浏览器大小，可结合使用 **setSize** 和 **setPosition** 方法，通过 *x* 和 *y* 坐标指定浏览器窗口在屏幕上的位置，并通过宽度和高度值指定其尺寸。当您需要测试 Web 应用在屏幕特定位置的特定大小窗口中的表现时，此方法尤为实用。

```
import org.openqa.selenium.Dimension;
import org.openqa.selenium.Point;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
public class SetBrowserSizeAndPosition {
public static void main(String[] args) {
// 使用 Firefox 初始化 WebDriver
WebDriver driver = new FirefoxDriver();
// 导航至 Apress 网页
driver.get("https://apress.com");
// 使用坐标设置窗口大小和位置
driver.manage().window().setPosition(new Point(30, 30)); // 将窗口位置设置为 x=30, y=30
driver.manage().window().setSize(new Dimension(450, 500)); // 将窗口大小设置为 450x500 像素
System.out.println("使用坐标设置浏览器大小");
// 关闭浏览器并结束会话
driver.quit();
}
}
```

在此示例中，您首先使用 Firefox WebDriver 打开 Apress 网页。然后，将窗口位置设置为屏幕上的 **x=30** 和 **y=30** 坐标，从而将浏览器窗口移动到该特定位置。之后，将窗口大小调整为 450x500 像素，确保浏览器窗口符合测试所需尺寸。这种方法让您能够精确控制测试环境，准确模拟用户条件，并确保应用的布局和功能在不同场景下保持一致。

通过操控浏览器窗口的大小和位置，您可以有效测试响应式设计、位置相关功能以及整体用户体验，确保您的 Web 应用既健壮又用户友好。

## 获取浏览器位置

了解浏览器窗口的当前位置非常有用，尤其是在运行涉及屏幕坐标的测试时，或者需要验证窗口在多窗口或多显示器场景中是否按预期定位时。

获取浏览器位置可以让您获得浏览器窗口在屏幕上的当前位置。

```
Point position = driver.manage().window().getPosition();
System.out.println("浏览器位置 - X: " + position.getX() + ", Y: " + position.getY());
```

通过获取表示浏览器位置的 **Point** 对象，您可以在测试中断言浏览器窗口的位置。这在窗口定位可能影响 UI 元素行为或可见性的场景中尤为有用。

## 获取窗口大小

了解浏览器窗口的当前大小对于响应式设计测试至关重要，它能让您验证 Web 应用在不同尺寸下是否正确适配。

获取浏览器窗口的大小有助于您了解 Web 应用当前所处的视口尺寸。

```
Dimension size = driver.manage().window().getSize();
System.out.println("窗口大小 - 宽度: " + size.getWidth() + ", 高度: " + size.getHeight());
```

通过获取的尺寸，您可以确认应用在给定窗口大小下是否按预期显示。这非常宝贵，因为它有助于确保应用的响应式设计行为正确，在所有设备尺寸上提供无缝的用户体验。

## 使用 Selenium WebDriver 浏览网页

浏览网页是自动化测试流程中的基本环节。Selenium WebDriver 提供了直观的命令，用于在浏览器历史记录中前进和后退、刷新当前页面，甚至在同一浏览器窗口中跳转到新 URL。让我们来探索这些导航命令。

### 后退导航

要模拟按下浏览器的后退按钮，请使用 WebDriver 的 **navigate().back()** 方法。这允许您测试当用户导航回上一页时 Web 应用的行为。

```
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
public class BackClickExample {
public static void main(String[] args) {
WebDriver driver = new ChromeDriver();
driver.get("https://example.com");
// 执行点击操作
WebElement link = driver.findElement(By.id("link-id"));
link.click();
// 后退导航
driver.navigate().back();
driver.quit();
}
}
```

导航到第二个页面后，调用 **navigate().back()** 将返回第一个页面。此命令在测试多页面工作流时至关重要，可确保每个页面在用户后退时保持其状态或执行正确的操作。

### 前进导航

您可能需要在浏览器历史记录中再次前进。**navigate().forward()** 命令模拟用户点击浏览器中的前进按钮。

```
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
public class ForwardClickExample {
public static void main(String[] args) {
WebDriver driver = new ChromeDriver();
driver.get("https://example.com");
WebElement link = driver.findElement(By.id("link-id"));
link.click();
// 后退导航
driver.navigate().back();
// 前进导航
driver.navigate().forward();
driver.quit();
}
}
```

此命令在测试用户会话的完整导航流程时尤为有用，可确保前进导航无缝运行，并且页面状态被正确保留或恢复。

### 刷新页面

刷新或重新加载当前页面是一种常见的浏览器操作，您可以使用 **navigate().refresh()** 方法将其自动化。这对于测试当用户手动刷新页面时 Web 应用的行为非常有用。

```
// 导航至 URL
driver.get("https://www.apress.com");
// 执行一些操作，然后刷新页面
driver.navigate().refresh();
```

刷新页面可确保应用的状态正确重置，或任何动态内容按预期加载。此操作对于测试包含动态或实时内容的页面至关重要，可验证数据在无需用户手动干预的情况下是否正确更新。

通过这些导航命令，您可以全面测试 Web 应用的导航流程和内容行为，确保流畅直观的用户体验。WebDriver 的导航方法让您能够模拟真实的用户交互，从而增强自动化测试的可靠性和覆盖率。



## 总结

本章介绍了使用 Java、Eclipse IDE 和 Selenium WebDriver 配置复杂自动化测试环境的基础步骤。首先从安装 Java 开始，为测试脚本提供了通用的编程基础。接着讲解了如何设置 Eclipse IDE，并将其定制为高效的开发与测试平台。

集成 Selenium WebDriver 是一个重要的里程碑，它赋予了自动化浏览器交互和精确模拟用户行为的能力。你深入学习了操作浏览器窗口的高级技巧，包括调整窗口尺寸和位置以适应各种测试场景。这一实践对于验证 Web 应用在多设备上的响应能力至关重要。

此外，你还处理了加载网页的复杂问题，特别关注了支撑 Web 安全性的协议。讨论中强调了 HTTPS 在保护数据完整性和用户隐私方面的重要性。通过应用 Selenium WebDriver 全面的导航命令，你能够轻松地在 Web 应用中导航，测试不同流程和功能下的用户体验。

你不仅提升了技术技能，还加深了对自动化测试工具策略性应用的理解。本章为你应对复杂的测试挑战做好了准备，确保你开发和测试的应用能够达到最高标准的质量、安全性和用户满意度。

