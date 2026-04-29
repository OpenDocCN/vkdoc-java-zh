# chocolatey

cinst elixir

...

完成设置后，你将拥有两个主要命令：`iex` 和 `mix`。

$ mix --version

Erlang/OTP 21 [erts-10.0.7] [source] [64-bit] [smp:4:4] 

[ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Mix 1.7.3 (compiled with Erlang/OTP 21)

$ iex --version

Erlang/OTP 21 [erts-10.0.7] [source] [64-bit] [smp:4:4] 

[ds:4:4:10] [async-threads:1] [hipe] [dtrace]

IEx 1.7.3 (compiled with Erlang/OTP 21)

`iex` 是 Elixir 的解释器（即 REPL），你可以直接在提示符下测试和编写代码。`mix` 是 Elixir 的项目管理和构建工具。

**使用 iex**

`iex` 主要用于逐条执行命令。以下是几个示例，展示 Elixir 如何使用 `IO` 模块在标准输出上生成文本，以及使用 `File` 模块从文件读取数据。

iex(1)> IO.puts "hello"

hello

:ok

第 10 章 第 10 周：elixir

iex(2)> File.read! "secret.key"

** (File.Error) could not read file "secret.key": no such file or directory

    (elixir) lib/file.ex:319: File.read!/1

iex(2)> File.read! "mybot/secret.key"

"585672177:.."

完成 `iex` 会话后，你可以按 Ctrl+C，然后选择 abort 来终止虚拟机。

iex(7)>

BREAK: (a)bort (c)ontinue (p)roc info (i)nfo (l)oaded

       (v)ersion (k)ill (D)b-tables (d)istribution

**使用 mix**

`mix` 是 Elixir 的项目管理工具。你可以使用 `new` 子命令创建新项目。

例如，对于以下 Telegram 项目，你可以通过以下命令生成新项目：

$ mix new telegrambot

* creating README.md

* creating .formatter.exs

* creating .gitignore

* creating mix.exs

* creating config

* creating config/config.exs

* creating lib

* creating lib/telegrambot.ex

* creating test

* creating test/test_helper.exs

* creating test/telegrambot_test.exs

第 10 章 第 10 周：elixir

你的 Mix 项目已成功创建。

你可以使用 "mix" 来编译、测试等：

    cd telegrambot

    mix test

与此同时，你可能听说过 Elixir 有一个非常优秀的 Web 框架，名为 Phoenix。

[`phoenixframework.org/`](https://phoenixframework.org/)

你可以使用 `mix` 的 `phoenix.new` 子命令为 Phoenix 生成一个可直接编码的项目模板。

例如：

mix phoenix.new hellophoenix

这将生成一个 Phoenix 应用程序的项目结构。这超出了本章的范围，但如果你对现代 Web 框架感到好奇，绝对值得一看。

**使用 mix 运行 iex**

为什么要用 `mix` 运行 `iex`？因为这样你可以在 `iex` 提供的 REPL 中，让 `mix` 完成所有项目管理任务，这意味着所有依赖项、配置和项目代码都将被识别、加载并可用。实现方法是在命令行中运行以下命令：

iex -S mix

这里的 `-S` 标志告诉 `iex` 加载 `mix`，而 `mix` 将加载自身以及由生成的基于 `mix` 的项目文件所定义的项目。

第 10 章 第 10 周：elixir

因此，在几分钟前创建的 `telegrambot` 文件夹中...

$ iex -S mix

Erlang/OTP 21 [erts-10.0.7] [source] [64-bit] [smp:4:4] 

[ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Interactive Elixir (1.7.3) - press Ctrl+C to exit (type h() 

ENTER for help)

然后你可以尝试执行项目文件中的代码。

iex(1)> Telegrambot.hello

:world

但是，顺便问一下，所有这些项目文件是由什么组成的？

**mix 项目的结构**

`mix` 为你生成了一堆文件，`telegrambot` 文件夹的目录结构如下所示。

.

├── README.md

├── config

│   └── config.exs

├── lib

│   └── telegrambot.ex

├── mix.exs

└── test

    ├── telegrambot_test.exs

    └── test_helper.exs

第 10 章 第 10 周：elixir

就我们的目的而言，我将重点描述以下 Elixir 文件：

• `config.exs`：你的项目配置，定义键和值

• `telegrambot.ex`：你自己的自定义代码和入口点

• `mix.exs`：`mix` 项目配置

注意，`telegram.ex` 是唯一具有 `.ex` 扩展名的文件，以将其识别为程序的入口点。

**config.exs**

这是你可以放置机器人令牌的位置之一。

use Mix.Config

