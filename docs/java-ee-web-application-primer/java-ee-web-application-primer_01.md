# 1. 入门

VirtualBox 允许你在物理计算机中创建虚拟计算机，使你能够在一个系统上运行多台计算机。设置 VirtualBox 只需很少的步骤。安装完成后，你就可以导入现有的虚拟计算机并开始使用该系统。

像安装你下载过的任何其他软件一样，点击并运行文件来安装 VirtualBox。如果你使用的是 Windows，请双击安装文件并按照提示进行安装。如果你使用的是 Mac，请打开你下载的 DMG 文件，并将 VirtualBox 文件拖到你的“应用程序”文件夹中。在安装过程中，将所有选项保留为默认设置。

启动 VirtualBox 程序。VirtualBox 允许你管理各种虚拟机并轻松创建新的虚拟机。你可以直接从安装程序运行 VirtualBox，也可以从桌面图标启动它。

注意

从 Oracle 网站下载 VirtualBox：

[`http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html`](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)



## Oracle 虚拟机

本着偷懒——我是说高效——的原则，我们使用了一个预构建的 Oracle 虚拟机（VM）镜像来开发网站。Oracle 免费提供此虚拟机下载（需注册）。如前所述，该虚拟机需要在你的计算机上安装开源软件 VirtualBox。虚拟机中托管了最新版本的 Oracle 数据库（12c 版），还包含了 SQL Developer 甚至 Java 1.8。你只需要自行安装 Eclipse，我们将在后续章节中介绍。

提示

从 Oracle 网站 [`http://www.oracle.com/technetwork/database/enterprise-edition/databaseappdev-vm-161299.html`](http://www.oracle.com/technetwork/database/enterprise-edition/databaseappdev-vm-161299.html) 下载 Oracle 数据库应用开发者虚拟机。下载前必须接受软件条款。

准备好 Oracle 虚拟机文件后，在菜单栏中选择“文件 ➤ 导入虚拟电脑”。点击“打开虚拟电脑”按钮选择 Oracle 虚拟机文件，导航至扩展名为 `.ova` 的文件。选择该文件后，VirtualBox 会弹出一个显示设置的对话框，你可以在此点击“导入”按钮。下一个窗口将显示当前虚拟设备的配置。

点击“导入”后，VirtualBox 会复制磁盘镜像，并按照对话框中描述的设置创建虚拟机。你将在 VirtualBox 管理器（VirtualBox Manager）的虚拟机列表中看到 Oracle 虚拟机。VirtualBox 管理器是打开 VirtualBox 时显示的第一个界面。选择你的虚拟机，点击“启动”，等待加载完成，然后你就可以像使用独立计算机一样操作该虚拟机了。

注意

如果你不使用 Oracle 虚拟机，也可以在 Windows 系统上完成本书中的所有内容。

你可以从 [`http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html`](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下载并安装适用于 Windows 的 Oracle 数据库 12c 版。

你还需要安装 SQL Developer，可在 [`http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html`](http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html) 找到。

最后，你需要从 [`http://www.eclipse.org/downloads`](http://www.eclipse.org/downloads) 安装 Eclipse Oxygen。

