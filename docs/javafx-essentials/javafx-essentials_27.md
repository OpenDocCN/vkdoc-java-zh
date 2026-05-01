# 主题化

当与设计师和 UX/UI 专家合作时，你会听到关于为应用程序换肤或更改其外观的说法。这两个术语通常可以互换使用，并且都反映了*主题化*的基本概念。

主题化的思想是通过更改控件的样式来改变整个应用程序的风格，而不改变其底层功能。

在 JavaFX 中，你可以创建、修改或使用现有主题来为你的应用程序、场景甚至只是一个 UI 控件换肤。

## CSS

JavaFX **层叠样式表**（**CSS**）可以应用于 JavaFX 场景图中的任何节点；它们是异步应用于节点的。样式也可以在运行时轻松地分配给场景，从而允许应用程序的外观动态变化。

它基于 W3C CSS 2.1 规范，并且目前兼容当前版本 3 工作草案中的一些新增内容。JavaFX CSS 支持和扩展的设计允许任何兼容的 CSS 解析器干净地解析 JavaFX CSS 样式表。这使得可以将用于 JavaFX 的 CSS 样式与用于其他目的（例如 HTML 页面）的 CSS 样式混合到单个样式表中。

所有 JavaFX 属性名称都带有 `-fx-` 扩展前缀，包括那些看起来可能与标准 HTML CSS 兼容的属性，因为某些 JavaFX 值的语义与标准值略有不同。

### 注意

有关 JavaFX CSS 的更多信息，请参阅《使用 CSS 为 JavaFX 应用程序换肤》文档以及位于 [`docs.oracle.com/javase/8/javafx/api/javafx/scene/doc-files/cssref.html`](http://docs.oracle.com/javase/8/javafx/api/javafx/scene/doc-files/cssref.html) 的参考指南。



### 应用 CSS 主题

以下是一个自定义的简单 JavaFX CSS 规则文件 `ButtonStyle.css`，它将在我们的主题化过程中用于为按钮设置主题：

```
/* ButtonStyle.css */
.button {
-fx-text-fill: SKYBLUE;
-fx-border-color: rgba(255, 255, 255, .80);
-fx-border-radius: 8;
-fx-padding: 6 6 6 6;
-fx-font: bold italic 20pt "Arial";
}
```

我们有两种方法应用 CSS 样式表来更改 JavaFX 应用程序的外观主题：

1.  使用 JavaFX 应用程序（`javafx.application.Application`）类的静态方法 `setUserAgentStylesheet(String URL)`，该方法会为整个应用程序层级设置样式，包括 JavaFX 应用程序中的每个场景及其所有子节点。使用方法如下：

    ```
    Application.setUserAgentStylesheet(getClass().getResource("ButtonStyle.css").toExternalForm());
    ```

    现在，你可以使用 JavaFX 8 预装的两个样式表：Caspian 和 Modena，并且我们可以使用相同的方法在它们之间切换，如下所示：

    ```
    // 切换到 JavaFX 2.x 的 CASPIAN 外观。
    Application.setUserAgentStylesheet(STYLESHEET_CASPIAN);

    // 切换到 JavaFX 8 的 Modena 外观。
    Application.setUserAgentStylesheet(STYLESHEET_MODENA);
    ```

    ### 提示

    如果你通过传递 `null` 值来调用 `setUserAgentStylesheet(null)`，则会加载默认外观（此处为 Modena）；而如果你使用的是 JavaFX 2.x Caspian，则会加载默认的 Caspian 外观。

2.  使用场景的 `getStylesheets().add(String URL)` 方法将自动为单个场景及其子节点设置样式，如下所示：

    ```
    Application.setUserAgentStylesheet(null); // 默认为 Modena
    // 为场景应用自定义外观。
    scene.getStylesheets()
    .add(getClass().getResource("ButtonStyle.css")
    .toExternalForm());
    ```

    基本上，由于调用了 `Application.setUserAgentStylesheet(null)`，会加载默认主题（Modena）。然后，通过调用 `getStylesheets().add()` 方法为场景设置额外的样式。

样式首先应用于父节点，然后应用于其子节点。节点在添加到场景图后即被设置样式，无论其是否显示。

JavaFX CSS 实现遵循以下优先级顺序——来自用户代理样式表的样式优先级低于来自代码设置的值，而来自代码设置的值优先级又低于场景或父节点的样式表。

内联样式具有最高优先级。来自父节点实例的样式表被认为比来自场景样式表的样式更具体。

