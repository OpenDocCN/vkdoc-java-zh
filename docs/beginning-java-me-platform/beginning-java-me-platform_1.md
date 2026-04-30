# 键

KEY_STAR

* 键

LEFT

左方向键

RIGHT

右方向键

UP

上方向键

DOWN

下方向键

FIRE

确定键或中心方向键

GAME_A, GAME_B, GAME_C, GAME_D

自定义游戏键

■**注意** 正如我在第 5 章中指出的，并非所有设备都提供所有按键代码，有些设备会将多个按键代码映射到同一个键。在编写应用程序时，请务必通过查找表或 switch-case 语句将按键代码与其所体现的行为抽象开来，以便适应这种情况。

当然，如果你正在轮询按键状态，你也不希望同时接收按键事件，因此你应该向你的 GameCanvas 的 super 调用传递 `true`，以指示应禁用事件处理。例如，在我的游戏中，我可能会编写类似清单 8-1 所示的代码。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **197**

**清单 8-1.** *轮询按键状态*

public class MyGameCanvas

extends GameCanvas {

Sprite cat;

public MyGameCanvas () {

super(true);

/* 在此处进行其他设置 */

}

private void moveCat() {

int keyStates = getKeyStates();

int x, y;

x = cat.getX();

y = cat.getY();

if ((keyStates & LEFT_PRESSED) != 0) {

x-=cat.getWidth()/4;

}

if ((keyStates & RIGHT_PRESSED) != 0) {

x+=cat.getWidth()/4;

}

if ((keyStates & UP_PRESSED) != 0) {

y-=cat.getHeight()/4;

}

if ((keyStates & DOWN_PRESSED) != 0) {

y+=cat.getHeight()/4;

}

cat.setPosition(x,y);

}

/* 画布其余部分的实现 */

}

**管理游戏执行**

轮询事件固然很好，但究竟*在哪里*进行轮询呢？你在游戏的控制循环中进行轮询，这是一个独立的线程，通常可以在与你的 GameCanvas 相同的类中实现，如清单 8-2 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**198**

第 8 章 ■ 使用 Java 移动游戏 API



**清单 8-2.** *在 GameCanvas 中实现游戏控制循环* public class MyGameCanvas

extends GameCanvas

implements Runnable {

Sprite cat;

public MyGameCanvas () {

super(true);

/* 在此处进行其他设置 */

}

private void moveCat() {

int keyStates = getKeyStates();

int x, y;

x = cat.getX();

y = cat.getY();

if ((keyStates & LEFT_PRESSED) != 0) {

x-=cat.getWidth()/4;

}

if ((keyStates & RIGHT_PRESSED) != 0) {

x+=cat.getWidth()/4;

}

if ((keyStates & UP_PRESSED) != 0) {

y-=cat.getHeight()/4;

}

if ((keyStates & DOWN_PRESSED) != 0) {

y+=cat.getHeight()/4;

}

cat.setPosition(x,y);

}

private final int DELAYMS = 100;

public void start() {

Thread t = new Thread(this);

t.start();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **199**

public void run() {

Graphics g = getGraphics();

while(true) {

updateGameState(); // 移动电脑控制的角色 moveCat();

// 轮询事件，移动主角

doPaint();

// 绘制屏幕

try {

Thread.sleep(DELAYMS);

}

catch(InterruptedException ex) {}

}

}

}

在这个比伪代码更具体、比完整游戏更简化的示例中，轮询操作发生在我之前展示的 moveCat 方法中。线程启动后，它会更新游戏角色、轮询按键，并每隔 DELAYMS（100 毫秒）重新绘制屏幕。当然，updateGameState 和 doPaint 方法尚未定义——随着我们继续探索 API，将会有更多相关介绍。

**将 GameCanvas 与 MIDlet 绑定**

正如你在第 5 章关于 Canvas 类的讨论中所记得的，Canvas 及其子类实现了 Displayable 接口，这意味着你可以使用 Display 的 setCurrent 方法将 GameCanvas 子类设置为活动的 Displayable，如清单 8-3 所示。

**清单 8-3.** *将 GameCanvas 设置为活动的 Displayable* public class GameCanvasSampleMIDlet extends MIDlet {

private MyGameCanvas canvas;

public GameCanvasSampleMIDlet () {

}

private void initialize() {

canvas = new MyGameCanvas();

getDisplay().setCurrent(canvas);

canvas.start();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**200**

第 8 章 ■ 使用 Java 移动游戏 API

public void exitMIDlet() {

canvas=null;

getDisplay().setCurrent(null);

destroyApp(true);

notifyDestroyed();

}

public void startApp() {

initialize();

}

public Display getDisplay() {

return Display.getDisplay(this);

}

public void pauseApp() {

}

public void destroyApp(boolean unconditional) {

}

}

在这里，MIDlet 的入口点调用 initialize，该方法创建 MyGameCanvas 实例，并在启动游戏循环之前将显示器的 Displayable 设置为新的 MyGameCanvas 实例。

这一过程——创建一个实现 Runnable 接口以运行游戏循环的 GameCanvas 子类，将 GameCanvas 设置为当前的 Displayable，并启动游戏循环——是任何基于 MIDP 2.0 的游戏应用程序的核心。

**分层视觉元素**

虽然引入支持按键轮询、双缓冲图形和游戏循环的 GameCanvas 是 Java ME 游戏编程的重要进步，但这只是故事的一半。Layer 类层次结构和相关的 LayerManager 极大地简化了游戏中图形处理的方式。

Layer 是一个抽象类，代表游戏中的可见元素。Layer 必须知道如何绘制自身，以及跟踪其位置和可见性。你可以对 Layer 执行以下操作：

• 使用 getX 和 getY 方法获取其在画布上的位置。

• 使用 getWidth 和 getHeight 方法获取其宽度和高度。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **201**

• 使用 isVisible 和 setVisible 方法确定（并设置）Layer 是否可见。

• 使用 move 和 setPosition 方法设置其位置（通过偏移量或相对于负责绘制对象的 Graphics 对象的绝对定位）。

Layer 本身很有趣，但不如 TiledLayer 和 Sprite 子类有趣，它们提供了 Layer 的具体实现。



您使用 `TiledLayer` 来呈现由小型规则重复位图（即图块）组成的大型图像。`TiledLayer` 非常适合提供游戏背景以及中间层。一个 `TiledLayer` 实例会将一个大的区域划分为 *单元格*，每个单元格被分配一个 *图块*，该图块是 `TiledLayer` 的 `Image` 实例中的一个子图像。

当您使用 `TiledLayer` 处理大型静态对象时，您可以使用 `Sprite` 处理较小的动画对象。与 `TiledLayer` 类似，`Sprite` 会获取一张图像并将其分割成更小的图像，称为 *帧*。与 `TiledLayer` 不同的是，`Sprite` 在其位置上显示 *单个* 帧，让您可以选择要显示哪一帧。`Sprite` 实例可以通过旋转或镜像翻转来变换它们正在显示的帧，这使得您能够仅使用少量帧来指定动画项目的各种外观。

各个层通过 `LayerManager` 类连接在一起，该类维护一个它必须绘制的 `Layer` 的有序列表。这个有序列表为帧提供了 Z 轴排序；列表中的第一个项目（索引为 0）最靠近用户。使用 `LayerManager` 的方法，您可以向此列表添加和移除层，以及重绘各个 `Layer`。

**管理图层**

`LayerManager` 的主要职责是帮助您组织游戏中的图层。为此，`LayerManager` 封装了一个 `Layer` 列表，并提供了一个视图窗口，该窗口定义了图层可见区域的大小以及该可见区域相对于 `LayerManager` 坐标系的位置。通过平移视图窗口，您可以在一组图层上平移显示，创建一个可以在游戏世界中向任意方向滚动的可视窗口。您可以使用 `setViewWindow` 方法来实现这一点，并传入视图窗口的边界（例如，左、上、宽、高）。例如，要向右滚动，只需将视图窗口向右移动（在调用 `setViewWindow` 时增加 x 坐标）。通常，您需要将此行为与用户角色的 `Sprite` 移动相协调，以便可见的游戏世界与用户周围的即时环境相对应。

`LayerManager` 的大部分方法对应于您可以对 `LayerManager` 的 `Layer` 列表执行的操作，如下所示：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**202**

第 8 章 ■ 使用 Java 移动游戏 API

• `append`：接受一个 `Layer` 并将其放置在 `LayerManager` 列表的末尾

• `insert`：接受一个 `Layer` 和一个索引，并将该 `Layer` 插入到 `LayerManager` 列表中的该索引处，将后续的 `Layer` 向后移动

• `getLayerAt`：接受一个索引并返回该索引处的 `Layer`

• `remove`：接受一个 `Layer` 并从 `LayerManager` 列表中移除该 `Layer`

• `getSize`：返回 `LayerManager` 列表中 `Layer` 的数量

■**注意** 请记住，您向 `LayerManager` 列表添加项目的顺序决定了绘制的 Z 轴顺序，最前面的项目位于索引 0——即您添加到列表的第一个项目！构建图层列表时，发现因为顺序搞反了而只能看到背景，这种情况并不少见。

您还可以通过调用 `LayerManager` 的 `paint` 方法，将其列表的内容呈现在显示屏上给用户看。当您调用 `paint` 时，`LayerManager` 会按照索引降序的顺序渲染其每个图层，从而实现接口所承诺的 Z 轴顺序。`paint` 方法接受用于渲染列表中各 `Layer` 的 `Graphics` 实例，以及 `Graphics` 实例中应开始绘制的偏移量。

您可以使用 `setViewWindow` 方法来设置 `LayerManager` 使用的裁剪区域。

■**提示** `LayerManager.paint` 方法是经过优化的；它不会渲染完全位于 `Graphics` 裁剪区域之外的项目。因此，如果您创建了一个自定义的 `Layer` 子类并实现了 `paint` 方法，您应该知道 `paint` 方法 *仅* 在有内容需要绘制时才会被调用。

**使用图块优化视觉图层**

大多数游戏至少有一个视觉组件可以由规则重复的位图组成，例如游戏关卡的背景。例如，考虑图 8-2，它显示了一个由三个图块组成的游戏板的八乘八图块网格。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00038.png)

![](img/00026.png)

![](img/00022.jpeg)

第 8 章 ■ 使用 Java 移动游戏 API

**203**

**图 8-2.** *(a) 中显示的三个图块构成了 (b) 中显示的图块图像，该图像被重复使用以创建 (c) 中显示的平铺区域。*

如图 8-2(a) 所示，每个图块必须具有相同的宽度和高度。

您可以多种方式组合这些图块，如图 8-2(b) 中的条带所示，这样每个图块构成 `Image` 对象的一部分。最后，您可以连续使用多个图块来创建更大的图像——例如游戏背景——如图 8-2(c) 所示。

`TiledLayer` 类接受一个由独特图块组成的图像（如图 8-2(b) 所示）以及您提供的一组指向该 `Image` 对象的索引，并用每个索引处的图块填充图像的单元格。这些索引从数字 1 开始，表示图像左上角的图块，然后从左到右、从上到下递增，遍历您提供的图像。

例如，您可以使用图 8-2(b) 中的图像以及清单 8-4 中指向该图像的索引来组成图 8-2(c)。

**清单 8-4.** *构成图像的图块索引数组* 1, 1, 1, 1, 1, 1, 1, 1,

1, 1, 2, 1, 1, 1, 1, 1,

1, 1, 1, 1, 3, 1, 1, 1,

1, 2, 1, 1, 1, 1, 1, 1,

1, 1, 1, 1, 1, 1, 2, 1,

1, 1, 1, 3, 1, 1, 1, 1,

1, 1, 1, 1, 1, 3, 1, 1,

1, 1, 1, 1, 1, 1, 1, 1

`TiledLayer` 图像的每个单元格可以是源位图中的单个图块，也可以是透明的，让 `TiledLayer` 后面的图层透出来。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**204**

第 8 章 ■ 使用 Java 移动游戏 API

`TiledLayer` 的构造函数会获取创建 `TiledLayer` 图像所需的所有信息，*除了* 单元格索引数组，该数组需要通过其 `setCell` 方法传入。因此，要创建一个 `TiledLayer`，您需要提供以下内容：

*   图块图像中的列数和行数
*   图块图像
*   图块图像中单个图块的宽度
*   图块图像中单个图块的高度

例如，要创建用于绘制图 8-2(c) 中图像的 `TiledLayer`，您可以编写类似清单 8-5 所示的代码。

**清单 8-5.** *创建 TiledLayer*

Image boardImage = Image.createImage(imageName);

private final int tileWidth = 16, tileHeight = 16;

private final int cellsWidth = 8, cellsHeight = 8;

board = new TiledLayer( cellsWidth, cellsHeight, boardImage, tileWidth, tileHeight);

一旦创建了 `TiledLayer`，您需要将 `TiledLayer` 中每个单元格的图像索引设置为图块位图中图块的索引。您可以使用 `setCell` 方法来实现，传入要设置的单元格的坐标以及图块图像中图块的索引，如清单 8-6 所示。

**清单 8-6.** *使用 setCell 方法*

int[] map = {

1, 1, 1, 1, 1, 1, 1, 1,

1, 1, 2, 1, 1, 1, 1, 1,

1, 1, 1, 1, 3, 1, 1, 1,

1, 2, 1, 1, 1, 1, 1, 1,

1, 1, 1, 1, 1, 1, 2, 1,

1, 1, 1, 3, 1, 1, 1, 1,

1, 1, 1, 1, 1, 3, 1, 1,

1, 1, 1, 1, 1, 1, 1, 1

};

for( int i = 0; i < map.length; i++ ) {

int x = i % cellsWidth;

int y = i / cellsHeight;

board.setCell(x, y, map[i]);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00014.jpeg)

![](img/00033.jpeg)

第 8 章 ■ 使用 Java 移动游戏 API

**205**

`TiledLayer` 中的图块可以动画化；也就是说，它们可以依次循环显示不同的图块——例如，当需要模拟流动的水的效果时。



为此，你只需使用 `createAnimatedTile` 方法，并传入动画图块帧的索引，将每个动画图块标记为动画状态。`createAnimatedTile` 方法会返回一个*负数*，你在使用 `setCell` 设置单元格值时需要使用该负数。相应地，你可以通过调用 `setAnimatedTile` 方法，并传入要更改的原始图块的索引以及它们应被更改到的目标索引，从而将给定索引的所有动画图块更改为不同的动画图块。

**生成动画**

虽然 `TiledLayer` 类适用于创建大型可见区域（或许在可见区域内带有基础动画），但 `Sprite` 类最适合用于由一个或多个位图组成的小型对象。你可以通过循环播放不同的动画帧来为 `Sprite` 对象设置动画。与 `TiledLayer` 类似，这些帧以单个 `Image` 对象的形式提供给 `Sprite`，该对象包含多个帧，每个帧具有固定的宽度和高度。图 8-3(a) 展示了一个动画蝴蝶的三个帧，而图 8-3(b) 展示了一种将这些帧组合成三帧动画源 `Image` 的方法。

**图 8-3.** *如图 (a) 所示，三个帧构成了动画；在 (b) 中，这些帧被组合成一个* Image *实例，以传递给* Sprite *类。*

■**提示** 源 `Image` 中的透明度以常规方式表示，例如使用 PNG 文件的 alpha 通道。

每个帧都被分配了一个唯一的索引；与 `TileLayer` 的图块不同，`Sprite` 类从 0 开始对帧进行计数。后续索引按从左到右、从上到下的顺序分配。例如，要创建一个与图 8-3(a) 中帧组成的动画相对应的 `Sprite` 实例，你可以编写如清单 8-7 所示的代码。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**206**

第 8 章 ■ 使用 Java 移动游戏 API

**清单 8-7.** *创建 Sprite 实例*

private final int tileWidth = 16, tileHeight = 16;

Image image = Image.createImage(imageName);

Sprite butterfly = new Sprite(image, tileWidth, tileHeight); 每个 `Sprite` 都有一个帧序列，该序列定义了要显示的帧的有序列表。默认情况下，这仅仅是可用帧列表的顺序。你可以使用 `setFrameSequence` 方法显式设置此序列，该方法接受一个帧索引数组，如清单 8-8 所示。

**清单 8-8.** *设置帧序列*

private static final int[] flightSequence = {

0, 1, 2, 2, 1, 0};

butterfly.setFrameSequence(flightSequence);

在任何时候，你都可以使用 `setFrame`、`prevFrame` 或 `nextFrame` 方法中的任意一个来更改 `Sprite` 将要绘制的帧。

■**提示** 请记住，`setFrame`、`prevFrame` 和 `nextFrame` 处理的是*帧序列*中的索引，而不是帧图像的索引！

每个 `Sprite` 实例都有一个*参考像素*，用于指示 `Sprite` 绘制其帧的位置。默认情况下，这仅仅是帧的左上角，但你可以使用 `defineReferencePixel` 方法更改此位置，并传入新参考像素的 *x* 和 *y* 坐标。当你考虑到 `Sprite` 类可以对构成 `Sprite` 的帧应用各种视觉变换时，参考像素尤其方便。这些变换包括以 90° 为倍数的旋转，以及围绕每次旋转的垂直轴进行镜像。当 `Sprite` 类应用变换时，`Sprite` 会自动重新定位，以使参考像素看起来保持静止；例如，旋转是围绕参考像素进行的。你可以使用 `setTransform` 方法对 `Sprite` 应用变换，并传入表 8-2 中所示的常量之一。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **207**

**表 8-2.** *可应用于 Sprite 帧的各种变换* **常量**

**动作**

Sprite.TRANS_NONE

无变换

Sprite.TRANS_ROT180

绕参考像素旋转 180°

Sprite.TRANS_MIRROR



关于参考像素的水平镜像

Sprite.TRANS_MIRROR_ROT180

绕参考像素旋转 180°，然后关于参考像素进行水平镜像

Sprite.TRANS_ROT90

绕参考像素顺时针旋转 90°

Sprite.TRANS_MIRROR_ROT90

关于参考像素进行水平镜像，然后顺时针旋转 90°

Sprite.TRANS_MIRROR_ROT270

关于参考像素进行水平镜像，然后逆时针旋转 90°

Sprite.ROT270

绕参考像素逆时针旋转 90°

最后，Sprite 类通过 `collidesWith` 方法提供碰撞检测功能，用于检查两个 Sprite 实例（或一个 Sprite 与一个 Image）是否发生碰撞。Sprite 类可以使用帧图像的矩形区域或帧中的不透明像素（速度稍慢）来执行此碰撞测试；只需传入 `true` 即可启用逐像素碰撞检测。使用 `collidesWith` 方法可以简化你的游戏开发，因为你无需自行实现碰撞检测逻辑。

**运用移动游戏 API**

虽然编写一个完整的游戏超出了本章的范围，但通过一个完整的示例来展示所有这些组件的协同工作仍具有指导意义。图 8-4 展示了一个使用移动游戏 API 的简单游戏，你可以用方向键控制一只猫移动，追逐飞舞的蝴蝶。当猫触碰到蝴蝶时，手机背光灯会闪烁并振动。

该应用程序包含两个类：一个负责实现 MIDlet 接口，另一个是继承自 GameCanvas 的子类，负责实现游戏行为本身。此外，MIDlet 中还包含了游戏所需的素材，如清单 8-9 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00015.png)

**208**

第 8 章 ■ 使用 Java 移动游戏 API

**图 8-4.** *一个使用移动游戏 API 的简单游戏* **清单 8-9.**

*MIDlet 内容*

src/

com/

apress/

rischpater/

SpriteCanvas.java

SpriteSampleMIDlet.java

res/

butterfly-sprite.png

cat.png

ground-tiles.png

接下来，让我们更详细地了解这些类。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **209**

**实现游戏 MIDlet**

MIDlet 本身很简单：启动时，它只需创建一个自定义画布的实例，将该新实例设置为当前的 Displayable，并启动游戏线程。终止时，它应将画布实例置空，将当前 Displayable 设为 null，销毁自身，并通知运行时该 MIDlet 应被销毁，如清单 8-10 所示。

**清单 8-10.** *SpriteSampleMIDlet 类*

package com.apress.rischpater;

import javax.microedition.midlet.*;

import javax.microedition.lcdui.*;

public class SpriteSampleMIDlet extends MIDlet {

private SpriteCanvas canvas;

public SpriteSampleMIDlet() {

}

private void initialize() {

if (canvas==null) {

try {

canvas = new SpriteCanvas(getDisplay());

getDisplay().setCurrent(canvas);

canvas.start();

}

catch(Exception ex) {}

} else {

canvas.setPaused(false);

}

}

public Display getDisplay() {

return Display.getDisplay(this);

}

public void exitMIDlet() {

destroyApp(true);

notifyDestroyed();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**210**

第 8 章 ■ 使用 Java 移动游戏 API

public void startApp() {

initialize();

}

public void pauseApp() {

canvas.setPaused(true);

}

public void destroyApp(boolean unconditional) {

canvas=null;

getDisplay().setCurrent(null);

}

}

这段代码很直观，特别是如果你已经了解 MIDlet 的生命周期。这个 MIDlet 类所做的其他工作仅在 `pauseApp` 方法中，即通过将游戏暂停状态设置为 `true` 来指示游戏画布暂停游戏。当 MIDlet 收到新的 `startApp` 调用时，游戏恢复运行；如果游戏画布已存在，应用程序将通过将游戏暂停状态设置为 `false` 来恢复游戏。

**实现游戏画布**

如果说 SpriteSampleMIDlet 类很简单，那么游戏画布也只是稍微复杂一点。



这是由于设置可见层和游戏循环的工作所致。清单 8-11 展示了扩展了 GameCanvas 类并实现了游戏画布和循环的 SpriteCanvas 类。

**清单 8-11.** *SpriteCanv*

*as 类*

package com.apress.rischpater;

import javax.microedition.lcdui.*;

import javax.microedition.lcdui.game.*;

import java.io.IOException;

import java.util.Random;

public class SpriteCanvas

extends GameCanvas

implements Runnable {

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **211**

private final int DELAYMS=75;

private boolean paused;

private Random random;

private Display display;

private LayerManager layers;

private TiledLayer board;

private Sprite[] butterfly;

private Sprite cat;

int[] map = {

1, 1, 1, 1, 1, 1, 1, 1,

1, 1, 2, 1, 1, 1, 1, 1,

1, 1, 1, 1, 3, 1, 1, 1,

1, 2, 1, 1, 1, 1, 1, 1,

1, 1, 1, 1, 1, 1, 2, 1,

1, 1, 1, 3, 1, 1, 1, 1,

1, 1, 1, 1, 1, 3, 1, 1,

1, 1, 1, 1, 1, 1, 1, 1 };

private static final int[] flightSequence = {

0, 1, 2, 2, 1, 0 };

private final int tileWidth = 16, tileHeight = 16;

private final int boardWidth = 8, boardHeight = 8;

public SpriteCanvas(Display d)

throws IOException {

super(true);

display = d;

random = new Random();

layers = new LayerManager();

layers.setViewWindow(-(getWidth()-boardWidth*tileWidth)/2,

-(getHeight()-boardHeight*tileHeight)/2,

2* boardWidth*tileWidth, 2*boardHeight*tileHeight );

createButterflies("/res/butterfly-sprite.png"); createCat("/res/cat.png");

createBoard("/res/ground-tiles.png");

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**212**

第 8 章 ■ 使用 Java 移动游戏 API

private void createBoard( String imageName )

throws IOException {

Image boardImage = Image.createImage(imageName);

board = new TiledLayer( boardWidth, boardHeight,

boardImage, tileWidth, tileHeight);

for( int i = 0; i < map.length; i++ ) {

int x = i % boardWidth;

int y = i / boardHeight;

board.setCell(x, y, map[i]);

}

layers.append(board);

}

private void createButterflies( String imageName )

throws IOException {

Image image = Image.createImage(imageName);

int x, y, i;

butterfly = new Sprite[2];

for ( i=0; i<butterfly.length; i++ ) {

butterfly[i] = new Sprite(image, tileWidth, tileHeight); x = random.nextInt(boardWidth)*tileWidth;

y = random.nextInt(boardHeight)*tileHeight;

butterfly[i].setPosition(x,y);

butterfly[i].defineReferencePixel(0,0);

butterfly[i].setTransform(Sprite.TRANS_NONE);

butterfly[i].setFrameSequence(flightSequence);

butterfly[i].setFrame(i % 3);

layers.append(butterfly[i]);

}

}

private void createCat( String imageName )

throws IOException {

Image image = Image.createImage(imageName);

int x, y;

int i;

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **213**

x = random.nextInt(boardWidth)*tileWidth;

y = random.nextInt(boardHeight)*tileHeight;

cat = new Sprite(image, tileWidth, tileHeight);

cat.setPosition(x,y);

cat.defineReferencePixel(0,0);

cat.setTransform(Sprite.TRANS_NONE);

cat.setFrame(0);

layers.append(cat);

}

public void start() {

Thread thread = new Thread(this);

thread.start();

paused=false;

}

private void detectCollisions() {

int i;

for (i=0; i<butterfly.length; i++) {

if (cat.collidesWith(butterfly[i],true)) {

display.flashBacklight(100);

display.vibrate(100);

}

}

}

public void run() {

Graphics g = getGraphics();

while(true) {

try {

moveCat();

moveButterflies();

detectCollisions();

layers.paint(g, 0, 0);

flushGraphics();

Thread.sleep(DELAYMS);

[www.it-ebooks.info](http://www.it-ebooks.info/)

**214**

第 8 章 ■ 使用 Java 移动游戏 API

synchronized(this) {

while (paused) {

wait();

}

}

}

catch(InterruptedException ex) {}

}

}

public void setPaused(boolean b)

{

synchronized(this) {

paused = b;

notify();

}

}

private void moveCat() {

int keyStates = getKeyStates();

int x, y;

x = cat.getX();

y = cat.getY();

if ((keyStates & LEFT_PRESSED) != 0) {

x -= cat.getWidth()/4;

}

if ((keyStates & RIGHT_PRESSED) != 0) {

x += cat.getWidth()/4;

}

if ((keyStates & UP_PRESSED) != 0) {

y -= cat.getHeight()/4;

}

if ((keyStates & DOWN_PRESSED) != 0) {

y += cat.getHeight()/4;

}



if ( x < 0 ) x = 0;

if ( y < 0 ) y = 0;

if ( x > board.getWidth() - cat.getWidth() )

x = board.getWidth() - cat.getWidth();

if ( y > board.getHeight() - cat.getHeight() )

y = board.getHeight() - cat.getHeight();

cat.setPosition(x,y);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **215**

private void moveButterflies() {

int dir;

int i;

int x, y, width, height;

for ( i=0; i<butterfly.length; i++ ) {

dir = random.nextInt(9) + 1;

x = butterfly[i].getX();

y = butterfly[i].getY();

width = butterfly[i].getWidth();

height = butterfly[i].getHeight();

switch(dir)

{

/* 7 8 9

4 5 6

1 2 3

*/

case 1:

x -= width/2;

y += height/2;

break;

case 2:

y += height/2;

break;

case 3:

x += width/2;

y += height/2;

break;

case 4:

x -= width/2;

break;

case 5:

break;

case 6:

x += width/2;

break;

case 7:

x -= width/2;

y -= height/2;

break;

case 8:

y -= height/2;

break;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**216**

第 8 章 ■ 使用 Java 移动游戏 API

case 9:

x += width/2;

y -= height/2;

break;

}

// 裁剪坐标

if ( x < 0 ) x = 0;

if ( y < 0 ) y = 0;

if ( x > board.getWidth() - width )

x = board.getWidth() - width;

if ( y > board.getHeight() - height )

y = board.getHeight() - height;

butterfly[i].setPosition(x,y);

butterfly[i].nextFrame();

}

}

}

总的来说，`SpriteCanvas` 类的方法可以分为三组：

• *游戏设置*：由构造函数以及辅助方法 `createButterflies`、`createCat` 和 `createBoard` 负责。

• *游戏运行*：由游戏循环负责；包括轮询按键和移动非玩家角色。

• *处理暂停和恢复事件*：当状态发生变化时（例如，由于来电），`SpriteCanvas` 必须暂停游戏。MIDlet 通过调用 `setPaused` 方法来触发此操作。

构造函数首先调用 `GameCanvas` 构造函数，表明这个 `GameCanvas` 子类将轮询按键，而不是接收按键事件。接着，它缓存了创建者提供的显示对象，并创建了一个新的 `Random` 实例供非玩家角色使用。最后，它创建并初始化了 `LayerManager`（通过视图窗口的技巧确保游戏板在显示中居中）以及游戏显示的各种图层。请注意，该方法按 Z 轴顺序创建各个图层，蝴蝶离用户最近；这是因为每个创建例程都会将生成的 `Layer` 添加到 `LayerManager` 中。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 8 章 ■ 使用 Java 移动游戏 API **217**

`createBoard` 方法创建一个包含用于构建游戏背景的图块的 `Image`，然后创建一个 `TiledLayer`，将 `TiledLayer` 中的单元格设置为 `map` 指示的图块。在实际游戏中，这会更加复杂，会调用一个关卡加载器，从游戏资源中加载特定的游戏关卡，但原理基本相同。代码初始化 `TiledLayer` 后，会将其添加到 `LayerManager`。

`createButterflies` 方法通过首先加载蝴蝶动画中使用的帧图像，然后依次创建每个蝴蝶精灵来创建两只蝴蝶。对于每个蝴蝶精灵，代码必须为蝴蝶选择一个随机的起始位置，初始化帧序列（显示蝴蝶动画如何遍历帧序列），并让蝴蝶从与前一个蝴蝶不同的帧开始。创建并初始化每个蝴蝶精灵后，代码会将其添加到 `LayerManager`。`createCat` 方法类似，只是猫没有动画帧，因此省略了设置帧序列的过程。

游戏循环——由 `start` 方法启动——必须管理猫和蝴蝶的移动，检测这些角色之间的任何碰撞，并重新绘制屏幕。`run` 方法连同辅助方法 `moveCat`、`moveButterflies` 和 `detectCollisions` 一起完成这项工作。最后，如果 `paused` 为 true，游戏循环会在 `paused` 变量上等待，从而为 MIDlet 提供一种暂停游戏的方式。

`moveCat` 方法首先轮询按键并测试方向键。对于按下的每个方向键，代码会逐步移动猫的位置；当测试完所有按键组合后，代码会检查以确保猫仍在游戏板上，然后设置猫的位置。

`moveButterflies` 方法的工作方式类似，但有两个不同之处。首先，蝴蝶随机移动；其次，`moveButterflies` 必须移动所有蝴蝶。它通过一个简单的循环来实现这一点。当然，这个方法与 `moveCat` 可以重构为使用一个单独的辅助函数，但我选择不这样做，以保持每个方法职责清晰。在更复杂的游戏中，这种行为很可能会委托给实现角色行为的整个类。

`detectCollisions` 的代码将碰撞检测委托给精灵猫，使用其 `collidesWith` 方法，并依次传入每个蝴蝶精灵。当猫检测到碰撞时，代码使用传递给画布构造函数的 `Display` 实例来闪烁背光和振动手机。

游戏的主循环通过 `setPaused` 修改器、其关联的 `paused` 变量以及 Java 的线程同步来管理游戏的暂停和恢复操作。

当成员变量 `paused` 为 true 时，`SpriteCanvas` 的 `run` 方法中的游戏循环会等待，线程进入休眠。当 MIDlet 再次调用 `setPaused` 时，它会触发对成员变量 `paused` 的通知，线程继续执行。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**218**

第 8 章 ■ 使用 Java 移动游戏 API

**总结**

MIDP 2.0 在 `javax.microedition.lcdui.game` 中包含了移动游戏 API，这是一组健壮的类，可用于实现你自己的游戏。该接口由五个类组成：

• `GameCanvas`：允许你轮询按键，并围绕使用 Java 线程的游戏循环概念来构建游戏。

• `LayerManager`：负责按你定义和绘制的 Z 轴顺序管理屏幕上的可见元素。

• `Layer`：表示 Z 轴顺序中的一个单独平面。

• `TiledLayer`：`Layer` 的一个实现，可用于从小图块创建大型位图图像。

• `Sprite`：`Layer` 的一个实现，可用于创建动画。

使用移动游戏 API，你可以围绕游戏循环的概念来构建你的游戏 MIDlet，该循环在其自己的线程中运行，管理事件并更新游戏状态。

你的游戏循环可以轮询按键或以传统方式响应事件，并根据需要使用 `GameCanvas` 的 `Graphics` 上下文绘制到屏幕。`LayerManager` 类通过管理一个 `Layer` 数组来简化绘制，每个 `Layer` 可以绘制自己的内容。该 API 定义了两种特定类型的图层：`TiledLayer`，适用于绘制游戏的大片区域，例如游戏关卡的背景；以及 `Sprite`，帮助你为特定的可见对象组织动画帧。

[www.it-ebooks.info](http://www.it-ebooks.info/)

间奏曲

**目前很少有移动设备实际实现 CDC，这似乎有些奇怪。然而，当你考虑到 CDC 的** **目的——弥合低成本、低功耗计算设备与当今传统便携式或固定工作站之间的鸿沟——** **很明显，*需要某种东西*** **来填补这一空白。移动设备和其他消费设备（如机顶盒）** **的处理能力和内存持续增长，使得越来越便宜的设备能够运行适合 CDC 的应用程序。**



**如果你确信自己要坚持 MIDP 编程——你已具备成为资深移动开发者的潜质——可以跳过第 3 部分，直接进入第 4 部分，在那里我将向你展示更多 Java ME 设备上日益常见的可选 Java 接口。另一方面，如果其他消费类设备的可能性让你兴奋，请继续阅读！Java ME 远不止 CLDC 和 MIDP 这么简单。**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 3 部分

CDC 开发

**Sun Microsystems 长期以来一直将 Java 视为适用于所有计算设备（无论大小）的平台。可以说，Java 在大型任务上取得了成功；Java Servlet 等技术为世界上一些大型企业提供动力。正如你在第 2 部分所见，Java 在一些最小的设备上也同样成功——具体来说，就是当今廉价、无处不在的手机。从 PersonalJava（后来演变为 Java ME CDC）开始，Sun 准备在中端设备（包括机顶盒和其他娱乐平台）上复制其成功。在本部分中，我将向你展示 CDC 如何补充 CLDC，介绍 CDC 及其配置文件的两种关键编程模型：Foundation Profile、Personal Basis Profile 和 Personal Profile。不要以为你读到的内容只适用于娱乐设备或高端移动设备。正如融合趋势帮助将 Java SE 的关键特性引入 Java ME 一样，在未来的几年里，我期望你在这些页面中看到的内容将同样适用于许多支持 Java 的非传统计算设备。**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章

介绍 Xlet 和 Personal Basis Profile

**J**ava 小程序的初始运行时环境是浏览器，但 Java ME 的出现改变了这一点。正如你在第 4 章所见，MIDlet 是 MIDP 对应用程序运行时的回应；在本章中，你将看到 Xlet 如何在 CDC 之上为 Personal Basis Profile (PBP) 扮演相同的角色。毫不意外，Xlet 履行的职责本质上与 MIDlet 相同，因此如果你熟悉 MIDlet，那么你在这里看到的内容将会非常熟悉。

在本章中，我将讨论 Xlet 的执行模型。阅读本章后，你将了解 Xlet 在 Java ME 环境中的起源以及 Xlet 的生命周期。你将能够编写自己的 Xlet，并使用 PBP 中可用的接口执行基本的 Xlet 间应用程序通信。

**理解 Xlet**

Xlet 模型通过 Sun 在电视领域对 Java 应用程序的尝试进入 Java ME。现在作为 CDC 之上 PBP 的一部分，Xlet 模型必须满足一些超出简单小程序要求的额外标准；值得注意的是，它必须允许多个应用程序共享设备资源，包括 I/O 设备。它还必须确保没有单个应用程序能够使 Java 虚拟机崩溃，因为虚拟机可能同时托管其他应用程序。因此，Xlet 必须具有特定的生命周期，让 Xlet 像 MIDlet 一样在活动（运行）和暂停状态下运行。

与 MIDlet 一样，你通过扩展特定接口来实现 Xlet——具体来说，就是 `javax.microedition.xlet.Xlet` 接口。当运行时初始化 Xlet 时，它会提供一个*上下文*，为你的 Xlet 提供一些与应用程序运行时交互的关键方法。

**223**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**224**

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**查看 Xlet 生命周期**

图 9-1 显示了 Xlet 可能存在的状态。

**图 9-1.** *Xlet 的生命周期*

如图 9-1 所示，Xlet 可以处于以下三种状态之一：

• *暂停*：加载后，Xlet 应用程序管理器调用 Xlet 的 `initXlet` 方法，该方法应初始化 Xlet。Xlet 现在处于*暂停*状态。

• *活动*：当 Xlet 应用程序管理器准备将设备接口的控制权交给 Xlet 时，它使用 `startXlet` 方法启动 Xlet。Xlet 现在处于*活动*状态。在任何时候，Xlet 应用程序管理器都可以通过调用 Xlet 的 `pauseXlet` 方法再次暂停 Xlet，使 Xlet 回到暂停状态，然后可以通过再次调用 `startXlet` 恢复执行。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**225**

• *销毁*：当应用程序管理器想要关闭 Xlet 时，它会调用 Xlet 的 `destroyXlet` 方法，该方法必须终止 Xlet 的执行。Xlet 现在处于*销毁*状态，等待运行时进行垃圾回收。

重要的是要记住，Xlet 可以从暂停状态直接进入销毁状态，并且在其生命周期中可能会反复进入活动和暂停状态。同样重要的是要记住另外两点：

• *不要调用* `System.exit`：运行的 Xlet 可能不止你一个，你不想关闭 Java 运行时。请改用你的 Xlet 可用的 `XletContext` 对象（本章稍后讨论）来指示你的应用程序何时想要关闭。

• *在暂停和退出时释放所有可能的资源以帮助垃圾回收器*：这对于远程通信接口、文件和图形上下文等资源尤其重要。应用程序应始终能够自行清理。

Xlet 生命周期与 MIDlet 生命周期的一个有趣区别是，如果 Xlet 想要忽略请求的状态更改，它可以抛出一个异常 `XletStateChangeException`。例如，初始化失败的 Xlet 可以抛出此异常以避免启动，或者即将被销毁的 Xlet 可以抛出此异常以避免被应用程序管理系统关闭。

**扩展 Xlet 接口**

一个 Xlet 必须提供五个方法：

• *构造函数*：这通常是空的。

• `initXlet`：此方法接受一个 `XletContext` 对象并初始化 Xlet。

• `startXlet`：当 Xlet 进入活动状态时，运行时调用此方法。此方法必须执行启动 Xlet 活动执行所需的任何操作。

• `pauseXlet`：此方法必须释放不必要的资源，并指示 Xlet 处于暂停状态。

• `destroyXlet`：当 Xlet 进入销毁状态并等待垃圾回收时，此方法必须释放所有资源。

清单 9-1 显示了一个最简单的 Xlet。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**226**

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**清单 9-1.** *最简单的 Xlet*

import javax.microedition.xlet.*;

public class SimplestXlet implements Xlet {

public SimplestXlet () {

}

public void destroyXlet( boolean unconditional )

throws XletStateChangeException {

}

public void initXlet( XletContext context )

throws XletStateChangeException {

}

public void pauseXlet() {

}

public void startXlet() throws XletStateChangeException {

}

}

这个 Xlet 显然什么也不做，但它说明了一个重要点：它表明 Xlet 接口实现也可以抛出 `XletStateChangeException`。此异常表示特定的状态转换失败——例如，从已加载到已初始化，或从运行到暂停，反之亦然。应用程序管理系统如何处理这些异常取决于失败的状态转换和平台的实现。

**使用 Xlet 上下文**

对于每个 Xlet，应用程序管理器都会关联一个上下文，它是 `javax.microedition.xlet.XletContext` 的一个实例。运行时提供此管理器，以便 Xlet 可以与其环境交互。`XletContext` 接口提供以下方法：

• `getClassLoader`：返回 Xlet 的基类加载器



• getContainer：返回 Xlet 应放置其组件的根容器

• getXletProperty：从 XletContext 返回一个命名属性

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 与个人基础配置文件

**227**

• notifyDestroyed：通知应用程序管理器 Xlet 已进入销毁状态

• notifyPaused：通知应用程序管理器 Xlet 不希望处于活动状态并已进入暂停状态

• notifyActive：通知应用程序管理器 Xlet 希望进入活动状态

通常在 Xlet 初始化时使用 getContainer 来获取容器，其他可见组件应放置其中。应用程序管理器通过这种方式与多个 Xlet 共享屏幕；每个 Xlet 获得一个独立的容器组件，用于渲染其用户界面。

与 MIDlet 类似，Xlet 在需要改变状态时必须向运行时发出信号；运行时随后响应请求，执行状态变更。这就是 Xlet 指示退出意图的方式：它不调用 System.exit，而是调用 notifyDestroyed。

同样，Xlet 可以通过调用 notifyPaused 指示希望进入暂停状态并将用户界面让给其他 Xlet，或通过调用 notifyActive 请求恢复至活动状态。

**编写一个简单的 Xlet**

为 PBP 编写 Xlet 在抽象层面很简单：只需实现 Xlet 接口，并在此过程中扩展一个 Java AWT 容器（如 java.awt.Component）。实际应用程序驻留在此容器内，您可以通过初始化时提供给 Xlet 的上下文与应用程序管理器交互。

**查看一个简单的 Xlet**

在清单 9-1 中，您看到了 Xlet 的骨架；清单 9-2 展示了一个实际运行的 Xlet。

**清单 9-2.** *一个简单的 Xlet*

package com.apress.rischpater.HelloXlet;

import java.awt.*;

import javax.microedition.xlet.*;

public class HelloXlet extends Component implements Xlet {

private XletContext context;

private Container rootContainer;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**228**

第 9 章 ■ 介绍 Xlet 与个人基础配置文件

public HelloXlet() {

}

public void initXlet(final XletContext xletContext)

throws XletStateChangeException {

context = xletContext;

if(rootContainer == null) {

try {

rootContainer = context.getContainer();

rootContainer.add(this);

} catch (UnavailableContainerException e) {

System.out.println("无法获取我们的容器！"); throw new XletStateChangeException( "无容器。 "

+ e.getMessage() );

}

}

}

public void startXlet() throws XletStateChangeException {

rootContainer.setVisible(true);

}

public void paint(Graphics g) {

g.drawString("Hello Xlet", 0, 100);

}

public void pauseXlet() {

System.out.println("HelloXet.pauseXlet()");

}

public void destroyXlet(boolean b) throws XletStateChangeException {

System.out.println("HelloXet.destroyXlet() - 再见");

}

}

图 9-2 显示了该 Xlet 在 Java ME 模拟器中运行时的输出。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00028.jpeg)

第 9 章 ■ 介绍 Xlet 与个人基础配置文件

**229**

**图 9-2.** *Hello Xlet*

该 Xlet 本身很简单：它实现了 Xlet 契约，并扩展了 Component——这是一个轻量级 AWT 类，当调用其 paint 方法时知道如何绘制。

在初始化时，Xlet 必须完成几件事。首先，它缓存应用程序管理系统提供的上下文，以便在需要退出时使用。接着，它获取上下文提供的根容器并缓存起来；该容器定义了 Xlet 在屏幕上允许绘制的区域。最后，它将自身添加到上下文提供的根容器中。

默认情况下，Xlet 的用户界面不可见。因此，当应用程序管理器调用 startXlet 使 Xlet 转换到活动状态时，应用程序必须使其根容器可见；这会触发重绘，显示由 paint 处理的应用程序界面，该界面执行简单的 Graphics 操作，在显示屏上绘制消息“Hello Xlet”。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**230**

第 9 章 ■ 介绍 Xlet 与个人基础配置文件

Xlet 在暂停时不应自行擦除；它可能仍是屏幕上的可见部分，只是未获得焦点。因此，暂停此 Xlet 很简单，因为它不做任何操作；pauseXlet 方法仅将应用程序管理器触发转换到暂停状态这一事实记录到控制台。

同样，销毁此 Xlet 也很简单；AMS 负责从根容器中移除框架，这会触发用户界面的销毁，因此销毁操作仅记录 Xlet 已被销毁的事实。（Xlet 也无需自行隐藏，因为应用程序管理器会为您处理。）

**理解 Xlet 的依赖关系**

值得注意的是，这个简单的 Xlet 实际上除了 CDC 之外还有两个关键依赖关系，这些依赖关系可能是您在设计自己的 Xlet 时面临的挑战。首先，也是最明显的，您必须记住 Xlet 不是 CDC 类，而是 PBP 类。这意味着，设备支持 Java ME CDC 并不一定意味着它提供 Xlet 运行时，除非它实现了 PBP。幸运的是，当今绝大多数 CDC 设备至少包含 PBP，因此在实际中这不太可能成为问题。

其次，核心 CDC 为连接的 Java ME 设备提供了 Java 类层次结构，但明确将确定应用程序如何运行以及使用何种窗口工具包的责任委托给 CDC 之上的各种配置文件。除了提供 Xlet 模型外，PBP 还提供了 Java AWT 的一个小子集：组件和容器。具体来说，PBP 支持那些在操作系统中没有对等体的 AWT 组件，让应用程序和框架开发者能够实现自己的窗口工具包。事实上，一些平台供应商已经这样做了，如果您在某些设备上使用 Java ME，可能根本不会使用像 AWT 这样的标准窗口工具包，而是使用第三方供应商提供的不同组件框架。

幸运的是，对于许多功能更强的设备，还有其他选择。多个 JSR 详细说明了适用于支持 CDC 设备的特定用户界面包，包括可缩放矢量图形（SVG），它以适合支持 CDC 设备的移动配置文件格式提供。JSR 226 定义了通过 javax.microedition.m2g 包支持 SVG 的接口，JSR 287 则定义了该接口的第二个迭代版本，支持事件和 SVG 文档对象模型（DOM）。此外，在某些设备上，可以通过 JSR 184 定义的 Mobile 3D Graphics API 或 JSR 239 中定义的 OpenGL ES Common 配置文件接口使用三维图形。

对许多 Java 开发者来说，JSR 209 定义的 AGUI 更为实用。它定义了 Java AWT 和 Swing 用户界面的一个合理子集，便于将 Swing 应用程序移植到 Java ME 设备。我将在第 10 章更详细地讨论 AGUI。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 与个人基础配置文件

**231**

当然，所有这些窗口工具包选项都依赖于特定设备支持的核心配置和配置文件；图 9-3 显示了由 JSR 流程定义的配置、配置文件和 GUI 配置文件的典型堆栈。

**图 9-3.** *CDC、CDC 之上的配置文件以及一些更常用的 CDC 可用 GUI 工具包之间的关系*



该图展示了两个堆栈：一个用于 CDC 1.0，另一个用于 CDC 1.1。几乎所有运行窗口工具包的平台都支持基于 CDC 的基础配置文件（FP）；正如你在第 1 章“基础配置文件简介”部分所学到的，FP

提供了网络和 I/O 支持，但不提供应用程序支持，这由 PBP 或个人配置文件（PP）负责，后者包含完整的 AWT 以及 Xlet 模型。在 PBP 或 PP 之上，可能还有其他窗口工具包，这些工具包要么是专有的，要么基于 JSR，例如用于 SVG、Mobile 3D 或 OpenGL ES 的 JSR。

在 CDC 1.1 上，堆栈看起来大致相同，只是 PBP 和 PP 都有一些细微调整和新的版本号，并且 AGUI 提供了大部分 AWT 和 Swing 功能，此外还可能包含其他工具包，例如 SVG、Mobile 3D Graphics 或 OpenGL ES。

作为基于 Java ME 的消费电子设备如何实现窗口工具包堆栈的一个示例，请考虑蓝光光盘 Java（BD-J）堆栈的 UI 方法，如图 9-4 所示。在这里，由 `havi.ui` 包定义的家庭音频视频互操作性（HAVi）组窗口工具包直接位于 PBP 之上，实际上，它仅使用 PBP 提供的轻量级组件（例如 `java.awt.Component`）实现了丰富的 UI 小部件集合。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**232**

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

**图 9-4.** *BD-J 软件堆栈*

清单 9-3 展示了相同的 Hello Xlet 示例，这次使用了作为 BD-J 堆栈一部分提供的 HAVi 类。

■**注意** 只有实际开发蓝光内容的开发者才能访问 BD-J 堆栈；通常，你需要获得支持 BD-J 开发的蓝光创作环境的许可证。

**清单 9-3.** *使用 HAVi 窗口工具包类编写的 Hello Xlet*  
`package com.apress.rischpater.HelloHaviXlet;`

`import java.awt.*;`

`import org.havi.ui.*;`

`import javax.microedition.xlet.*;`

`public class HelloHaviXlet extends Component implements Xlet {`

`private HScene scene;`

`public void initXlet(XletContext context)`

`throws XletStateChangeException {`

`scene=HSceneFactory.getInstance().getBestScene(new HSceneTemplate()); scene.add(this);`

`}`

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

**233**

`public void startXlet() throws XletStateChangeException {`

`scene.setVisible(true);`

`}`

`public void pauseXlet() {`

`}`

`public void destroyXlet(boolean b) throws XletStateChangeException {`

`scene.dispose();`

`}`

`public void paint(Graphics g) {`

`g.drawString("Some text", 0, 100);`

`}`

`}`

从根本上说，清单 9-3 中的代码看起来与清单 9-2 中的代码非常相似。HAVi 工具包定义了*场景*的概念，你可以将其视为类似于容器；`initXlet` 方法创建一个新场景并将其定位在显示器上。除此之外，代码与之前的示例相同，只是你必须在完成场景使用后显式地释放它；此 Xlet 在 `destroyXlet` 方法中执行此操作。

因此，很明显，在着手开发 Xlet 应用程序时，你应该在开始设计之前回答以下问题：

• 支持哪个版本的 CDC？

• 支持哪个版本的 FP？

• 支持哪个版本的 PBP 或 PP？

• 有哪些额外的窗口工具包可用（如果有的话）？

在第 10 章中，在简要讨论了针对 PP 对 Applet 模型支持的 Applet 开发之后，我将更深入地讨论 AWT 和 AGUI 的编程。

**使用 PBP 开发轻量级用户界面**

在 Java SE AWT 中，整个 AWT 层次结构都继承自 `Component` 类及其子类 `Container`，并且该类层次结构充当原生 UI 组件的代理；例如，`java.awt.Checkbox` 类只是对应同伴组件的代理，该组件封装了原生平台窗口工具包的行为。在某些支持 PBP 的平台上


[www.it-ebooks.info](http://www.it-ebooks.info/)

**234**

第 9 章 ■ 介绍 Xlet 与 Personal Basis Profile

在某些主机上，除了 Java 运行时提供的原生窗口工具包外，可能没有其他工具包；而在另一些主机上，对等层级结构消耗的内存可能过大，导致平台无法支持。为了解决这个问题，正如你已经了解到的，PBP

并未为你的应用程序开发提供完整的用户界面层级结构。事实上，PBP 明确*排除了*诸如文本标签、文本输入框、按钮、列表和菜单等重量级组件。相反，它要求使用以下四个 AWT 顶级组件类：

• java.awt.Component：所有用户界面组件的基类

• java.awt.Container：可包含其他组件的 AWT 组件的基类

• java.awt.Frame：带有标题栏和边框的顶级窗口

• java.awt.Window：顶级窗口的基类 信不信由你，这四个核心组件类足以实现一个完整的窗口工具包，因为 Component 和 Container 类可以表示包括嵌套组件在内的各种组件，而 Frame 和 Window 类则允许你定义包含这些组件的顶级窗口。当然，PBP 所需的 AWT 子集还包括大量其他类，使你能够实现用户界面，例如 java.awt.Graphics、java.awt.Image 和 java.awt.AWTEvent。

正如你在上一节中所见，构建使用 GUI 的应用程序时，你只有两种选择：要么使用他人提供的包中的组件，要么自行开发。Java 社区已经定义了几种这样的窗口工具包，包括更多用于 UI 的标准 Java 类（AWT 和/或 Swing，具体取决于目标平台上的配置文件和包），此外还有用于 BD-J 的 HAVi 层级结构等。此外，为你的应用程序自行创建层级结构也是可行的，尤其是当你的用户界面需求较为简单时。

**为窗口工具包实现你自己的组件** 在 Java 用户界面范式中，*组件*是指任何具有图形表示形式、可以在屏幕上显示并与用户交互的对象。*容器*是一种可以包含其他组件的组件。在实现你自己的 GUI 时，大部分工作涉及创建你自己的原始组件，例如按钮。这需要实现 java.awt.Component 的子类。

实现你自己的工具包的关键在于处理工具包中每个组件的绘制。通常，绘制发生在以下两种情况之一：系统触发的绘制，例如当组件在屏幕上变为可见或调整大小时；或者应用程序触发的绘制，当组件因内容发生变化而决定需要重绘自身时。AWT 约定——你必须通过实现 Component 接口来履行——要求通过回调组件的方式完成绘制，具体来说是通过其 paint 方法。你的 paint 方法会接收到一个 Graphics 对象，该对象包含你在画布上进行二维绘制所需的所有原语。

重要的是，你应避免缓存系统调用你的 paint 方法时收到的 Graphics 对象，也避免在 paint 方法之外的其他方法中进行绘制。其他方法可能会在组件不适合绘制的时间被调用，例如当组件不可见时，或者 Graphics 对象未处于可执行绘制的状态时。当框架调用你的 paint 方法时，Graphics 对象已预先配置好绘制组件所需的适当状态，包括以下图形渲染选项：

• Graphics 对象的颜色被设置为组件的前景色属性。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 与 Personal Basis Profile

**235**


• Graphics 对象的字体被设置为组件的字体属性。
• Graphics 对象的平移被设置为使原点 (0,0) 代表组件的左上角。
• Graphics 对象的裁剪矩形被设置为组件中需要重绘的区域。

当然，你可以根据需要自由重新配置传递给 `paint` 方法的 Graphics 对象。

为了向组件发出需要重绘的信号，应用程序（或组件本身）应调用组件的 `repaint` 方法之一。在调用 `repaint` 时，应尽可能包含需要重绘的边界，以便组件可以将重绘限制在必要的区域。你还可以传递一个以毫秒为单位的时间间隔，指示组件应在指定的毫秒数过去之前重绘自身。这种形式的 `repaint` 在执行多次重绘时特别有用，因为它允许你一次性将所有绘制工作排队。

请注意，尽管看起来相似，但组件提供的 `update` 方法与 `repaint` 并不相同。只有 AWT 事件系统会响应应用程序触发的重绘请求而调用 `update`；系统重绘不会触发对 `update` 的调用。这让你可以挂钩系统级重绘请求（通过重写 `update`）或应用程序级重绘请求（通过重写 `repaint`）。然而，很可能你会希望 `repaint` *和* `update` 都采用默认的组件行为。

可以包含多个组件的容器子类，它们本身也是 Component 的子类，并且也可以通过重写 `paint` 方法来执行自己的绘制。Container 的 `paint` 方法的默认实现是，对其可见子组件中与要绘制的矩形相交的部分调用 `paint`。因此，如果你正在实现一个知道如何绘制的 Container 子类，那么你的容器的 `paint` 方法必须调用超类的 `paint` 方法，如清单 9-4 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**236**

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

**清单 9-4.** *调用超类的 paint 方法* public class PaintingContainer extends Container {

public void paint(Graphics g) {

// 首先绘制我的内容…

super.paint(g);

}

}

对 Graphics 类及其方法的全面讨论超出了本章的范围，但重要的是要记住，Graphics 对象既存储了一组特定绘制原语的上下文（字体、颜色、裁剪区域等），也提供了绘制图形原语的方法，包括矩形、弧、线、多边形、字符和图像。最后一种原语——绘制图像的能力——可能对你的窗口工具包组件至关重要，因为你可以让美工为组件的各个部分（例如按钮的边界、背景等）绘制图像，然后你的 Component 子类只需在其 `paint` 方法中将这些图像与 Graphics 对象组合，使用最少的图形原语来提供特定的外观和感觉。

**编写一个简单的轻量级组件**

实现一个丰富的窗口工具包*可能*需要大量工作，但并非必须如此，特别是当你的应用程序只有少数用户界面原语时。清单 9-5 展示了一个简单的组件：一个带有文本标签的圆形按钮。

**清单 9-5.** *RoundButton 轻量级组件* import java.awt.*;

import java.lang.*;

import java.util.*;

import java.awt.event.*;

public class RoundButton extends Component {

protected String label;

protected boolean pressed = false;

private Image offscreen;

private static int PREFERRED_SIZE = 100;

private static int LABEL_PAD = 40;

public RoundButton(String l) {

label = l;

enableEvents(AWTEvent.MOUSE_EVENT_MASK);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

**237**

public void invalidate() {

super.invalidate();

offscreen = null;

}



public void update(Graphics g) {

paint(g);

}

public void paint(Graphics g) {

int s = Math.min(getSize().width - 1, getSize().height - 1);

// 使用双缓冲进行绘制

if(offscreen == null) {

offscreen = createImage(getSize().width, getSize().height);

}

Graphics og = offscreen.getGraphics();

og.setClip(0,0,getSize().width,getSize().height);

// 绘制背景，指示按压状态。

if(pressed) {

og.setColor(getBackground().darker().darker());

} else {

og.setColor(getBackground());

}

og.fillArc(0, 0, s, s, 0, 360);

// 绘制按钮的边框

og.setColor(getBackground().darker().darker().darker()); og.drawArc(0, 0, s, s, 0, 360);

// 在按钮中心绘制标签

Font f = getFont();

if(f != null) {

FontMetrics fm = getFontMetrics(getFont());

og.setColor(getForeground());

og.drawString(label,

s/2 - fm.stringWidth(label)/2,

s/2 + fm.getMaxDescent());

}

g.drawImage(offscreen,0,0,null);

og.dispose();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**238**

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

public Dimension getPreferredSize() {

Font f = getFont();

if(f != null) {

FontMetrics fm = getFontMetrics(getFont());

int max = Math.max(fm.stringWidth(label) + LABEL_PAD,

fm.getHeight() + LABEL_PAD);

return new Dimension(max, max);

} else {

return new Dimension(PREFERRED_SIZE, PREFERRED_SIZE);

}

}

public Dimension getMinimumSize() {

return new Dimension(PREFERRED_SIZE, PREFERRED_SIZE);

}

public void processMouseEvent(MouseEvent e) {

switch(e.getID()) {

case MouseEvent.MOUSE_PRESSED:

pressed = true;

repaint();

break;

case MouseEvent.MOUSE_RELEASED:

if(pressed == true) {

pressed = false;

repaint();

}

break;

case

MouseEvent.MOUSE_ENTERED:

break;

case MouseEvent.MOUSE_EXITED:

if(pressed == true) {

pressed = false;

repaint();

}

break;

}

super.processMouseEvent(e);

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和个人基础配置文件

**239**

`RoundButton` 展示了我在上一节中讨论的所有原则，它使用双缓冲技术将绘制内容缓存到一个离屏 `Image` 实例中，以防止组件重绘时出现闪烁。这个组件并不复杂，但代码稍长，因此我们逐字段、逐方法地进行分析。

`RoundButton` 有一个 `label` 字段，用于存储向用户显示的按钮标签字符串。它还必须跟踪自身的状态（是否被按下），这通过受保护的 `pressed` 字段来实现。它维护了一个用于离屏绘制的 `Image` 对象，恰如其分地命名为 `offscreen`，以及两个私有常量变量：按钮的首选大小和按钮标签的默认内边距。

■**提示** 你可能会疑惑为什么我选择 `protected` 作为这些字段的访问保护级别。我假设这个类将作为一个更复杂的 GUI 框架的一部分，因此可能会有子类自定义此按钮的行为，并需要访问其字段。

`RoundButton` 的构造函数只是缓存了你提供的标签，并为该组件启用鼠标事件。作为双缓冲的一部分，`RoundButton` 必须重写 `invalidate` 方法。窗口环境会在组件尺寸调整和布局时调用 `invalidate`，这进而要求 `RoundButton` 分配一个新的离屏位图用于绘制。为了提高速度，`invalidate` 方法仅使当前的离屏位图失效；它将分配新离屏位图的任务留给了 `paint` 方法。

`update` 方法——由窗口工具包层次结构因系统重绘而调用——根据定义，必须清除组件的背景并完全重绘组件。由于 `paint` 方法会将整个组件绘制到离屏位图，然后将该位图复制到屏幕，因此 `update` 可以直接调用 `paint`。

`paint` 方法执行 `RoundButton` 的实际工作。它首先在需要时创建离屏位图，然后获取该离屏位图的 `Graphics` 实例。接着，它按照从后到前的顺序在离屏位图上绘制按钮，执行以下操作：



**1.** 它计算按钮的背景色，如果按钮正在被按下，颜色会稍微深一些。

**2.** 它使用上一步计算出的颜色绘制按钮的填充圆形。

**3.** 绘制完背景后，它使用比按钮背景色更深的色调绘制边框。

**4.** 绘制完背景和边框后，它绘制按钮的标签，该标签在按钮定义的区域内居中显示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**240**

第 9 章 ■ 介绍 Xlet 和个人基础框架配置文件

**5.** 现在它已完全更新了离屏位图，接着使用当前图形上下文绘制与该离屏位图对应的图像。

**6.** 它释放离屏位图的图形上下文。

`getPreferredSize` 和 `getMinimumSize` 方法为布局管理器提供了关于组件所需尺寸的线索。这些方法会考虑按钮标签的大小。

`RoundButton` 只需要处理鼠标事件，它通过 `processMouseEvent` 方法来实现；当您按下鼠标且鼠标光标位于按钮边界内时，按钮应通过加深背景色来直观地显示按下状态；当您在 `RoundButton` 边界内释放鼠标按钮，或者鼠标光标移出 `RoundButton` 边界*且*您之前已按下 `RoundButton` 时，这种加深效果必须消失。`processMouseEvent` 方法中的 `switch` 语句处理了此逻辑的各种情况。`processMouseEvent` 还会调用 `repaint` 来强制 `RoundButton` 重绘，以直观地显示其状态变化。

**了解 PBP 的窗口工具包限制**

您刚刚看到了 PBP 提供的窗口工具包的一个关键限制：禁止使用重量级组件。然而，还有其他一些限制您应该了解：

*   `java.awt.Frame` 实例（对应于原生窗口）的数量、大小和位置可能会受到严格限制。
*   `java.awt.Frame` 实例的装饰、标题和可调整大小性由原生窗口管理器控制，这些只是指导原则，而非硬性要求。原生窗口工具包可以自由地忽略您的应用程序关于装饰（包括标题、大小和位置）的请求。
*   平台可能有也可能没有鼠标，因此可能不会生成鼠标事件。
*   平台可能有也可能没有完整的键盘，因此可能不会生成键盘事件。
*   Alpha 合成可能不支持源图像在目标图像上的完整合成。
*   在指定图形笔画时，`BasicStroke` 属性可能会被忽略。
*   您可能无法在特定组件的基础上设置光标位图。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和个人基础框架配置文件

**241**

实际上，这些限制的结果是，许多 PBP 实现只支持单个顶级窗口，因此您无法构建包含多个窗口的应用程序。要确定您正在使用的 PBP 实现是否存在这些限制，您可以查询每个限制对应的系统属性。表 9-1 显示了这些系统属性及其值。

**表 9-1.** *PBP 系统属性及其值* **属性**

**值**

`java.awt.AlphaComposite.SRC_OVER.isRestricted`

当且仅当 `AlphaComposite.SRC_OVER` 受限时为 `true`

`java.awt.Graphics2D.setStroke.BasicStroke.isRestricted`

当且仅当在 `Graphics2D.setStroke` 中使用 `BasicStroke` 受限时为 `true`

`java.awt.event.MouseEvent.isRestricted`

当且仅当 `MouseEvent` 受限时为 `true`

`java.awt.event.MouseEvent.supportLevel`

如果受限，则为 `MouseEvent` 的支持级别；否则未定义¹

`java.awt.event.KeyEvent.isRestricted`

当且仅当 `KeyEvent` 受限时为 `true`

`java.awt.event.KeyEvent.supportMask`

如果受限，则为描述 `KeyEvent` 支持的掩码；否则未定义²

`java.awt.Component.setCursor.isRestricted`

如果任何 `Component` 的光标图像无法更改则为 `true`；平台可选支持

`java.awt.Frame.setLocation.isRestricted`

如果 `Frame` 位置仅限于单个值则为 `true`；平台可选支持

`java.awt.Frame.setResizable.isRestricted`

如果 `Frame` 的可调整大小性可能无法更改则为 `true`；平台可选支持

`java.awt.Frame.setSize.isRestricted`

如果 `Frame` 大小仅限于单个值则为 `true`；平台可选支持

`java.awt.Frame.setTitle.isRestricted`

如果 `Frame` 标题可能无法更改则为 `true`；平台可选支持

`java.awt.Frame.setUndecorated.isRestricted`

如果 `Frame` 装饰可能无法更改则为 `true`；平台可选支持

*¹ `java.awt.event.MouseEvent.supportLevel` 属性：如果平台不生成鼠标事件则为 `0`，如果平台提供除指针移动事件外的所有事件则为 `1`，如果提供 AWT 定义的所有事件则为 `2`。*
*² `java.awt.event.KeyEvent.supportMask` 是一个位掩码，其中 `1` 表示支持 `VK_LEFT` 和 `VK_RIGHT` 键，`2` 表示支持 `VK_UP` 和 `VK_DOWN` 键，`4` 表示支持 `VK_0` 到 `VK_9`（数字键盘），`8` 表示支持 `VK_A` 到 `VK_Z` 以及 `VK_SPACE` 和 `VK_BACK_SPACE`（字母数字键盘）。*

[www.it-ebooks.info](http://www.it-ebooks.info/)

**242**

第 9 章 ■ 介绍 Xlet 和个人基础框架配置文件

**获取 Xlet 属性和资源**

正如您在本章“使用 Xlet 上下文”一节中所见，应用程序运行时提供给您的 Xlet 的 `XletContext` 包含用于获取运行时属性的 `getXletProperty` 方法。目前 Java ME 标准本身定义的唯一属性是 `XletContext.ARGS` 属性，它允许您的应用程序收集用户在启动应用程序时传递的任何命令行启动参数，从而使应用程序管理器能够将选项传递给您的 Xlet。

Xlet 还可以使用静态类方法 `System.getProperty` 查询系统属性，该方法通常返回平台专有的系统级属性，而不是由您的应用程序所运行的 PBP 或 PP 实现定义的运行时属性。例如，对于面向视听市场的 Java ME 消费设备，这些属性包括有关文件系统、广播配置文件、互联网访问可用性以及附加包（如 UI 小部件集，例如 HAVi 包）版本号的信息。

Xlet 通常打包为 JAR 文件，因此您可以将其他资源作为关联 JAR 文件的一部分包含在应用程序中。要访问 Xlet 的 JAR 文件中的其他资源，您的实现必须使用 Xlet 上下文的类加载器，就像在独立应用程序或小程序中一样。例如，您可以编写如清单 9-6 所示的代码。

**清单 9-6.** *从 Xlet JAR 文件访问图像*
```java
ClassLoader cl = context.getClassLoader();
Icon icon = new ImageIcon(cl.getResource("images/icon.png"));
```
使用 `XletContext` 提供的类加载器，您可以按名称获取应用程序和系统资源，并且可以使用以下方法之一获取资源的 URL 或 `InputStream`：

*   `getResource`：返回命名资源的 URL
*   `getResourceAsStream`：返回命名资源的 `InputStream`
*   `getSystemResource`：返回命名系统资源的 URL，该资源是目标硬件的平台特定属性
*   `getSystemResourceAsStream`：返回命名系统资源的 `InputStream`，该资源是目标硬件的平台特定属性

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和个人基础框架配置文件

**243**

**与其他 Xlet 通信**



MIDP 提供的 MIDlet 执行模型与 PBP 提供的 Xlet 执行模型之间的一个关键区别在于，Xlet 执行模型要求 Xlet 支持基本的 Xlet 间通信（IXC）。对于 Xlet 而言，数据共享正变得越来越重要，因为能够运行 Xlet 的平台通常可以访问大量数据（通过广播或因特网等广域网），但应用程序和数据的存储空间却有限。因此，应用程序之间的数据共享变得愈发关键，因为让每个应用程序都包含访问和解码网络数据源所需的代码，并缓存其运行所需数据的副本，这种做法并不现实。

在 Java 环境中，应用程序共享数据的常用方式是通过 Java RMI 栈，它支持应用程序间和设备间的通信。实际上，RMI 栈的一个健壮子集*可以*用于 Java ME；这在 JSR 66 中有所讨论，我将在第 11 章中更详细地探讨。这种对 RMI 的支持对于某些机顶盒以及其他严重依赖中间件的平台至关重要，因为 RMI 使服务器和客户端能够以 Java 原生、面向对象的方式交换数据，从而简化了多层系统的设计和部署。

然而，并非所有 PBP 设备都需要完整的 RMI 栈，因此 PBP 仅定义了适合让 Xlet 共享数据的 RMI 子集。毫不意外的是，PBP 机制紧密地镜像了 RMI 架构，利用了一些类以及相同的基本流程来定义通信过程。

IXC 要求数据由封装了待共享数据的共享 Java 对象来表示。这些对象必须是*可远程的*——也就是说，它们必须实现 `java.rmi.Remote` 接口。Xlet 通过 IXC 注册表共享这些可远程对象，该注册表是一个单例，由本地主机上运行的所有 Xlet 共享，可通过 `XletContext` 获取。Xlet 可以使用 IXC 注册表注册可远程对象，也可以通过注册表获取对远程对象的访问权限。图 9-5 展示了两个不同 Xlet 与它们共享的一个对象之间的关系示意图。

如图表所示，第一个 Xlet 通过注册表共享的对象与第二个 Xlet 实际使用的对象之间存在细微差别。由于 Java 类加载器的运作方式，Xlet 不能简单地直接共享对象；相反，IXC 注册表会创建并返回一个存根对象，该对象充当共享对象的代理。这与 RMI 模型类似，不过在完整的 RMI 实现中，要么必须使用 RMI 编译器生成存根（如果你使用的是 Java 5 之前的版本），要么 RMI 运行时可以自动完成此操作（如果你使用的是 Java 5 之后的版本）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**244**

第 9 章 ■ 介绍 XLETS 和 PERSONAL BASIS PROFILE

**图 9-5.** *两个 Xlet 与它们共享的一个对象之间的关系* **实现一个共享对象**

考虑一个能够显式访问网络并希望与其他应用程序共享数据的 Xlet。你必须将此数据包装在一个可远程类中，如清单 9-7 所示。

**清单 9-7.** *使用 IXC 导出的数据包装类*

import java.rmi.*;

public class Location

implements java.rmi.Remote {

private String location;

private String forecast;

public Location() {

}

public void setLocation(String l)

throws RemoteException {

location = l;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 XLETS 和 PERSONAL BASIS PROFILE

**245**

public void setForecast(String f)

throws RemoteException {

forecast = f;

}

public String getLocation()

throws RemoteException {

if (location != null) {

return location;

}

else {

return "";

}

}

public String getForecast()

throws RemoteException {

if (forecast != null) {

return forecast;

}

else {

return "";

}

}

}



这些内容相当简单，除了该类与通用数据容器类之间存在两个关键区别：声明中导入了 `java.rmi`，并且每个访问器和修改器都可能抛出 `java.rmi.RemoteException`。在实现可远程类时，其所有方法都可能抛出 `java.rmi.RemoteException`，因为该类的实现实际上可能是由 IXC 注册表提供的远程存根，并且可能无法与该类的实际实例进行通信。

正如我之前所述，整个 `java.rmi` 类层次结构*并非*全部可用于运行在 PBP 之上的 Xlet。相反，PBP 仅要求以下 RMI 类：

• `java.rmi.Remote`：用于标识可远程类
• `java.rmi.AccessException`：当远程对象的调用者没有执行所请求操作的权限时抛出
• `java.rmi.AlreadyBoundException`：如果尝试将对象绑定到注册表中一个已绑定到其他对象的名称时抛出

[www.it-ebooks.info](http://www.it-ebooks.info/)

**246**

第 9 章 ■ 介绍 XLET 和 PERSONAL BASIS PROFILE

• `java.rmi.NotBoundException`：如果尝试查找或解绑一个没有关联绑定的对象时抛出
• `java.rmi.RemoteException`：远程方法调用期间可能发生的通信相关异常的通用超类
• `java.rmi.UnexpectedException`：如果远程方法调用的客户端接收到一个异常，该异常不在远程接口中方法的 throws 子句声明的异常类型之列时抛出
• `java.rmi.registry.Registry`：远程对象注册表的远程接口

重要的是要记住，IXC 机制允许你调用另一个 Xlet 上下文中的对象的方法，而不是在最简单的意义上共享数据。换句话说，你不能通过 IXC 和实现了 `java.rmi.Remote` 的对象来获取或修改对象的属性；相反，你必须通过方法来提供属性。鉴于这本身就是良好的面向对象实践，这应该不是问题，但这确实意味着需要为你希望远程 Xlet 可用的类中的任何字段定义适当的访问器和修改器方法。这就是为什么 `Location` 将 `setLocation`、`setForecast`、`getLocation` 和 `getForecast` 定义为私有字段的修改器和访问器，而不是简单地将这些字段设为公有。

**共享对象以供其他 Xlet 查找**

一旦你定义了可远程对象，你仍然需要让它们对其他 Xlet 可用。这需要两步过程：首先，创建你的 Xlet 将要共享的对象，然后将它们添加到系统范围的 Xlet 注册表中。该注册表是 `javax.microedition.xlet.ixc.IxcRegistry` 的一个实例，它维护了机器上运行的所有 Xlet 注册的对象的名称-对象映射。

■**注意** 不要将 `IxcRegistry` 与 `java.rmi.Naming` 或 `java.rmi.registry.LocateRegistry` 注册表混淆；即使机器上可用 RMI，`IxcRegistry` 也是一个独立的可远程对象注册表。

你不能直接获取 `IxcRegistry` 的实例；相反，你需要从 `XletContext` 中获取一个。在底层，PBP 实现在 `IxcRegistry` 实例和 `XletContext` 之间维护严格的一一对应关系，通过系统范围的 IXC 导出对象数据库共享数据。使用 `IxcRegistry`，你可以执行以下操作：

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 XLET 和 PERSONAL BASIS PROFILE

**247**

• 通过调用其静态方法 `getRegistry` 获取特定上下文的 `IxcRegistry` 实例。
• 使用 `bind` 方法将实现了 `java.rmi.Remote` 的对象注册到一个唯一的名称。
• 使用 `list` 返回一个字符串数组，命名注册表中的每个绑定对象。
• 调用 `lookup` 来检索特定的 `Remote` 对象。
• 使用 `unbind` 注销特定对象，或使用 `unbindAll` 注销当前 `XletContext` 中注册的所有对象。



• 使用 `rebind` 方法，将一个新的实现了 `java.rmi.Remote` 接口的对象注册到一个已存在的名称上。

清单 9-8 展示了一个原型 Xlet 如何向其他 Xlet 提供一个远程的 Location 实例。

**清单 9-8.** *一个为 IXC 消费生成对象的 Xlet* import java.rmi.*;

import javax.microedition.xlet.*;

import javax.microedition.xlet.ixc.*;

public class XletLocationProducer implements Xlet {

private XletContext context;

private Location location;

private static final String NAME =

"XletLocationProducer.Location";

public XletLocationProducer () {

}

public void initXlet( XletContext c )

throws XletStateChangeException {

location = new Location();

context = c;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**248**

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

try {

IxcRegistry registry = IxcRegistry.getRegistry(context); if ( registry == null ) {

throw new XLetStateChangeException("No registry");

}

registry.bind(NAME, location);

}

catch(AlreadyBoundException e){

throw new XletStateChangeException("Something bound");

}

catch(StubException e){

throw new XletStateChangeException("Stub error");

}

}

public void pauseXlet() {

}

public void startXlet()

throws XletStateChangeException {

}

public void destroyXlet(boolean b)

throws XletStateChangeException {

IxcRegistry registry = IxcRegistry.getRegistry(context); if ( registry == null ) {

throw new XLetStateChangeException("No registry");

}

registry.unbindAll();

}

}

该 Xlet 在初始化期间创建了一个它想要远程化的 Location 类实例，并使用当前 XletContext 的 IxcRegistry 将其绑定到名称 XletLocationProducer.Location。这*应该*总是成功的，但可能因以下三个原因之一而失败：

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**249**

• IxcRegistry 无法为 XletContext 获取一个实例。这种情况本不应发生，但如果发生了，得到的 registry 将为 null。
• 另一个 Xlet 已经将一个远程对象绑定到了名称 XletLocationProducer.Location。如果该 Xlet 在多个上下文中运行，或者另一个 Xlet 为其想要远程化的对象使用了相同的名称，就可能发生这种情况。
• IxcRegistry 未能为远程化的类创建存根。

在这些失败中，最可能的是 StubException，如果你没有正确实现你的远程类，就可能发生这种情况。要正确实现你的远程类，你必须确保：
• 该类实现了 `java.rmi.Remote`
• 每个远程对象方法在其 throws 子句中声明了 `java.rmi.RemoteException`
• 每个远程对象方法的类型只接受并返回原始 Java 类型（当然包括 void）或那些扩展了 `java.io.Serializable` 的类
仔细想想，这些限制是合理的，因为 IXC 机制必须有一种方式来知道远程对象是可远程化的，能够处理远程执行期间遇到的异常，并且能够在远程执行期间将对象从一个 Java 类加载器传递到另一个。

在 Xlet 退出时，该 Xlet 使用 `unbindAll` 从 IxcRegistry 中注销该对象。这也可能失败，但在退出时 Xlet 对这些失败几乎无能为力，因此 Xlet 仅记录这些失败以便调试。Xlet 并非必须执行此操作——运行时会在它不执行时自行处理——但这是一个好习惯。

■**提示** 当你的 Xlet 退出时，此时使用 `unbindAll` 而不是为每个命名对象调用 `unbind`，这样如果你向 registry 添加了额外的对象，就不需要再添加相应的 `unbind` 请求。

**使用共享对象**

在任何时候，一个 Xlet 都可以通过另一个 Xlet 用于注册对象的知名名称，向 IxcRegistry 查询一个远程对象。清单 9-9 展示了一个假设的消费者 Xlet，它使用了由 XletLocationProducer 共享的 Location 对象。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**250**

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**清单 9-9.** *访问远程对象*

import java.rmi.*;

import javax.microedition.xlet.*;

import javax.microedition.xlet.ixc.*;

public class XletLocationConsumer implements Xlet {

private XletContext context;

private Location location;

private static final String NAME =

"XletLocationProducer.location";

public XletLocationConsumer () {

}

public void initXlet( XletContext c )

throws XletStateChangeException {

context = c;

try {

IxcRegistry registry = IxcRegistry.getRegistry(context); if ( registry == null ) {

throw new XLetStateChangeException("No registry");

}

location = (Location)registry.lookup(NAME);

}

catch(NotBoundException e){

throw new XletStateChangeException("Nothing bound");

}

catch(StubException e){

throw new XletStateChangeException("Stub error");

}

}

public void pauseXlet() {

}

public void startXlet()

throws XletStateChangeException {

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile

**251**

public void destroyXlet( boolean b )

throws XletStateChangeException {

}

}

获取远程对象的过程类似于注册远程对象：获取 XletContext 的 IxcRegistry 引用，然后调用 `lookup` 来获取远程对象的存根。返回的存根将是原始类的代理，因此你可以将其强制转换为原始类定义；你应该确保生产者和消费者 Xlet 都包含了远程类的类定义。

在查找远程对象时，许多与注册时相同的情况都可能发生。在极少数情况下，可能没有 registry 可用于获取远程对象，或者 registry 可能无法为远程对象创建本地存根。然而，更常见的情况是远程对象根本没有被注册，因为生产者 Xlet 没有运行。如果发生这种情况，registry 的 `lookup` 方法将抛出 `NotBoundException`，你的 Xlet 就可以根据其业务规则自行决定如何处理，而不使用该远程对象。

请做好准备，你的远程对象可能在使用过程中消失。当这种情况发生时，访问该对象的任何方法都会抛出 `RemoteException`。这可能是由多种原因造成的，但最可能的原因是生成该对象的 Xlet 已经退出，并且原始的远程对象已被释放。因此，通常最好只在需要其服务时才访问远程对象，并在使用完毕后立即释放它，而不是依赖服务 Xlet 和对象在消费者 Xlet 的整个生命周期内持续存在。

**总结**

CDC 与 CLDC 具有相似的应用程序执行约束，包括有限的内存和 I/O 选项。为了反映这一点，基于 CDC 的 PBP 定义了 Xlet，这是一个与 MIDP 定义的 MIDlet 相似的可执行接口。

与 MIDlet 一样，Xlet 有一个定义良好的生命周期，由三个状态组成：*暂停*、*活动*和*销毁*。Xlet 在创建后从暂停状态开始其生命周期，当应用程序管理器调用其 `startXlet` 方法时进入活动状态。类似地，应用程序管理器可以通过调用其 `pauseXlet` 方法随时暂停 Xlet，并且可以通过再次调用 `startXlet` 强制 Xlet 重新进入活动状态来恢复执行。在任何时候，无论 Xlet 处于何种状态，应用程序管理器都可以通过调用 `destroyXlet` 方法来终止 Xlet 的执行。然而，与 MIDlet 不同的是，Xlet 可以通过抛出 `XletStateChangeException` 来拒绝状态转换并生成错误。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**252**

第 9 章 ■ 介绍 Xlet 和 Personal Basis Profile



Xlet 拥有一个伴随的 XletContext 对象，该对象为 Xlet 提供对应用程序管理环境关键信息的访问，包括 Xlet 使用的类加载器、应放置其 GUI 元素的根容器以及运行时属性。Xlet 还可以将应用程序管理系统提供的任何启动参数作为 XletContext 的属性进行访问。

多个 Xlet 可以同时运行；应用程序管理器将屏幕划分为多个独立的容器，为每个 Xlet 提供一个根容器。与定义了用户界面层次结构的 MIDP 不同，PBP 仅要求存在轻量级 AWT 组件，Xlet 及其支持类可以重写这些组件以创建用户界面。这可能会给跨平台 Xlet 带来挑战，因为支持 CDC 的不同 Java ME 设备可能具有不同的用户界面，范围从基于轻量级 AWT 层次结构的自定义 GUI，到实现了大部分 Java Swing 的 AGUI，再到其他 Java 包（例如提供 SVG 和 3D 图形的包）。

Xlet 可以通过基于 Java RMI 的接口共享数据；支持 PBP 的设备的运行时包含一个系统范围的注册表，用于注册实现了 `java.rmi.Remote` 接口的远程对象。使用此接口，您可以编写实现通过方法共享数据的对象的类，或者将计算任务卸载到另一个正在运行的 Xlet。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章

介绍 Applet 以及高级图形和用户界面

**基**于 CDC 的高端设备在许多方面与真正的计算机特性非常接近。许多设备可以始终访问互联网或通过网关连接到互联网的其他网络；有些甚至可能内置了 Web 浏览器。为了体现这些能力，JSR 62 和 216 中定义的 PP 定义了对 applet 的支持，并提供了与上一章中看到的 Xlet 应用程序模型的向后兼容性。

PP 定义的 applet 模型与 Java 从一开始就使用的经典 applet 模型相同；PP 设备可以在嵌入式 Web 浏览器或提供相同功能的原生执行环境中执行 applet。

此外，PP 要求存在 Java AWT，这使得创建复杂的用户界面变得容易。

在本章中，我将解释提供 PP 的设备所支持的 applet 执行模型。在讨论为支持 PP 的设备编写 Xlet 和 applet 时可用的用户界面选项之前，我将回顾编写 applet 的基础知识。

在此过程中，我将讨论在支持 PP 的设备上可能遇到的 AWT 和高级图形与用户界面（AGUI）实现。

**为 Java ME 编写 Applet**

Java applet 执行模型的最初目的是为在 Web 浏览器范围内通过 Web 下载的 Java 代码提供执行环境。

applet 的历史可以追溯到 Java 的早期，它遵守严格的安全限制，包括以下内容：

• Applet 不能定义或调用原生方法。

• Applet 不能访问执行 applet 的主机上的文件。

**253**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**254**

第 10 章 ■ 介绍 APPLET 以及高级图形和用户界面

• Applet 不能建立网络连接，除非连接到提供该 applet 的主机。

• Applet 不能调用执行 applet 的主机上的任何程序。

这些是 Xlet 没有的限制，这使得 applet 执行模型在提供与现有 applet 的向后兼容性或提供强大的 Web 浏览体验方面，比为新应用程序提供应用程序执行环境更受欢迎。

与 Xlet 和 MIDlet 一样，applet 具有通过基类 `java.applet.Applet` 提供的接口强制执行的生命周期。一个 applet 上下文伴随一个正在运行的 applet，允许 applet 访问资源，例如声音、图像以及在同一上下文中运行的其他 applet。

**查看 Applet 生命周期**

图 10-1 显示了 applet 在其执行过程中经历的状态。与 Xlet 和 MIDlet 不同，applet 不能暂停执行；相反，applet 执行环境可以停止或启动 applet。具体来说，运行时可以执行以下操作：

• 创建 applet，调用 applet 的构造函数。

• 通过调用其 `init` 方法，让 applet 有机会进行初始化。

• 通过调用其 `start` 方法启动 applet。

• 当 applet 失去焦点时（例如，当浏览器导航到不同页面时），通过调用其 `stop` 方法停止 applet。

• 当 applet 重新获得焦点时，通过再次调用 `start` 来**重新启动** applet（可能重新加载并重新初始化它）。

• 在垃圾回收 applet 之前，停止 applet 并通过调用其 `destroy` 方法让其有机会执行任何最终清理。当然，applet 也可以在任何其他时间被销毁。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍 APPLET 以及高级图形和用户界面

**255**

**图 10-1.** *Applet 的生命周期*

清单 10-1 显示了 applet 为遵循 applet 生命周期必须实现的接口。

**清单 10-1.** *Applet 接口*

```java
import java.applet.*;

public class SimplestApplet extends Applet {

    public void init() {
    }

    public void start() {
    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**256**

第 10 章 ■ 介绍 APPLET 以及高级图形和用户界面

```java
    public void stop() {
    }

    public void destroy() {
    }
}
```

从清单 10-1 可以看出，与 Xlet 模型相比，applet 模型在两个关键方面存在不足：它不能拒绝状态转换，并且没有暂停或恢复执行的概念。请注意，如果 applet 在状态转换期间无事可做，则它*不*需要重写清单 10-1 中的任何方法；该类为每个方法提供了默认实现。

**呈现 Applet 的用户界面**

在底层，applet 实际上是容器；具体来说，它们是 `java.awt.Container` 的子类，这意味着 applet 可以包含其他用户界面对象，或者通过重写 `paint` 方法执行自己的绘制。清单 10-2 中显示的 Hello World applet 演示了重写 `paint` 方法，窗口工具包在 applet 首次绘制以及每次显示时都会调用该方法。

**清单 10-2.** *重写 paint 方法*

```java
package com.apress.rischpater.HelloApplet;

import java.applet.*;
import java.awt.Graphics;

public class HelloApplet
    extends Applet {

    public void paint(Graphics g) {
        g.drawString("Hello world!", 0, 0);
    }
}
```

清单 10-3 展示了一种使用 Java AWT 组件和容器的方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍 APPLET 以及高级图形和用户界面

**257**

**清单 10-3.** *使用 Java AWT 组件和容器*

```java
package com.apress.rischpater.HelloApplet;

import java.awt.*;
import java.applet.*;

public class HelloApplet extends Applet {

    public void init() {
        Label label;
        label = new Label("Hello world!");
        add(label);
    }
}
```

这两个示例之间的区别在于绘制的方式。清单 10-2 中的代码直接使用窗口工具包的 `Graphics` 对象，而清单 10-3 中的代码将绘制委托给窗口工具包提供的组件。由于只有 PP 支持 applet，因此您可以确信，在支持 applet 的平台上至少可以使用 Java AWT。

**访问 Applet 的上下文**



小程序可以访问提供该小程序的服务器上的资源，包括图像和音频剪辑。这些资源可以由小程序本身或小程序的上下文直接获取。你可以通过调用小程序的`getAppletContext`方法来获取小程序的上下文（`AppletContext`类的实例）。`AppletContext`表示一个或多个小程序在其中执行的环境，例如网页或基于 Java 的应用程序管理器。

拥有一个`AppletContext`实例后，你可以执行以下操作：

• 使用`getApplet`获取对在`AppletContext`内运行的指定名称小程序的引用。

• 使用`getApplets`枚举由特定`AppletContext`管理的小程序列表。

• 使用`getAudioClip`从小程序的服务器获取音频剪辑，或使用`getImage`从小程序的服务器获取图像。

• 使用`showDocument`发送查看网页的请求。

• 使用`showStatus`更新上下文显示的状态。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**258**

第 10 章 ■ 小程序简介及高级图形与用户界面

由于`AppletContext`类的方法明显以浏览器为中心，并且由于传统 Java 小程序与网页浏览器的关系，这些方法不如提供给 MIDlet 和 Xlet 的上下文强大。与 MIDlet 和 Xlet 上下文相比，`AppletContext`类的一个有趣缺陷是小程序无法通过上下文请求自身终止；相反，想要终止自身执行的小程序必须向一个不同于包含该小程序的文档的 URL 发起`showDocument`请求。

**小程序间通信**

正如你可能从`AppletContext`的`getApplets`方法推断的那样，可以同时运行多个小程序，并且多个小程序可以共享同一个小程序上下文。

这使得共享同一上下文的多个小程序能够执行小程序间通信，因为共享单一上下文的每个小程序都可以了解彼此的存在并调用彼此的方法。对于从小程序服务器获取信息的多个小程序，你可以执行小程序间通信来流水线化请求，例如，这样只需要一个小程序执行网络活动。为了使小程序能够相互通信，这些小程序必须源自同一台服务器（并且，取决于实现，是同一台服务器上的同一个*目录*），并在同一个上下文中运行——通常是在浏览器的同一个窗口或同一个屏幕上。

为了让一个小程序找到另一个，搜索的小程序必须知道被搜索小程序的名称。如何指定这个名称取决于目标设备加载小程序的方式；如果目标硬件使用网页浏览器作为小程序的容器，你可以在指定小程序源时使用`<APPLET>`标签或`<PARAM>`标签来指定小程序的名称。例如，你可以编写如清单 10-4 所示的代码。

**清单 10-4.** *指定小程序的名称*

<APPLET CODEBASE="applets/" CODE="HelloWorld.class"

WIDTH=200

HEIGHT=200

NAME="hello"/>

清单 10-4 使用了`APPLET`标签的`NAME`属性，但你也可以使用更具扩展性的`APPLET`标签的`PARAM`属性，如清单 10-5 所示。

**清单 10-5.** *另一种指定小程序名称的方式*

<APPLET CODEBASE="applets/" CODE="HelloWorld.class"

WIDTH=200

HEIGHT=200>

<PARAM NAME="name" VALUE="hello"/>

</APPLET>

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 小程序简介及高级图形与用户界面

**259**

`APPLET` HTML 标签指定了 Java 类文件`HelloWorld.class`中定义的 Java 小程序应从 Web 服务器的`applets`目录加载，并在当前小程序上下文中被赋予名称`hello`。

回到小程序间通信的话题，考虑两个小程序：生产者（Producer）和消费者（Consumer）。生产者提供了一个在单一执行环境（如网页）上下文中由消费者使用的接口。生产者只需要声明一个供消费者使用的方法，如清单 10-6 所示。

**清单 10-6.** *在生产者中声明一个供消费者使用的方法* public class Producer extends Applet {

… 此处为方法 …

public void processRequest(String anArgument) {

… 对 anArgument 进行处理 …

}

}

服务于生产者小程序的 HTML 必须标识生产者，以便消费者可以使用`AppletContext`通过名称找到它，如清单 10-7 所示。

**清单 10-7.** *标识生产者小程序*

<APPLET CODEBASE="applets/" CODE="Producer.class"

WIDTH=200

HEIGHT=200

NAME="producer"/>

消费者小程序调用生产者的`processRequest`方法，它只需要在小程序上下文中定位生产者。然后它可以像对待任何其他 Java 对象一样对待结果对象，调用其`processRequest`方法，如清单 10-8 所示。

**清单 10-8.** *调用生产者的 processRequest 方法* public class Consumer extends Applet

implements ActionListener {

… 此处为方法 …

public void actionPerformed(ActionEvent e) {

Applet producer = null;

producer = getAppletContext().getApplet("producer"); if (producer != null &&

producer instanceof Producer) {

((Producer)producer).processRequest("Hi!");

[www.it-ebooks.info](http://www.it-ebooks.info/)

**260**

第 10 章 ■ 小程序简介及高级图形与用户界面

} else {

// 以某种方式处理错误

}

}

}

换句话说，小程序上下文提供了一个在相同上下文中运行的小程序的命名注册表；一旦你从注册表中检索到一个小程序，就可以简单地调用它的一个方法。当然，良好的编程实践要求你在执行此操作时进行某种错误处理，要么通过显式测试目标是否实现了你为小程序间通信所需的接口，要么通过捕获可能在你调度的类不是你所期望的类时发生的`NoSuchMethod`异常。发生这种情况的一个常见原因是小程序管理器已加载了一个小程序但未加载另一个。

**使用 AWT 开发用户界面**

历史上，PP（它是 PBP 的超集）源自 PersonalJava，这是 Sun 首次涉足机顶盒和其他消费电子设备领域。因此，它包含了对大多数 Java AWT 的支持，包括重量级组件。在内存和其实时操作系统（RTOS）占用方面成本高昂，包含 AWT 是必需的，因为 PP 支持与 Web 小程序向后兼容的小程序，并允许设备制造商托管和为新设备提供小程序。

■**注意** 为了向后兼容，PP 支持已弃用的 Java 1.0.2 事件模型，该模型基于`java.awt.Event`类。但这并不意味着你应该使用 Java 1.02 事件模型，因为它已被弃用，并可能在 PP 的未来版本中消失。

PP 包含了 J2SE 1.3 中的大部分 AWT 类。以下列出了一些关键的遗漏项：

• 二维图形类，例如`java.awt.Paint`和`java.awt.Stroke`

• 通过`java.awt.PrintJob`和`java.awt.PrintGraphics`对打印的支持

• 通过`java.awt.Component.AccessibleAWTComponent`层次结构对可访问接口的支持

重要的是要记住，AWT 仅在实现 PP 的 CDC 平台上可用；然而，PP 同时包含了 Xlet 和小程序执行模型。换句话说，只要你的小程序和 Xlet 在支持 PP 的硬件上运行，你就可以利用 PP 中定义的 AWT 来开发小程序和 Xlet。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 小程序简介及高级图形与用户界面

**261**



图 10-2 展示了用于用户界面组件的 AWT 类层次结构，并标出了该层次结构中的主要类。你可以将这些组件分为两个关键组：AWT *组件* 和 AWT *容器*。组件（Component 是其超类）代表任何用户界面对象，包括按钮、复选框、单选按钮、标签、列表和文本输入项。容器（Container 是其超类）是一种可以包含其他组件的组件。容器拥有布局管理器，负责决定组件在容器内的显示位置。

**图 10-2.** *AWT 类层次结构*

[www.it-ebooks.info](http://www.it-ebooks.info/)

**262**

第 10 章 ■ 小程序与高级图形及用户界面入门

■**注意** 如果你需要学习使用 Java AWT 进行编程的基础知识，请参阅 Jeff Friesen 所著的 *Beginning Java SE 6 Platform: From Novice to Professional*（Apress，2007），或访问 Sun 公司关于该主题的文档，网址为 http://java.sun.com/javase/6/docs/technotes/guides/awt/。

**使用 AWT 容器**

在 AWT 中，容器就是一个可以包含其他组件的组件。轻量级的 Panel 容器允许你将相关组件分组，而 Window 和 Frame 容器在原生平台的窗口环境中具有相应的重量级实现。Window 在原生平台的窗口环境中提供一个顶级窗口，而 Frame 则提供顶级标题和边框。

容器维护一个组件列表，并允许你向该列表添加更多组件。Container 的各种 add 方法允许你添加组件，不仅指定要添加的组件，还指定新组件在组件列表中的位置，以及组件在容器内放置方式的任何约束条件。

该列表仅表示容器内的组件及其 Z 轴顺序；容器的*布局管理器*决定了组件在容器边界内的视觉布局方式。布局管理器使用每个组件指定的最小和首选大小、容器边界以及一种算法，来确定组件在容器内的显示方式。AWT 定义了五种布局管理器：

• BorderLayout：将其组件排列到五个区域：北、南、东、西和中心。当使用 BorderLayout 向容器添加组件时，你需要指明组件应占据哪个区域；BorderLayout 在定位每个组件时会考虑其首选大小。

• CardLayout：将其组件排列成每个组件占据一张虚拟卡片；一次只能看到一张卡片（因此只有一个组件）。CardLayout 定义了一组方法，允许应用程序按顺序遍历卡片或显示特定卡片。

• FlowLayout：按从左到右、从上到下的流式方式排列组件。

• GridBagLayout：垂直和水平对齐组件，而不要求组件大小相同。每个组件都有一组相应的约束条件，指示组件应如何放置在容器中。

• GridLayout：在矩形网格中垂直和水平对齐组件，要求每个组件大小相同。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 小程序与高级图形及用户界面入门

**263**

当你创建一个容器时，其默认布局管理器是 FlowLayout。

你可以使用 getComponent 或 getComponentAt 方法查询容器以获取其某个组件；前者接受组件列表中的索引，后者接受 Point 或 *x* 和 *y* 坐标，并返回指定位置的组件。你还可以通过调用容器的 isAncestorOf 方法来测试容器是否包含特定组件。

通常，在创建用户界面时，你会使用容器来分组组件，而用户界面本身可能继承自 Panel 或 Window 类。典型的模式是在容器的构造函数中，或在构造函数调用的方法（如 initComponents 方法）中创建容器的组件。由于手动创建用户界面可能很繁琐，可以考虑使用像 NetBeans 这样的工具，它为 Container 类提供了源代码视图和设计视图。

■**提示** 在 NetBeans 中，右键单击“项目”窗格中的包，选择“新建”➤“JPanel 表单…”或“新建”➤“JFrame 表单”，然后为新文档选择“设计”视图。你可以在“源代码”视图中将容器的基类更改为 java.awt.Panel 或 java.awt.Frame，而 NetBeans 提供的组件面板在 Swing 组件下方包含了 AWT 组件。

由于许多消费电子设备存在限制，Window 类与底层窗口之间的关系可能有些薄弱。这是因为顶级窗口的位置、大小和标签由原生窗口管理器控制。那些允许你调整容器标题、边界和位置的方法，只是为原生窗口管理器提供指导，而非管理器必须遵守的绝对规则。因此，窗口管理器可能会忽略这些请求，或对其进行修改，以便恰当地显示 Window。

在某些平台上，这些操作可能是异步的；诸如 getLocation、getSize 和 getLocationOnScreen 之类的接口可能无法反映 Window 的实际几何形状。此外，窗口管理器可能会忽略 Frame 的装饰、标题和可调整大小属性。

**使用 AWT 组件**

java.awt.Component 的各种子类是你用户界面真正发挥作用的地方。正如你在图 10-2 中所见，组件层次结构包含了创建大多数用户界面所需的基本控件，并且你始终可以通过在容器中组合多个组件，或直接绘制组件内容并处理事件，来创建自己的组件。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**264**

第 10 章 ■ 小程序与高级图形及用户界面入门

java.awt.Component 接口管理以下几项不同内容：

• 组件的属性，例如组件的位置、字体、可见性、不透明度等

• 组件的事件处理；这些方法包含一组已弃用的方法，现在已被我将在本章后面“处理 AWT 事件”一节中讨论的监听器模型所取代

• 支持绘制组件的方法

• 管理焦点并指示哪个组件拥有焦点的方法

正如你已经看到的，AWT 组件有两种类型：重量级和轻量级。重量级组件是原生窗口工具包中*对等*对象的*代理*接口；例如，如果 java.awt.Button 类有一个与按钮对应的原生组件，那么某处就会有一个封装了该原生按钮接口的对等类。另一方面，轻量级组件在原生窗口工具包中没有对应的实体；Java 环境会即时渲染它们。虽然这不是强制要求，但如果你在符合 PP 规范的设备上遇到 AWT，AWT 很可能会实现重量级组件的接口。

重量级组件的一个优势在于，使用重量级组件开发的应用程序通常能够匹配原生平台的外观和感觉，因为重量级组件工具包使用的控件与底层平台提供的控件相同。然而，用于桥接 AWT 与底层平台的类的实现可能会消耗宝贵的堆和 ROM 空间，这就是为什么并非所有 CDC 配置文件都包含重量级组件的原因。



在处理组件时，最好避免混合使用轻量级和重量级组件，这是由于 AWT 和底层平台都存在限制。轻量级组件（包括 AGUI 中的组件，我将在本章后面的“使用 AGUI 开发用户界面”一节中讨论）支持透明区域，这与重量级组件不同。除此之外，这意味着重量级组件会遮挡其背后的内容，并且只能显示为矩形。此外，当重量级组件和轻量级组件相交时，无论 Z 轴顺序如何，重量级组件始终位于顶层。最后，重量级对象会消耗落在其上的所有鼠标事件；而轻量级组件则会将事件传递给其父容器。

**处理 AWT 事件**

使用 AWT 时，你可以通过重写从窗口工具包接收事件的特定方法来处理事件。你可以通过以下两种方式之一来实现：拦截传递给组件的低级事件，或者为高级事件注册监听器。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍小程序以及高级图形和用户界面

**265**

处理低级事件最好由特定组件来完成，例如在编写新的轻量级组件时（我将在第 9 章讨论这个主题）。如果你需要处理低级事件，可以按照以下步骤操作：

**1.** 通过调用 `enableEvents` 并传入一个常量来启用对相应事件的接收，该常量表示小程序想要接收的事件类型。

**2.** 重写 `processEvent` 或其委托方法之一（`processComponentEvent`、`processFocusEvent`、`processKeyEvent`、`processMouseEvent`、`processMouseMotionEvent`、`processInputMethodEvent` 或 `processMouseWheelEvent`）来处理任何传入的事件。

**3.** 当你不想再接收任何事件时，通过调用 `disableEvents` 来禁用事件接收。

然而，处理事件最简单的方法是扩展组件以包含一个针对高级事件的特定监听器，然后实现相应的监听器。Java 事件模型——被 AWT 和 Swing（AGUI 的基础）共同使用——包含了针对各种事件的监听器接口，包括以下这些：

• `ActionListener`：处理由组件生成的高级操作
• `FocusListener`：处理 `java.awt.Container` 及其子类中的焦点变化
• `KeyListener`：处理单个键盘事件
• `MouseListener`、`MouseMotionListener` 和 `MouseWheelListener`：处理由鼠标和指针生成的事件
• `TextListener`：处理指示文本字段内容已更改的事件

清单 10-9 展示了一个响应其按钮点击的简单小程序。

**清单 10-9.** *响应按钮点击*

package com.apress.rischpater.buttonsample;

import java.awt.*;

import java.awt.event.*;

import java.applet.*;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**266**

第 10 章 ■ 介绍小程序以及高级图形和用户界面

public class ButtonApplet extends Applet

implements ActionListener {

Label label;

public ButtonApplet() {

}

public void init() {

label = new Label("Hello World");

Button button = new Button("Click Here");

add(label);

add(button);

button.addActionListener(this);

}

public void actionPerformed(ActionEvent e) {

label.setText("Click!");

}

}

虽然这个例子很简单，但它展示了基本思想：对接收事件感兴趣的对象必须声明自己实现了相应的监听接口，然后通过调用适当的方法向事件生成者表明它们正在监听事件。这个例子通过实现提供 `actionPerformed` 方法的 `ActionListener` 契约来做到这一点。此外，小程序通知生成 `ActionEvents` 的按钮，它希望接收该按钮生成的任何 `ActionEvents`。

**使用 AGUI 开发用户界面**



JSR 209 中定义的 AGUI 包为 Java ME 设备提供了 Java Swing 类的一个子集。使用 AGUI，你基本上可以开发一个既能运行在支持 AGUI 的 Java ME 设备上，也能运行在传统计算机上的单一应用程序。AGUI 包依赖于 CDC 1.1、FP 1.1 和 PBP 1.1，并提供了以下包：

•   java.awt 包的一个子集，用于提供实现 AGUI 轻量级组件的类层次结构
•   java.awt.font 包，用于支持渲染 PostScript Type 1、Type 1 多母版、OpenType 和 TrueType 字体
•   java.awt.geom 包，用于对与二维几何相关的对象进行操作

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍小程序以及高级图形和用户界面

**267**

•   java.awt.image 包，用于支持图像的流式创建和修改；AGUI 要求支持 GIF89a、JPEG、JFIF 和 PNG 1.0 版图像
•   java.nio 包，用于指定字节顺序
•   javax.imageio 包，以及 javax.imageio.event、javax.imageio.metadata 和 javax.imageio.stream，用于支持图像 I/O
•   javax.microedition.plaf 包，该包与 Java SE 实现的不同之处在于，其实现 API 不是类而是接口，以允许 OEM 和服务提供商深度定制 AGUI 的外观和感觉
•   javax.microedition.agui.event 类，用于支持消费电子设备上常见的专用硬件按键
•   javax.swing 包及其子包 javax.swing.border、javax.swing.event、javax.swing.plaf、javax.swing.table、javax.swing.text、javax.swing.tree 和 javax.swing.undo，用于提供在多个平台上工作方式相同的轻量级组件

使用 AGUI 类似于使用 AWT，因为你需要围绕包含组件的容器来组织用户界面，并注册事件监听器来处理用户界面事件。然而，存在两个关键区别，尤其是在使用 Swing 提供的一些更复杂的组件时。首先，Swing 的设计者围绕 MVC 设计模式设计了 Swing 的组件。理解 MVC 可以使与更复杂的 Swing 组件的交互变得更容易。其次，Swing 支持可插拔的外观和感觉（尽管 AGUI 支持并*不*提供相同的功能）。

■**注意** 如果你需要学习使用 Java Swing 进行编程的基础知识，与 AWT 类似，我建议你购买一本 Jeff Friesen 所著的 *Beginning Java SE 6 Platform: From Novice to Professional*（Apress，2007 年），或者访问 Sun 公司关于该主题的文档，网址为 http://java.sun.com/javase/6/docs/technotes/guides/swing/。

使用 AGUI 与使用 Swing 非常相似，凭借你现有的 Swing 经验，你应该会感到得心应手。然而，存在一些差异，包括以下内容：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**268**

第 10 章 ■ 介绍小程序以及高级图形和用户界面

•   对需要相应顶级窗口的容器行为的可能限制
•   对输入约束和设备特定按键的额外支持
•   对组件子类化绘制行为的限制
此外，根据 AGUI 实现的不同，几个类方法有一些可选的限制；你的应用程序可以通过系统属性查询系统以确定实际行为。表 10-1 显示了你可查询的系统属性列表及其含义；后续章节将更详细地描述 AGUI 的限制。

**表 10-1.** *AGUI 系统属性及其值* **属性**

**值**

javax.swing.JComponent.setBackground.isRestricted

当且仅当 JComponent.setBackground 受到本地窗口工具包限制时为 true

javax.swing.JComponent.setBorder.isRestricted

当且仅当 JComponent.setBorder 受到本地窗口工具包限制时为 true

javax.swing.JComponent.setForeground.isRestricted

当且仅当 JComponent.setForeground 受到本地窗口工具包限制时为 true


JComponent.setForeground 受到限制

javax.swing.JComponent.setToolTipText.

当且仅当对 `isRestricted` 的调用导致
JComponent.setToolTipText 静默失败时，返回 true
因为工具提示不受支持

javax.swing.JList.setCellRenderer.isRestricted

当且仅当
JList.setCellRenderer 无法设置
自定义单元格渲染器时，返回 true

javax.swing.setMnemonic.isRestricted

当且仅当对 setMnemonic 的调用
静默失败时，返回 true

javax.swing.text.JTextComponent.

当且仅当对
setFocusAccelerator.isRestricted 的调用导致
JTextComponent.setFocusAccelerator
静默失败时，返回 true

javax.swing.JMenuBar.clientPropertiesSupported

当且仅当 JMenuBar 支持
客户端属性时，返回 true

javax.swing.JMenuBar.NUM_SOFT_KEYS

设备上可用的软键数量，
如果支持 JMenuBar 客户端属性；
否则未定义

javax.swing.JMenuBar.NUM_SOFT_MENUS

设备上可用的软菜单数量，
如果支持 JMenuBar 客户端属性；
否则未定义

javax.swing.JTabbedPane.setToolTipTextAt.

当且仅当对
isRestricted 的调用导致
JTabbedPane.setToolTipTextAt 静默失败时，返回 true

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍小程序以及高级图形和用户界面

**269**

最后，温馨提醒：AGUI 与 Swing 一样，*不*是线程安全的。Swing 组件一次只能由一个线程访问；通常，这个线程是事件分发线程。如果你正在编写一个需要与 AGUI 接口交互的多线程应用程序，应确保所有 AGUI 更新都在事件分发线程上执行。你可以使用 `javax.swing.SwingUtilities` 类来实现这一点；它提供了静态方法 `invokeLater` 和 `invokeAndWait`，这两个方法接受一个 `Runnable`，在窗口系统处理完所有待处理事件后，在事件分发线程上执行该 `Runnable`。使用 `invokeLater` 方法将要在事件处理线程上执行的操作排队；这比 `invokeAndWait` 更可取，因为后者会阻塞，直到窗口系统完成你的任务。

**理解对顶级窗口的限制**

PBP 允许设备禁止多个顶级窗口。当 AGUI 与不允许有多个顶级窗口的设备结合使用时，AGUI 也受到同样的限制：单个图形设备上只允许有一个 `JFrame`。

尝试构造第二个 `JFrame` 会失败，运行时将抛出 `java.lang.UnsupportedOperationException`。这意味着，为了在支持 AGUI 的设备之间获得最大的可移植性，你的用户界面应完全驻留在一个窗口中。

这对其他容器也有影响。例如，`JOptionPane` 可能只能使用轻量级容器来显示对话框，并且可能无法通过创建父组件为 `null` 的 `JOptionPane` 来创建顶级对话框。

类似地，弹出菜单应根植于特定的容器中；你不能创建调用者为 `null` 的 `JPopupMenu`。此外，与 `JOptionPane` 一样，`JPopupMenu` 在其组成中可能只使用轻量级组件。

**使用 AGUI 新增的输入支持**

许多消费电子设备没有传统的键盘；事实上，许多消费电子设备彼此之间拥有*不同*的输入设备。

这对任何通用的窗口工具包都构成了挑战，它必须以某种方式抽象不同设备之间的差异。

AGUI 借鉴了输入约束的概念，该概念通过将 `INPUT_CONSTRAINT` 客户端属性通过 `putClientProperty` 方法传递给 `JTextComponent`，来限制用户可以从 MIDP 2.0 输入的内容。表 10-2 显示了 AGUI 实现可能支持的输入约束。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**270**

第 10 章 ■ 介绍小程序以及高级图形和用户界面

**表 10-2.** *AGUI 支持的输入约束和值* **约束**

**传递给 putClientProperty 的数值**

**描述**

ANY

允许用户输入任何文本。

EMAILADDR

允许用户输入电子邮件地址。

NUMERIC

允许用户输入整数值。

PHONENUMBER

允许用户输入电话号码。

URL

允许用户输入 URL。

DECIMAL

允许用户输入有符号十进制值。

由于 AGUI 可能实现在具有特定硬件键的设备上——例如遥控器或 PDA 或智能手机上的物理按钮——AGUI 定义了 `javax.microedition.agui.event.DeviceKeyEvent`，用于指定硬件按键事件，例如键盘音量控制、专用应用程序启动功能或媒体控制（如快退或快进）。按键事件监听器可能会接收到值为 `VK_UNDEFINED` 的按键事件（`java.awt.event.KeyEvent` 类的子类实例），然后使用 `instanceof` 测试该事件，以确定该 `KeyEvent` 是否真的是 `DeviceKeyEvent`。如果是，则可以通过调用 `DeviceKeyEvent` 的 `getDeviceKeyCode` 方法来确定用户按下了哪个硬件键。

最后，许多消费电子设备支持*软键*，应用程序可以为这些软键分配自定义标签和操作。AGUI 允许你通过 `JMenuBar` 接口来定义这些软键，指定一个*菜单类型*和一个*菜单优先级*。菜单类型指示你的应用程序通过软键提供的操作的意图，菜单优先级指示此命令标签相对于同一屏幕上的其他命令标签的重要性。

**理解绘图算法的变化**

AGUI 的一个关键折衷是支持 Swing API，无论它们是以轻量级组件还是重量级组件的形式实现。为了实现这一目标，平台供应商可能会进行许多更改，以更好地支持实现 AGUI 的许多平台上常见的原生渲染管线。

最显著的变化可能是，`JComponent` 子类的渲染默认是双缓冲的。你可以通过调用任何 `JComponent` 实现者的 `isDoubleBuffered` 方法来确定双缓冲是否处于活动状态。你也可以通过调用 `setDoubleBuffered` 并传递 `false` 来请求组件执行单缓冲绘制，但 AGUI 实现可以选择忽略你的请求。

许多消费设备上的底层 UI 工具包远不如支持当今 Java SE 实现的工具包灵活。为了满足这些限制，

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 10 章 ■ 介绍小程序以及高级图形和用户界面

**271**

AGUI 限制了应用程序在组件之上进行渲染的能力。由于组件可能是重量级的，`JComponent` 的绘制方法（`paint`、`paintComponent`、`paintBorder` 和 `paintComponents`）在 `JComponent` 的第一个具体子类上已被设为 `final`。这是因为 AGUI 提供了与底层平台优化的、紧密的集成，并且可能使用平台的窗口工具包来实现 AGUI。

看待此限制的另一种方式是结合 Swing 和 AGUI 提供的可插拔外观（PLAF）；在 Swing 中，开发人员很容易覆盖 Swing 组件的呈现方式，以提供自己的外观。在 AGUI 中，此功能主要是为了让实现 AGUI 的平台供应商能够为设备提供最佳外观，而不是为了像你我这样的开发人员创建特定的外观。

**总结**

为了与 Web 上的大量小程序保持兼容，并提供熟悉的编程模型，PP 提供了对小程序的执行模型的支持。

与当今 Web 浏览器中使用的小程序模型相同，CDC 设备提供的小程序模型在嵌入式 Web 浏览器或强制执行相同安全模型的其他执行环境的上下文中运行小程序。

小程序的生命周期与 MIDlet 和 Xlet 相似，但并不完全相同。



值得注意的是，并没有定义好的方法来暂停和重启一个 applet，并且与 MIDlet 和 Xlet 不同，applet 不能显式地请求其包含的应用程序来终止 applet 的执行。由于 applet 是`java.awt.Container`的子类，它可以包含其他用户界面组件，或者简单地重写其`paint`方法并直接绘制其用户界面。

为了支持 applet，PP 还支持大部分 AWT，这是 Java 的第一个用户界面层次结构。虽然 AWT 的实现并不完整——它缺乏对打印、可访问接口以及部分二维图形的支持——但它的限制相对较少。与 Java SE AWT 一样，PP 提供的 AWT 通常使用重量级组件实现，使您能够创建反映托管 Java 环境的原生平台外观和感觉的复杂用户界面。

AGUI 是一个可选包，需要 CDC、FP 和 PBP 的支持，它为您提供了在某些设备上开发丰富用户界面的另一种方式。您不仅可以使用 AGUI 编写 applet，还可以编写 Xlet。基于无处不在的 Swing 用户界面类层次结构，AGUI 有一些限制，例如对顶层容器的限制以及组件如何自定义绘制行为的限制。AGUI 还提供了一个更健壮的事件框架，支持当今消费电子设备上常见的软键和硬件按钮。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章

使用远程方法调用

**C**DC 专为具有始终在线或几乎始终在线互联网连接的设备而设计。CDC 设备为网络编程提供了巨大潜力，因为有限资源的需求与可靠的网络连接取得了平衡。Java 对远程方法调用（RMI）的支持提供了一种编程模型，它是 Web 服务提供的轻量级模型的一个受欢迎的替代方案。（具有讽刺意味的是，RMI 模型在很大程度上早于向 Web 服务的转变，因为它基于 Sun 公司开发的远程过程调用语义，并在各种远程计算计划中普及。）在本章中，我首先带您快速浏览 Java SE 中实现的 Java RMI 设施。在向您介绍了 Java RMI 的高级架构和方法之后，我将介绍关键的 Java RMI 接口，并展示这些接口如何协同工作以满足许多网络化应用程序的两个关键目标：远程化 Java 计算和远程化对 Java 对象的访问。接下来，我将注意力转向 JSR 66，即 Java RMI 可选包（RMI OP），它定义了在某些支持 CDC 的设备上可用的 RMI 实现。在向您展示了通过 Java RMI OP 可用的内容之后，我将提供一个示例来帮助巩固您在本章中遇到的概念。

**理解 Java RMI**

Java 编程语言的一个关键特性是它能够通过 Java RMI 范式支持分布式编程——即在独立主机上运行、来回传递对象的应用程序。

所有分布式面向对象应用程序都需要执行三种任务：

• *远程对象发现*：应用程序需要发现远程对象引用的机制。

• *远程通信*：在不同机器上的不同上下文中运行的应用程序需要能够就对象的不断变化的状态进行通信。

• *远程行为定义*：应用程序还必须能够就派生这些对象的**273** 定义进行通信。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**274**

第 11 章 ■ 使用远程方法调用

让我们看看 Java RMI 的架构如何满足这些要求。

**理解 Java RMI 的架构**

Java RMI 通过两种关键方式满足分布式程序的需求：通过提供远程发现注册表，以及通过提供用于交换关于 Java 类和对象信息的健壮通信机制。图 11-1 显示了基于 RMI 的应用程序中客户端和服务器之间的关系。

**图 11-1.** *RMI 应用程序架构*

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**275**

基于 RMI 的解决方案有几个关键组件：

• *RMI 客户端应用程序*：它在客户端的 JVM 内运行。支持 RMI 客户端应用程序的是 Java 远程方法协议（JRMP）。1

• *RMI 注册表*：由应用服务器维护，它为 RMI 客户端提供对象发现功能。

• *RMI 服务器*：托管在应用服务器的 JVM 上，负责执行远程对象。

• *Web 服务器*：负责向 RMI 客户端应用程序提供新代码。

您像构建任何其他 Java 应用程序一样，通过接口和类来构建使用 RMI 的分布式应用程序。在您的分布式应用程序中，一些对象是*可远程化的*——也就是说，您可以通过 RMI 提供的协议跨 JVM 调用可远程化对象的方法。您使用运行在已知服务器上的 RMI 注册表来获取对这些对象的引用；注册表返回一个实现了远程对象提供的方法的*存根*。在底层，远程对象与本地对象不同；相反，它是一个代理，实现了与远程 JVM 的对象实例通信的网络接口。存根对象实现了与远程对象相同的接口，这意味着它可以被转换为远程对象可实现的任何对象。图 11-2 显示了这种关系；远程对象是 RMI 服务器中的实心圆，存根是 RMI 客户端中的虚线圆。

**图 11-2.** *RMI 实现中远程对象和本地对象之间的关系*
1. 其他协议，例如基于 Internet Inter-Orb 协议的 Java RMI（RMI-IIOP）以及专有实现，可以支持 RMI；这些协议不受 CDC 支持，此处不讨论。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**276**

第 11 章 ■ 使用远程方法调用

事实上，RMI 不仅仅是您的接口、实现和存根。早期版本的 RMI 需要存根和*骨架*，骨架是服务器端的类，负责在存根和服务对象之间进行通信。您像构建存根一样，使用 RMI 编译器 `rmic` 构建这些骨架。如今，随着 Java 反射接口的兴起，RMI 应用程序只需要存根；事实上，正如您将在本章后面看到的，当使用为 Java ME 定制的 RMI 包时，甚至不需要存根类。

Java RMI 模型相对于专有和传统的 Web 服务方法有其优势，包括以下几点：

• *面向对象*：RMI 模型以无缝方式扩展了 Java 执行模型，允许您通过 RMI 接口传递 Java 对象。这使您在构建应用程序时可以继续使用面向对象的技术和设计模式。

• *安全性*：RMI 模型利用了 Java 现有的安全模型。

• *遗留系统支持*：提供 RMI 服务的服务器可以通过 JNI 调用连接到遗留服务，让您将遗留系统包装在 Java 接口中，并将其集成到您的网络化应用程序中。

这些优势使得 RMI 在许多情况下具有吸引力，尤其是在机顶盒市场的消费电子应用中，自九十年代末以来，安全性和与大规模遗留计算的集成一直是一项持续的努力。与此同时，RMI 有些过时，它起源于九十年代中期到后期。与其主要竞争对手，基于 XML 和 HTTP 的 Web 服务模型相比，它可能被视为有所欠缺，尤其是在以下方面：



•   *与 Web 服务的互操作性*：Web 服务模型已成为许多市场中领先的客户端/服务器架构模式。将 RMI 应用程序连接到现有 Web 服务需要额外的工作，这与将 RMI 应用程序连接到任何其他遗留应用程序所需的工作类似。

•   *重量*：RMI 在其支持的平台上会带来体积上的代价；RMI 在移动设备上需要 CDC 和 Foundation Profile。这比 Java ME Web 服务实现需要更强大的硬件，后者可以在 CLDC 平台上运行。此外，在无线链路上，RMI 协议可能比精心设计的 Web 服务交换昂贵得多。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**277**

•   *网络支持*：尽管完整的 RMI 实现支持通过防火墙的自动 HTTP 隧道，正如你将在本章后面的“了解 Java RMI 可选包提供的内容”一节中看到的，但 Java RMI OP 并不支持。因此，要在 Java ME 设备上支持 Java RMI，你的网络安全策略需要比单纯部署 Web 服务时考虑得更多。

尽管存在这些限制，但在开发应用程序时，Java RMI 可能是一个不错的选择，特别是当你的情况满足以下两个条件之一时。首先，显然，如果你的应用程序基础设施已经使用了 RMI，那么在开发过程中继续使用 RMI 可以最好地与之集成。这在电信消费电子市场的某些领域尤其如此，在这些领域，针对受限电视机的中间件解决方案很常见。其次，如果你正在开发一个具有强远程计算需求的应用程序，RMI 为通过代理表达远程计算程序提供了比传统 Web 服务更好的抽象层。

**介绍 Java RMI 接口**

理解 Java RMI 的关键在于理解接口的概念。正如你所知，接口是一种契约：它承诺某个特定对象将执行特定功能（类的方法），但它不说明如何执行这些功能。换句话说，*接口将行为的定义与行为的实现分离开来*。这一点至关重要，因为 RMI 在其整个实现中都使用了这一概念；你的远程对象是根据其接口来定义的，而其实现（即它们作为远程对象运行的能力）则在程序后台进行。

Java RMI 接口定义在`java.rmi`包层次结构中。该层次结构定义了一个接口、三个类和一系列异常：

•   *`java.rmi.Remote`接口*：指定了用于远程执行的接口的意图
•   *`java.rmi.MarshalledObject`类*：抽象化一个包含对象序列化表示的字节流
•   *`java.rmi.Naming`类*：提供了在远程对象注册表中存储和获取远程对象引用的接口
•   *`java.rmi.RMISecurityManager`类*：定义了从远程服务器下载代码时使用的默认安全管理器
•   *诸如`java.rmi.RemoteException`的异常*：在处理分布式环境中可能发生的错误时出现

[www.it-ebooks.info](http://www.it-ebooks.info/)

**278**

第 11 章 ■ 使用远程方法调用

■**注意** Java RMI 的一个并行实现提供了基于 Internet Inter-Orb 协议（RMI-IIOP）的 RMI。它现在是 Java SE 的一部分，位于`javax.rmi`层次结构中，但在 Java ME 中没有相应的支持。

要实现分布式系统的服务器端，还需要一些额外的代码。`java.rmi.server`层次结构提供了这些代码，包括`java.rmi.server.UnicastRemoteObject`和`java.rmi.activation.Activatable`类。这两个类的区别在于，扩展`UnicastRemoteObject`的远程对象需要服务器在对象的整个生命周期内运行，而扩展`Activatable`的对象则在 Java 的 RMI 守护进程`rmid`的上下文中运行。

**了解 Java RMI 可选包**

Java RMI OP（在 JSR 66 中正式化）在移动 Java 的历史上出现得相当早。它于 2000 年获得批准，源于摩托罗拉、西门子、Sun 等公司为将 RMI 引入运行 J2ME（Java ME 的前身）的移动平台所做的努力。这些合作伙伴开发了 RMI OP，以满足利用 Java 标准进行信息传递的高度连接、分布式应用程序的需求。如今，Sun 为被许可方提供了一个完全用 Java 实现的 Java RMI OP 参考实现，该实现可以直接集成到满足最低要求的硬件上。RMI OP 与 Java SE RMI 实现可互操作，使其成为将嵌入式设备与现有基于 RMI 的解决方案集成的理想选择。

**查看 Java RMI 可选包的要求**

尽管经过精心精简以满足嵌入式设备的需求，RMI OP 仍然有一些相当大的要求，包括以下内容：

•   在 Java ME 和其他平台要求的现有基础上，需要 2.5MB 的可用 ROM
•   在 Java ME 和其他平台要求的现有基础上，需要 1MB 或更多的可用 RAM
•   与网络的 TCP/IP 连接
•   支持在 Java ME 连接设备配置之上运行的 Java ME Foundation Profile

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**279**

许多设备供应商已转向在移动和嵌入式设备上提供对 CDC 的支持，尽管很少有供应商仍能提供运行 RMI OP 所需的处理能力。随着内存和处理器的成本持续下降，并且我们看到个人计算机与其他消费设备之间更大的融合，这种情况很可能会改变，RMI OP 将从仅在专用环境中可用转变为更普遍地可用。

**了解 Java RMI 可选包提供的内容**

RMI OP 与 Java SE 提供的 RMI 有何不同？首先确定 RMI OP 与 Java SE 的*相同*之处很有用，因为你很可能会围绕 Java SE RMI 设计你的解决方案，并使用基于 Java SE 或 Java EE 的服务器环境来支持你的分布式应用程序。

根据定义，RMI OP 必须支持以下功能：

•   完全支持 RMI 调用语义，包括对 RMI 有线协议的支持
•   支持编组——即支持在远程主机上表示对象
•   能够通过`UnicastRemoteObject`类从支持 RMI OP 的设备导出远程对象
•   跨客户端和服务器的分布式垃圾回收和垃圾回收器接口
•   支持不持久存在于调用之间且需要全职服务器管理其激活的对象的接口
•   支持注册表接口，以及支持 RMI OP 的设备远程导出注册表对象的能力

对于一个针对低端设备的标准来说，RMI OP 出奇地全面。通过支持`UnicastRemoteObject`、分布式垃圾回收以及注册和导出远程对象的能力，支持 RMI OP 的硬件可以完全参与分布式计算，在对象分发过程中同时充当客户端和服务器。

这种对称性是 RMI OP 相对于传统 Web 服务计算模型的一个关键特性，这既是因为它是与 Java SE RMI 完全互操作所必需的，也是因为它允许嵌入式设备向远程服务器提供对象。事实上，在提供 RMI OP 的设备上，RMI 可用于同一设备上的应用程序间通信，或用于跨网络的真正分布式应用程序。



[www.it-ebooks.info](http://www.it-ebooks.info/)

**280**

第 11 章 ■ 使用远程方法调用

那么缺少什么呢？实际上很多，但很可能你并不会用到其中的大部分：

• 支持通过 HTTP 代理 RMI 请求

• 支持 RMI 多路复用协议，该协议允许在仅存在单个通信通道时，多个 JVM 相互调用远程方法

• 实现 `Activatable` 接口，为可激活对象提供具体实现

• 已弃用的方法、类和接口（反正你也不应该使用它们！）

• 支持 RMI 骨架/存根协议，以及存根和骨架编译器的需求

这些缺陷会带来一些实际影响，但并没有你想象的那么多。首先，也是限制最大的，是 RMI OP 只能使用原始的基于 TCP/IP 套接字的 RPC 机制进行通信：不支持通过防火墙进行隧道传输。

这对你部署应用程序的网络拓扑结构有直接影响，因为你必须配置保护服务器的防火墙以允许 Java RMI 流量。如果你希望设备托管在激活之间不持久化的对象，那么缺少 `Activatable` 的具体实现可能会成为一个问题，但你可以自由地提供该接口的自定义实现。而不需要构建存根和骨架实际上是一件好事；你只需要为你的 Java SE 应用程序做这项工作。

**应用 Java RMI**

当使用 Java 编写分布式应用程序时，你必须结合 `java.rmi` 包执行以下步骤：

**1.** 为远程服务编写 Java 接口。

**2.** 实现远程服务，从 `UnicastRemoteObject` 或 `Activatable` 派生你的实现。

**3.** 如果你将解决方案的一部分部署在 Java SE 或 Java EE 上，则从实现生成存根类。

**4.** 编写一个远程服务主机容器，用于创建和管理你的远程服务。

**5.** 编写你的 Java 客户端，在客户端上调用远程对象。

**6.** 安装并部署客户端和服务器。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**281**

虽然这听起来工作量很大，但它使你免于处理开发远程应用程序的许多底层细节。步骤 1 和 2 是编写 Java 代码——这是你已经知道如何做的事情。Java SE 附带了 `rmic` 编译器来执行步骤 3。

步骤 4 只是编写一个小的 Java 应用程序，而步骤 5 是你无论如何都必须做的——你的客户端应用程序才是关键。步骤 6 可能是一个小任务，也可能是一个大任务，具体取决于你的应用程序和服务的性质。

让我们使用本书中一直使用的天气示例，逐一完成这些步骤。我们为 CDC 编写的 `WeatherApplet` 示例需要一个天气数据源，该数据源可能来自商业或政府服务。在接下来的章节中，我将引导你构建一个可远程的 `Location` 类，该类适合使用 RMI OP 来传输位置和天气数据。

为了实现可远程的 `Location` 对象，你需要定义各种接口和实现之间的关系。图 11-3 显示了 `Location` 接口、RMI OP 层次结构以及 `Location` 的实现 `LocationImpl` 之间的关系。

**图 11-3.** *可远程的 Location 接口及其实现*
`Location` 接口——我最初在第 6 章中将其作为一个表示空间位置和特定位置天气预报的类引入——通过四个方法 `getLocation`、`setLocation`、`getForecast` 和 `setForecast` 将两个 `String` 属性作为属性导出。`Location` 接口是可远程的，这通过它扩展了 `java.rmi.Remote` 接口来表明。它的具体实现 `LocationImpl` 基于 `java.rmi.UnicastRemoteObject` 的 RMI OP 实现，可以在分布式计算环境中共享。

■**注意** 由于 NetBeans 不直接支持 RMI 应用程序的开发，接下来的示例要求你已安装 JDK，该 JDK 应该在你安装 NetBeans 时已经安装。你可能需要确保已安装的 JDK 的 `bin` 目录在你的路径中，以便访问诸如 `javac` 和 `rmic` 之类的命令。

**为服务编写 Java 接口**

正如我之前提到的，RMI 的关键在于定义远程服务的接口。清单 11-1 显示了 `Location` 接口。

**清单 11-1.** *Location 接口*

```java
public interface Location
    extends java.rmi.Remote {
    public String getLocation()
        throws java.rmi.RemoteException;
    public void setLocation(String l)
        throws java.rmi.RemoteException;
    public String getForecast()
        throws java.rmi.RemoteException;
    public void setForecast(String f)
        throws java.rmi.RemoteException;
}
```

这个接口很直接，尽管你可以看到每个方法都可能抛出 `java.rmi.RemoteException`。这是因为接口的实现可能是代理，远离实际的实现。在这种情况下，接口的实现必须有一种方式来指示异常情况，例如网络连接丢失。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**283**

**使用 Java SE 实现服务**

服务本身是 `LocationImpl` 类，它在 `UnicastRemoteObject` 的帮助下提供了 `Location` 接口的具体实现，如清单 11-2 所示。

**清单 11-2.** *LocationImpl 类*

```java
public class LocationImpl
    extends
    java.rmi.server.UnicastRemoteObject
    implements
    Location {
    private String location;
    private String forecast;
    public LocationImpl()
        throws java.rmi.RemoteException {
        super();
    }
    public String getLocation()
        throws java.rmi.RemoteException {
        if (location != null) {
            return location;
        } else {
            return "";
        }
    }
    public void setLocation(String l)
        throws java.rmi.RemoteException {
        location = l;
    }
    public String getForecast()
        throws java.rmi.RemoteException {
        if (forecast != null) {
            return forecast;
        } else {
            return "";
        }
    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**284**

第 11 章 ■ 使用远程方法调用

```java
    public void setForecast(String f)
        throws java.rmi.RemoteException {
        forecast = f;
    }
}
```

请注意此实现的默认构造函数，它必须立即调用其父类的构造函数。这是因为该类必须调用 `UnicastRemoteObject` 的构造函数来执行与 RMI 子系统的链接和远程对象初始化。

`LocationImpl` 扩展了 `UnicastRemoteObject`，它提供了与 RMI 子系统的必要链接。这是将对象链接到 RMI 子系统并准备将其导出到远程系统的一种方法；另一种方法是调用 `UnicastRemoteObject.exportObject` 方法，并传入一个实现 `Remote` 的对象。无论哪种情况，`UnicastRemoteObject` 实现都会在一个端口上导出该对象，使其可用于接收传入的方法调用。

■**注意** 一个完整的 `Location` 实现会使用另一个远程计算接口（例如 Web 服务）来获取远程位置的天气预报。为简洁起见，我在此省略了这一点。

虽然你通常不需要直接调用它，但 `UnicastRemoteObject` 有一个对应的 `unexportObject` 方法，可以强制将对象从 RMI 运行时中移除。调用此方法后，如果成功，你传入的对象将无法再接受传入的 RMI 调用。

**为 Java SE 生成存根类**

如果你愿意，可以生成



生成 Java SE 存根类以包含在 Java SE 应用程序中；如果你希望将应用程序同时部署到 Java ME 和 Java SE，或者你的分布式架构要求从 Java ME 设备向运行 Java SE 的硬件提供对象，这会非常方便。有趣的是，你并非对 Java 源代码执行此步骤，而是对从实现生成的类文件执行此操作。

对于像这样的小项目，只需编译 `LocationImpl`，然后在生成的类文件上运行 RMI 编译器 `rmic` 即可。为此，打开一个 shell 并输入清单 11-3 中所示的命令。

**清单 11-3.** *手动构建 Java SE 存根类*
c:\book\Chapters\chapter11\code>javac Location.java

c:\book\Chapters\chapter11\code>javac LocationImpl.java
c:\book\Chapters\chapter11\code>rmic LocationImpl

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**285**

这些命令使用 `javac` 为 `Location.java` 和 `LocationImpl.java` 生成类文件；最后一行使用 `rmic` 生成骨架 `LocationImpl_Stub.class`。

■**注意** 虽然如果你使用 Mac OS X 或 Linux，命令行的外观会有所不同，但命令语法保持不变。

**编写远程服务主机应用程序**

远程服务需要做两件事。首先，它必须创建其提供的对象实现的实例。其次，它必须将这些对象注册到 RMI 命名服务，指定客户端可以在何处访问远程对象。清单 11-4 展示了如何执行此操作。

**清单 11-4.** *一个简单的 RMI 服务器应用程序*
import java.rmi.Naming;

public class LocationServer {

    public LocationServer() {
        try {
            Location c = new LocationImpl();
            Naming.rebind("rmi://localhost:1099/LocationService", c);
        } catch (Exception e) {
            System.out.println("Exception: " + e);
        }
    }

    public static void main(String args[]) {
        new LocationServer();
    }
}

这非常简单，并展示了使用 RMI 构建远程应用程序的强大功能。服务器创建了一个 `LocationImpl` 的新实例，并将其注册到命名守护进程——仅此而已！

要在独立工作站上测试运行该服务，你必须首先启动 RMI 注册表，这是一个 Java 应用程序，`LocationServer` 将其对象注册到其中以供客户端查找。你可以在一个 shell 中启动 `rmiregistry`，在另一个 shell 中启动 `LocationServer`（使用 `java` 启动 Java 虚拟机）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**286**

第 11 章 ■ 使用远程方法调用

■**提示** 如果你无法运行该服务，或者客户端和服务器无法通信，请检查你是否正在运行防火墙或其他会阻止 `rmiregistry` 监听默认端口 (1099) 的软件。

另一种可能性是在不同的端口（例如 8081）上运行 `rmiregistry`，并更改应用程序中的端口号。

当然，你的生产级服务器不会在你的笔记本电脑上运行 `rmiregistry`；相反，你会将生成的实现托管在诸如 Sun 的 GlassFish 或 BEA 的 WebLogic Server 之类的应用服务器上。如何打包和安装远程服务会根据你选择的应用服务器而略有不同。

**从客户端调用远程对象**

从客户端调用远程对象非常简单：只需查找远程服务并获取远程对象的实例，如清单 11-5 所示。

**清单 11-5.** *调用远程对象*

import java.rmi.*;
…
Location c = (Location)Naming.lookup(
    "rmi://localhost/LocationService");
…

调用远程对象时，必须确保指定远程主机（本例中为 `localhost`）以及端口和提供该对象的远程服务的名称。`java.rmi.Naming` 类负责其余工作，联系远程服务并获取远程对象的实例。

**总结**

一些运行 CDC 的设备支持 RMI OP，这是一个可选扩展，将 Java RMI 的大部分功能带到了受限设备。与 Java SE 实现一样，RMI OP 提供了远程对象发现、远程通信和远程行为定义的功能，以及 RMI OP 的 Java 实现提供的安全通信通道。Java RMI 大量利用接口来解耦对象的描述行为与其实现。远程客户端使用抽象接口来描述远程对象将如何行为，并结合由 Java 运行时生成的底层对象，该对象与提供对象实际实现的远程服务器进行通信。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 11 章 ■ 使用远程方法调用

**287**

由 JSR 66 定义的 RMI OP 包提供了 Java RMI 的一个全面子集，包括远程调用语义、远程对象表示、从 CDC 设备导出对象以及分布式垃圾回收。RMI OP 基于原始的 J2SE RMI 实现及其有线协议；它*不是* RMI-IIOP 的实现，后者将 CORBA 引入 Java。在编写使用 RMI OP 的应用程序时，你必须为远程服务定义接口，使用你定义的接口实现远程服务，并编写管理这些接口的远程服务主机容器。如果你的应用程序部分运行在支持 Java SE 或 Java EE 的硬件上，你还必须生成 RMI 用于支持跨网络多虚拟机对象分发的存根类。

在 Java ME 中使用 Java RMI 并非适合所有人。在当今使用基于 HTTP 的 XML 的分布式 Web 服务世界中，它最适合需要完全面向对象方法进行分布式计算的环境，或者那些需要你将 Java ME 设备连接到已向其他 Java 客户端提供 RMI 服务的现有网络服务的遗留环境。

[www.it-ebooks.info](http://www.it-ebooks.info/)

间奏曲

**本书的下一部分对你作为移动应用程序开发者至关重要。如今的 Java ME 平台比以往任何时候都更加互联，它必须支持使用无处不在的 HTTP 访问当今的互联网，并且为更低级的互联网协议以及蓝牙和短信等无线协议提供了可选接口。**

**几乎每个有价值的应用程序都需要至少一些数据通信能力。**

**请仔细阅读第 12 章，因为它对 GCF 的解释是 Java ME 内部通信的基础。如果你正在开发使用 Web 服务的应用程序，请阅读第 13 章，因为它教你如何使用可扩展标记语言 (XML)，即互联网的通用语言。无线消息传递（第 14 章的主题）是可选的，但即使你不打算使用无线消息传递接口，我建议你至少浏览一下本章，以便了解可供你使用的无线通信选项。**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 4 部分

与世界其他地区通信

**自 Java 诞生以来，对网络通信的支持一直是其关键组成部分，Java ME 延续了这一趋势，并为整个 Java 开发者社区带来了重要贡献。本部分的三个章节探讨了创建使用互联网的 Java ME 应用程序的关键方面。在第 12 章中，你将学习如何使用 GCF，这是 Java ME 提供的最重要的框架和 API 之一。**



**它贯穿了 Java ME 关于应用程序如何通过网络等物理接口进行通信的理念。在第 13 章中，你将了解在处理 XML 时有哪些选择——这是当今许多通信应用程序的重要方面。最后，在第 14 章中，你将看到如何访问许多 Java ME 设备上提供的广域无线网络功能。**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章

访问网络上的远程数据

**S**un Microsystems 早期的成功在于为科学和工程目的制造高性能计算平台。大多数工作站（包括 Sun 的硬件）的一个关键特性是对网络的支持。体现在 Sun 的座右铭“网络就是计算机”中，这种对网络的承诺一直延续到今天，通过 Java 平台提供并构建的稳健网络解决方案得以体现。

Java ME 也不例外；它从一开始就被设计为一个与网络上其他计算机交互的平台。

在本章中，我将讨论 Java ME 对 Java 平台最重要的贡献之一：通用连接框架（GCF）。该框架提供了一个统一的 API，用于使用不同的协议与网络上的系统进行交互，并且已被扩展以涵盖文件操作（见第 7 章）、使用 SMS 的无线通信（见第 14 章）以及非接触式通信（见第 15 章）。首先，我将解释什么是 GCF 以及它作为应用程序开发人员能为你提供什么。接下来，我将向你展示如何使用 GCF 在当今的 IP 网络上通过 TCP 和 UDP 执行低级通信。然而，本章的主要内容是向你展示如何使用 GCF 通过 HTTP 访问互联网上的远程资源，这是几乎所有 Java ME 网络化应用程序的重要方面。最后，我将简要说明 Java ME 的权限模型如何影响你的应用程序。

**介绍通用连接框架**

Java SE 世界中的 I/O 和通信是一场狂野之旅：你会发现位于 java.net 和 java.io 包中的套接字、文件和连接类（派生自 URLConnection）。虽然可以说功能强大，但 Java SE 的 I/O 框架的缺点在于，它要求你掌握数量相对较多的接口，其中许多具有不同的语义。事实证明，这对于移动设备来说同样笨重；这些深奥而复杂的类层次结构无法很好地扩展到移动设备，尤其是那些运行早期版本 Java ME 的设备。因此，随着第一个 CLDC 的引入，Sun 引入了 GCF，这是一个用于管理连接和执行 I/O 的直观的接口和类层次结构。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**294**

第 12 章 ■ 访问网络上的远程数据

GCF 已被证明非常受欢迎，以至于在其最初的应用之外也被广泛采用；平台架构师现在使用 GCF 抽象出对文件、其他网络协议、智能卡、射频识别（RFID）卡、蓝牙外设甚至条形码的通信。事实上，目前正在进行将 GCF 重新整合到 Java SE 中的工作，如 JSR 197 所述，这展示了固定和移动计算范式的融合如何从两种环境中汲取灵感。

包含在 javax.microedition.io 包中的 GCF 提供了一种抽象的连接方法。通过提供一个可扩展的通用类接口层次结构（见图 12-1）和一个基于 URL 的通用请求方案来创建这些类实例的工厂，GCF 显著减少了支持通信应用程序所需的类数量。这既减小了 Java ME 平台的占用空间，也降低了你在构建应用程序时作为 Java ME 开发人员必须管理的复杂性。

**图 12-1.** *最初在 CLDC 1.0 中引入的 GCF 层次结构*



[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**295**

GCF 层次结构是一个紧密编织的接口集合，其中夹杂着少量类：

• *Connector* 类：负责充当 Connection 对象的工厂。

• *Connection* 接口：表示两个实体之间的通用连接。

Connection 接口定义了 close 方法，该方法负责关闭连接。

• *DatagramConnection* 接口：定义了在面向数据报的连接（如 UDP）上可执行的操作。

• *StreamConnection* 接口：定义了在面向流的连接（如 TCP）上可执行的操作。StreamConnection 实例是双向的，由 InputConnection（从中读取数据）和 OutputConnection（向其写入数据）组成。

• *StreamConnectionNotifier* 接口：定义了在监听新的 StreamConnection 时可执行的操作。

• *ContentConnection* 接口：支持传递通过已知编解码器编码的内容。

• *Datagram* 接口：为单个数据报定义了抽象接口。

数据报是双向的，通过扩展 DataInput 和 DataOutput 接口来实现此功能。

• *IOException* 类：表示通用通信错误，例如网络故障，以及 ConnectionNotFoundException，当您尝试创建平台无法解析的连接时，Connector 类方法会抛出此异常。（此 IOException 类并非正式属于 GCF 的一部分。）使用 GCF 时，请遵循以下步骤：

**1.** 创建一个 URL，描述您想要打开的套接字类型，并详细说明要连接的资源的地址和名称。

**2.** 调用 Connector.open 类方法，传入您构建的 URL，以接收特定 Connection 子类的实例。

**3.** 根据 Connector.open 返回的连接类型，使用具体的 Connection 子类方法来处理该连接。

清单 12-1 中的伪代码演示了此模式，即打开到服务器 www.nowhere.com 的 HTTP 连接，并获取一个用于读取的 InputStream。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**296**

第 12 章 ■ 访问网络上的远程数据

**清单 12-1.** *打开 HTTP 连接并获取 InputStream* String url = "http://www.nowhere.com";

…

StreamConnection c = null;

InputStream s = null;

try {

c = (StreamConnection)Connector.open(url);

s = c.openInputStream();

…

} catch (ConnectionNotFoundException e) {...}

catch (IllegalArgumentException e) {...}

catch (IOException ioe) {...}

finally {

try {

if (s != null) s.close();

if (c != null) c.close();

} catch (Exception e) {...}

}

这段伪代码对于大多数使用连接的应用程序来说相当典型。您首先构建一个 URL，描述应在何处建立连接，然后使用 Connection.open 生成一个具体的 Connection 类，该类实现与您在 URL 中指定的地址的连接。连接打开后，您从连接中获取流（或数据报），并使用提供的流或数据执行 I/O 操作。完成后，您需要关闭流（不是数据报连接）和连接本身。

使用连接进行编程充满了异常，因为您受制于外部环境。网络可能不可用，手机可能没有服务，或者设备上可能没有足够的资源来满足您的网络请求。因此，open 方法可能会抛出多种异常：

• ConnectionNotFoundException：如果平台不支持请求的协议，或者平台找不到 URL 指定的目标

• IllegalArgumentException：如果您指定了无效参数

• IOException：如果在打开连接时发生任何类型的 I/O 错误



• SecurityException：如果您的应用程序未被允许访问您请求的协议

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**297**

虽然清单 12-1 中的示例仅传递了要打开的 URL，但您也可以指定访问模式（`Connector.READ`、`Connector.WRITE`或`Connector.READ_WRITE`之一），以指示所生成连接的访问模式。如果提供了访问模式，您还可以指示您的应用程序在使用连接时，如果特定协议支持超时机制，则希望接收超时通知。

您可能对 URL 的概念很熟悉，但由于 URL 在连接的创建中起着关键作用，因此有必要回顾一下其语义。使用 GCF 时，URL 既描述了连接的类型，也描述了连接实例应连接到的位置。URL 的语法在 RFC 1738 和 RFC 2396 中有详细描述；URL 是一个如下所示的单一字符串：

`scheme://user:password@host:port/path;parameters`

其中：

• `scheme` 指定了连接所使用的协议，例如 HTTP。

• `user` 指定了访问连接时所需的可选用户名。

• `password` 指定了访问连接时所需的可选密码。

• `host` 是远程连接端的完全限定域名或其他地址。

• `port` 指定了远程连接端使用的可选端口。

• `path` 是远程连接端的路径，其解释和格式可能因协议而异。路径可能包含一个或多个用于修改连接的参数。

在底层，`Connector`的实现会使用您在 URL 中指定的协议来确定应实例化并返回哪种类型的`Connection`。Java ME 以及用于扩展 Java ME 通信能力的各种 RFC 已经定义了几种不同的协议。表 12-1 列出了常见的协议、对应的协议类型、返回的`Connection`子类，以及该协议被引入 Java ME 平台时所依据的 JSR 规范。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**298**

第 12 章 ■ 访问网络上的远程数据

**表 12-1.** *连接协议*

**URL 协议**

**协议类型**

**GCF 连接类型**

**定义规范**

**必需或可选**

btl2cap

蓝牙

L2CAPConnection

JSR 82

可选

datagram

数据报

DatagramConnection

CLDC、CDC 以及

可选

JSR 197（针对 J2SE）

file

文件系统访问

FileConnection、

JSR 75†

可选

InputConnection

http

HTTP

HttpConnection

MIDP 1.0、*

必需

MIDP 2.0、**

Foundation Profile、***

以及 JSR 197（针对 Java SE）

https

安全 HTTP

HttpsConnection

MIDP 2.0**

必需

comm

串行 I/O

CommConnection

MIDP 2.0**

可选

sms

短信

MessageConnection

JSR 120、JSR 205

可选

mms

彩信

MessageConnection

JSR 120、JSR 205

可选

cbs

小区广播

MessageConnection

JSR 120、JSR 205

可选

服务 (CBS)

apdu

应用协议

APDUConnection

JSR 177††

可选

数据单元 (APDU)

jcrmi

Java Card 远程

JavaCardRMIConnection

JSR 177††

可选

方法调用

socket

套接字

SocketConnection

MIDP 2.0**

可选

serversocket

套接字

ServerSocketConnection

MIDP 2.0**

可选

datagram

UDP

UDPDatagramConnection

MIDP 2.0**

可选

**JSR 37 定义了 MIDP 1.0。*

***JSR 118 定义了 MIDP 2.0。*

****JSR 46 定义了 Foundation Profile 1.0；JSR 219 定义了 Foundation Profile 1.1。*

*†我将在第 7 章讨论这一点。*

*††我将在第 15 章讨论这一点。*

从表 12-1 可以看出，并非所有 Java ME 平台都支持最常见的连接类型。在设计应用程序时，您必须牢记这一点。例如，开发一个需要使用 UDP 数据报的应用程序，并希望其在可能运行 CLDC MIDP 1.0 的旧款手机上广泛部署，这几乎毫无意义，因为这些手机很可能不支持`UDPDatagramConnection`，甚至一些 CLDC MIDP 2.0 手机也缺乏对该类的支持。一般来说，可以安全地假设任何 CLDC 或 CDC 设备都支持 HTTP。



[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**299**

在 MIDP 2.0 实现中通常支持通过 HTTPS 的安全连接，但某些设备可能缺乏 HTTPS 支持。原始（流或数据报）套接字支持较为少见，而串行支持甚至比流或数据报支持更不常见。其他方案，例如支持 SMS（我将在第 14 章中描述）、蓝牙和 FCOP（见第 7 章）的方案，其可用性因您所针对的运营商和手机类别而异。在定义应用程序功能时，值得您花些精力进行市场调研，以确定有多少设备实现了您所需的可选包。表 12-2 按配置文件总结了特定连接实现的可用性。

**表 12-2.** *连接实现*

**连接**

**必需？**

**CLDC 1.0，**

**MIDP 1.0**

**MIDP 2.0**

**Foundation 和**

**1.1**

**相关配置文件**

CommConnection

否

✓

Connection

是

✓

✓

✓

✓

ContentConnection

是

✓

✓

✓

✓

DatagramConnection

否

✓

✓

✓

✓

HttpConnection

是

✓

✓

✓

HttpsConnection

是

✓

InputConnection

是

✓

✓

✓

✓

OutputConnection

是

✓

✓

✓

✓

SecureConnection

否

✓

ServerSocketConnection

否

✓

SocketConnection

否

✓

StreamConnection

是

✓

✓

✓

✓

StreamConnectionNotifier

是

✓

✓

✓

✓

UDPDatagramConnection

否

✓

GCF 并非完美：批评者可以合理地指出，Connection 类在将常见通信操作抽象为抽象方法方面做得还不够，以至于许多概念上相似的操作（例如向远程主机发送数据报或向智能卡发送协议数据单元）在 Connection 类的不同子类中具有截然不同的接口语义。这很不幸，因为它使得学习通过可选包提供的各种通信选项变得更加困难，并且使得在不使用适配器类的情况下，采用面向组件的方法来开发通信应用程序变得不可能。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**300**

第 12 章 ■ 访问网络上的远程数据

**使用套接字和数据报进行通信**

尽管绝大多数联网移动应用程序可能使用 HTTP 来满足其通信需求，但某些应用程序可能仍然需要原始套接字或数据报功能。这包括系统管理员实用程序、即时消息客户端，以及将这些协议应用于游戏或多媒体应用程序的新颖应用。

不幸的是，对这些功能（通常通过 TCP 实现套接字，通过 UDP 实现数据报）的支持并非在所有设备上都可用。如表 12-2 所示，MIDP 2.0 可选地提供对套接字通信的支持，而所有 Java ME 平台都可能提供对数据报通信的支持。因此，在为您应用程序建立商业案例时，您应考虑这些协议的可用性，并进行一些研究以精确确定哪些设备将支持您的应用程序，以及目标市场中是否有足够多的设备来证明您的开发投入是合理的。如果没有，您可能需要考虑通过其他协议（如 HTTP）来隧道传输您的数据通信。

■**提示** 诸如 http://developers.sun.com/mobility/device/device 上的 Java ME 设备表和 http://wurfl.sourceforge.net 上的 WURFL 设备描述存储库等资源，可以帮助您确定哪些设备支持套接字或数据报通信。

**使用 GCF 进行套接字编程**

使用 GCF 进行套接字编程很简单：构造一个指向套接字目标的 URL，然后使用 Connector 打开它。假设一切顺利——通往服务器的路径可用且服务器正在运行——那么您就可以获取流，以在设备建立的连接上执行输入和输出。

打开套接字连接时，`Connector.open` 返回一个 `SocketConnection` 实例，该实例实现了 `StreamConnection`。对于*大多数*面向套接字的编程，使用 `StreamConnection` 就足够了，如清单 12-2 所示。


**列表 12-2.** *使用 StreamConnection*

String url = "socket://nowhere.com:7";

…

StreamConnection c = null;

InputStream s = null;

OutputStream o = null;

byte[12] b;

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**301**

try {

c = (StreamConnection)Connector.open(url);

s = c.openInputStream();

o = c.openOutputStream();

o.write("Hello world!".getBytes());

s.read( b, 0, b.length );

} catch (ConnectionNotFoundException e) {...}

catch (IllegalArgumentException e) {...}

catch (IOException ioe) {...}

finally {

try {

if (o != null) o.close();

if (s != null) s.close();

if (c != null) c.close();

} catch (Exception e) {...}

}

这段代码打开了一个指向 7 号端口（即回显端口）的 TCP 套接字，并写入消息“Hello world!”。写入消息后，它读取响应，然后关闭流和连接。从代码中可以看出，你使用输入和输出流类 `InputStream` 和 `OutputStream` 来执行实际的 I/O 操作；它们允许你通过 `read` 和 `write` 方法交换单个字节数组。为了实现更高级别的访问，你还可以使用 `StreamConnection` 的 `openDataInputStream` 和 `openDataOutputStream` 方法从连接中获取 `DataInputStream` 和 `DataOutputStream` 实例；正如你在第 2 章中回忆的那样，它们允许你读写包括浮点数、整数和字符串在内的原始数据类型。

`Connector.open` 返回的 `SocketConnection` 实例比 `StreamConnection` 拥有更多的方法，它允许你通过调用以下方法对套接字执行一些底层操作：

• `getAddress`：允许你通过调用套接字获取套接字绑定的远程地址
• `getLocalAddress`：允许你获取套接字绑定的本地地址
• `getPort`：允许你获取套接字绑定的远程端口
• `getLocalPort`：允许你获取套接字绑定的本地端口
• `getSocketOption`：允许你获取一个套接字选项
• `setSocketOption`：允许你设置一个套接字选项

[www.it-ebooks.info](http://www.it-ebooks.info/)

**302**

第 12 章 ■ 访问网络上的远程数据

这些套接字选项是你在 Linux 或 UNIX 上使用 TCP 套接字时可能遇到过的套接字选项的子集；表 12-3 总结了你能够获取或修改的选项。你将选项标识符传递给 `getSocketOption`，它会返回相应的选项值；你将选项标识符和一个新值传递给 `setSocketOption`，它会尝试设置你指定的选项。

**表 12-3.** *套接字选项*

**选项标识符**

**含义**

SocketConnection.DELAY

写入小缓冲区时的延迟时间

SocketConnection.KEEPALIVE

为保持连接活跃而应保持套接字打开的时间

SocketConnection.LINGER

当套接字另一端无响应时，套接字应保持打开状态以等待剩余输出数据的时间

SocketConnection.RCVBUF

接收缓冲区的大小（以字节为单位）

SocketConnection.SNDBUF

发送缓冲区的大小（以字节为单位）

GCF 还通过 `StreamConnectionNotifier` 和 `ServerSocketConnection` 类支持接受传入的套接字请求，尽管在实践中 Java ME 应用程序很少使用此功能。至少有三个原因说明你可能不希望 Java ME 应用程序在套接字上监听传入连接。首先，监听传入连接需要硬件为网络子系统供电。对于许多便携式设备，尤其是那些带有无线电台的设备，这会极大地缩短电池寿命。其次，大多数 Java ME 设备运行所在的网络在为 Java ME 设备分配地址时通常使用动态地址分配；即使运行网络硬件没有能量损耗，如果不事先使用某种注册机制，你也不知道哪个地址对应哪个特定设备。最后，只有少数设备提供此支持。



如果你确信这些原因不适用于你，只需指定要监听的端口即可获取 `ServerSocketConnection`，如清单 12-3 所示。

**清单 12-3.** *获取 ServerSocketConnection* String url = "socket://7";

ServerSocketConnection ssc = null;

try {

ssc = (ServerSocketConnection)Connector.open(url);

while (true) {

SocketConnection sc = (SocketConnection)ssc.acceptAndOpen(); InputStream s = sc.openInputStream();

OutputStream o = sc.openOutputStream();

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**303**

try {

...

}

catch(Exception e) {...}

finally {

if (s!=null) s.close();

if (o!=null) o.close();

if (sc!=null) sc.close();

}

}

} catch (Exception e) {...}

finally {

try {

if (ssc != null) ssc.close();

} catch (Exception e) {...}

}

这段代码与使用 UNIX 套接字编写的传统 select/accept 服务器循环非常相似。创建 `ServerSocketConnection` 后，代码会阻塞在 `ServerSocketConnection` 实例的 `acceptAndOpen` 方法上，该方法在客户端连接后返回一个 `SocketConnection` 实例。接着，它使用套接字打开输入和输出流以执行必要的 I/O 服务；完成后，进行清理并返回等待下一个请求。

当然，accept/respond 循环仅在应用程序的生命周期内运行；真正的服务器可能需要在应用程序未运行时也能监听。在 MIDP 平台上，你可以通过推送注册表（push registry）来实现这一点，即在安装时或运行时为你的应用程序注册传入请求。要在安装时注册，只需在应用程序描述符中，将描述要监听的套接字的 GCF URL 和你的 MIDlet 名称作为 `MIDlet-Push` 字段的值包含进来，如下所示：

MIDlet-Push-1: socket://:7, ServerMIDlet, *

这指示应用程序管理系统在端口 7 上出现传入的 TCP 请求时调用你的应用程序。然后，你的应用程序可以使用 `PushRegistry` 的 `listConnections` 方法（我在第 14 章中介绍）来获取有数据等待的连接列表。你也可以通过调用其 `registerConnection` 方法，以编程方式将你的应用程序添加到推送注册表中，传递的信息与你在应用程序描述符的 `MIDlet-Push` 字段中提供的信息完全相同。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**304**

第 12 章 ■ 访问网络上的远程数据

**使用 GCF 进行数据报通信**

数据报通信——通常适用于不需要排序和流量控制的短小单条消息——使用 `Datagram` 类来封装数据报的概念。与套接字类似，你使用 `Connector` 类打开连接；在使用数据报通信时，你会获得一个诸如 `DatagramConnection` 或 `UDPDatagramConnection` 的类，并用它创建 `Datagram` 实例。一旦拥有 `Datagram` 实例，你就可以用它来发送和接收单个数据报。清单 12-4 将“Hello world!”写入一个数据报回显服务器。

**清单 12-4.** *将“Hello world!”写入数据报回显服务器* String url = "datagram://noplace.com:7";

DatagramConnection dgc = null;

Datagram d;

String s = "Hello world!";

byte m[] = s.getBytes();

byte[] b = new Byte[12];

try {

dgc = (DatagramConnection)Connector.open(url);

try {

d = dgc.newDatagram(m, m.length);

dgc.send(d);

d.reset();

dgc.receive(d);

d.readFully(b, 0, b.length);

} finally {

dgc.close();

}

} catch (Exception e) {...}

finally {

try {

if (dgc != null) dgc.close();

} catch (Exception e) {}

}

这里的 GCF 模式很清晰，不过输入输出不再使用流，而是使用 `Datagram` 类。`DatagramConnection` 类充当 `Datagram` 对象的工厂，然后你将 `Datagram` 对象分别传递给 `send` 和 `receive` 来发送和接收单个数据报。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**305**

`Datagram` 类是一个抽象类，它封装了所有数据报协议共有的以下概念：

- 数据报有一个*缓冲区*，用于在单个数据报包中存储数据。
- 数据报有一个*偏移量*，表示在缓冲区中读取或写入数据的当前位置。
- 数据报的缓冲区有一个*长度*，表示数据报缓冲区的大小。
- 数据报具有源地址或目标地址的表示形式。

如你在图 12-1 中所见，`Datagram` 实现了 `DataInput` 和 `DataOutput` 接口，允许你读写各种原始数据类型，包括布尔型、字节型、字符型、双精度浮点型、浮点型、整型、长整型和 UTF 字符串。`DataInput` 和 `DataOutput` 的接口让人联想到 `DataInputStream` 和 `DataOutputStream` 提供的接口；这些类为各种支持的类型 *X* 提供了 `read *X*` 和 `write *X*` 方法，包括以下内容：

- `readBoolean`，用于读取一个输入字节，如果该字节非零则返回 true；`writeBoolean`，用于将一个布尔值作为单个字节写入
- `readByte` 和 `writeByte`，分别用于读取和写入单个字节
- `readChar` 和 `writeChar`，分别用于读取和写入单个字符
- `readFloat` 和 `writeFloat`，分别用于读取和写入浮点数
- `readShort` 和 `writeShort`，分别用于读取和写入短整数
- `readInt` 和 `writeInt`，分别用于读取和写入整数
- `readLong` 和 `writeLong`，分别用于读取和写入长整数
- `readUnsignedByte` 和 `writeUnsignedByte`，分别用于读取和写入单个无符号字节
- `readUTF` 和 `writeUTF`，分别用于读取和写入 UTF-8 编码的字符串
- `readFully` 和 `write`，分别用于读取和写入字节数组

[www.it-ebooks.info](http://www.it-ebooks.info/)

**306**

第 12 章 ■ 访问网络上的远程数据

每个 `Datagram` 实例代表一条消息，要么等待写入连接，要么先前已从连接中读取；你可以通过调用其 `reset` 方法来重用 `Datagram` 实例。你还可以使用以下方法查询 `Datagram` 的各种属性：

- `getAddress`：返回 `Datagram` 的地址
- `getData`：返回 `Datagram` 缓冲区的内容
- `getOffset`：返回 `Datagram` 缓冲区的偏移量
- `setData`：允许你根据给定的字节数组设置 `Datagram` 的缓冲区、偏移量和长度
- `setLength`：允许你设置 `Datagram` 缓冲区的长度

**使用 HTTP 进行通信**

在提供电子邮件的协议之后，HTTP 可能是当今最广泛部署和理解的用于数据交换的协议。本质上，它是一个简单的客户端/服务器协议，其简单性和通用性使其成为当今互联网上最重要的协议之一。除了作为在 Web 上传递内容的协议——由蒂姆·伯纳斯-李创建，其起源是在欧洲核子研究组织（CERN）为超文本文档提供传输——它现在支撑着许多其他客户端/服务器交换，例如支持 Web 服务的交换。

（我将在下一章中更多地讨论计算领域的 Web 服务模型。）

**回顾 HTTP**

HTTP 是一个客户端/服务器协议



一种客户端应用程序（即*用户代理*）向服务器发出*请求*，以对特定 URL 上的操作或内容进行访问的协议。该请求包含提供请求元数据的*标头*，以及一个*对象主体*，它是与请求相关的可选数据块。服务器以相同方式回复请求，返回提供请求元数据的标头，后跟可选的响应对象主体。HTTP 可以在任何可靠的流协议上运行，但在实践中，它通常基于 TCP 运行。该协议本身使用八位表示字符，但请求和响应标头以纯文本形式编写，这使得开发人员易于理解、实现和排查问题。例如，客户端 Web 浏览器可能会发送清单 12-5 中的消息，以获取位于 URL http://www.noplace.com 的主页。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**307**

**清单 12-5.** *获取一个网页*

GET /index.html HTTP/1.1

Host: www.noplace.com

请求的第一行就是请求本身；请求包含一个方法、一个资源和协议版本。GET 请求要求远程服务器返回指定的资源——在本例中，是文档 index.html——使用 HTTP 1.1 版本。

请求的后续行包含关于请求的元数据，以冒号分隔、换行符分隔的键值对形式表示，例如服务器目标主机（在示例中为 www.noplace.com）。如果请求包含对象主体，它将在空行之后跟随标头，如清单 12-6 所示。

**清单 12-6.** *向 Web 服务器发布信息* POST /do HTTP/1.1

Host: www.noplace.com

name=value

此处，远程服务器被要求通过调用脚本 do 并将对象主体传递给该脚本来处理对象主体 name=value。表 12-4 列出了已定义的 HTTP 方法（也称为*动词*）及其用途。

**表 12-4.** *HTTP 方法及其含义*

**方法**

**含义**

HEAD

请求与 GET 创建的响应相同的响应，但不包含对象主体

GET

请求指定资源的表示形式

POST

提交数据以供指定资源处理

PUT

上传指定资源的表示形式

DELETE

删除指定资源

TRACE

回显接收到的请求

OPTIONS

返回服务器为该资源支持的原始 HTTP 方法 CONNECT

将请求连接转换为透明的 TCP 隧道 回到清单 12-5，服务器可能会做出如清单 12-7 所示的响应。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**308**

第 12 章 ■ 访问网络上的远程数据

**清单 12-7.** *一个典型的服务器响应*

HTTP/1.1 200 OK

Date: Mon, 23 Jun 2008 22:13:40 GMT

Server: Apache/2.2.8 (Unix) mod_ssl/2.2.8 OpenSSL/0.9.7l DAV/2 PHP/5.2.5

Content-Location: index.html

Vary: negotiate,accept-language,accept-charset

TCN: choice

Last-Modified: Mon, 24 Sept 2007 01:12:03 GMT

ETag: "ec11-5b0-43ad74ee73ec0;44bbb82e73280"

Accept-Ranges: bytes

Content-Length: 49

Connection: close

Content-Type: text/html

<html>

<body>

Hello world!

</body>

</html>

HTTP 服务器响应的第一行始终是响应状态，包含服务器版本、数字状态指示和人类可读的状态字符串。状态码始终是三位数字；第一位数字表示响应类别，第二位和第三位表示详细信息。常见的类别有 200（表示成功）和 400（表示客户端错误导致服务器无法完成请求）。表 12-5 列出了常见的（但并非全部）HTTP 状态码。

在响应状态之后是一系列标头；每个标头的实际含义现在并不重要，重要的是理解每个标头都有一个名称（例如 Date）后跟一个值（Mon, 23 Jun 2008 22:13:40 GMT）。在使用 HTTP 时，您可能会经常遇到一些标头，例如 Content-Length 和 Content-Type；许多其他标头可能对您的工作并非必需。幸运的是，最常见的标头，如 Date、Content-Length 和 Content-Type，都是不言自明的；它们分别指示服务器生成响应的日期和时间、响应中的字节数以及服务器用于编码响应的编码方法。

最后，在标头之后是响应的对象主体——在本例中，是一个简短的 HTML 文档。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**309**

**表 12-5.** *常见的 HTTP 状态码*

**状态码**

**含义**

已收到请求标头；客户端可以发送请求主体。

正在切换协议。

成功 HTTP 请求的标准响应。

请求已完成并导致创建了新资源。

请求已被接受处理，但处理尚未完成。

无内容。

永久移动；此请求及所有未来请求都应定向到给定的 URL。

临时移动；此请求应定向到给定的 URL。

可以使用 GET 方法请求在另一个 URL 下找到响应。

自上次请求以来资源未被修改。

请求包含错误的语法或无法完成。

用户代理的身份验证是可能的但未发生。

请求合法，但服务器拒绝响应。

请求的资源未找到。

对资源使用了该资源不支持的方法进行请求。

处理请求时发生内部服务器错误。

服务当前不可用。

使用的 HTTP 版本不受支持。

已超出带宽限制。*

*这不是官方状态码，*

*但许多服务器会返回它。*

**将 HTTP 与 GCF 结合使用**

HTTP 能够使用标准动词为服务请求提供元数据，这使您可以定义远程系统能够提供的各种服务，而不仅仅是返回网页片段。不过，通常 Java ME 设备只需要读取远程内容的能力。您可以使用 GCF 和 ContentConnection 下载内容，如清单 12-8 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**310**

第 12 章 ■ 访问网络上的远程数据

**清单 12-8.** *使用 ContentConnection 下载图像* public void run() {

ContentConnection cc = null;

DataInputStream in = null;

try {

String url = "http://www.noplace.com/image.png"; cc = (ContentConnection)Connector.open(url);

in = new DataInputStream(cc.openInputStream());

int length = (int)cc.getLength();

byte[] data = null;

if (length != -1) {

data = new byte[length];

in.readFully(data);

}

else {

int chunkSize = 512;

int index = 0;

int readLength = 0;

data = new byte[chunkSize];

do {

if (data.length < index + chunkSize) {

byte[] newData = new byte[index + chunkSize];

System.arraycopy(data, 0, newData, 0, data.length);

data = newData;

}

readLength = in.read(data, index, chunkSize);

index += readLength;

} while

(readLength == chunkSize);

length = index;

}

Image image = Image.createImage(data, 0, length);

…

}

catch (Exception e) {...}

finally {

try {

if (in != null) in.close();

}

catch (IOException ioe) {}

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**311**



`ContentConnection` 类继承自 `StreamConnection`，并提供了三个新方法：`getEncoding`、`getLength` 和 `getType`。本示例使用 `getLength` 方法来确定远程服务器返回的字节数；但遗憾的是，无法保证服务器一定会报告此信息。因此，本示例会测试返回的内容长度；如果该值为 `undefined`，代码将以 512 字节的块为单位读取数据。无论哪种情况，代码都会使用返回的数据创建一个新图像，并关闭输入流和 `ContentConnection`。此代码封装在一个可运行的方法 `run` 中，因为在实际应用中，你很可能需要为所有 I/O 操作使用线程。

（我在“将 HTTP 付诸实践”一节中向你展示了一个具体示例，其中详细介绍了使用 HTTP 的完整应用程序。）

`ContentConnection` 类是对 HTTP 提供的元数据类型的高级抽象，它只允许你获取远程内容。使用 `ContentConnection` 时，你在使用 `Connector` 类创建连接时指定一个远程资源。在底层，平台使用 HTTP GET 方法以及你在请求用于读取远程资源的 `DataInputStream` 时指定的连接来发起所需的请求，这使你无法控制 HTTP 请求本身的语义。一旦平台完成请求，你只能了解关于响应的三件事：响应的编码方式（使用 `getEncoding` 方法）、返回的字节数（使用 `getLength` 方法）以及响应的多用途互联网邮件扩展（MIME）类型（使用 `getType` 方法）。

■**注意** 内容类型和关于响应的其他信息仅在服务器提供时才可用；你的应用程序必须准备好处理服务器不返回此信息的可能性（除非你也能控制服务器实现，即便如此，你也需要处理因缺少此元数据而在应用程序中出现的任何错误）。

由于 `ContentConnection` 是一个如此简单的接口，当你的应用程序只需要获取远程资源的内容时，它是理想的选择。然而，对于更复杂的 HTTP 使用场景，它却远远不够，因为它既无法访问伴随 HTTP 请求和响应的大部分元数据，也无法让你指定 HTTP 在发起请求时调用的方法。要更精细地访问 HTTP 协议，你需要使用 `HttpConnection` 类的实例——该类继承自 `ContentConnection`。（实际上，当你对带有 `http` 方案的 URL 调用 `Connector.open` 时，返回的实例是 `HttpConnection`，而不是清单 12-8 中代码所暗示的 `ContentConnection`。）

反映与远程 HTTP 服务器的底层连接，`HttpConnection` 实例可以处于以下三种状态之一：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**312**

第 12 章 ■ 访问网络上的远程数据

• **设置**：你可以设置请求参数，包括方法。

• **已连接**：请求方法和参数已发送，并正在请求响应。

• **已关闭**：HTTP 连接已终止。

与其他类型的连接不同，你需要知道 `HttpConnection` 处于何种状态，因为某些方法只有在连接处于适当状态时才能工作。例如，一旦连接处于已连接状态，你就无法设置连接应使用的方法类型，因为连接已经发送了请求。当你调用任何需要连接交换数据的方法（例如打开用于读取或写入连接的流）时，就会发生从设置状态到已连接状态的转换。

当连接处于设置状态时，你可以使用连接的 `setRequestMethod` 方法设置请求方法，并传入 `HttpConnection.GET`、`HttpConnection.POST` 或 `HttpConnection.HEAD` 之一。你还可以为请求指定任意的请求头，例如提供的 MIME 内容类型。你可以使用连接的 `setRequestProperty` 方法，将请求头的名称和值作为字符串传入，如下所示：

```java
hc.setRequestProperty("Content-Type", "application/x-urlformencoded");
```

许多 `HttpConnection` 方法与 HTTP 响应头有直接的对应关系，包括以下方法：

*   `getDate`：返回 `Date` 头的值，以自纪元开始以来的秒数表示
*   `getExpiration`：返回资源何时过期，由 `Expires` 头指示，以自纪元开始以来的秒数表示
*   `getLastModified`：返回资源最后修改的时间，由 `Last-Modified` 头指示，以自纪元开始以来的秒数表示
*   `getResponseCode`：返回三位数的 HTTP 响应码
*   `getResponseMessage`：返回 HTTP 响应消息（如果有）
*   `getHeaderField`：按名称返回特定 HTTP 头的值

你还可以通过传递一个整数，使用 `getHeaderField` 和 `getHeaderFieldKey` 方法来枚举所有响应头；它们将响应头块视为单个数组，返回第 *n* 个头的值和名称。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**313**

`HttpConnection` 类还包含一些用于检查 URL 的实用函数。这些实用函数允许你确定 GCF 创建 `HttpConnection` 实例时 URL 包含的方案、主机名、端口和文件。这些 `HttpConnection` 方法包括 `getProtocol`、`getHost`、`getPort` 和 `getFile`。你还可以通过调用其 `getURL` 方法来确定特定 `HttpConnection` 引用的 URL。

需要使用 `HttpConnection` 而非 `ContentConnection` 的最常见原因可能是需要提交数据进行处理，例如将用户输入提交给 Web 服务。这可能涉及使用 HTTP POST 方法而不是 GET。你使用 `HttpConnection` 并获取其 `OutputStream`，以便将数据写入服务器，如清单 12-9 所示。

**清单 12-9.** *向服务器发送数据*

```java
void run() throws IOException {

    String url = "http://www.noplace.com/do";

    HttpConnection hc = null;

    DataInputStream is = null;

    OutputStream os = null;

    int rc;

    try {

        hc = (HttpConnection)Connector.open(url);

        hc.setRequestMethod(HttpConnection.POST);

        hc.setRequestProperty("Content-Type",

                "application/x-urlformencoded");

        os = hc.openOutputStream();

        os.write("name=value\n".getBytes());

        rc = hc.getResponseCode();

        if (rc != HttpConnection.HTTP_OK) {

            throw new IOException("HTTP response code: " + rc);

        }

        is = hc.openInputStream();

        int length = (int)hc.getLength();

        byte[] data = null;

        if (length != -1) {

            data = new byte[length];

            is.readFully(data);

        }

        else {

            int chunkSize = 512;

            int index = 0;

            int readLength = 0;

            data = new byte[chunkSize];

            do {

                if (data.length < index + chunkSize) {

                    byte[] newData = new byte[index + chunkSize];

                    System.arraycopy(data, 0, newData, 0, data.length);

                    data = newData;

                }

                readLength = is.read(data, index, chunkSize);

                index += readLength;

            } while (readLength == chunkSize);

            length = index;

        }

        …

    } catch (Exception e) {…}

    finally {

        try {

            if (is != null) is.close();

            if (os != null) os.close();

            if (hc != null) hc.close();

        }

        catch (Exception e) {…}

    }

}
```

使用 GCF 的设置完全相同；唯一的区别是，这次代码将结果强制转换为 `HttpConnection`。然后，代码将请求方法设置为 `POST`。



使用 `setRequestMethod` 方法，它会向请求中添加一个描述内容类型的额外标头。在大多数实现中，为请求获取输出流的过程会导致 `HttpConnection` 从设置状态转移到已连接状态；此时远程服务器正在等待应用程序写入 POST 请求的对象体。

代码首先通过 `openOutputStream` 获取连接上的输出流，然后写入一个简单的表单编码名称-值对。即使实现缓冲了你使用 `HttpConnection` 实例和 `OutputStream` 准备的标头和对象体，当你调用 `getResponseCode` 时，`HttpConnection` *必须*打开连接、发送请求并读取 HTTP 响应标头。

此时，连接肯定处于已连接状态，你可以使用诸如 `getLength` 之类的连接方法来获取结果信息，就像这段代码用来确定结果对象体长度一样。代码的其余部分与

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**315**

使用 `ContentConnection` 和 `InputStream` 从服务器读取结果相同，在代码完成从流中读取后关闭流和连接。

**将 HTTP 付诸实践**

使用 GCF 连接远程资源在理论上很简单，但在实践中稍微复杂一些，原因有二。首先，也是最重要的，你必须处理所有可能出现的异常；你之前看到的伪代码表明异常*可能*发生，但伪代码并没有对它们进行任何有用的处理。其次，你希望在一个单独的线程上执行 I/O 操作，因为连接在与远程服务器交换数据时通常会阻塞它们所操作的线程。虽然在某些设备和网络上，这种延迟可以忽略不计，但无法保证这一点，而让连接在主线程上执行工作的结果就是用户界面冻结——这是用户无法容忍的。

清单 12-10 展示了 `WeatherFetcher` 类，它使用 HTTP 获取特定城市和州的天气预报。`WeatherWidget` 示例使用它从远程 Web 服务器获取天气预报，如清单 12-11 所示。（如果你正在跟随本章的清单进行操作，你将需要对你项目中的*所有*清单进行更改，因为它们是相互关联的。）

■**注意** `WeatherFetcher` 的实现假设远程 Web 服务器能够根据给定的城市和州提供天气预报，并且生成的天气预报以纯文本形式提供给应用程序。在下一章中，我将基于你在这里学到的内容，探讨实际的 Web 服务如何利用 XML 为请求和响应数据提供结构。

**清单 12-10.** *WeatherFetcher 类*

package com.apress.rischpater.weatherwidget;

import java.io.*;

import javax.microedition.io.*;

public class WeatherFetcher implements Runnable {

private String url = "http://www.noplace.com/weather"; private Thread thread;

private Location location;

private boolean cancelled;

WeatherWidget app;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**316**

第 12 章 ■ 访问网络上的远程数据

public WeatherFetcher(Location l, WeatherWidget a) {

location = l;

app = a;

cancelled = false;

if (l!=null && a!=null) {

thread = new Thread(this);

thread.start();

}

}

public void cancel() {

cancelled = true;

}

public void run() {

String vars;

String forecast = "";

HttpConnection hc = null;

InputStream in = null;

OutputStream out = null;

vars = "location="+WeatherFetcher.urlEncode(location.getLocation()); try {

hc = (HttpConnection)Connector.open(url);

hc.setRequestMethod(HttpConnection.POST);

hc.setRequestProperty("Content-Type",

"application/x-www-formrlencoded");

hc.setRequestProperty("Content-Length",

Integer.toString(vars.length()));

out=hc.openOutputStream();



out.write(vars.getBytes());

in=hc.openInputStream();

int length=(int)hc.getLength();

byte[] data = new byte[length];

in.read(data);

forecast = new String(data);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**317**

catch(Exception e){ forecast = e.getMessage(); }

finally {

try {

if (in!=null) hc.close();

if (out!=null) hc.close();

if (hc!=null) hc.close();

}

catch(Exception e) {}

}

if (!cancelled) {

location.setForecast(forecast);

app.update();

}

}

private static String urlEncode(String s)

{

if (s!=null) {

StringBuffer tmp = new StringBuffer();

int i=0;

try {

while (true) {

int b = (int)s.charAt(i++);

if ((b>=0x30 && b<=0x39) || /* 0-9 */

(b>=0x41 && b<=0x5A) || /* A-Z */

(b>=0x61 && b<=0x7A)) { /* a-z */

tmp.append((char)b);

} else {

tmp.append("%");

if (b <= 0xf) tmp.append("0");

tmp.append(Integer.toHexString(b));

}

}

}

catch (Exception e) {}

return tmp.toString();

}

return null;

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**318**

第 12 章 ■ 访问网络上的远程数据

`WeatherFetcher`类实现了`Runnable`接口，因为实际的连接发生在一个单独的线程中。这使得`MIDlet`能够在不阻塞用户界面的情况下执行网络通信——这是当今用户期望的功能。给定一个位置和一个应用程序实例（用于在网络操作完成时通知），该类创建一个新线程，连接到远程服务器，将位置信息发送到服务器，并获取结果。这项工作在类的构造函数中启动，构造函数会保存提供的位置信息和应用程序用户界面的引用，然后使用该类的新建实例启动一个新线程。`WeatherFetcher`类需要引用使用它的`MIDlet`；当更新发生时，该类通过调用`MIDlet`的`update`方法来通知`MIDlet`。清单 12-11 展示了`WeatherWidget MIDlet`的这一改动，稍后我将进一步讨论这一改动。

用户界面可以随时取消操作；如果在`WeatherFetcher`及其线程阻塞等待网络响应时选择了不同的位置，就可能发生这种情况。为此，用户界面调用`cancel`方法；它只是设置一个取消标志，`WeatherFetcher`在更新用户界面之前会检查这个标志。实际上，`cancel`并没有真正取消 HTTP 连接，因为确实没有好的方法来实现这一点；相反，它允许 HTTP 连接完成，但取消更新用户界面。

`run`方法实际执行获取特定位置天气预报所需的工作；现在这段代码你应该已经熟悉了。`WeatherConnection`类假设远程 Web 服务器已将所需位置作为编码为表单参数的名称-值对接受；这对于也为 Web 表单提供内容的简单 Web 服务器来说是典型的做法。因此，`run`做的第一件事就是创建一个名称-值对，并将结果存储在`vars`中；该对的语法适用于任何响应表单的 Web 服务器，看起来像这样：

location=Berkeley,CA

这一步必须对参数字符串进行*URL 编码*；某些字符（如空格字符" "）不能原样传输，而是表示为单个百分号（%）后跟该字符的两位十六进制 ASCII 码。

因此，空格字符" "将被表示为字符串`%20`。虽然示例只传递了一个名为`location`的值，但它同样可以轻松传递多个值；这些值将使用与号（&）连接起来，如下所示：city=Berkeley&state=CA

然而，通常情况下，如果你正在构建一个与 Web 服务器交换结构化数据的应用程序，你可能会使用 XML 来编码这些结构化数据——这个话题我留到第 13 章再讲。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**319**



■**提示** 如果你使用 HTTP GET 或 POST 方法传递名称-值对，务必对值进行 URL 编码，否则 URL 格式将不正确，响应服务器很可能无法处理你的结果。URL 编码的完整语法在 RFC 2396 中有详细说明。

待发送数据经过 URL 编码后，就该设置 HTTP 连接了。它通过 GCF 实现，将请求方法设置为 POST，表明发送的内容类型是 URL 编码的表单数据（就像来自网页浏览器一样），并指定数据的长度。（本例*本可以*使用 HTTP GET 请求，只需将表单数据附加在 URL 末尾即可，但向你展示如何发起 HTTP POST 请求更有用，因为对于任何严肃的 Web 服务交互，你很可能都会使用 POST。）

配置好连接后，代码将参数写入连接的输出流，然后打开输入流并读取结果。实际上，代码很可能在此处阻塞；在移动无线设备上，此交互过程可能需要大约一秒钟。事实上，处理此交互所需的时间很可能*更长*，至少是应用程序首次执行时，因为许多设备（尤其是 MIDP 设备）会询问用户是否允许 MIDlet 执行该请求。只有经过签名的第三方应用程序才被允许在未经许可的情况下使用网络资源；其他应用程序在发起任何网络请求之前都会提示用户请求许可。

代码从网络读取结果后，会使用结果数据构造一个新的字符串，并用新数据更新 Location 对象，然后通知应用程序 Location 对象已更改。

■**提示** 粗略来说，WeatherFetcher 使用了观察者模式，将初始化时接收到的 Location 对象作为模型，将应用程序作为更新的接收者。我本可以构建更复杂的接口和类层次结构来表示这一点，但这并不会简化本例的目的——即向你展示如何使用 HttpConnection。

静态的 urlEncode 方法很简单，它基于输入字符串创建一个新字符串。

新字符串中，任何非字母字符都会被替换为 URL 编码后的字符。坦率地说，我不明白为什么 CLDC 和 CDC 没有在某个地方提供这个实用函数；大多数需要联网的应用程序无论如何都会用到它。

将 WeatherFetcher 类集成到 WeatherWidget 应用程序中并不需要太多工作；从第 6 章中你上次看到的实现开始，你只需要对 MIDlet 本身进行几处修改，以便在 MIDlet 首次启动时或从位置列表中选择新位置时发起天气更新请求。

清单 12-11 展示了这些修改。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**320**

第 12 章 ■ 访问网络上的远程数据

**清单 12-11.** *修改后的 WeatherWidget MIDlet，使用 WeatherFetcher 类* package com.apress.rischpater.weatherwidget;

import javax.microedition.midlet.*;

import javax.microedition.lcdui.*;

import java.util.*;

public class WeatherWidget extends MIDlet implements CommandListener {

private Form wxForm;

private StringItem locationItem;

private StringItem wxItem;

private Command exitCommand;

private Command screenCommand;

private Command settingCommand;

private Command okCommand;

private Command backCommand;

private Command updateCommand;

private List locationList;

private TextBox locationTextBox;

private Alert cannotAddLocationAlert;

private WeatherFetcher fetcher;

private Location location;

private LocationStore locationStore;

private void initialize() {

locationStore = new LocationStore();

String[] locations = locationStore.getLocationStrings(); try {

if (locations.length > 0 )

location = locationStore.getLocation(locations[0]);

}

catch(Exception e){}



fetcher = new WeatherFetcher(location, this);

getDisplay().setCurrent(get_wxForm());

}

public void startApp() {

initialize();

}

public void pauseApp() {

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**321**

public void destroyApp(boolean unconditional) {

if (fetcher!=null) fetcher.cancel();

fetcher = null;

}

public void update() {

get_wxItem().setText(get_forecast());

try

{

locationStore.updateLocation(location);

}

catch(Exception e){}

fetcher = null;

}

public void commandAction(Command command, Displayable displayable) {

// 在此插入全局前置操作代码

if (displayable == wxForm) {

if (command == exitCommand) {

exitMIDlet();

} else if (command == settingCommand) {

getDisplay().setCurrent(get_locationList());

}

} else if (displayable == locationList) {

if (command == screenCommand) {

getDisplay().setCurrent(get_locationTextBox());

} else if (command == List.SELECT_COMMAND) {

int index = get_locationList().getSelectedIndex();

set_location(get_locationList().getString(index));

if (fetcher != null) fetcher.cancel();

fetcher = new WeatherFetcher(location, this);

getDisplay().setCurrent(get_wxForm());

} else if (command == backCommand) {

getDisplay().setCurrent(get_wxForm());

}

} else if (displayable == locationTextBox) {

if (command == backCommand) {

getDisplay().setCurrent(get_locationList());

} else if (command == okCommand) {

add_location(locationTextBox.getString());

getDisplay().setCurrent(get_locationList());

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**322**

第 12 章 ■ 访问网络上的远程数据

} else if (displayable == cannotAddLocationAlert) {

if (command == backCommand) {

getDisplay().setCurrent(get_locationList());

}

}

}

public String get_forecast() {

if (location == null) {

return "未知预报";

} else {

return location.getForecast();

}

}

public String get_location() {

if (location == null) {

return "未知";

} else {

return location.getLocation();

}

}

public void set_location( String l ) {

try {

location = locationStore.getLocation(l);

}

catch(Exception e)

{}

get_wxForm().setTitle(l);

}

public void add_location( String l ) {

String locations[];

int i;

try {

locationStore.addLocation( new Location( l, "" ));

} catch (Exception e) {

getDisplay().setCurrent(get_cannotAddLocationAlert());

}

// 延迟刷新位置列表。

locationList = null;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**323**

public Display getDisplay() {

return Display.getDisplay(this);

}

public void exitMIDlet() {

if (fetcher!=null) fetcher.cancel();

fetcher = null;

getDisplay().setCurrent(null);

destroyApp(true);

notifyDestroyed();

}

public StringItem get_wxItem() {

if (wxItem == null) {

wxItem = new StringItem("预报", get_forecast());

}

return wxItem;

}

public Form get_wxForm() {

if (wxForm == null) {

wxForm = new Form(get_location(), new Item[] {

get_wxItem()

});

wxForm.addCommand(get_exitCommand());

wxForm.addCommand(get_settingCommand());

wxForm.setCommandListener(this);

}

return wxForm;

}

public TextBox get_locationTextBox() {

if (locationTextBox == null) {

locationTextBox = new TextBox("添加位置", "", 80, 0); locationTextBox.addCommand(get_backCommand());

locationTextBox.addCommand(get_okCommand());

locationTextBox.setCommandListener(this);

}

return locationTextBox;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**324**

第 12 章 ■ 访问网络上的远程数据

public List get_locationList() {

if (locationList == null) {

String[] locations;

locations = locationStore.getLocationStrings();

locationList = new List("位置", List.IMPLICIT, locations, null); locationList.addCommand(get_screenCommand());

locationList.addCommand(get_backCommand());

locationList.setCommandListener(this);

}

return locationList;

}

public Alert get_cannotAddLocationAlert() {

if (cannotAddLocationAlert == null)

{

cannotAddLocationAlert = new Alert("无法添加位置"); cannotAddLocationAlert.setString("添加您输入的位置时发生错误。该位置尚未被添加。");



cannotAddLocationAlert.addCommand(get_backCommand());

}

return cannotAddLocationAlert;

}

public Command get_settingCommand() {

if (settingCommand == null) {

settingCommand = new Command("设置", Command.OK, 1);

}

return

settingCommand;

}

public Command get_okCommand() {

if (okCommand == null) {

okCommand = new Command("确定", Command.OK, 1);

}

return okCommand;

}

public Command get_exitCommand() {

if (exitCommand == null) {

exitCommand = new Command("退出", Command.EXIT, 1);

}

return exitCommand;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**325**

public Command get_screenCommand() {

if (screenCommand == null) {

screenCommand = new Command("添加位置", Command.SCREEN, 1);

}

return screenCommand;

}

public Command get_backCommand() {

if (backCommand == null) {

backCommand = new Command("返回", Command.BACK, 1);

}

return backCommand;

}

}

MIDlet 现在必须声明一个 `WeatherFetcher` 实例，以便在启动时或选择某个位置时，能够发起和取消天气更新的请求。`initialize` 方法负责处理第一种情况，即应用程序在启动时请求更新天气预报。

新的 `update` 方法由 `WeatherFetcher` 实例调用，它必须完成两件事：用新获取的预报更新用户界面，以及用新的天气数据更新 `LocationStore`。它会静默忽略更新 `LocationStore` 时的任何失败，前提是旧的缓存数据可能足够好，并且后续查看同一位置时无论如何都会重新获取天气预报数据。

当您选择新位置时，MIDlet 的主命令处理器 `commandAction` 必须做出响应：取消任何待处理的操作，为新建的位置创建一个新的 `Fetcher`，并发起一个新的网络请求。

最后，退出 MIDlet 时应终止网络操作；这通过 `destroyApp` 和 `exitMIDlet` 两者共同完成，前者由应用程序管理器在设备必须终止应用程序时调用，后者由用户界面本身调用。

**使用 HTTPS 保护您的 HTTP 事务**

虽然 HTTP 作为应用层协议有很多优点，但安全性并非其中之一。通过 HTTP 共享信息就像在拥挤的餐厅里交谈；您获得的唯一安全性来自模糊性，而这根本不是真正的安全。为了满足电子商务等安全应用的需求，出现了 HTTPS 协议，它本质上是在安全套接字连接（如 TLS）上实现的 HTTP。

HTTPS 通过证书提供客户端/服务器身份验证，并使用双方商定的协议对端到端通信进行加密，从而抵御多种攻击，例如中间人攻击和窃听攻击。

作为 MIDP 2.0 的强制要求，HTTPS 是 Java ME 平台的一个重要补充，在许多应用中都有使用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**326**

第 12 章 ■ 访问网络上的远程数据

幸运的是，在您的应用程序中使用 HTTPS 就像添加几个 `s` 字符一样简单。创建连接时，改为从 `Connector` 请求一个 `HttpsConnection`，如清单 12-12 所示。

**清单 12-12.** *请求一个 HttpsConnection* HttpsConnection hc = (HttpsConnection)

Connector.open("https://www.noplace.com/");

生成的 `HttpsConnection` 实现了 `HttpConnection`，因此您可以以相同的方式使用它。此外，您还可以获取关于该连接的两条额外信息：服务器通过其 `getPort` 方法接受连接的端口，以及通过其 `getSecurityInfo` 方法获取关于协商后的安全连接的详细信息。

`getSecurityInfo` 方法返回一个 `SecurityInfo` 实例，该实例描述了您的应用程序与远程服务器之间建立的连接类型；从中您可以获取四条信息：GCF 如何加密事务、承载协议、承载协议的版本以及远程证书。这些信息可通过以下 `SecurityInfo` 方法获得：

• `getCipherSuite`：返回一个字符串，命名 GCF 用于加密事务的密码套件

• `getProtocolName`：返回一个字符串，指示承载事务的协议名称

• `getProtocolVersion`：返回一个字符串，指示承载事务的协议版本

• `getServerCertificate`：返回远程服务器的证书 `getServerCertificate` 返回的证书是一个实现了 `Certificate` 接口的对象；您可以查询其属性，例如证书的颁发者和到期时间。

虽然为您的应用程序添加 HTTPS 可能很容易，但构建一个安全的应用程序却并非如此。正如顶尖计算机安全专家 Gene Spafford 所言，HTTPS 就像

“用装甲车在公园长椅上的人和纸板箱里做生意的人之间运送成卷的便士。”要构建真正安全的应用程序，您还必须对应用程序与远程服务器交换的数据负责；例如，电子商务应用程序应注意保护用户的凭据，以防用户设备丢失或被盗时造成泄露。我将在第 15 章向您展示 Java ME 提供的一些应对这些挑战的工具。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**327**

**授予网络连接权限**

正如我在“使用 HTTP”一节中指出的，网络应用程序需要权限。这个由 MIDP 强制要求的规定，确保应用程序不会生成未经授权的网络连接，从而避免产生数据使用费用或将您的信息传输给未经授权的个人。在 MIDP 中，通过 GCF 进行的网络访问受到基于应用程序要创建的连接类型的权限保护。表 12-6 显示了这些权限的名称。

**表 12-6.** *管理 GCF 访问的 MIDP 权限*

**权限**

**接口**

javax.microedition.io.Connector.http

HttpConnection

javax.microedition.io.Connector.https

HttpsConnection

javax.microedition.io.Connector.datagram

DatagramConnection

javax.microedition.io.Connector.datagramreceiver

DatagramConnection

javax.microedition.io.Connector.socket

SocketConnection

javax.microedition.io.Connector.serversocket

ServerSocketConnection

javax.microedition.io.Connector.ssl

HttpsConnection

javax.microedition.io.Connector.comm

CommConnection

javax.microedition.io.PushRegistry

PushRegistry

默认情况下，在 NetBeans 提供的模拟器中运行时，您的 MIDlet 作为不受信任的 MIDlet 运行，并且只有在用户授予连接权限时才能使用连接。

正如我在第 3 章中所述，受信任的应用程序必须在 JAD 属性 `MIDlet-Permissions` 和 `MIDlet-Permissions-Opt` 中指定它们所需的权限。在 NetBeans 中，最简单的方法是在应用程序的属性中指定：选择“属性”➤“应用程序描述符”➤“API 权限”，然后单击标有“添加”的按钮。

图 12-2 显示了 NetBeans 中的此屏幕。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00042.png)

**328**

第 12 章 ■ 访问网络上的远程数据

**图 12-2.** *指定 MIDlet 权限*

**总结**

与 Java 的其他配置不同，Java ME 通过 GCF 提供了一种统一的数据通信方法。GCF 提供了一个用于连接的工厂类，称为 `Connector`，它允许您基于 URL 创建 `Connection` 接口的子类。



你传递给 `Connector.open` 的参数。URL 不仅指定了连接的目标地址，还指定了协议类型和端口，让你能够创建各种不同的连接。然而，实际的连接类型（应用层协议和传输层协议）受限于你的应用程序所运行的平台；某些协议（如 HTTP）在几乎所有 Java ME 平台上都可用，包括 CDC 和 CLDC，而其他协议（如数据报协议或底层流式套接字协议）则可能不可用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 12 章 ■ 访问网络上的远程数据

**329**

你使用 `Connector.open` 返回的 `Connection` 接口子类来执行实际的通信；它通常提供一种方式来获取流或数据报，用于与连接的远程端交换字节。大多数 `Connection` 的子类都提供了额外的方法来执行特定于协议的操作，例如设置套接字选项。

`Connection` 接口子类的一个重要示例是以 `ContentConnection` 开头并包含 `HttpConnection` 的层次结构；你可以使用这两个类与远程 Web 服务器执行 HTTP 交换，以获取内容并与远程 Web 服务交互。仅使用 `HttpConnection`，你就可以将数据以 URL 编码形式发布到远程服务器，并获取响应，供你的应用程序解析、存储和显示；你也可以通过 `HttpsConnection` 在 HTTPS 上执行相同操作。

在 CLDC MIDP 平台上运行时，你的应用程序需要权限才能建立连接。不受信任的应用程序会提示用户授予建立连接的权限；受信任的应用程序可以通过 JAD 属性 `MIDlet-Permissions` 和 `MIDlet-Permissions-Opt` 表明其对特定连接类型的访问需求。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章

访问 Web 服务

**在**上一章中，你学习了如何建立与远程资源（例如远程 Web 服务器）的连接。虽然连接到远程资源是许多 Java ME 应用程序的关键组成部分，但当今大多数联网的 Java ME 应用程序还需要更多：能够与当今的 Web 服务互操作，这些服务由专为机器对机器交互而设计的 Web 服务器提供。Web 服务是客户端/服务器模型的重要演进，随着移动用户越来越依赖移动设备访问产品和服务，那些具有清晰用户界面、能够呈现来自 Web 服务的数据（如天气、金融和旅行信息）的应用程序需求旺盛。

在本章中，我将向你展示如何使用 JSR 172 指定的可选 J2ME Web 服务规范，将 Java ME 平台与驱动当今应用程序的 Web 服务连接起来，以及在 J2ME Web 服务规范不可用时如何实现相同的功能。我将从客户端角度回顾 Web 服务架构开始，以便你理解使用 Web 服务与简单地从互联网上拉取远程资源有何不同。在此过程中，你将了解 Web 服务架构的三种类别，以及 HTTP 和 XML 如何在支持当今 Web 服务中发挥关键作用。最后，我将详细讨论上一章中缺失的两个 Web 服务关键部分：如何在 Java ME 应用程序中生成和解析 XML。

**从客户端视角看 Web 服务**

计算领域的大多数突破并非革命性的，而是演进性的。Web 服务也不例外；其核心是客户端/服务器计算模型的一种应用，使用 HTTP 作为承载协议，并使用诸如 XML、JavaScript 对象表示法（JSON）或 YAML 非标记语言（YAML）等知名语言进行数据表示。一个完整的 Web 服务有三个关键组件，如图 13-1 所示：

**331**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**332**

第 13 章 ■ 访问 Web 服务

*   ***服务代理***：负责帮助客户端确定一个或多个*服务提供者*的位置
*   ***一个或多个服务提供者***：负责向客户端提供远程计算设施，例如计算和对象交换
*   ***一个或多个服务请求者***：使用代理查找提供者并与之交互的客户端

■**注意** 在接下来的讨论中，我将使用术语*客户端*来指代服务请求者。

**图 13-1.** *Web 服务的示意图*
在经典的 Web 服务中，请求者首先通过使用通用描述、发现和集成（UDDI）注册协议与服务代理协调，来发现哪些 Web 服务可用；一旦请求者确定了提供者提供哪些服务，请求者和提供者就会使用 Web 服务描述语言（WSDL）就实际可用的服务进行通信。实际的请求者-提供者通信利用简单对象访问协议（SOAP）。

UDDI、WSDL 和 SOAP 都是 XML 应用程序，因为它们使用 XML 作为表示数据的基础语言。在实践中，HTTP 承载这些语言编码的请求和响应，当然，任何能够承载对象数据和对象元数据的可靠协议都足以胜任。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**333**

**考虑架构**

在实践中，术语*Web 服务*已逐渐适用于任何利用 Web 技术的机器对机器通信应用程序。这种称谓不仅仅是描述某类应用程序的简写，而且随着范式的演进，它也是恰当的。

在大型环境中，Web 服务的职责划分（如图 13-1 所示）非常重要。然而，在许多情况下，职责划分可以（并且已经）被简化，以反映需求和计算环境的差异。

其中一种简化是移除代理，或者将代理与服务提供者合并。在这种模型中，客户端对服务提供者所提供服务的性质和位置有预定义的概念。这对于许多移动客户端尤其如此，因为动态发现和能力协商的复杂性和开销会显著增加应用程序的内存成本。

另一种对 Web 服务进行分类的方法是查看客户端与服务器之间交互的性质。广义上讲，有三种交互类别：远程过程调用（RPC）服务、面向服务的架构（SOA）服务以及使用表述性状态转移（REST）的服务。我喜欢将它们分别视为采用功能方法、消息传递方法和资源方法。

面向 RPC 的 Web 服务是最常见的类型之一，其语义可追溯到前 Web 时代的客户端/服务器编程，即客户端请求远程服务器执行特定操作。封装请求和响应的语义通常因服务而异；小型服务大量使用简单的 XML 表示对象和请求，而更复杂的应用程序几乎总是使用 SOAP 来表示对象。

■**注意** 你在第 11 章首次看到的 Java RMI 是 RPC 接口的另一个示例。



SOA Web 服务是规模最大、最复杂的服务之一，并且如今得到了众多行业厂商的广泛支持。与 RPC（其核心概念是方法调用，通常与底层实现和领域紧密耦合）不同，SOA 将请求表示为消息。换句话说，它的重点是消息，而非操作。在多个不同子系统或组织之间实现大规模 Web 服务时，SOA 能提供诸多优势。SOA Web 服务使用 SOAP（一种用于数据交换的 XML 对象表示形式）和 WSDL 来描述服务器或客户端提供或需要的服务类型。

最后，基于 REST 的 Web 服务尝试利用 HTTP 提供的多种方法来描述客户端可以请求服务对已记录对象执行的操作类型。RESTful Web 服务的一个标志是呈现代表对象的知名 URL，而不是代表操作、服务或一个通用的

[www.it-ebooks.info](http://www.it-ebooks.info/)

**334**

第 13 章 ■ 访问 Web 服务

调用点。客户端使用 HTTP 方法（如 GET、PUT 或 DELETE）请求与这些对象进行交互。RESTful 应用程序可能会使用 SOAP，但 RESTful 服务中强烈的简约倾向使得许多架构师使用简单的、自制的 XML 对象表示形式。

虽然大型 Web 服务使用包括 UDDI、WSDL 和 SOAP 在内的万维网联盟（W3C）协议层次结构，但你完全有可能仅使用 XML 来构建一个 Web 服务。许多在软件混搭中使用的流行 Web 2.0 服务就是这样做的，它们避开了完整 W3C 技术栈的复杂性，转而采用仅基于 SOAP 或偶尔仅应用 XML 的轻量级对象表示形式。越来越多的 Web 服务可能提供对其他结构化数据表示形式（如 JSON 或 YAML）的支持，使软件开发人员能够利用 JavaScript 或 Ruby 等编程语言对这些较新数据表示形式的支持。

■**提示** 虽然你完全可以基于或使用提供 JSON 或 YAML 接口的 Web 服务来构建你的 Java ME 应用程序，但你应该准备好做更多工作来支持这些数据表示形式，因为在我撰写本文时，Java ME 平台上对它们的支持很少。我设想，随着越来越多的服务在其客户端/服务器通信中使用这些表示形式，这种情况在未来几年可能会发生变化。

**通过网络交换数据**

如果你正在为当今的任何 Web 服务编写客户端，那么你很可能会使用 HTTP 或 HTTPS。正如我在上一章“使用 HTTPS 保护你的 HTTP 事务”一节中指出的那样，HTTP 是一种很好的数据交换协议，但它的安全性并不高。许多 Web 服务，包括电子商务服务，几乎肯定使用 HTTPS。重要的是要记住，你不仅需要使用 HTTPS 保护用户数据的安全，还需要负责在应用程序的其他地方保护他们的数据，例如当数据存储在记录存储或文件中时。

编写 Web 服务客户端时的另一个考虑因素是你可能需要使用的 HTTP 方法；在使用 REST 时尤其如此，因为 REST 范式依赖于 HTTP 方法来提供关于 Web 服务操作的一些语义。例如，在为一个基于 REST 构建的消息传递应用程序编写 Web 服务客户端时，你可能会发现用于删除消息的 Web 服务定义要求你发送一个使用 DELETE 方法的 HTTP 请求。幸运的是，这就像使用 HttpConnection（或 HttpsConnection）的 `setRequestMethod` 方法并传入你想要使用的方法一样简单，如清单 13-1 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**335**

**清单 13-1.** *指定 HTTP 方法*

try {

hc = (HttpConnection)Connector.open(url);

hc.setRequestMethod("DELETE");

…

} catch (Exception e) {…}

实际上，你可以将*任何*字符串传递给 `setRequestMethod`，但你当然应该坚持使用 HTTP 定义的方法，我在第 12 章的表 12-4 中为你总结了这些方法。

另一个考虑因素是某些 Web 服务对*消息摘要*的需求，消息摘要是从客户端到服务器的 Web 请求或对象正文的安全哈希值。我将在第 15 章讨论如何使用 Java ME 开发人员可用的各种选项来计算消息摘要。

在 HTTP 层为 Web 服务开发客户端时，你可能遇到的最后一件事是需要一个 cookie 来保存会话状态。这对于源自 SOA 的 Web 服务尤其如此；这些服务通常要求客户端应用程序登录，并在每次事务期间呈现登录序列期间提供的一些状态数据。这通常通过*cookie* 来实现——服务器在 HTTP 标头中提供的一小段数据，用于封装与服务器的会话。Web 客户端一直使用 cookie 来存储客户端和服务器之间的状态数据；你的应用程序可能也需要执行相同的操作。HTTP 如何使用 cookie 携带会话状态的完整细节在 RFC 2965 和 RFC 2109 中有所描述。但是，你应该了解以下事实：

• Web 服务器使用 `Set-Cookie` 标头将 cookie 传递给你的应用程序。
• Cookie 由几个部分组成：第一部分是 cookie 的会话 ID，这是你需要在后续请求中回发给服务器的数据，其余部分是有关 cookie 的元数据，例如其过期时间以及它适用于哪个域。分号分隔这些部分。
• 你通过使用 `Cookie` 标头将 cookie 传递给 Web 服务器。

要查找来自服务器的 cookie，你只需使用 HttpConnection 的 `getHeaderField` 方法，如清单 13-2 所示。

**清单 13-2.** *从服务器查找 Cookie* String cookie = hc.getHeaderField("Set-Cookie"); if (cookie != null) {

int semicolon = cookie.indexOf(';');

/* session is at cookie.substring(0, semicolon); */

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**336**

第 13 章 ■ 访问 Web 服务

一旦你获得了一个 cookie，你应该将其分解为会话 ID 和域部分，你可以通过在其分号处解析 cookie 字符串来做到这一点；这将两部分分开。一个存储结果会话的好地方是在实现你的 Web 服务客户端的 Java 类的成员字段中。然后，你将根据后续请求的 URL 检查该域，仅当域匹配时才使用 HttpConnection 的 `setRequestProperty` 发送 cookie，如清单 13-3 所示。

**清单 13-3.** *随 HTTP 请求发送 Cookie* HttpConnection hc = (HttpConnection)Connector.open(url); hc.setRequestProperty("cookie", mSession);

你可能希望将 cookie 存储在其他地方，例如记录存储中，但有一个问题：它们会过期，而过期时间取决于服务器的配置方式。如果你想持久化 cookie，你绝对应该解析 cookie 以获取过期日期，并通过在 cookie 过期时将其从应用程序的存储中移除来适当地处理过期。

**使用 XML 进行数据表示**

XML 提供了一种标准语法，用于创建自定义标记语言，让你可以描述文档中的数据。它源自标准通用标记语言（SGML），是当今使用最广泛的标记语言之一。在本节中，我将仅为你提供 XML 的简要介绍，即你在自己的应用程序中使用简单 XML 所需了解的关键细节。有关 XML 的全面介绍，我鼓励你访问 W3C 文档，网址为 http://www.w3c.org/XML。

XML 语法为你提供了一种表示文档的通用语法



树状结构始于一个单一的*根*元素，该元素必须包含零个或多个*子*元素，而这些子元素自身也可以拥有子元素。元素由*标签*构成，标签具有属性，并且可以包含其他标签或数据，如清单 13-4 所示的天气预报示例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**337**

**清单 13-4.** *一个 XML 文档示例*

<?xml version="1.0" encoding="UTF-8"?>

<weather city="Berkeley" state="CA" country="USA">

<temperatures units="F">

<temperature type="high">76</temperature>

<temperature type="low">56</temperature>

<temperature type="current">72</temperature>

</temperatures>

<wind>

<direction units="compass">ENE</direction>

<speed units="MPH">5</speed></wind>

<precipitation probability="0" type="rain" units="in"/>

<text>

<when time="today">

Mostly cloudy in the morning then becoming mostly sunny.

Patchy fog in the morning. Highs in the 70s to lower 80s.

Afternoon seabreeze 10 to 20 mph.

</when>

<when time="tonight">

Mostly clear except areas of low clouds and fog developing overnight. Lows in the mid to upper 50s. Evening seabreeze 10 to 20 mph.

</when>

</text>

</weather>

图 13-2 展示了一个表示此数据结构的树。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**338**

第 13 章 ■ 访问 Web 服务

**图 13-2.** *表示清单 13-4 中 XML 文档结构的树*

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**339**

此示例展示了 XML 的几个关键特性：

• XML 文档以*序言*开头，由`<?`和`?>`界定。序言指明了文件所使用的 XML 版本以及文件的字符集编码。

• XML 文档可以包含注释，注释以`<!--`开头，以`-->`结束。（但注释不能嵌套。）

• 元素由`<`和`>`包含的纯文本组成。（实际上，这两个符号之间能包含的内容是有限制的；就本讨论而言，可以安全地假设元素名称只能由字母数字字符和下划线字符组成。）

• 起始标签表示元素数据的开始；结束标签由`</`和`>`包含相同的标签名称来表示。

• 元素之间的空白字符不重要。

• 元素可以有*属性*，属性是包含在起始标签内的名称-值对。

• 元素区分大小写。

• 空元素不包含任何元素，使用`<`和`/>`表示。

• 元素可以包含其他元素或文本数据。

■**注意**

本章使用的天气数据模式是我编造的，目的是展示 XML 的一些关键特性。在互联网上，存在多种天气数据模式，例如美国政府国家气象局在 http://www.weather.gov/data/current_obs/ 定义的模式，或者雅虎天气在 http://developer.yahoo.com/weather/ 描述的格式。

XML 文档可以通过两种方式表示文本：作为平面字符数据，其中可能包含五个*实体*来表示特殊字符`<`、`>`、`&`、`'`和`"`；或者作为特殊的未解析字符指令，其中包含原始字符数据，XML 解析器会接受这些数据而不尝试解析。表 13-1 展示了这五个字符实体的定义。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**340**

第 13 章 ■ 访问 Web 服务

**表 13-1.** *XML 字符实体*

**实体**

**字符**

**表示**

`&amp;`

`&`

&符号

`&lt;`

`<`

小于号

`&gt;`

`>`

大于号

`&apos;`

`'`

撇号

`&quot;`

`"`

引号

清单 13-5 展示了一个简单的未解析字符指令示例。

**清单 13-5.** *使用 XML 的 CDATA 指令的未解析字符数据示例*

<?xml version="1.0" encoding="UTF-8"?>

<root>

<![CDATA[

CDATA 结构中的每个字符都被视为普通字符；没有任何限制，因此我可以写像 3 < 4 这样的内容而不会混淆 XML 解析器。

]]>

</root>


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


一个 XML 文档可以定义一个或多个*命名空间*，这些命名空间定义了文档中具有唯一名称的元素和属性。这使得 XML 文档可以为多个目的使用同一个元素或属性，例如，一个 `<id>` 元素既可以表示客户 ID，也可以表示产品 ID。使用命名空间时，需要使用 XML 保留的 `xmlns` 标签来定义一个唯一的 URL 来标识该命名空间；在标签中，命名空间名称位于标签名称之前。使用冒号将命名空间名称与标签名称分隔开，如清单 13-6 所示。

**清单 13-6.** *XML 命名空间示例*

<?xml version="1.0" encoding="UTF-8"?>

<wx:weather city="Berkeley" state="CA" country="USA"

>

<wx:temperatures units="F">

<wx:temperature type="high">76</wx:temperature>

<wx:temperature type="low">56</wx:temperature>

<wx:temperature type="current">56</wx:temperature>

</wx:temperatures>

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 WEB 服务

**341**

<wx:wind units="MPH">

<wx:direction units="compass">ENE</wx:direction>

<wx:speed>5</wx:speed></wx:wind>

<wx:precipitation/>

<wx:text>

<wx:when time="today">

Mostly cloudy in the morning then becoming mostly sunny.

Patchy fog in the morning. Highs in the 70s to lower 80s.

Afternoon seabreeze 10 to 20 mph.

</wx:when>

<wx:when time="tonight">

Mostly clear except areas of low clouds and fog developing overnight. Lows in the mid to upper 50s. Evening seabreeze 10 to 20 mph.

</wx:when>

</wx:text>

</wx:weather>

XML 文档提供了不同级别的有效性；*格式良好*的文档是指满足 XML 自身语法要求的文档，而*有效*的文档则满足由用户或文档语义的其他 XML 表示（如 XML 模式或 XML 文档类型定义 (DTD)）所施加的额外语义约束。在实践中，当为运行在支持 Java ME 的设备上的轻量级客户端使用 XML 时，您将创建和操作格式良好的 XML 内容，而将真正的验证工作留给服务器以及您解析 XML 本身的方式。

■**注意** 近年来，XML 已被广泛采用作为数据表示挑战的万能解决方案，而 YAML 和 JSON 是其用于基于 Web 编程的亲密伙伴。然而，基于二进制的协议（如无线应用协议 (WAP) 二进制 XML (WBXML，W3C 在 http://www.w3.org/TR/wbxml/ 详述的 XML 二进制版本)）和基于标签的表示法仍有其用武之地，它们提供了更紧凑的数据表示，尤其适用于在较慢网络上运行或对数据传输收取高额费用的应用程序。

**探索 Java ME 中用于 Web 服务的 XML 支持**

从您的 Java ME 应用程序中使用 Web 服务通常涉及以下步骤：**1.** 您的应用程序呈现一个用户界面。

**2.** 作为用户或应用程序执行的某个操作的结果，应用程序必须向远程 Web 服务发出一个 Web 服务请求。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**342**

第 13 章 ■ 访问 WEB 服务

**3.** 应用程序生成一个表示 Web 服务请求的 XML 文档（通常称为请求数据的*编组*）。

**4.** 应用程序使用 HTTP，通过 POST 请求将 XML 文档传输到远程 Web 服务。

**5.** 应用程序使用 HTTP，从远程 Web 服务接收一个响应 XML 文档。

**6.** 应用程序解析生成的 XML 文档，以获取应用程序或用户感兴趣的值（通常称为响应的*解组*）。

**7.** 应用程序使用从解析生成的 XML 文档中获得的数据。

因此，Web 服务为 Java ME 开发人员提出了三个常见的编程任务，这些任务可以通过添加新的 API 在多个应用程序中进行泛化：

•   使用 HTTP 与远程服务器交互（如第 12 章所述，GCF 以及 `HttpConnection` 和 `HttpsConnection` 处理此问题）
•   从 Java 对象生成 XML 文档——通常表示为 `String` 的实例
•   解析 XML 文档以获取值，并将这些值放入一个或多个 Java 对象的字段中

GCF 很好地解决了第一个问题；此外，还有对在 J2ME Web 服务规范中封装 GCF 功能的支持，我将在本章后面的“介绍 J2ME Web 服务规范”一节中详细介绍。

Java ME 中的 XML 生成——第二个问题——通常采用两种形式之一：使用模板组件和 `StringBuffer` 类的实例进行简单拼接，或者使用支持 SOA 的 J2ME Web 服务规范。我将在本章的后续部分讨论这些内容。您可以通过两种方式解决第三个问题——解析 XML：要么依赖可选的 Web 服务规范，要么在您的应用程序中包含一个轻量级解析器，如 `kXML`。

XML 解析器有三种类型：*DOM* 解析器、*推送*解析器和*拉取*解析器。DOM 解析器解析整个文档并返回一个文档树的实例，允许您使用其方法查询树中的元素；它们在实现和表示文档树方面通常代价高昂，并且通常不在 Java ME 环境中使用。推送解析器使用 XML 事件（例如标签的定义）调用客户端接口的方法；本质上，它们将 XML 中的数据“推送”到为解析具有特定标签的 XML 而编写的特定解析器类。拉取解析器的工作方式相反；扫描 XML 的代码调用 XML 解析器的方法来获取单个标签和其他信息。正如您在本章后续部分所见，推送和拉取模型都可用于 Java ME，选择哪种主要取决于您要使用的解析器的可用性。在实践中，我认为可以公平地说，拉取解析器可能比推送解析器使用稍少的内存；然而，给定其中一种，您可以轻松地编写另一种（如我将在本章后面所示），因此它们本质上是等效的，只是名称和与它们交互的代码结构不同。

**在 Java ME 应用程序中生成 XML**

在 Java ME 应用程序中生成 XML 是一项简单但繁琐的任务，使用 `StringBuffer` 类即可。您只需逐个标签地构建 XML 文档，使用编译时字符串作为标签和应用程序对象的值作为标签和属性值。清单 13-7 显示了 `WeatherWidget` 的 `Location` 类的修订版本，它支持诸如清单 13-4 中的 XML 文档可能包含的额外天气信息，包括一个 `toXml` 方法，该方法创建 `Location` 类的 XML 表示。

**清单 13-7.** *可以生成其值的 XML 表示的 Location 类版本*

package com.apress.rischpater.weatherwidget;

import javax.microedition.rms.*;

import java.io.*;

public class Location {

private final static int FIELD_VERSION = 1;

private final static int FIELD_CITY = 2;

private final static int FIELD_FORECAST = 3;

private final static int FIELD_TEMP = 4;

private final static int FIELD_WINDSPEED = 5;

private final static int FIELD_WINDDIR = 6;

private final static int FIELD_PRECIP = 7;

private final static int FIELD_STATE = 8;

private final static int FIELD_COUNTRY = 9;

private final static int FIELD_PRECIP_PROB = 10;

private final static int FIELD_PRECIP_TYPE = 11;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**344**

第 13 章 ■ 访问 WEB 服务

private final static String XML_PREAMBLE =



"<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";  
private final static String XML_START_OPEN = "<";  
private final static String XML_START_EMPTYOPEN = "<";  
private final static String XML_START_CLOSE="</";  
private final static String XML_END_OPEN = ">";  
private final static String XML_END_EMPTYOPEN = "/>";  
private final static String XML_END_CLOSE = ">";  
private final static String XML_ATTR_IS = "=";  
private final static String XML_ATTR_QUOTE = "\"";  
private final static String XML_NEWLINE="\n";  
public final static String XML_TAG_WEATHER="weather";  
public final static String XML_ATTR_CITY="city";  
public final static String XML_ATTR_STATE="state";  
public final static String XML_ATTR_COUNTRY="country";  
public final static String XML_TAG_TEMPS="temperatures";  
public final static String XML_ATTR_UNITS="units";  
public final static String XML_TAG_TEMP="temperature";  
public final static String XML_ATTR_TYPE="type";  
public final static String XML_TAG_WIND="wind";  
public final static String XML_TAG_DIRECTION="direction";  
public final static String XML_TAG_SPEED="speed";  
public final static String XML_TAG_PRECIP="precipitation";  
public final static String XML_ATTR_PROB="probability";  
public final static String XML_TAG_TEXT="text";  
public final static String XML_TAG_WHEN="when";  
public final static String XML_ATTR_TIME="time";  
private final static String XML_ATTR_WTIME=" time=\"today\"";  
private final static String XML_ATTR_TUNITS=" units=\"F\"";  
private final static String XML_ATTR_WUNITS=" units=\"MPH\"";  
private final static String XML_ATTR_PUNITS=" units=\"in\"";  
public final static int NO_ID = -1;  

private final static int version = 1;  

private String city, state, country;  

private String forecast;  

private String temp;  

private String windSpeed;  

private String windDirection;  

private String precipitation, precipitationProb, precipitationType;  
private int recordid;  

[www.it-ebooks.info](http://www.it-ebooks.info/)  

第 13 章 ■ 访问 Web 服务  

**345**  

/** 创建一个新的 Location 实例 */  
public Location() {  
    recordid = NO_ID;  
}  

public Location(String l) {  
    setLocation(l);  
    recordid = NO_ID;  
}  

public Location(byte[] b) {  
    fromBytes(b);  
    recordid = NO_ID;  
}  

public Location(byte[] b, int id) {  
    fromBytes(b);  
    recordid = id;  
}  

public String getLocation() {  
    StringBuffer result = new StringBuffer();  
    if (city!=null) {  
        result.append(city);  
    }  
    result.append(",");  
    if (state!=null) {  
        result.append(state);  
    }  
    result.append(",");  
    if (country!=null) {  
        result.append(country);  
    }  
    return result.toString();  
}  

public String getCity() {  
    if(city != null)  
        return city;  
    else  
        return "";  
}  

[www.it-ebooks.info](http://www.it-ebooks.info/)  

**346**  

第 13 章 ■ 访问 Web 服务  

public String getState() {  
    if(state != null)  
        return state;  
    else  
        return "";  
}  

public String getCountry() {  
    if(country != null)  
        return country;  
    else  
        return "";  
}  

public void setLocation(String l) {  
    int cityStart, stateStart, countryStart;  
    cityStart = 0;  
    stateStart = l.indexOf(",");  
    if (stateStart==-1) {  
        city = l;  
        state="";  
        country="";  
        return;  
    }  
    countryStart = l.indexOf(",",stateStart+1);  
    city = l.substring(cityStart,stateStart);  
    if (countryStart==-1)  
    {  
        state = l.substring(stateStart-1);  
        country = "USA";  
        return;  
    }  
    state = l.substring(stateStart+1,countryStart);  
    country = l.substring(countryStart+1);  
}  

public void setLocation(String c, String s, String co) {  
    city = c;  
    state = s;  
    country = co;  
}  

[www.it-ebooks.info](http://www.it-ebooks.info/)  

第 13 章 ■ 访问 Web 服务  

**347**  

public String getWind() {  
    if (windSpeed != null && windDirection != null)  
        return windSpeed + " mph from the " + windDirection;  
    else  
        return "";  
}  

public void setWind(String s, String d) {  
    windSpeed = s;  
    windDirection = d;  
}  

public String getTemperature() {  
    if (temp!=null)  
        return temp;  
    else  
        return "";  
}  

public void setTemperature(String t) {  
    temp = t;  
}  

public String getPrecipitation() {  
    StringBuffer result = new StringBuffer();  
    if (precipitation != null) {  
        result.append(precipitation);  
    } else {  
        result.append("unknown");  
    }  
    result.append("in of ");  
    if (precipitationType != null) {  



result.append(precipitationType);

} else {

result.append("unknown");

}

result.append("(");

if (precipitationProb != null) {

result.append(precipitationProb);

} else {

result.append("unknown");

}

result.append("%)");

return result.toString();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**348**

第 13 章 ■ 访问 Web 服务

public void setPrecipitation(String p, String pp, String pt) {

precipitation = p;

precipitationProb = pp;

precipitationType = pt;

}

public String getForecast() {

if (forecast != null) {

return forecast;

} else {

return "";

}

}

public void setForecast(String f) {

forecast = f;

}

public int getId() {

return recordid;

}

public void setId(int id) {

recordid = id;

}

public byte[] toBytes() {

byte[] b;

ByteArrayOutputStream baos = new ByteArrayOutputStream(); DataOutputStream dos = new DataOutputStream(baos);

// 记录格式为字段标签，后跟每个字段

try {

dos.writeInt(FIELD_VERSION);

dos.writeInt(version);

if (city != null) {

dos.writeInt(FIELD_CITY);

dos.writeUTF(getCity());

}

if (state != null) {

dos.writeInt(FIELD_STATE);

dos.writeUTF(getState());

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**349**

if (country != null) {

dos.writeInt(FIELD_COUNTRY);

dos.writeUTF(getCountry());

}

if (forecast != null)

{

dos.writeInt(FIELD_FORECAST);

dos.writeUTF(getForecast());

}

if (temp != null) {

dos.writeInt(FIELD_TEMP);

dos.writeUTF(getTemperature());

}

if (precipitation != null &&

precipitationProb != null &&

precipitationType != null) {

dos.writeInt(FIELD_PRECIP);

dos.writeUTF(precipitation);

dos.writeInt(FIELD_PRECIP_PROB);

dos.writeUTF(precipitationProb);

dos.writeInt(FIELD_PRECIP_TYPE);

dos.writeUTF(precipitationType);

}

if (windDirection != null && windSpeed != null) {

dos.writeInt(FIELD_WINDDIR);

dos.writeUTF(windDirection);

dos.writeInt(FIELD_WINDSPEED);

dos.writeUTF(windSpeed);

}

}

catch(Exception e) {

return null;

}

// 获取此项的字节数据。

b = baos.toByteArray();

dos = null;

baos = null;

return b;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**350**

第 13 章 ■ 访问 Web 服务

public void fromBytes(byte[] b) {

ByteArrayInputStream bais = new ByteArrayInputStream(b); DataInputStream dis = new DataInputStream(bais);

String dir = null, speed = null;

String c = null, s = null, co = null;

String p = null, pp = null, pt = null;

// 读取每个标签，然后读取每个字段

try

{

while(true) {

int tag = dis.readInt();

switch(tag) {

case FIELD_VERSION:

// 不检查版本；只有一个版本

dis.readInt();

break;

case FIELD_CITY:

c = dis.readUTF();

break;

case FIELD_STATE:

s = dis.readUTF();

break;

case FIELD_COUNTRY:

co = dis.readUTF();

break;

case FIELD_FORECAST:

setForecast(dis.readUTF());

break;

case

FIELD_TEMP:

setTemperature(dis.readUTF());

break;

case FIELD_PRECIP:

p = dis.readUTF();

break;

case FIELD_PRECIP_PROB:

pp = dis.readUTF();

break;

case FIELD_PRECIP_TYPE:

pt = dis.readUTF();

break;

case FIELD_WINDDIR:

dir = dis.readUTF();

break;

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**351**

case FIELD_WINDSPEED:

speed = dis.readUTF();

break;

}

}

}

catch (Exception e) {}

finally {

setLocation(c, s, co);

setPrecipitation(p, pp, pt);

setWind(speed, dir);

try {

dis.close();

bais.close();

}

catch (Exception e) {}

}

dis = null;

bais = null;

}

public String toXml() {

StringBuffer xmlBuffer = new StringBuffer();

xmlBuffer.append(XML_PREAMBLE);

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_WEATHER);

if (city != null) {

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_CITY);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(city);

xmlBuffer.append(XML_ATTR_QUOTE);

}

if (state != null) {

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_STATE);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(state);

xmlBuffer.append(XML_ATTR_QUOTE);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**352**

第 13 章 ■ 访问 Web 服务

if (country != null) {

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_COUNTRY);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append(XML_ATTR_QUOTE);



xmlBuffer.append(country);

xmlBuffer.append(XML_ATTR_QUOTE);

}

if (forecast != null ||

temp != null ||

(precipitation != null &&

precipitationProb != null &&

precipitationType != null) ||

(windSpeed != null && windDirection != null)) {

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(XML_NEWLINE);

if (temp != null) {

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_TEMPS);

xmlBuffer.append(XML_ATTR_TUNITS);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_TEMP);

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_TYPE);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append("\"current\"");

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(temp);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_TEMP);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_TEMPS);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

}

if (windSpeed != null && windDirection != null) {

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_WIND);

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**353**

xmlBuffer.append(XML_ATTR_WUNITS);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_SPEED);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(windSpeed);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_SPEED);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_DIRECTION);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(windDirection);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_DIRECTION);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_WIND);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

}

if (precipitation != null &&

precipitationProb != null &&

precipitationType != null) {

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_PRECIP);

xmlBuffer.append(XML_ATTR_PUNITS);

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_PROB);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(precipitationProb);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(" ");

xmlBuffer.append(XML_ATTR_TYPE);

xmlBuffer.append(XML_ATTR_IS);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(precipitationType);

xmlBuffer.append(XML_ATTR_QUOTE);

xmlBuffer.append(XML_END_OPEN);

[www.it-ebooks.info](http://www.it-ebooks.info/)

**354**

第 13 章 ■ 访问 Web 服务

xmlBuffer.append(precipitation);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_PRECIP);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

}

if (forecast != null) {

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_TEXT);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_OPEN);

xmlBuffer.append(XML_TAG_WHEN);

xmlBuffer.append(XML_ATTR_WTIME);

xmlBuffer.append(XML_END_OPEN);

xmlBuffer.append(forecast);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_WHEN);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_TEXT);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

}

xmlBuffer.append(XML_START_CLOSE);

xmlBuffer.append(XML_TAG_WEATHER);

xmlBuffer.append(XML_END_CLOSE);

xmlBuffer.append(XML_NEWLINE);

} else {

xmlBuffer.append(XML_END_EMPTYOPEN);

}

return xmlBuffer.toString();

}

public void fromXml(String xml) {

/* 如清单 13-9 和 13-13 所示 */

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**355**

代码以对象现在存储的各种字段的静态定义开始，同时还有代码在构建其 XML 表示时使用的各种 XML 片段的静态 String 声明。该类的大部分内容与第 6 章保持一致，只是增加了新字段以及这些字段的访问器/修改器方法；`toBytes` 和 `fromBytes` 方法已更改，以使用我在第 6 章讨论记录存储时介绍的相同标签-值系统来序列化新字段。`toXml` 方法通过附加序言、静态 XML 片段以及每个成员变量的值，在 `StringBuffer` 实例中构建 XML。这种技术虽然粗糙，但效果很好；根据你想要表示为 XML 的对象类型，你通常可以稍微简化代码，例如当你拥有相似对象的向量时。

如果你使用 Java 已有相当长的时间，你可能会查看 `toXml` 方法，并倾向于使用 Java 反射做类似的事情，创建一个通用的 XML 编码器对象，该对象接收一个对象实例，查询该对象的成员字段，并根据每个字段的类型和内容编写 XML。这对于 Java SE 来说是一种极好的方法，因为 Java SE 提供了反射，但不幸的是，这种方法在 Java ME 设备上效果不佳，因为正如我在第 2 章中讨论的，CLDC 缺乏对反射的支持。如果你的代码注定只在 CDC 上运行，那么面向反射的方法可能很有意义。

**J2ME Web 服务规范简介**

J2ME Web 服务规范（如其名称所示，其开发早于 Java ME 平台，并且是 CLDC 和 CDC 设备的一个选项）由 JSR 172 描述，它为 Java ME 平台提供了两个补充：访问基于远程 SOAP 的 Web 服务，以及一个推送 XML 解析器。这些补充——统称为可选的 Web 服务规范——都是可选的；实现 JSR 172 的平台可以提供基于 SOAP 的 Web 服务支持、推送 XML 解析器，或两者都提供。如今许多高级移动设备都包含了 JSR 172 的这两个部分，尽管它并非在所有地方都可用；与其他可选规范一样，在产品的早期开发阶段，确认目标设备上指定 API 的可用性非常重要。

基于 SOAP 的 Web 服务支持的实现是 Java API for XML-based RPC (JAX-RPC) 的一个子集，JAX-RPC 是基于 Web 的 RPC 服务的 Java 接口。该子集提供了 JAX-RPC 的客户端实现，包括 boolean、byte、short、int、long、String、数组和复杂类型在 Java 和 SOAP 之间的编组和解组，以及客户端对 RPC 调用的基于静态桩的调用。由于平台限制，它并非 JAX-RPC 的完整实现；实现该规范的设备不能提供服务端点或使用可扩展的类型映射。

使用 JAX-RPC 支持的应用程序必须遵循以下步骤：**1.** 在编译时，根据服务的 WSDL 描述定义并生成一个桩。

**2.** 在运行时，实例化该桩的一个实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**356**

第 13 章 ■ 访问 Web 服务

**3.** 在运行时，调用桩上对应于服务端点操作实现的方法。

**4.** 将生成的桩与 Java ME 客户端应用程序打包在一起。

这里的工作与你在第 11 章中描述的使用 Java RMI 可选支持时需要做的工作类似，因为 JAX-RPC 接口使用相同的总体架构，依赖本地桩对象为远程服务提供者提供接口。



虽然 JAX-RPC 接口在对接某些现有遗留服务时为你提供了一个重要选项，但越来越多的 Web 服务正在转向或完全实现 RESTful 方法，在这种方法中，SOAP 相对繁重的语义被更具描述性的 XML 用法所取代。从某种程度上说，这正让 XML 回归其本源，即用于提供数据对象内容的人类和机器可读的描述。就此而言，JSR 172 的第二个可选组件对你来说变得重要：XML 解析器。

JSR 172 定义的 XML 解析器是 JSR 63 中定义的 XML 解析器的一个严格子集。它规定：

• 实现不得为 XML 简单 API（SAX）解析提供任何支持。

• 实现不得为 DOM 提供任何支持，因为 DOM 在实现大小和运行时内存占用方面过于庞大，不适合在 Java ME 设备上使用。

• 实现不得支持 XML 转换（XSLT）。

• 不要求实现支持 XML 验证，因为验证在处理能力和内存方面代价高昂。

• 实现必须支持预定义的 XML 实体（例如 `&`），但不要求支持可选的 XML 实体。

• 实现必须支持 XML 命名空间。

• 实现必须同时支持 UTF-8 和 UTF-16 字符编码。

可选的解析器实现包含三个包：`javax.xml.parsers`、`org.xml.sax` 和 `org.xml.sax.helpers`。第一个包包含平台自身的解析器实现，而其他包则为解析器提供实用工具。解析器实现必须实现 SAX2 解析接口，正如这些包的命名空间所反映的那样。

SAX 为解析 XML 提供了一个直接的接口，解析器从流中消费 XML，并在解析过程中生成代表特定 XML 元素、注释和其他项的事件。在使用 SAX 解析器时，你需要提供一个事件处理器，该处理器接收 SAX 解析器发送的事件，并根据这些事件构建结果对象。你的事件处理器实现由 `org.xml.sax.helpers.DefaultHandler` 类定义的接口，并重写其方法来处理单个 SAX 事件。

你可以选择处理的一些事件包括以下内容：

• `startDocument`：表示 XML 文档的开始。
• `endDocument`：表示 XML 文档的结束。
• `startElement`：表示元素的开始，并传入元素名称、命名空间和属性。属性通过 `Attributes` 类的实例传递，该类提供了元素所有属性的集合，方便你查找特定属性的值。
• `endElement`：表示元素的结束。
• `characters`：获取元素内的字符数据。

回到 WeatherWidget 示例，清单 13-8 展示了 `LocationParserHandler` 类——它是 `DefaultHandler` 的一个子类，用于解析表示带有相关预报的 `Location` 对象的 XML 文档。

**清单 13-8.** *LocationParserHandler SAX 处理器*
```java
package com.apress.rischpater.weatherwidget;

import javax.xml.parsers.*;
import org.xml.sax.*;
import org.xml.sax.helpers.*;

public class LocationParserHandler extends DefaultHandler {
    private Location location;
    private boolean setForecast;
    private boolean hasForecast;
    private boolean setTemp;
    private String precipitation, precipType, precipProb;
    private String windSpeed, windDirection;
    private StringBuffer buffer;

    public LocationParserHandler(Location l) {
        location = l;
    }

    public Location getLocation() {
        return location;
    }

    public void startDocument() {
        if (location == null)
            location = new Location();
        setForecast = false;
        hasForecast = false;
        setTemp = false;
    }
}
```



public void startElement(String uri, String localName,

String qName, Attributes attributes)

throws SAXException {

buffer = new StringBuffer();

if (qName.equals(Location.XML_TAG_WEATHER) == 0) {

String c = attributes.getValue(Location.XML_ATTR_CITY); String s = attributes.getValue(Location.XML_ATTR_STATE); String co = attributes.getValue(Location.XML_ATTR_COUNTRY); location.setLocation(c,s,co);

hasForecast = false;

}

if (qName.equals(Location.XML_TAG_TEMPS) == 0) {

String u = attributes.getValue(Location.XML_ATTR_UNITS); if (u != null && u.equals("F") != 0)

throw new SAXException("Invalid temperature units"); hasForecast = false;

}

if

(qName.equals(Location.XML_TAG_TEMP) == 0) {

String t = attributes.getValue(Location.XML_ATTR_TYPE); if (t != null && t.equals("current") != 0) setTemp = true;

else

setTemp = false;

hasForecast = false;

}

if (qName.equals(Location.XML_TAG_WIND) == 0) {

String u = attributes.getValue(Location.XML_ATTR_UNITS); if (u != null && u.equals("MPH") != 0) throw new SAXException("Invalid temperature units"); hasForecast = false;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**359**

if (qName.equals(Location.XML_TAG_PRECIP) == 0)

{

String u = attributes.getValue(Location.XML_ATTR_UNITS); if (u != null && u.equals("in") != 0) throw new SAXException("Invalid precipitation units"); precipProb = attributes.getValue(Location.XML_ATTR_PROB); precipType = attributes.getValue(Location.XML_ATTR_TYPE); hasForecast = false;

}

if (qName.equals(Location.XML_TAG_TEXT) == 0)

hasForecast = true;

if (qName.equals(Location.XML_TAG_WHEN) == 0 && hasForecast) {

String w = attributes.getValue(Location.XML_ATTR_TIME); if (w != null && w.equals("now") == 0) setForecast = true;

else

setForecast = false;

}

}

public void characters(char[] ch, int start, int length) {

if (buffer != null)

buffer.append(ch, start, length);

}

public void endElement(String uri, String localName, String qName) {

if (qName.equals(Location.XML_TAG_SPEED) == 0)

windSpeed = buffer.toString();

if

(qName.equals(Location.XML_TAG_DIRECTION) == 0)

windDirection = buffer.toString();

if (qName.equals(Location.XML_TAG_WIND) == 0)

location.setWind(windSpeed, windDirection);

if (qName.equals(Location.XML_TAG_PRECIP) == 0) {

precipitation = buffer.toString();

location.setPrecipitation(precipitation,precipProb,precipType);

}

if (qName.equals(Location.XML_TAG_TEMP) == 0 && setTemp) location.setTemperature(buffer.toString());

if (qName.equals(Location.XML_TAG_WHEN) == 0 && setForecast) location.setForecast(buffer.toString());

[www.it-ebooks.info](http://www.it-ebooks.info/)

**360**

第 13 章 ■ 访问 Web 服务

buffer = null;

}

public void endDocument() {

}

}

用于解析 XML 格式的 Location 对象的处理器，在解析操作期间使用一个 Location 实例和几个私有变量来跟踪对象的状态，并在成员变量 location 中构建最终的 Location 对象。其中的关键是 StringBuffer buffer，处理器用它来构建任何字符数据的字符串表示形式，例如当前温度或天气预报。

当 SAX 解析器处理 XML 时（我将在清单 13-9 中向你展示如何设置和启动该过程），处理器接收到的第一个事件是 startDocument。处理该事件的方法只是设置处理器的内部状态，如果客户端使用了处理器的默认构造函数（未提供 Location 对象），则创建一个新的 Location 来保存解析结果。

每当 SAX 解析器遇到一个新元素的开始，例如 `<weather>` 或 `<temperature>`，它就会调用 startElement。该方法接收元素的完整名称（在 qName 中）、命名空间 URL（在 uri 中）中的名称（在 localName 中）；通常你会希望检查 qName，除非你正在解析具有多个命名空间的 XML。清单 13-8 正是这样做的，它设置处理器的状态以接受刚刚遇到的标签的数据。在某些情况下，例如解析



<weather>标签的处理程序可以通过直接操作各个属性来立即完成所有工作；你可以使用属性的`getValue`方法获取其值，或使用`getType`方法获取其类型。在其他情况下，代码会利用属性值对传入数据进行运行时检查——例如，拒绝使用非英制单位的天气数据。

一旦 SAX 解析器调用了`startElement`，接下来它会根据正在解析的元素是否包含字符数据，调用`characters`或`endElement`。`characters`元素接收一个包含解析器已读取字符的字节数组；对于单个实体，它可能被多次调用，因此你需要自行缓冲结果。

■**注意** 不要忽略`characters`的`start`和`length`参数！SAX 解析器自行决定如何管理其内部字符数组，而你在传入的字符数组中获取的内容，在`start`和`length`共同指定的范围之外可能是无效数据。

如果说`startElement`是处理程序准备处理传入元素的地方，那么`endElement`显然是它应该将元素所代表的数据暂存起来的地方。此处的`endElement`正是这样做的，它根据指定的元素类型决定将累积的字符数据存储到何处，并在将缓冲区内容存储到`location`的相应字段后，清空用于累积数据的`StringBuffer`。

当 SAX 解析器遇到文档结束时，它会调用处理程序的`endDocument`；这是更新用于存储已解析 XML 数据的变量的另一个机会。`LocationParserHandler`方法不需要这样做，因为它会在整个解析操作中持续更新`location`，因此其`endDocument`是一个空方法。

■**注意** 如果你的类没有对 SAX 事件执行任何操作，则无需提供这些方法；在清单 13-8 中，我为了清晰和完整性包含了`endDocument`，但它并非必需。

使用 SAX 解析器很简单：实例化解析器和位置处理程序，然后将一个流连同解析器处理程序一起提供给解析器进行解析。清单 13-9 展示了`Location`类的`fromXml`方法中的这一过程，该方法接收一个 XML 文档，并使用 SAX 解析器将其字段的值设置为文档中指定的值。

**清单 13-9.** *使用 JSR 172 提供的 SAX 解析器*

```java
package com.apress.rischpater.weatherwidget;

import javax.microedition.rms.*;
import java.io.*;
import javax.xml.parsers.*;
import org.xml.sax.*;
import org.xml.sax.helpers.*;

public class Location {
    /* 清单 13-7 中的其他方法 */
    public void fromXml(String xml) {
        SAXParser parser = null;
        LocationParserHandler handler = new LocationParserHandler(this);
        byte[] xmlBytes;
        ByteArrayInputStream bis;
        try {
            SAXParserFactory f = SAXParserFactory.newInstance();
            parser = f.newSAXParser();
        }
        catch(Exception e) { return; };
        xmlBytes = xml.getBytes();
        bis = new ByteArrayInputStream(xmlBytes);
        try {
            parser.parse(bis, handler);
        }
        catch(Exception e){}
        finally {
            try {
                bis.close();
            }
            catch(Exception e) {}
        }
    }
}
```

`fromXml`方法创建一个初始的`LocationParserHandler`，并将其链接到当前的`Location`实例。在底层，平台可能提供不同的 SAX 解析器实现，因此使用`SAXParserFactory`获取解析器实例非常重要；具体做法是创建工厂实例，然后调用该实例的`newSAXParser`方法。解析器以`InputStream`的形式接收数据；将传入的 XML 转换为字节数组，并与`ByteArrayInputStream`类一起使用，即可为解析器的`parse`方法提供所需的输入流。仅此而已！

所有这些代码都假设一个 RESTful Web 服务，该服务根据位置提供包含天气数据的 XML 文档；清单 13-10 展示了一个实现此功能的`WeatherFetcher`版本，它基于你在前一章学到的知识构建。

**清单 13-10.** *WeatherFetcher 的 RESTful 实现*

```java
package com.apress.rischpater.weatherwidget;

import java.io.*;
import javax.microedition.io.*;

public class WeatherFetcher implements Runnable {
    private static String url = "http://www.noplace.com/location/";
    private Location location;
    private boolean cancelled;
    private WeatherWidget app;

    /** 创建 WeatherFetcher 的新实例 */
    public WeatherFetcher(Location l, WeatherWidget a) {
        location = l;
        app = a;
        cancelled = false;
        if (l!=null && a!=null) {
            Thread thread = new Thread(this);
            thread.start();
        }
    }

    public void cancel() {
        cancelled = true;
    }

    private static String urlEncode(String s) {
        if (s!=null) {
            StringBuffer tmp = new StringBuffer();
            int i=0;
            try {
                while (true) {
                    int b = (int)s.charAt(i++);
                    if ((b>=0x30 && b<=0x39) ||
                        (b>=0x41 && b<=0x5A) ||
                        (b>=0x61 && b<=0x7A)) {
                        tmp.append((char)b);
                    } else {
                        tmp.append("%");
                        if (b <= 0xf) tmp.append("0");
                        tmp.append(Integer.toHexString(b));
                    }
                }
            }
            catch (Exception e) {}
            return tmp.toString();
        }
        return null;
    }

    private String requestEncode() {
        StringBuffer result = new StringBuffer();
        result.append(url);
        result.append(urlEncode(location.getLocation()));
        return result.toString();
    }

    public void run() {
        String requestUrl;
        String response = "";
        HttpConnection hc = null;
        InputStream in = null;
        requestUrl = requestEncode();
        try {
            hc = (HttpConnection)Connector.open(requestUrl);
            hc.setRequestMethod(HttpConnection.GET);
            in = hc.openInputStream();
            int length = (int)hc.getLength();
            byte[] data = new byte[length];
            in.read(data);
            response = new String(data);
        }
        catch(Exception e){}
        finally {
            try {
                if (in!=null) in.close();
                if (hc!=null) hc.close();
            }
            catch(Exception e) {}
        }
        if (!cancelled) {
            location.fromXml(response);
            app.update();
        }
    }
}
```

回顾一下，在 REST 服务中，URL 代表对象，HTTP 方法代表作用于这些对象的动词。因此，天气服务的 URL 实际上是特定对象的前缀；访问加利福尼亚州伯克利市的天气 Web 服务时，应用程序可能会生成以下 URL：

`http://www.noplace.com/location/Berkeley%2C%20CA%2C%20USA`

末尾斜杠后的字符串是唯一位置的 URL 安全编码（本例中为“Berkeley, CA, USA”）。`WeatherFetcher`类在`run`方法开始时完成此映射，它使用该类静态定义的服务 URL 前缀及其`requestEncode`方法创建 URL。接着，它创建一个使用`GET`方法的`HttpConnection`，并将响应读入字符串，供`Location`实例解析。

■**提示** 尽管此示例使用更多 RAM 将整个文档读入内存再解析，但调试起来更容易，因为在开发过程中，Web 服务客户端获取的 XML 可以写入调试文件或其他位置。我本可以轻松地将`Location`类设计为接受`InputStream`作为`fromXml`参数，并将`HttpConnection`的`InputStream`实例直接传递给`Location`实例进行解析，从而节省一些内存。当你需要解析大型 XML 文档时，可以考虑这种方法，因为底层的 SAX 解析器在解析操作期间可能不需要将整个文档读入内存。

**介绍 kXML 解析器**



正如我在上一节开头所述，JSR 172 提供的 XML 解析器是可选的（实际上，*整个* JSR 172 都是可选的！），你很可能会遇到不提供 SAX 解析器的设备。幸运的是，有一个轻量级的替代方案——kXML，它可以在 http://kxml.sourceforge.net 上以宽松的许可证获取。要使用该解析器，请下载实现 kXML 解析器的 JAR 文件，并通过属性检查器将其包含在你的 NetBeans 项目中（见图 13-3）。

kXML 包现已开始其第三次迭代；在我撰写本文时，kXML 版本 1 已弃用，而 kXML 版本 2 是团队开发版本 3 期间人们应使用的版本。kXML 版本 2 在 `org.kxml2.io` 包中通过 `KXmlParser` 类实现了一个经典的拉解析器。使用此类，你可以通过以下各种 `next` 方法从包含 XML 的流中拉取项目：

• `next`：返回流中下一个 XML 令牌的类型
• `nextTag`：返回流中下一个标签的名称
• `nextText`：返回流中下一个 CDATA 区域的内容
• `nextToken`：返回流中的下一个 XML 令牌

这种方法——从源流中拉取标签，而不是将事件发送到新类——可能会略微减少你必须编写和交付的代码量，尽管许多开发者可能会觉得它不如你在上一节中看到的 SAX 推解析器清晰。正如你很快将在清单 13-12 中看到的，围绕拉解析器构建一个看起来非常像推解析器的包装器是很容易的。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00023.png)

**366**

第 13 章 ■ 访问 Web 服务

**图 13-3.** *向你的 NetBeans 项目添加外部库* 要使用 kXML 解析器，请实例化它，向其提供流，并在调用 `next` 时循环，如清单 13-11 所示。

**清单 13-11.** *使用 kXML 解析器*

```java
public void parse(ByteArrayInputStream in) {
    try {
        InputStreamReader reader = new InputStreamReader(in);
        KXmlParser parser = new KXmlParser();
        boolean keepParsing = true;
        parser.setInput(reader);
        while(keepParsing) {
            int type = parser.next();
            …处理标签…
        }
    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**367**

```java
    catch(Exception e) {
        System.out.println(e.toString());
        e.printStackTrace();
    }
}
```

清单 13-12 展示了 `LocationParser` 类的实际应用，该类使用 kXML 解析器将 XML 解析为 `Location` 对象。

**清单 13-12.** *使用 kXML 解析器*

```java
package com.apress.rischpater.weatherwidget;

import org.kxml2.io.*;
import java.io.*;

public class LocationParser {
    private Location location;
    private boolean setForecast;
    private boolean hasForecast;
    private boolean setTemp;
    private String precipitation, precipType, precipProb;
    private String windSpeed, windDirection;
    private StringBuffer buffer;

    public LocationParser() {
        location = new Location();
    }

    public LocationParser(Location l) {
        location = l;
    }

    public void parse(ByteArrayInputStream in) {
        try {
            InputStreamReader reader = new InputStreamReader(in);
            KXmlParser parser = new KXmlParser();
            boolean keepParsing = true;
            parser.setInput(reader);
            while(keepParsing) {
                int type = parser.next();
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**368**

第 13 章 ■ 访问 Web 服务

```java
                switch(type) {
                    case KXmlParser.START_DOCUMENT:
                        startDocument(parser);
                        break;
                    case KXmlParser.START_TAG:
                        startElement(parser);
                        break;
                    case KXmlParser.END_TAG:
                        endElement(parser);
                        break;
                    case KXmlParser.TEXT:
                        characters(parser);
                        break;
                    case KXmlParser.END_DOCUMENT:
                        endDocument(parser);
                        keepParsing = false;
                        break;
                }
            }
        }
        catch(Exception e) {}
    }

    public Location getLocation() {
        return location;
    }

    public void startDocument(KXmlParser parser) {
        if ( location == null )
            location = new Location();
        setForecast = false;
        hasForecast = false;
        setTemp = false;
    }

    public void startElement(KXmlParser parser)
        throws Exception {
        String qName = parser.getName();
        buffer = new StringBuffer();
        if ( qName.equals(Location.XML_TAG_WEATHER)) {
```



String c = parser.getAttributeValue(null,Location.XML_ATTR_CITY); String s = parser.getAttributeValue(null,Location.XML_ATTR_STATE); String co = parser.getAttributeValue(null,Location.XML_ATTR_COUNTRY); location.setLocation(c,s,co);

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**369**

hasForecast = false;

}

if ( qName.equals(Location.XML_TAG_TEMPS)) {

String u = parser.getAttributeValue(null,Location.XML_ATTR_UNITS); if ( u != null && u.equals("F") != 0) throw new Exception("Invalid temperature units"); hasForecast = false;

}

if ( qName.equals(Location.XML_TAG_TEMP)) {

String t = parser.getAttributeValue(null,Location.XML_ATTR_TYPE); if (t != null && t.equals("current") == 0) {

setTemp = true;

} else {

setTemp = false;

}

hasForecast = false;

}

if ( qName.equals(Location.XML_TAG_WIND)) {

String u = parser.getAttributeValue(null,Location.XML_ATTR_UNITS); if ( u != null && u.equals("MPH") != 0) throw new Exception("Invalid temperature units"); hasForecast = false;

}

if ( qName.equals(Location.XML_TAG_PRECIP))

{

String u = parser.getAttributeValue(null,Location.XML_ATTR_UNITS); if (u != null && u.equals("in") != 0) throw new Exception("Invalid precipitation units"); precipProb = parser.getAttributeValue(null,Location.XML_ATTR_PROB); precipType =

parser.getAttributeValue(null,Location.XML_ATTR_TYPE);

hasForecast = false;

}

if (qName.equals(Location.XML_TAG_TEXT)) {

hasForecast = true;

}

if (qName.equals(Location.XML_TAG_WHEN) && hasForecast) {

String w = parser.getAttributeValue(null,Location.XML_ATTR_TIME); if (w != null && w.equals("now") == 0) {

setForecast = true;

} else {

setForecast = false;

}

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**370**

第 13 章 ■ 访问 Web 服务

public void characters(KXmlParser parser) {

if (buffer != null)

buffer.append(parser.getText());

}

public void endElement(KXmlParser parser) {

String qName = parser.getName();

if (qName.equals(Location.XML_TAG_SPEED) == 0) {

windSpeed = buffer.toString();

}

if (qName.equals(Location.XML_TAG_DIRECTION) == 0) {

windDirection = buffer.toString();

}

if (qName.equals(Location.XML_TAG_WIND) == 0) {

location.setWind(windSpeed, windDirection);

}

if (qName.equals(Location.XML_TAG_PRECIP) == 0) {

precipitation = buffer.toString();

location.setPrecipitation(precipitation,precipProb,precipType);

}

if (qName.equals(Location.XML_TAG_TEMP) == 0 && setTemp )

{

location.setTemperature(buffer.toString());

}

if (qName.equals(Location.XML_TAG_WHEN) == 0 && setForecast) {

location.setForecast( buffer.toString());

}

}

public void endDocument(KXmlParser parser) {

}

}

`LocationParser` 的代码与 `LocationParserHandler` 非常相似，原因也很充分：它们解析的是同一个 XML。一旦从 XML 解析器获取到事件，只要你能处理这些事件，解析器是拉模式还是推模式就几乎没有区别。与 `LocationParserHandler` 一样，`LocationParser` 在解析文档时，使用一个 `Location` 对象和一些临时变量来存储中间状态。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 访问 Web 服务

**371**

大部分工作都在类的 `parse` 方法中完成，该方法接收一个用于读取 XML 的字节流。它创建一个 `KXmlParser` 实例，设置解析器从由传入字节流派生的 `InputStream` 中读取数据，然后在一个 `while` 循环中开始从解析器读取标签。该循环仅使用一个 `switch/case` 语句，为解析器遇到的 XML 令牌类型生成解析器事件，并调用我从 `LocationParserHandler` 实现中派生的事件处理器。

每个解析器的事件处理器都接收一个 `KXmlParser` 实例，以便它可以向解析器查询关于该事件的更多信息，例如遇到的标签名称（由 `getName` 返回）、属性值（由 `getAttributeValue` 返回），或者解析器在标签内遇到的字符数据（由 `getText` 返回）。



在 WeatherWidget 中使用支持 kXML 的 LocationParser 与使用 JSR 172 SAX 解析器几乎完全相同，如清单 13-13 所示。

**清单 13-13.** *使用 kXML 解析器和 LocationParser 类* package com.apress.rischpater.weatherwidget;

import javax.microedition.rms.*;

import java.io.*;

import javax.xml.parsers.*;

import org.xml.sax.*;

import org.xml.sax.helpers.*;

public class Location {

/* 来自清单 13-7 的其他方法 */

public void fromXml(String xml) {

LocationParser parser = new LocationParser(this);

byte[] xmlBytes;

ByteArrayInputStream bis;

xmlBytes = xml.getBytes();

bis = new ByteArrayInputStream(xmlBytes);

try {

parser.parse(bis);

}

catch(Exception e){}

finally {

try {

bis.close();

}

catch(Exception e) {}

}

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**372**

第 13 章 ■ 访问 Web 服务

fromXml 方法仍然接收一个字符串，但它不再需要获取 SAX 解析器的工厂并创建 SAX 解析器，而只需创建一个 LocationParser 实例，将传入的字符串转换为字节数组流，然后调用 LocationParser 的 parse 方法。

■**注意** kXML 并非唯一适用于 Java ME 应用的 XML 解析器；其他解析器包括 NanoXML 和 Xparse-J。就个人而言，我偏好 kXML，因为它接口简洁、应用广泛、体积小巧且运行稳定，但如果你在寻找其他选择，不妨用 Google 搜索一下建议。

**总结**

与 Web 服务交互是许多 Java ME 应用的关键部分。虽然 Java ME 本身对访问 Web 服务的支持不多，但通过一些努力，你可以弥合这一差距。

Web 服务要求你将数据表示为 XML，以便通过 HTTP 与远程 Web 服务进行交换；虽然你可以使用 GCF 和 HttpConnection 轻松完成数据交换，但处理 XML 则需要多做一点工作。编码 XML 相对直接，通常是通过组合静态定义的字符串和要表示为 XML 的对象中的字段值来构建 XML 文档。你可以使用 StringBuffer 高效地完成此操作，并在完成后调用其 toString 方法以获取 XML 文档。

在需要解析 XML 时，你有多种选择；其中两种最常用的是由可选的 J2ME Web 服务规范（JSR 172）定义的 XML 解析器和 kXML 解析器。

JSR 172 既定义了用于使用 WSDL 和 SOAP 的远程 Web 服务的可选 RMI 风格接口，也定义了一个可选的 SAX 解析器。使用 SAX 解析器，你可以通过向 SAX 解析器提供一个处理器来解析 XML，该处理器处理 SAX 解析器在扫描 XML 文档时生成的事件。

kXML 解析器被打包成一个 JAR 文件，你可以将其包含在你的应用中；它提供了一个简单的拉式解析器，你可以通过遍历文档中的 XML 标记来使用它。你通过使用解析器的方法来完成此遍历，并调用你自己的代码来处理生成的标记。使用 kXML 解析器的一个好方法是让它生成类似于 SAX 解析器的事件。这可以在解析器的文档扫描部分（kXML 解析器和用于获取连续 XML 标记的循环的职责）与解析器的解析部分之间提供合理的抽象层次。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章

使用无线消息 API 进行消息传递

**几**乎所有的 CLDC 设备以及部分 CDC 设备都提供了连接到如今无处不在的全球蜂窝网络的硬件。除了为支持 Java ME 的设备提供无线 IP 连接外，这些网络还通过其他协议（如 SMS 和 MMS）提供无线消息传递。为了使你的应用能够访问这些无线消息协议，Sun Microsystems 及其合作伙伴开发了无线消息 API（WMA），这是一套构建在 GCF 之上的综合性消息接口。

WMA 是 Java ME 设备的一个可选包，最初由 JSR 120 定义，并由 JSR 205 扩展以支持 MMS。

在本章中，我将向你展示如何使用 WMA 发送和接收 SMS 和 MMS 消息。首先，我将讨论无线消息服务，以及它们与你职业生涯中可能遇到的典型 IP 连接功能有何不同。接下来，我将展示 WMA 的组织方式，并介绍 WMA 提供的新类。之后，我将向你展示如何使用 Java ME 推送注册表——这是开发和部署无线消息应用时的一个重要组件。最后，我将通过一个具体示例，向你展示如何在你的应用中使用 WMA 发送和接收 SMS 消息。

**介绍无线消息服务**

无线消息协议的出现早于 Java ME 和基于蜂窝网络的 IP；第一代数字网络通过用于呼叫控制的寻呼信道集成了 SMS。

如今，各种无线消息服务——SMS、小区广播（SMS-CB）和 MMS——已成为无数应用（用于消息传递、高级内容（如铃声、壁纸和应用）交付、游戏及其他用途）的承载网络。虽然许多应用直接使用这些协议，用户通过其无线设备上的原生消息应用发起或接收消息，但越来越多的应用使用 Java ME 作为消息协议的前端。这提供了 **373**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**374**

第 14 章 ■ 使用无线消息 API 进行消息传递

更好的用户体验，因为它可以验证数据输入并提供特定于应用的 UI。例如，一个专用的 Java ME 消息应用可以为消息对话提供线程化界面，而不是使用大多数原生无线终端消息应用所采用的电子邮件收件箱消息隐喻。

**介绍短消息服务**

SMS 是当今广泛使用的无线消息协议中最古老的一种。该协议最初于 1985 年由全球移动通信系统（GSM）标准定义，如今估计有 24 亿活跃用户使用，每年交换数万亿条消息。

所有主要的地面蜂窝网络现在都支持某种形式的（通常是可互操作的）SMS，许多卫星通信网络也支持。

SMS 消息由在相同或不同网络上的用户之间发送的短数据包（通常为压缩成 140 字节的 160 个字符）组成。SMS 消息由 SMS 中心（SMSC）路由，并被称为*移动台发起*（MO）或*移动台终止*（MT）。在用户之间发送时，消息通常是文本形式，尽管该协议也支持二进制负载。SMS 支持的二进制格式可用于发送和接收铃声和图片，或连接多条消息以便发送更长的文本或二进制消息。在大多数网络上，你可以将 SMS 消息发送到特定端口，以便不同的应用可以在专用端口上通信，这与 TCP/IP 网络非常相似。SMS 网络高度可靠，但不保证消息送达；例如，收件人可能关闭了她的无线终端，并且 SMS 消息可能无法在其终端上送达，直到网络判定该消息过旧而无法送达并将其完全丢弃。

在当今的大多数蜂窝网络上，SMS *聚合器*为你提供了从 Web 上的服务器发送和接收 SMS 消息的选项。使用聚合器，你可以安排获得使用*短码*（一个几位数的号码，例如 40404）作为发起和接收消息的端点的权利。用户可以通过使用短码来通过聚合器寻址特定的服务器，就像他们向特定电话号码发送消息一样。

**介绍多媒体消息服务**



在看到短信（SMS）的成功之后，网络基础设施提供商和运营商共同开发了彩信（MMS）——一种用于在手机之间传输富多媒体消息的独立协议。与仅限于简单格式化文本消息、和弦铃声和小位图的短信不同，彩信消息可以承载数字化音频、富文本以及当今无线终端上摄像头拍摄的图像。彩信可运行于包括短信或 TCP/IP 在内的多种无线协议之上，随着用户不断在无线终端之间创作和分享内容，其使用量正在增长。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息通信 **375**

彩信借鉴了电子邮件协议中的许多概念，包括消息主题、消息正文和多个消息部分的概念。彩信消息也可以发送给多个收件人。与短信一样，一个特殊的服务器（彩信中心）负责在无线终端之间路由彩信消息。尽管人们交换的彩信消息数量少于短信消息，但从应用程序开发者的角度来看，商业格局大致相同。公司可以在特定网络上获取短号码，并将这些短号码注册到聚合器——即运营在 Web 和蜂窝网络之间路由彩信消息的服务器的公司。

由于彩信是一项较新的服务，它也存在一些缺点。拥有支持彩信功能的手机或付费接收彩信服务的人较少。此外，虽然短信在全球各大运营商之间基本实现了互操作性，但彩信尚未做到。例如，在美国，只有少数几家主要运营商就彩信互操作性达成了协议。如果你和我使用不同的运营商，我们可能能够交换彩信消息，也可能无法交换。

**介绍小区广播服务**

虽然短信是一种点对点消息服务——发送者在发送消息时必须指定特定的接收者——但短信小区广播（SMS-CB）是一种点对区域（也称为*一对多*）协议，它使应用程序开发者和运营商能够针对单条消息定位多个无线终端。作为无线广域网（WAN）上可用消息标准中相对较新的补充，短信小区广播直到最近才在少数网络上得到采用，用于广告、天气警报、基于位置的新闻等应用。

使用短信小区广播时，消息较短（82 字节的压缩文本或二进制数据），但可以串联起来以提供传递较长消息的机制。消息不是发送给特定收件人，而是发送给一个小区范围，通常是在特定的地理区域内。一些先进的网络提供了整合地理信息的接口，从而无需短信小区广播应用程序引用特定的小区；相反，通过这些接口，你可以根据纬度和经度来引用区域。

**介绍无线消息 API**

无线消息 API（WMA）扩展了通用连接框架（GCF，你首次在第 12 章中详细接触过），以包含各种消息的概念，在概念上类似于 UDP 数据报。JSR 120 定义了`javax.wireless.messaging`包，详细说明了 WMA 1.0 版本，而 JSR 205 则详细说明了 WMA 2.0 版本。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**376**

第 14 章 ■ 使用无线消息 API 进行消息通信

■**注意** 由于目前出货的几乎所有无线终端都支持 WMA 2.0，除非我特别说明，否则以下内容均适用于 WMA 2.0。你需要记住的 WMA 1.0 和 WMA 2.0 之间的关键区别在于，WMA 2.0 增加了对短信小区广播和彩信消息的支持。

图 14-1 展示了你在第 12 章首次看到的 GCF 类，以及 WMA 引入的类。WMA 1.0 定义了创建消息实例的基本机制，而 WMA 2.0 定义了用于表示彩信消息的多部分消息，涉及以下类和接口：



• MessageConnection：作为表示单个消息的 Message 子类实例的工厂。你可以像获取其他任何 Connection 一样，通过在传递给 `Connector.open` 的 URL 中指定连接类型来获取 MessageConnection 实例。

• Message：所有 WMA 消息的超类。它提供了操作消息时间戳和地址的接口。

• TextMessage：表示承载文本的 SMS 消息的接口。

• BinaryMessage：表示承载二进制负载的 SMS 消息的接口。

• MultipartMessage：表示承载一个或多个消息部分的 MMS 消息的接口。只有 WMA 2.0 提供了 MultipartMessage 类。

• MessagePart：表示消息的单个部分，例如图像或文本片段。它在组合和解析 MMS 消息时使用。只有 WMA 2.0 提供了 MessagePart 类。

• MessageListener：提供了一种机制，AMS 可以通过该机制通知你的应用程序有传入消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **377**

**图 14-1.** *GCF 以及 WMA 定义的 GCF 扩展*

[www.it-ebooks.info](http://www.it-ebooks.info/)

**378**

第 14 章 ■ 使用无线消息 API 进行消息传递 WMA 使用 GCF 的 `Connector.open` 方法来创建 MessageConnection 实例，就像你在第 12 章和第 13 章中学习如何创建其他 Connection 子类一样。与其他 Connection 子类一样，你可以通过使用 URL 指定所需的连接类型来获取 Connection 子类；URL 的格式为

`protocol://recipient:port`

URL 的每个部分定义如下：

• protocol：协议——对于 SMS-CB 消息为 `cbs`，对于 MMS 消息为 `mms`，对于 SMS 消息为 `sms`
• recipient：MO 消息的接收方地址（电话号码、短代码或电子邮件地址）；当为空时，表示应用程序将使用生成的 MessageConnection 来接收消息
• port：消息将发送到的端口——对于 SMS 或 SMS-CB 消息为数字端口，对于 MMS 消息为字母数字字符串

在创建 MessageConnection 实例时，你可以为使用 SMS 和 MMS 协议的 MO 或 MT 消息创建实例，但 WMA 2.0 仅支持 MT SMS-CB 消息。因此，如果协议是 `cbs`，则在传递给 `Connector.open` 的 URL 中指定接收方是错误的。

与 TCP/IP 和 UDP 连接类似，SMS、SMS-CB 和 MMS 消息被定向到特定端口；不同的应用程序可以使用不同的端口来区分数据。某些端口可能被保留，供手机原生软件和网络使用的特定应用程序使用；例如，在 GSM 网络上，网络保留了表 14-1 中列出的端口，尝试在这些端口上发送或接收 SMS 消息将导致 `SecurityException`。

某些网络，例如主要在北美使用的码分多址（CDMA）网络，可能根本不使用端口号，在这些网络上打开 MessageConnection 时，你应该完全省略端口号。

**表 14-1.** *GSM 网络上的保留端口*

**端口号**

**用途**

WAP 无线电话应用（WTA）安全、无连接会话服务 2923

WAP WTA 安全会话服务

WAP 推送无连接会话服务（客户端侧）

WAP 推送安全、无连接会话服务（客户端侧） 5502

用于用户身份模块（SIM）访问的服务卡读取器服务 5503

互联网访问配置读取器

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **379**

**端口号**

**用途**

动态菜单控制协议（DMCP）

消息访问协议服务

简单电子邮件通知服务

WAP 无连接会话服务

WAP 会话服务

WAP 安全、无连接会话服务

WAP 安全会话服务

WAP vCal Secure，用于日历数据的安全传输

SyncML 空中下载（OTA）配置



WAP OTA 配置

**创建消息**

一旦你通过传递包含收件人地址的 URL，从 GCF 的 Connector 类获取到一个 MO MessageConnection，你就可以使用它的 newMessage 方法来创建具体的 Message 子类（例如 TextMessage）的新实例，如下所示：`TextMessage tm = (TextMessage)c.newMessage(MessageConnection.TEXT_MESSAGE);` 正如你所料，你可以使用 newMessage 方法来创建以下任何 Message 子类的实例：

• TextMessage：传递 `MessageConnection.TEXT_MESSAGE` 以获取 TextMessage 实例（用于文本短信）。

• BinaryMessage：传递 `MessageConnection.BINARY_MESSAGE` 以获取 BinaryMessage 实例（用于二进制短信）。

• MultipartMessage：传递 `MessageConnection.MULTIPART_MESSAGE` 以获取 MultipartMessage 实例（用于彩信）。

你也可以使用 MessageConnection 实例向不同的收件人发送消息；只需调用 newMessage，同时传递指示所需消息类型的常量以及包含收件人地址的字符串。

如果你的应用程序需要接收消息，你可以通过传递一个不带收件人地址的 URL 来获取一个为 MT 消息配置的 MessageConnection 实例，然后调用该 MessageConnection 的 receive 方法。正如你将在接下来的“接收消息”一节中看到的，receive 方法会阻塞调用它的线程，直到设备接收到新消息，因此你绝对应该在一个不同于 UI 线程的线程上执行此操作。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**380**

第 14 章 ■ 使用无线消息 API 进行消息传递

**发送消息**

创建消息后，你需要设置消息的数据负载。不同的 Message 子类在设置方式上略有不同。Message 类仅提供用于管理消息收件人地址和消息发送时间的方法：

• getAddress：返回与消息关联的地址

• getTimestamp：以 `java.util.Date` 实例的形式返回消息发送的时间

• setAddress：为消息关联一个新地址，丢弃之前与该消息关联的任何地址

正如我之前在“介绍短消息服务”一节中提到的，SMS 消息包的实现可以连接数据包（称为*段*），使你能够发送超过协议 160 字符（140 字节）限制的消息。WMA 要求任何实现都必须支持将最多三个段连接为一条消息，从而允许你发送相当长的消息，大约 400 字节或更多（实际长度通常稍短，因为协议需要占用空间来支持段连接、可选的端口号等）。你可以通过调用 MessageConnection 的 numberOfSegments 方法并传递一个 TextMessage 或 BinaryMessage 来确定一条消息消耗了多少个段；结果就是该消息跨越的段数。

MMS 的实现则不同，它允许你将消息创建为多个部分的集合，这些部分由 MessagePart 的实例表示。不是一次性设置 MultipartMessage 的负载，而是逐个添加各个部分。

设置和获取 TextMessage 的负载

正如你所想象的，管理 TextMessage 实例的负载非常简单。该类提供了两个方法：`getPayloadText` 和 `setPayloadText`。两者都将消息负载视为 `String` 实例；你调用 `getPayloadText` 来获取包含该实例负载的 `String`，同时你向 `setPayloadText` 传递一个字符串来设置该实例的负载。你可以使用类似于清单 14-1 中的代码来设置文本消息的负载。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **381**

**清单 14-1.** *设置 TextMessage 实例的负载* `String receiver = "+18885551212";`

`String port = "1234";`

`String address = "sms://" + receiver + ":" + port; MessageConnection c = null;`

`try {`

`c = (MessageConnection) Connector.open(address);`

`TextMessage t = (TextMessage) c.newMessage(`

`MessageConnection.TEXT_MESSAGE);`

`t.setAddress(address);`

`t.setPayloadText("Hello world!");`

`c.send(t);`

`} catch (Exception e) {}`

`finally {`

`if (c != null) {`

`try {`

`c.close();`

`} catch (Exception e) { /* recover */}`

`}`

`}`

这段代码首先创建一个 MessageConnection 实例，并使用它创建一个新的 TextMessage 实例。接着，它设置 TextMessage 实例的收件人地址，并在发送消息之前将其负载设置为消息 "Hello world!"。

■**注意** 如果你熟悉电话协议，特别是 SMS，你就会知道 SMS 消息可以根据字符集、网络运营商，甚至网络运营商使用的承载网络，以多种不同方式中的任何一种进行编码。幸运的是，WMA 实现负责处理消息编码和解码，以适应手机和承载网络可能需要的任何协议。

设置和获取 BinaryMessage 的负载

与 TextMessage 类似，BinaryMessage 有一个简单的接口来管理其负载，将负载视为字节数组。`getPayloadData` 和 `setPayloadData` 方法分别类似于 `getPayloadText` 和 `setPayloadText`。

当人们发送二进制 SMS 消息时，负载通常是小型图像或和弦铃声，手机和 SMSC 将其编码和解码为增强型消息服务（EMS）对象。不幸的是，WMA 实现不支持 EMS 编码或解码，因此除非你自己编写编解码器，否则无法直接与 EMS 编码的对象或服务进行互操作。但是，你可以通过 BinaryMessage 类使用二进制 SMS 来发送和接收其他应用程序数据，例如将压缩的金融数据（例如股票市场更新）、天气或配置数据推送到你的应用程序。

管理 MultipartMessage 的多个部分

处理 MultipartMessage 实例与处理其他 Message 子类略有不同，因为 MMS 消息的负载由一个或多个*部分*组成。每个部分都是一个单独的多媒体实体，例如图像、文本块或声音，很像传统的电子邮件消息。图 14-2 显示了一个包含多个部分的消息的示意图。

**图 14-2.** *MultipartMessage 实例的示意图* MultipartMessage 的实例有几个字段：

• *时间戳*：继承自 Message 类，可通过 `getTimestamp` 方法获取时间戳。

• *起始内容 ID*：该 ID 是一个字符串，用于命名将首先向用户显示的消息部分。可通过 `getStartContentId` 和 `setStartContentId` 方法获取。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **383**

• *主题*：主题是一个字符串，可通过 `getSubject` 和 `setSubject` 方法获取。

• *零个或多个地址*：地址是表示消息收件人的字符串。你可以使用 `addAddress` 方法添加单个地址，使用 `getAddresses` 方法获取地址数组，或使用 `removeAddresses` 方法移除一个地址（或所有地址）。

• *零个或多个命名标头*：标头包含关于消息的元数据。每个标头都是一个字符串，可通过其名称访问，你也可以使用字符串通过 `getHeader` 和 `setHeader` 方法指定该名称。



•   *零个或多个消息部分*：消息部分是包含消息各个独立部分的 MessagePart 实例。你可以使用 `addMessagePart` 方法将 MessagePart 实例添加到多部分消息中，使用 `getMessageParts` 获取消息中 MessagePart 的数组，或使用以下方法之一从消息中移除 MessagePart 实例：`removeMessagePart`、`removeMessagePartId` 或 `removeMessagePartLocation`。

当然，你不能直接访问这些字段；相反，你需要使用 `MultipartMessage` 类提供的访问器和修改器方法。

`MultipartMessage` 类封装了整个消息的概念，而 `MessagePart` 类则封装了消息的单个附件。每个部分都有一个唯一的名称，称为*内容 ID*。在处理 MessagePart 实例时，你经常通过此 ID 来引用它们，你可以通过调用特定部分的 `getContentID` 方法来获取该 ID。实例还有另外四个字段：

•   *消息内容*：一个字节数组，你可以使用 `getContent` 或 `getContentAsStream` 方法获取。

•   *消息部分的指定内容位置*：一个字符串，通常包含一个 URL，你可以使用 `getContentLocation` 方法获取。

•   *用于编码部分文本的编码方法*：由 `getEncoding` 方法确定。

•   *部分的 MIME 类型*：指示部分的类型（例如文本、PNG 图像等），你可以使用 `getMIMEType` 方法获取。由于 `MultipartMessage` 类的面向部分的特性，创建一个要发送的消息比简单地创建一个 `TextMessage` 或 `BinaryMessage` 实例并设置其负载更复杂。相反，你需要遵循以下步骤：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**384**

第 14 章 ■ 使用无线消息 API 进行消息传递 **1.** 创建一个或多个 `MessagePart` 实例，将每个部分的内容、其 ID 和部分类型传递给 `MessagePart` 构造函数。

**2.** 通过在 `MessageConnection` 实例上调用 `newMessage` 来创建一个 `MultipartMessage` 实例。

**3.** 使用 `addMessagePart` 将你在第一步中创建的部分添加到 `MultipartMessage` 中。

**4.** 使用 `MultipartMessage` 的 `setSubject` 方法设置消息的主题（如果有）。

**5.** 使用 `MultipartMessage` 的 `addAddress` 方法设置消息的收件人。

清单 14-2 展示了发送单个 PNG 图像此序列的伪代码。

**清单 14-2.** *在多部分消息中发送单个 PNG 图像的伪代码* String address = "mms://" + receiver + ":" + appId; MessageConnection c = null;

try {

c = (MessageConnection) Connector.open(address);

MultipartMessage mpm = (MultipartMessage)c.newMessage(

MessageConnection.MULTIPART_MESSAGE);

mpm.setSubject("An image");

InputStream is = getClass().getResourceAsStream("/img/i.png"); byte[] bImage = new byte[is.available()];

is.read(bImage);

mpm.addMessagePart(new MessagePart(bImage, 0, bImage.length,

"image/png", "id1", null, null));

c.send(mpm);

} catch (Exception e) { /* 恢复 */ }

finally {

if (c != null) {

try {

c.close();

} catch (IOException e) { /* 恢复 */}

}

}

这段代码以与清单 14-1 相同的方式开始，不同之处在于它创建了一个 `MultipartMessage` 实例而不是 `TextMessage` 实例。接下来，它将主题设置为 "An image"。之后，它使用 `InputStream` 实例将整个图像读入

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **385**

内存，并创建一个由 PNG 图像内容组成的新 `MessagePart`。最后，它使用 `MessageConnection` 发送图像。

**接收消息**

要接收消息，你需要一个配置为接受 MT 消息的 `MessageConnection` 实例。你可以使用 `Connector.open` 创建一个，传递协议以及你的应用程序要接收的 MT 消息类型的可选端口或应用程序 ID，如下所示： MessageConnection c = (MessageConnection)Connector.open("sms://:" + port); 然后，你可以通过调用 `MessageConnection` 的 `receive` 函数来接收新消息，如下所示：

TextMessage msg = (TextMessage)c.receive();

听起来很简单，对吧？有一个问题：`receive` 会阻塞，直到应用程序收到消息，所以如果你在默认线程上执行此操作，你的应用程序 UI 将挂起，直到有传入消息到达。你可以通过以下两种方式之一解决此问题：注册一个 WMA 在有消息可用时通知的监听器，或者在单独的线程上调用 `receive`。

注册监听器很容易；与其他 Java 监听器一样，你只需创建一个实现相应监听器接口（此处为 `MessageListener`）的类，并提供 `notifyIncomingMessage` 方法。当设备收到消息时，AMS 会使用一个配置为接收传入消息的 `MessageConnection` 来调用监听器。

最健壮的方法实际上是同时注册一个监听器，并在 WMA 调用你的监听器时调用 `receive`，因为当 Java ME 运行时和 WMA 处理到达的传入消息时，`receive` 可能会阻塞一小段时间。我将在本章后面的“发送和接收 MMS 消息”部分向你展示这种技术。

当然，你只能在应用程序运行时通过这种方式接收传入消息。要在应用程序未运行时接收传入消息，你的应用程序必须在 AMS 维护的推送注册表中注册自己。反过来，当设备收到针对你的应用程序的消息时，AMS 会调用你的应用程序。我将在接下来的“使用推送注册表”部分向你展示如何做到这一点。

**管理消息头**

`MultipartMessage` 实例提供了一组*头*，这些是中间服务器在消息从源点传输到目的地的过程中可能添加到消息中的名称-值对。你可以通过将头的名称传递给 `getHeader` 来获取消息头的值，也可以通过将头的名称和值传递给 `setHeader` 来设置头的值。使用 HTTP，你可以创建自己的头并包含特定于应用程序的数据。然而，`MultipartMessage` 所代表的 MMS 协议

[www.it-ebooks.info](http://www.it-ebooks.info/)

**386**

第 14 章 ■ 使用无线消息 API 进行消息传递 只有协议规范定义的有限数量的头。表 14-2 显示了最常见的头及其用途。请注意，特定消息可能不包含所有这些头，或者可能包含表 14-2 中未显示的其他头。

**表 14-2.** *常见的 MultipartMessage 头及其含义* **头名称**

**用途**

X-Mms-Delivery-Time

消息必须传递给收件人的日期和时间

X-Mms-Expiry

消息应过期且 MMSC 不应传递的日期和时间

X-Mms-Message-Class

消息的类别——Personal、Advertisement、Informational 或 Auto

X-Mms-Priority

消息的优先级——High、Normal 或 Low X-Mms-Transaction-Id

消息的分配事务 ID

■**注意** `MultipartMessage` 底层的 MMS 协议由开放移动联盟（OMA）及其 WAP-209-MMSEncapsulation 标准规定。如果你要深入研究 `MultipartMessage` 类和 MMS 协议，建议仔细阅读 OMA 网站（http://www.openmobilealliance.org）上的此标准及其他 MMS 文档。JSR 205 引用了此标准；JSR 206 的附录 D 讨论了 OMA 定义的 MMS 消息结构如何映射到 WMA 提供的接口。



**了解使用 WMA 时所需的权限** 正如使用 GCF 进行 HTTP 或套接字请求时需要权限一样，使用 GCF 获取 `MessageConnection` 实例也需要权限。WMA 为其支持的每种消息定义了以下权限：

• `javax.microedition.io.Connector.sms`：获取能够发送或接收文本或二进制 SMS 消息的 `MessageConnection` 实例所需权限
• `javax.microedition.io.Connector.cbs`：获取能够接收 SMS-CB 消息的 `MessageConnection` 实例所需权限
• `javax.microedition.io.Connector.mms`：获取能够发送或接收 MMS 消息的 `MessageConnection` 实例所需权限

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **387**

除了这些权限之外，你的应用程序还需要以下特定权限来发送或接收每种消息：

• `javax.microedition.io.Connector.sms.receive`：接收文本或二进制 SMS 消息所需权限
• `javax.microedition.io.Connector.sms.send`：发送文本或二进制 SMS 消息所需权限
• `javax.microedition.io.Connector.cbs.receive`：接收 SMS-CB 消息所需权限
• `javax.microedition.io.Connector.mms.receive`：接收 MMS 消息所需权限
• `javax.microedition.io.Connector.mms.send`：发送 MMS 消息所需权限 这种细粒度的权限使用有助于防止恶意 MIDlet 在未经用户批准的情况下发送或接收消息。此外，AMS 会在允许 MIDlet 创建 `MessageConnection` 实例或发送/接收消息之前提示用户。

与其他权限一样，你必须在签署 MIDlet 进行认证和分发之前，在 JAD 文件的 `MIDlet-Permissions` 字段中指定这些权限。

**使用推送注册表**

我曾在第 4 章首次向你介绍 Java ME 推送注册表，你学习了如何使用它以及 AMS 来注册一个在闹钟触发时启动的应用程序。你也可以使用推送注册表——通过 `PushRegistry` 类提供的方法——在收到传入消息时启动你的应用程序。图 14-3（包含与图 4-3 相同的事件序列）显示了当你的应用程序向推送注册表注册以接收传入消息，并且设备收到针对你应用程序的传入消息时的事件序列。

在第 4 章中，你只看到了如何使用 `PushRegistry` 类动态注册闹钟，该类封装了推送注册表的接口。

■**注意** `PushRegistry` 类提供所有类方法；你不需要 `PushRegistry` 的实例。这与 MIDP 配置文件中的许多其他类不同，在那些类中，你需要获取该类的单例实例并调用其方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**388**

第 14 章 ■ 使用无线消息 API 进行消息传递 **图 14-3.** *使用推送注册表接收消息* 稍后，在第 12 章的“将套接字与 GCF 结合使用”部分，你了解了如何将 URL 包含在 JAD 文件的 `MIDlet-Push` 字段中，用于传入端点。

执行此操作时，你必须指定三项内容：

• 描述入站端点的 URL（语法与 `Connector.open` 的 URL 相同）
• 当对入站端点发出传入请求时，你希望 AMS 启动的 MIDlet 类
• 允许建立到入站端点连接的发送方地址

清单 14-3 显示了一些假设的 `MIDlet-Push` 条目。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **389**

**清单 14-3.** *一些假设的 MIDlet-Push 条目* `MIDlet-Push-1: socket://:7, com.apress.rischpater.EchoMIDLet, *`
`MIDlet-Push-2: socket://:80, com.apress.rischpater.HttpMIDlet, 192.168.1.128`
`MIDlet-Push-3: sms://:1234, com.apress.rischpater.SMSMIDlet, +1123456????`

从示例中可以看出，允许的发送方条目可以包含通配符；就像当今大多数 shell 中的文件名一样，你可以使用 `*` 指定一个或多个字符，使用 `?` 指定单个字符。

■**注意** 使用推送注册表需要权限；请确保在 MIDlet 的 JAD 文件的 `MIDlet-Permissions` 条目中包含你正在使用的 GCF 端点的适当权限。

当 AMS 响应入站连接请求（例如 MT 消息）启动你的应用程序时，AMS 会将入站连接的 GCF URL 添加到一个列表中。你可以使用 `PushRegistry` 类的 `listConnections` 方法请求此列表，该方法返回一个入站连接列表。这实际上比你需要的*更多*信息，因为它列出了 AMS 正在为你的 MIDlet 监听的所有入站端点。通过传递 `true`，你将仅收到那些具有待处理数据连接的端点列表。清单 14-4 显示了一些执行此操作的伪代码。

**清单 14-4.** *使用 PushRegistry 的 listConnections 方法* `private boolean processIncomingRequests() {`
`    String[] connections =`
`        PushRegistry.listConnections(true);`
`    if (connections != null && connections.length > 0) {`
`        for (int i=0; i < connections.length; i++) {`
`            c = (MessageConnection) Connector.open(connections[i]);`
`            Message m = c.receive();`
`            if (m instanceof TextMessage) {`
`                processOneMessage((TextMessage)m);`
`            }`
`        }`
`        return(true);`
`    }`
`    return(false);`
`}`

[www.it-ebooks.info](http://www.it-ebooks.info/)

**390**

第 14 章 ■ 使用无线消息 API 进行消息传递 此示例简单地遍历 `listConnections` 提供的入站连接，接收每条入站消息，并依次处理每条消息。

**动态注册以接收传入消息**

当你在 MIDlet 的 JAD 文件中指定入站连接的 URL 时，你是在*静态地*向 AMS 表明你对入站连接的兴趣——也就是说，在构建和打包应用程序时。有时你可能不想这样做——例如，如果你的应用程序让用户决定设备收到传入消息时是否应启动它。为了允许用户确定你的 MIDlet 是否应响应传入消息，你的应用程序可以*动态地*注册其对入站请求的兴趣——即在运行时。

动态注册入站消息很简单：只需调用 `PushRegistry.registerConnection` 并传入入站连接 URL、要启动的类以及过滤器，如清单 14-5 所示。

**清单 14-5.** *使用推送注册表动态注册入站连接* `PushRegistry.registerConnection("sms://:1234",`
`    "com.apress.rischpater.SMSMIDlet",`
`    "*" );`

这样做在功能上等同于在 `MIDlet-Push` 条目中列出入站端点 URL；一旦你这样做了，AMS 会在你的 MIDlet 未运行时将入站连接请求发送给它。要禁用此功能——换句话说，取消注册——请调用 `PushRegistry.unregisterConnection`，仅传入你传递给 `registerConnection` 的 URL：

`PushRegistry.unregisterConnection("sms://:1234");` 一旦你这样做了，AMS 将不再通知你的 MIDlet 有关你指定的 GCF URL 上的入站连接。

**使用 PushRegistry API**

第 4 章向你展示了如何注册闹钟，前面的清单向你展示了如何注册传入连接。你还可以确定先前为入站连接指定的过滤器，或负责处理入站连接的 MIDlet。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **391**

你可以调用 `PushRegistry.getFilter` 并传入一个 GCF URL，以获取一个包含先前为入站连接指定的过滤器的字符串；如果你的应用程序动态指定其入站端点和过滤器（可能代表用户），你可能需要这样做。



你调用 `PushRegistry.getMidlet`（注意其大小写与 MIDP API 中许多其他 MIDlet 实例不一致）来确定负责处理特定入站连接的 URL。

**应用无线消息 API**

通过 WMA 进行内容分发是 Java ME 应用领域中一个至关重要且不断发展的部分。服务器可以轻松地异步向设备推送信息，而当今 Java ME 手机丰富的 UI 功能比向无线终端的浏览器推送 WAP 内容能带来更好的用户体验。MO 消息在某些应用中也扮演着重要角色，例如社交网络应用，或者那些持续网络连接成本过高或过于沉重而不值得的应用。

在演示如何在实际示例中使用 WMA 时，我选择不扩展 WeatherWidget 来接收 SMS 天气更新，因为这无法向你展示如何在真实应用中使用 WMA 发起消息。相反，下面提供的是两个简单的 MIDlet：一个用于发送和接收文本 SMS 消息，另一个用于在 MMS 消息中发送和接收图片。

**发送和接收 SMS 消息**

图 14-4 显示了在 Java ME 模拟器中运行的两个 SMSMIDlet 实例。如你所见，用户界面很简单，包含一个允许你输入目标电话号码（技术上称为移动台国际用户目录号码，即 MSISDN）的文本字段，以及另一个允许你输入消息的文本字段。

为什么需要两个实例？Java ME 模拟器的特性之一是其能够完全模拟 WMA 提供的功能，包括发送和接收消息。通过同时运行两个 SMSMIDlet 实例，你可以测试消息的发送和接收。正如你在每个模拟器窗口的标题栏中所见，每个模拟器实例模拟了不同的 MSISDN（图中为 MSISDN +5550000 和 +5550001）。通过使用每个模拟器的 MSISDN，你可以在将应用加载到实际设备之前测试 WMA 的使用。这让你能在工作站上比在目标设备上调试更高效地调试实现。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00020.png)

**392**

第 14 章 ■ 使用无线消息 API 进行消息传递 **图 14-4.** *在模拟器的两个实例中运行的 SMSMidlet* SMSMIDlet 在其实现中使用了三个类。其中两个——SMSMidlet 和 SMSSender——是公共类，而剩下的一个——SetMessage——是私有类。SMSMIDlet 类（如清单 14-6 所示）执行设置用户界面和接收消息所需的工作。

■**提示** 当然，清单 14-6 在你定义 SMSSender 类之前无法编译，我将在本节后面的清单 14-7 中展示该类。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **393**

**清单 14-6.** *SMSMidlet 类*

package com.apress.rischpater.smsmidlet;

import javax.microedition.midlet.*;

import javax.microedition.io.*;

import javax.microedition.lcdui.*;

import javax.wireless.messaging.*;

import java.io.IOException;

public class SMSMIDlet

extends MIDlet

implements CommandListener, MessageListener, Runnable {

private SMSSender sender = null;

private Command exitCommand = new Command("退出", Command.EXIT, 2); private Command sendCommand = new Command("发送", Command.SCREEN, 1); private final String port = "1234";

private TextField numberEntry= null;

private TextField msgEntry = null;

private Form form = null;

private String senderAddress = null;

private boolean endNow = false;

private MessageConnection c = null;

String msgReceived = null;

public SMSMIDlet() {

sender = SMSSender.getInstance();

}

public void commandAction(javax.microedition.lcdui.Command c, javax.microedition.lcdui.Displayable d) {

if (c == exitCommand) {

if (!sender.isSending()) {

destroyApp(true);

notifyDestroyed();

}

} else if (c == sendCommand) {



String dest = numberEntry.getString();

String msg = msgEntry.getString();

if (dest.length() > 0)

sender.sendMsg(dest, port, msg);

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**394**

第 14 章 ■ 使用无线消息 API 进行消息传递 protected void destroyApp(boolean param) {

try {

endNow = true;

c.close();

} catch (IOException ex) {}

}

protected void pauseApp() {

try {

endNow = true;

c.close();

} catch (IOException ex) {}

}

protected void startApp() {

if (form == null) {

form = new Form("SMSMIDlet");

numberEntry = new TextField("连接到:",

null, 256,

TextField.PHONENUMBER);

msgEntry = new TextField("消息:",

null, 160,

TextField.ANY);

form.append(numberEntry);

form.append(msgEntry);

form.addCommand(exitCommand);

form.addCommand(sendCommand);

form.setCommandListener(this);

}

Display.getDisplay(this).setCurrent(form);

startReceive();

}

private void startReceive() {

Thread t;

t = new Thread(this);

t.start();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **395**

public void run() {

Message msg = null;

c = null;

endNow = false;

try {

c = (MessageConnection) Connector.open("sms://:" + port); msg = c.receive();

while ((msg != null) && (!endNow)) {

if (msg instanceof TextMessage) {

msgReceived = ((TextMessage)msg).getPayloadText();

Display.getDisplay(this).callSerially(new SetMessage());

}

msg = c.receive();

}

} catch (IOException e) {}

}

class SetMessage implements Runnable {

public void run() {

msgEntry.setString(msgReceived);

}

}

}

该 MIDlet 使用了两个 `Command` 实例：一个用于发出退出信号，另一个用于在选择发送操作时发出信号。它还使用了两个 `TextField` 实例：一个用于输入接收方的 MSISDN，另一个用于输入您发送或接收的消息。

我

基于本书第三版中提供的示例代码实现了此示例和下一个示例应用程序，而不是使用 NetBeans 构建新的用户界面。因此，用户界面代码的流程略有不同；这些示例说明了如何手动构建一个拥有自己应用程序逻辑的 MIDlet，而不是使用 NetBeans 来创建 UI。我在类创建时创建了 `Command` 实例，并在 MIDlet 的 `startApp` 方法中创建了 UI 的其余部分。这使得简单 MIDlet 的代码易于阅读，但对于使用许多不同屏幕的大型 MIDlet 应用程序来说，可扩展性不佳。

该 MIDlet 使用第二个线程来接收传入消息；它在 `startApp` 方法中通过调用 `startReceive` 来启动此线程。此方法简单地分配一个新的 `Thread` 实例，并使用 MIDlet 自身作为 `Runnable` 接口的实现来提供线程的 `run` 方法。我本可以轻松地使用一个内部类，但这似乎得不偿失。因此，当 MIDlet 启动时，用户界面在主线程上运行，而第二个线程启动并在 `run` 方法中对 `c.receive` 的调用处阻塞。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**396**

第 14 章 ■ 使用无线消息 API 进行消息传递 当新的文本 SMS 消息到达应用程序的端口时，`c.receive` 返回新的 `TextMessage` 实例。线程的 `run` 方法从传入消息中提取有效载荷文本，并使用 `Display` 实例的 `callSerially` 方法安排在主线程上更新显示。然后它循环等待另一条传入的文本消息。

当您需要从单独的线程安排在应用程序主线程上执行操作时，`callSerially` 非常方便。它会在主线程处理完任何待处理的 UI 事件后，调度您提供的类实例的 `run` 方法执行。

该线程使用 `callSerially` 来调度内部类 `SetMessage` 实例的 `run` 方法，该方法只是将输出文本字段的内容设置为传入消息的文本。

要发送消息，`SMSMIDlet` 会调用 `SMSSender` 类的 `sendMsg` 方法。

这个单例类使用一个单独的线程来发送每条 MO 消息，以防止阻塞主线程。清单 14-7 展示了 `SMSSender` 类。

**清单 14-7.** *SMSSender 类*

package com.apress.rischpater.smsmidlet;

import javax.microedition.io.*;

import javax.wireless.messaging.*;

import java.io.IOException;

public class SMSSender implements Runnable {

private static SMSSender me = new SMSSender();

private String receiver = null;

private String port = null;

private String msgString = null;

private boolean sending = false;

private SMSSender() {

}

public static SMSSender getInstance() {

return me;

}

public boolean isSending() {

return sending;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **397**

public void sendMsg(String r, String p, String m) {

if (sending) return;

receiver = r;

port = p;

msgString = m;

Thread t = new Thread(this);

t.start();

}

public void run() {

String address = "sms://" + receiver + ":" + port; sending = true;

MessageConnection c = null;

try {

c = (MessageConnection) Connector.open(address);

TextMessage txtmessage = (TextMessage) c.newMessage(

MessageConnection.TEXT_MESSAGE);

txtmessage.setAddress(address);

txtmessage.setPayloadText(msgString);

c.send(txtmessage);

} catch (Exception e) {}

if (c != null) {

try {

c.close();

} catch (IOException ioe) {}

}

sending = false;

}

}

通过将构造函数设为私有，将类的实例保存在私有字段 `me` 中，并通过公共方法 `getInstance` 返回该实例，该类强制实现了单例关系，并确保该类的客户端只能获得该类的一个实例。

该类完成的工作分为两个方法：`sendMsg` 和 `run`。`sendMsg` 在调度一个新线程（使用已分配的 `SMSSender` 实例作为 `Runnable` 对象）之前，先保存它要发送的消息的参数——接收方地址、接收方端口和消息文本本身。

一旦 Java 运行时启动新线程，它就会调用 `run` 方法，该方法实际将消息文本发送到您提供的地址和端口。过程很简单：创建目标地址，使用 GCF 创建一个 `MessageConnection` 实例以便获取一个新的 `TextMessage` 实例，配置 `TextMessage`

[www.it-ebooks.info](http://www.it-ebooks.info/)

**398**

第 14 章 ■ 使用无线消息 API 进行消息传递 实例，然后使用 `MessageConnection` 和配置好的 `TextMessage` 实例发送消息。一旦 `run` 方法发送了消息，它会在退出前通过关闭 `MessageConnection` 进行清理。

请注意，此示例将错误处理降至最低；`try-catch` 块向您展示了可能发生错误的地方（例如，缺乏无线覆盖会导致 `send` 调用抛出异常），但代码并未对这些错误进行任何处理。在构建使用无线网络进行数据交换的应用程序时，特别关注应用程序如何处理错误非常重要，因为错误可能频繁发生（尤其是考虑到无线覆盖的不可预测性）。在应用程序中发送和接收无线消息时如何处理错误，在一定程度上取决于应用程序的性质，但通常属于以下三大类之一：

• *完全忽略错误*：简单的应用程序——尤其是那些依赖复杂后端服务器的应用程序——通常可以简单地依赖后端来重新发送消息和管理错误。发送许多重复消息的应用程序，例如那些报告用户位置或其他状态的应用程序，通常可以忽略传输失败，因为无论如何，失败后不久就会发送后续消息。



• *通知用户并让用户自行处理错误*：这种方法适用于简单的应用程序，但很容易让用户感到沮丧，尤其是当错误发生时应用程序丢失了您输入的内容。与其创建一个强制用户响应各种错误的模态界面，不如静默失败，并通过被动方式（例如信号状态图标）通知用户异常情况。

• *将消息排队以便稍后发送*：需要可靠消息传递的应用程序应提供自己的消息队列，很可能以记录存储中的记录形式存在。这可以优雅地处理大多数可能发生的错误，而不会导致用户数据丢失。

**发送和接收彩信**

通过 WMA 交换彩信的原理与交换短信类似，但有两个复杂之处。首先，正如你在“管理 MultipartMessage 的多个部分”一节中所看到的，发送或接收彩信不仅需要处理消息本身，还需要处理构成消息的内容。

其次，彩信协议本身比短信协议昂贵得多；发送或接收彩信所需的时间可能远长于发送或接收短信，因为彩信更大，并且比短信更重地使用网络的数据通道。这意味着，在使用彩信时，您的应用程序*必须*使用多线程来避免阻塞用户界面线程。此外，您的应用程序应准备好同时接收多条消息，因为高负载的应用程序很可能在接收一条彩信时，另一条彩信又到达了。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00018.png)

第 14 章 ■ 使用无线消息 API 进行消息传递 **399**

图 14-5 显示了 MMSMIDlet 应用程序，同样在两个不同的实例中运行。与 SMSMIDlet 示例不同，MMSMIDlet 示例发送的是图像而不是文本。

**图 14-5.** *两个并排运行的 MMSMIDlet 实例（模拟中）* 清单 14-8 显示了 MMSMIDlet 类，它包含了 MMSMIDlet 示例的用户界面和接收功能。

■**提示** 与清单 14-6 一样，清单 14-8 不能独立编译。您还需要 MMSSender 类，我将在清单 14-9 中向您展示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**400**

第 14 章 ■ 使用无线消息 API 进行消息传递 **清单 14-8.** *MMSMidlet 类*

package com.apress.rischpater.mmsmidlet;

import javax.microedition.midlet.*;

import javax.microedition.io.*;

import javax.microedition.lcdui.*;

import javax.wireless.messaging.*;

import java.io.IOException;

public class MMSMIDlet

extends MIDlet

implements CommandListener, Runnable, MessageListener {

private MMSSender sender = null;

private Command exitCommand = new Command("退出", Command.EXIT, 2); private Command sendCommand = new Command("发送", Command.SCREEN, 1); private TextField numberEntry = null;

private static final String FRACTAL_PATH = "/icons/fractal.png"; private ImageItem imageItem = null;

private Form form = null;

private boolean endNow = false;

private MessageConnection c = null;

private final String appId = "MMSMIDlet";

protected int msgAvail = 0;

private final Integer monitor = new Integer(0);

public MMSMIDlet() {

sender =

MMSSender.getInstance();

}

public void commandAction(javax.microedition.lcdui.Command c, javax.microedition.lcdui.Displayable d) {

if (c == exitCommand) {

if (!sender.isSending()) {

destroyApp(true);

notifyDestroyed();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **401**

} else if (c == sendCommand) {

String dest = numberEntry.getString();

if (dest.length() > 0)

sender.sendMsg(dest, appId, "一个分形！", FRACTAL_PATH);

}

}

protected void destroyApp(boolean param) {

try {

endNow = true;

c.close();

} catch (IOException ex) {}

}

protected void pauseApp() {

endNow = true;

try {

c.setMessageListener(null);

c.close();



} catch (IOException ex) {}

}

protected void startApp() {

if (form == null) {

form = new Form("MMSMIDlet");

numberEntry = new TextField("连接到:",

null, 256,

TextField.PHONENUMBER);

imageItem = new ImageItem(null,null,

ImageItem.LAYOUT_DEFAULT,null);

form.append(numberEntry);

form.append(imageItem);

form.addCommand(exitCommand);

form.addCommand(sendCommand);

form.setCommandListener(this);

}

Display.getDisplay(this).setCurrent(form);

startReceive();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**402**

第 14 章 ■ 使用无线消息 API 进行消息传递 private void startReceive() {

Thread t;

try {

c = (MessageConnection) Connector.open("mms://:" + appId); c.setMessageListener(this);

} catch (Exception e) {}

if (c != null) {

t = new Thread(this);

t.start();

}

}

public void run() {

Message msg = null;

endNow = false;

msgAvail = 0;

while (!endNow) {

synchronized(monitor) {

if (msgAvail <= 0)

try {

monitor.wait();

} catch (InterruptedException e) {}

msgAvail--;

}

try {

msg = c.receive();

if (msg instanceof MultipartMessage) {

MultipartMessage mpm = (MultipartMessage)msg;

MessagePart[] parts = mpm.getMessageParts();

if (parts != null) {

for (int i = 0; i < parts.length; i++) {

MessagePart mp = parts[i];

String type = mp.getMIMEType();

byte[] ba = mp.getContent();

if (type.equals("image/png")) {

Image image =

Image.createImage(ba, 0, ba.length);

Display.getDisplay(this).callSerially(

new SetImage(image));

}

}

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **403**

} catch (Exception e) {}

}

}

private void getMessage() {

synchronized(monitor) {

msgAvail++;

monitor.notify();

}

}

public void notifyIncomingMessage(MessageConnection c) {

if (c != null) {

getMessage();

}

}

class SetImage implements Runnable {

private Image img = null;

public SetImage(Image i) {

img = i;

}

public void run() {

imageItem.setImage(img);

}

}

}

MMSMIDlet 类中的职责划分与 SMSMIDlet 类相同，尽管接收和发送代码都更复杂，这反映了 MMS 交互的异步特性。startReceive 中消息接收的初始化与 SMSMIDlet 中的实现类似，但有一个例外：MMSMIDlet 实现向从 GCF 返回的 MessageConnection 注册了一个消息监听器，以便 Java ME 运行时可以通过 notifyIncomingMessage 方法通知它传入的消息。

当 MIDlet 收到传入消息的通知时，它会调用 getMessage。getMessage 和接收线程的 run 方法（最初由 startReceive 生成）一起使用消息计数器 msgAvail 和监视器变量 monitor 来协调消息接收；如果 MIDlet 正在接收消息，getMessage 会在通知接收线程有另一个传入消息待处理之前等待。

接收线程的主循环稍微复杂一些，因为它使用了监视器；它首先在监视器上等待，然后接收传入的消息。接收消息与 SMSMIDlet 示例中的相同——它只是在为 MT 消息配置的 MessageConnection 实例上调用 receive——但消息处理更棘手，因为消息可能包含任意数量的消息部分。接收线程检查以确保它从 MessageConnection 实例接收到的 Message 是一个 MultipartMessage 实例，然后检查它的每个部分，以找到 MIME 类型为 image/png 的部分。一旦找到，它就会使用该部分的字节创建一个 Image 实例。然后，它使用内部类 SetImage 用该部分的图像更新显示。最后，线程的 run 循环恢复在监视器变量上等待另一个传入消息通知。

与 SMSMIDlet 示例一样，发送也在单独的线程上进行；MMSSender 类管理每个外发消息的发送。清单 14-9 显示了 MMSSender 类。

**清单 14-9.** *MMSSender 类*

package com.apress.rischpater.mmsmidlet;

import javax.microedition.io.*;

import javax.wireless.messaging.*;

import java.io.*;

public class MMSSender implements Runnable {

private static MMSSender me = new MMSSender();

private MMSSender() {

}

public static MMSSender getInstance() {

return me;

}

private String receiver = null;

private String appId = null;

private String image = null;

private String msg = null;

private String encoding = null;

private boolean sending = false;

public void sendMsg(String r, String id, String m, String i) {

if (sending) return;

receiver = r;

appId = id;

msg = m;

image = i;

encoding = System.getProperty("microedition.encoding");

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **405**

Thread t = new Thread(this);

t.start();

}

public boolean isSending() {

return sending;

}

public void run() {

sending = true;

try {

sendMMS();

} catch (Exception e) {}

sending = false;

}

private void sendMMS() {

String address = "mms://" + receiver + ":" + appId; System.out.println(address);

MessageConnection c = null;

try {

c = (MessageConnection) Connector.open(address);

MultipartMessage mpm = (MultipartMessage) c.newMessage(

MessageConnection.MULTIPART_MESSAGE);

mpm.setSubject("MMSMIDlet 图像");

if (image!=null) {

InputStream is = getClass().getResourceAsStream(image); byte[] bImage = new byte[is.available()];

is.read(bImage);

mpm.addMessagePart(

new MessagePart(bImage, 0, bImage.length,

"image/png", "id1", null, null));

}

if (msg!=null) {

byte[] bMsg = msg.getBytes();

mpm.addMessagePart(

new MessagePart(bMsg, 0, bMsg.length,

"text/plain", "txt1", null, encoding));

}

c.send(mpm);

} catch (Exception e) {}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**406**

第 14 章 ■ 使用无线消息 API 进行消息传递 finally {

if (c != null) {

try {

c.close();

} catch (IOException e) {}

}

}

}

}

与 SMSSender 类的结构相同，MMSSender 必须以两种不同的方式处理事情。首先，它必须确定主机 Java ME 虚拟机使用的字符编码方案，以便它可以将其与任何包含文本部分的外发 MMS 消息一起发送。其次，它必须管理多个消息部分。尽管 MMSMIDlet 的用户界面只支持显示传入 MMS 消息的图像部分，但 MMSSender 可以同时发送文本部分和图像部分。通过同时发送文本部分和图像部分，我向您展示了如何在单个 MultipartMessage 实例中发送多个部分，并彻底测试 MIDlet 消息接收功能的实现。

获取主机虚拟机使用的编码方案很容易；sendMsg 方法只需使用值 microedition.encoding 调用 System.getProperty 方法。这将返回编码方案的标准名称，然后您可以在发送 MMS 消息的文本部分时使用它。一旦 sendMsg 方法捕获了其传入参数，它就会创建并启动一个新线程来发送外发消息内容。

该线程使用私有的 sendMMS 方法来实际执行发送；此方法必须为每个指定的有效负载创建 MessagePart 实例，将这些部分添加到新的 MultipartMessage 实例，然后发送 MultipartMessage。对于文本和图像部分，该方法首先获取表示有效负载的字节数组。

F



对于图像负载，它会从 MIDlet JAR 文件中的资源获取字节；对于字符串负载，它会从包含消息的字符串中获取字节。然后，它会创建一个新的`MessagePart`实例，在创建时指定 MIME 类型和该部分的名称。在创建`MessagePart`时，`sendMMS`方法没有为该部分指定位置，因为不存在位置。此外，包含待发送消息文本的`MessagePart`实例还包含了最初从 Java ME 运行时获取的编码。一旦`sendMMS`使用所需数据创建了`MessagePart`实例，它就会将该消息部分添加到待发消息中。将两个消息部分都添加到待发消息后，代码使用`MessageConnection`的`send`方法发送消息，就像发送任何其他 WMA 消息一样。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 14 章 ■ 使用无线消息 API 进行消息传递 **407**

■**注意** 此示例发送的是编译时随 MIDlet 一起包含的图像。动态生成并编码图像以通过 WMA 发送可能比较困难。我在第 16 章中讨论的移动媒体 API（MMAPI）可以从设备摄像头生成 JPEG 图像，但如果没有 PNG 编码器等额外代码，通常无法对`Image`对象进行编码以通过 MMS 使用 WMA 发送。

**总结**

WMA 首先在 JSR 120 中定义，并在 JSR 205 中进行了扩展，它定义了 GCF 的一个扩展，允许 MIDlet 发送和接收 SMS 和 MMS 消息，以及接收 SMS-CB 消息。本质上，使用它就像编写任何其他支持 GCF 的应用程序一样：你使用`Connector`的`open`方法作为`MessageConnection`对象的工厂，然后可以使用它创建特定的`Message`子类实例，以表示设备要发送或已接收的消息。

你通过 GCF 创建的`MessageConnection`实例充当消息的工厂；你可以使用其`newMessage`方法创建新的 MO 消息，并传入你想要创建的消息类型，或者通过调用其`receive`方法（该方法会阻塞当前线程，直到消息到达）等待传入消息。WMA 定义了`Message`类来封装消息的地址和时间戳概念。`Message`的子类定义了文本 SMS 消息、二进制 SMS 消息和 MMS 消息的特定语义：分别是`TextMessage`、`BinaryMessage`和`MultipartMessage`类。使用这些类，你可以设置消息负载，或者对于`MultipartMessage`实例，你可以管理多个收件人地址以及构成消息的各个部分。一旦配置好待发消息，你就可以使用`MessageConnection`的`send`方法发送它。

使用 WMA 要求你的 MIDlet 具有权限。在打包应用程序时，请确保在你的 JAD 文件中包含你的应用程序所使用的消息类型以及 MIDlet 所使用的消息方向（发送或接收）的权限。

[www.it-ebooks.info](http://www.it-ebooks.info/)

间奏曲

**当你阅读本书时，你已经从所有 Java ME 实现提供的基础知识，逐步深入到构成当今商业上成功的 Java ME 市场的配置、配置文件和包。你可能跳过了第 2 部分或第 3 部分，这取决于你对 Java ME 特定配置的兴趣。**

**在接下来的篇幅中，我将讨论 Java ME 平台的一些最重要的扩展。有些扩展，如 MMAPI，几乎可以在当今生产的所有 Java ME 设备上找到。其他扩展则不那么普遍，要么是因为它们是 Java ME 领域的新成员，要么是因为制造商推动这些 API 面向开发者和消费者的动力较小。与之前的部分不同，那些部分我旨在让你作为主菜来享用和消化，这些章节的组织方式不同。每一章都旨在独立成篇，让你领略 Java ME 平台为满足需求而发展的各种方式。请享用，祝你好胃口！**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 5 部分

其他 Java ME

接口

**在最后四个部分中，你已经学习了使用支持 Java ME 的两种 Java 配置（CLDC 和 CDC）构建应用程序的基础知识。在此过程中，你已经看到了 Java ME 平台中使其如此强大的大部分接口和库：Java 语言和基本 Java 接口；通过 MIDP、AWT 和 AGUI 提供的 GUI 支持；通过 Java 现有机制或 FCOP 提供的文件支持；通过 HTTP 和无线协议进行的通信；以及其他特性。你所看到的大部分内容完全属于 Java ME 平台；有些部分，如 FCOP 和 PIM API 以及对 Web 服务和无线消息传递的支持，实际上是 Java ME 平台的扩展，这些扩展非常普遍，几乎在所有 Java ME 设备上都能遇到。其他部分，如 Personal Profile 和 AGUI 提供的高级图形工具包，则不那么常见，但你未来遇到它们的可能性越来越大。**

**然而，本书的其余部分并非关于 Java ME 本身，而是关于一些位于 Java ME 之上的最强大、最令人兴奋的包。在我撰写本文时，有超过 20 个 JSR 定义了增强 Java ME 平台的可选包。它们提供了支持额外功能的接口，例如加密接口、视频支持和基于位置的服务。并非所有由 JSR 流程定义的包都适用于所有 Java ME 设备，但所有设备之间日益增长的融合趋势意味着，考虑到你的应用程序创意，你很可能会找到一个记录着实现你所需接口的 Java ME Java 包的 JSR，并且会有足够多的设备支持该包。因此，你的应用程序很可能拥有一个可行的市场。**

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章

保护 Java ME

应用程序

**安**全性在当今移动市场的成功中扮演着重要角色。消费设备比以往任何时候都更深入地融入电子商务的基础设施中。这场革命由移动版本的安全和信任技术（主要得益于上一代加密技术的革命）推动，这些技术也为互联网上的电子商务提供动力。

在本章中，我将介绍 Java ME 开发者可用的一些不同组件，这些组件可以帮助你创建更安全的应用程序。首先，我将探讨为什么你应该掌握当今安全基础知识，包括在设计安全应用程序时回顾一些关键构建块，然后我将讨论 Java ME 的安全与信任服务 API——一个提供智能卡和加密支持的可选包。接下来，我将简要介绍 Java ME 相对较新的非接触式通信 API，该 API 通过近场通信设备实现安全商务应用，使你能够将移动设备用作无线钱包。最后，我将以对 Bouncy Castle 加密包的讨论作为结束——这是一个功能齐全的开源包，用于为 Java ME 设备提供加密功能。

**理解安全需求**



当我编写第一个网络应用程序时，互联网还是一个很小的世界——虽然小到并非人人都认识彼此，但密码常常以明文形式传输，如果人们有意为之，几乎每个人都能读到它们。当然，那也是在万维网出现之前，编写网络应用程序需要严谨的协议工作，或者对当时流行的远程过程调用语义有深入理解。转眼几十年过去，互联网已不再是狂野蛮荒的边疆，而是一座熙熙攘攘的大都市。

虽然人们很容易将加强安全性的需求归咎于用户数量的激增，但事实远比这复杂，它既反映了人类的存在，也同样反映了人性。如今，保护应用程序的安全，在抵御（真实或想象中的）针对知识产权和商业资源（如内容、存储、服务和个人信息）的攻击方面，与赢得相关利益方的信任同等重要。

设计安全的应用程序，既需要深入了解应用程序面临的风险，也需要掌握可以采取哪些对策来减轻这些风险。简而言之，*风险*是任何可能导致损失的可能事件。风险与*威胁*相关联——威胁是触发风险事件的一种方式。以下是风险威胁的示例：

*   用户未经付费就访问你的应用程序
*   用户的个人数据（如身份信息）在知情或不知情的情况下被泄露给未经授权的第三方
*   第三方在与你的客户互动时冒充你

在当今高度网络化的世界里，大多数人关注的是由网络应用程序显现出来的风险，但当然，这并非唯一需要关注的风险。考虑一下用户丢失移动设备，第三方访问其记录存储以获取个人数据的情况。

你可以通过采用*对策*来应对威胁，这些对策旨在阻止威胁触发风险，例如：

*   仅通过可信的分发服务，将你的应用程序和内容提供给那些能防止再分发的设备
*   在通过网络传输或存储到设备记录存储之前，对易受攻击的个人数据进行加密
*   在与客户交互时，使用带有签名证书的安全网络协议（如 HTTPS）来验证身份

对策设计的核心是成本概念：在选择对策时，你的目标是让假想的对手执行某项任务的成本，高于该任务在考虑实际风险事件发生可能性后的价值。你必须在安全带来的成本（更高的复杂性、更大的占用空间、更复杂的用户操作）与安全威胁实现后的成本之间取得精细的平衡。

当今保护应用程序安全的一个关键工具是*密码学*：隐藏信息的艺术与实践。密码学解决方案主要基于数论的最新进展，提供了几种应对安全威胁的工具，包括：

*   **密码**：密码允许你加密数据或解密已加密的数据。加密密码接收你的原始消息（称为*明文*），并将其转换为没有相应解密密码的第三方无法读取的形式。密码依赖于*密钥*；要解密加密的消息，你必须拥有密钥。当今的密码利用数论的进步，允许你将密钥分为公钥和私钥两部分；共享秘密消息的参与者自己保留私钥，并使用接收者的公钥来加密消息。
*   **消息摘要**：消息摘要不过是一个巧妙设计的哈希函数；它接收一大块数据，并将其缩减为一小块数据。两种流行的消息摘要函数是 SHA-1（为任意输入生成 20 字节长的摘要）和 MD5（为任意输入生成 16 字节长的摘要）。
*   **数字签名**：数字签名是一种个性化的消息摘要。它使用个人的私钥创建消息摘要，该摘要可以使用签名者的公钥进行验证，以证明生成签名的人确实签署了该消息。
*   **证书**：证书实际上只是数字签名的扩展——它是由可信第三方签署的、用于证明你身份的文件。

■**注意** 要全面了解密码学概念和算法，布鲁斯·施奈尔（Bruce Schneier）所著的《应用密码学：协议、算法与 C 语言源码》（John Wiley & Sons, 1995）是最佳选择。

这些工具如今在移动市场中得到了广泛应用。以数字签名和证书为例：它们为当今无线应用的“甲板内”交付提供了基础。通过 Java 验证计划，第三方在将你的应用程序交给运营商分发之前对其进行签名；当设备下载你的应用程序时，它会验证签名，并根据签署你应用程序的权限授予对受限 API 的访问权限。

这些工具的另一个应用是基于 TLS 的 HTTPS。HTTPS 是 HTTP 的安全版本，你可能正在用它来保护应用程序的网络通信。HTTPS 可以依赖客户端和服务器之间交换的证书来证明双方的身份；无论如何，它都使用公钥密码来加密客户端和服务器之间的网络通信。

当然，密码学只是众多对策中的一种。设计安全的应用程序需要*纵深防御*——永远不要只依赖单一的安全措施。包括物理安全（例如，防止访问你的应用服务数据中心）、日志记录、审计以及使用安全网络通道在内的策略，都能帮助你保护网络应用程序的安全。请记住，密码学只能解决你所面临安全挑战的一部分，而且你面临的许多挑战可能是社会性的（想想当今电子邮件中普遍存在的网络钓鱼诈骗），而非技术性的。

■**注意** 布鲁斯·施奈尔（Bruce Schneier）的《秘密与谎言：网络世界中的数字安全》（John Wiley & Sons, 2000）以及凯文·米特尼克（Kevin D. Mitnick）和威廉·L·西蒙（William L. Simon）合著的《欺骗的艺术》（Wiley Publishing, 2002）是开始学习更多关于应用程序安全面临的技术和社会威胁以及如何建立应对这些威胁的对策的好起点。

**审视 Java ME 的安全与信任服务**

Java SE 提供了大量密码学接口，包括 Java 密码体系结构（JCA）和 Java 密码扩展（JCE）；但 Java ME 的基础实现并非如此。这其中有技术和法律等多方面原因；内存占用和计算复杂性使得密码学在某些 Java ME 设备上难以实现，而如果 Java ME 捆绑了强大的密码学解决方案，出口限制可能会阻碍其在某些市场的采用。


然而，Java ME 的可扩展特性解决了这一问题。JSR 177 定义了 J2ME 的安全与信任服务 API（SATSA）。除了支持加密操作外，SATSA 还包含 Java ME 的可选组件，这些组件提供了与硬件安全组件通信的 API，以及证书和签名管理功能。SATSA 定义了设备可能支持的四个关键可选包：

• *应用协议数据单元（APDU）*：此通信 API 支持你的应用程序与加密硬件（如 Java 智能卡）之间的底层通信。

• *SATSA-Java Card RMI（SATSA-JCRMI）*：此通信 API 允许你的应用程序与 Java 智能卡进行高级通信。

• *SATSA-公钥基础设施（SATSA-PKI）*：此 API 提供对公钥管理的支持。

• *SATSA-CRYPTO*：此 API 提供用于加密的 java.security 包的一个子集。

APDU 协议建立在 GCF 之上，为与 Java 智能卡、通用用户识别模块（USIM）和安全令牌智能卡通信提供了通用接口。APDU 接口适用于底层通信，处理与加密硬件交换的单个字节级别；对于许多应用程序，SATSA-JCRMI API 可能更合适。另外两个 API——SATSA-PKI API 和 SATSA-CRYPTO——为你的应用程序提供高级加密服务。

■**注意** 在我撰写本文时，还没有一个统一的 SDK 能在 NetBeans 或较旧的 Sun Java Wireless Toolkit 中实现所有这些可选包。更糟糕的是，JSR 177 中描述的这些包不仅是可选的，而且供应商可能选择只实现 JSR 177 中描述的*部分*包。这可能导致这些 API 的可用性严重碎片化。

**使用 APDU API 与加密硬件通信**

如今的智能卡保护着许多流行的消费电子设备，包括一些手机、计算机和消费电子机顶盒。它们也是一些工业通信控制系统的关键安全特性。智能卡的核心只是一张小型塑料卡，内部嵌入了一个或多个加固的集成电路，提供存储空间，可能还有处理能力。智能卡通常具有防篡改特性，例如安全文件系统、安全微处理器，以及人眼可见的防篡改指示设备（如全息图或其他信息）。一些智能卡支持加密功能，如密钥生成和密码算法；SATSA API 的目的是支持使用支持 Java ME 的硬件访问这些卡。

在最底层的软件层面，Java 智能卡或 USIM 等硬件通过比特和字节通道与其主机硬件通信，类似于网络套接字或串行端口。国际标准化组织（ISO）提供了 ISO 7816 标准，用于描述智能卡与其主机硬件之间的机械、电子和软件通信方案。该软件方案依赖于应用程序标识符（AID）的概念，允许选择特定的智能卡功能；与智能卡的通信是通过 ISO 定义的应用协议数据单元协议（因此 SATSA 包用于与卡通信的协议得名 *APDU*）完成的。

由于这种对卡功能的底层访问涉及通过通信通道交换字节，你可能会认为 Java GCF 是支持这种通信的绝佳方式，而你的想法是正确的。图 15-1 显示了 GCF 类与 APDU 层次结构之间的关系。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**418**

第 15 章 ■ 保护 Java ME 应用程序

**图 15-1.** *APDU 类与 Java ME GCF 层次结构的关系*

两个接口——`javax.microedition.apdu.APDUConnection` 和 `javax.microedition.jcrmi.JavaCardRMIConnection`——为你的应用程序提供了与智能卡通信的方式。顾名思义，你使用 `APDUConnection` 与卡进行底层 APDU 交互，而使用 `JavaCardRMIConnection` 对 Java 智能卡执行 Java RMI——这是我在下一节“使用 JCRMI 与 Java 智能卡通信”中会涉及的主题。

就像使用 GCF 访问 FCOP 或网络接口一样，你从一个统一资源标识符（URI）开始，该 URI 描述了你的应用程序将要连接的实体。对于智能卡，URI 的格式如下：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**419**

第 15 章 ■ 保护 Java ME 应用程序

`protocol:[slotid];target`

每个部分的定义如下：

• `protocol`：对于基于 APDU 的连接（返回实现 `APDUConnection` 的对象），为 `apdu`；对于基于 JCRMI 的连接（返回实现 `JavaCardRMIConnection` 的对象），为 `jcrmi`。

• `slotid`：一个整数，表示用户插入卡的插槽。

• `target`：要连接的特定智能卡应用程序的应用程序标识符，或者连接到 SIM 应用程序工具包的关键字 `SAT`。

由于设备可以支持多个卡槽，你可以使用 `System.getProperty` 方法并传入 `microedition.smartcardslots` 属性来查询支持智能卡的硬件，该属性返回一个逗号分隔的现有卡槽标识符列表。每个标识符由两个字符组成：第一个字符表示你在指定 URI 以指示插槽时使用的索引，第二个字符是 `C` 或 `H`，表示该插槽是冷插拔还是热插拔——即，卡片只能在设备关闭时移除（冷插拔），还是在设备运行时移除（热插拔）。

一旦你建立了与智能卡的连接，就可以使用 `exchangeAPDU` 方法执行 I/O。使用 `exchangeAPDU`，你可以向智能卡发送命令 APDU，并以字节数组的形式接收响应。如有必要，你可以使用多个 `APDUConnection` 实例建立与智能卡的多个逻辑通道，从而允许你同时使用智能卡上的多个应用程序。例如，你可以编写如清单 15-1 所示的代码。

**清单 15-1.** *与智能卡交换 APDU*

```java
APDUConnection ac;

try {

    byte[] commandAPDU = …;

    String url = "apdu:0;AID=A1.0.0.67.4.7.1F.3.2C.5";

    ac = (APDUConnection)Connector.open(url);

    // 发送命令 APDU 并接收响应 APDU

    byte[] responseAPDU = ac.exchangeAPDU(commandAPDU);

    // 处理响应

    …

} catch(Exception e){

    // 处理异常

    …

```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**420**

第 15 章 ■ 保护 Java ME 应用程序

```java
} finally {

    …

    if (ac != null) {

        // 关闭连接

        ac.close();

    }

}
```

当然，你应该准备好处理异常；可能会发生中断，例如用户在使用过程中弹出智能卡。完成后，你还必须关闭连接，以释放 Java VM 用于连接智能卡的底层资源。

`APDUConnection` 接口还支持启用、禁用、更改和确认个人识别码（PIN），智能卡所有者使用该 PIN 在允许智能卡执行操作之前进行身份验证。以下方法用于与用户交互，并在处理 PIN 后返回智能卡响应：

• `changePin`：提示用户输入卡的当前 PIN，然后输入新 PIN，并在适当时将 PIN 更改提交到卡。

• `disablePIN`：提示用户输入卡的当前 PIN，并禁用后续使用 PIN 进行身份验证的需求。



• enablePIN：提示用户输入当前卡片 PIN 码，并启用后续使用卡片时需要输入 PIN 码的功能
• enterPIN：提示用户输入当前卡片 PIN 码并验证该 PIN 码

要打开一个

APDUConnection，您的应用程序必须以特权身份运行；您必须指定的权限是：对于任意智能卡应用程序 ID，使用`javax.microedition.apdu.aid`；对于 USIM 工具包，使用`javax.microedition.apdu.sat`。通常，此权限仅授予运营商、制造商或第三方可信域中的应用程序。

**使用 JCRMI 与 Java 智能卡通信**

APDUConnection 提供了对智能卡最低级别的访问。这种访问级别不适合胆小者；它要求对智能卡基础知识以及智能卡上特定应用程序的通信协议有深入理解。这样的接口非常适合进行抽象，而这正是 JCRMI 的目的。

JCRMI 利用 Java RMI 架构的概念，允许直接使用 Java 访问支持 Java 的智能卡上的操作（关于 RMI 在 Java 中的工作原理，请参阅第 11 章以获取更深入的理解）。您无需在单个比特和字节层面工作，而是可以连接到卡上的 Java 智能卡应用程序，获取该应用程序的存根接口，并远程调用其方法，如清单 15-2 所示。

**清单 15-2.** *使用 JCRMI 连接 Java 智能卡* try {

String url = "jcrmi:0;AID=A0.0.0.67.4.7.1F.3.2C.3"; JavaCardRMIConnection jc =

(JavaCardRMIConnection)Connector.open(url);

Wallet wallet = (Wallet)jc.getInitialReference();

…

currentBalance = wallet.getBalance();

…

jc.close();

} catch (Exception e) {

…

}

本质上，GCF Connector 类与您指定的 AID 的组合充当了远程 Java 对象的注册表；您使用 JavaCardRMIConnection 实例的`getInitialReference`方法来获取代表卡上远程应用程序的存根引用。

与 APDUConnection 一样，异常也可能发生；如果卡片未插入、智能卡应用程序不存在，或者 Java ME 应用程序根本不允许使用智能卡接口，都可能发生异常。请注意，如果在远程对象使用过程中弹出卡片，对远程对象的访问当然会失败；重新插入卡片需要 Java ME 应用程序重新连接到智能卡应用程序。

JCRMI 接口在以下方面比 Java RMI 或 Java RMI OP 受到显著更多的限制：

• 远程类最多只能实现 15 个接口。
• 远程方法只能返回基本 Java 类型（boolean、byte、short、int 以及这些类型的一维数组）。
• 参数和返回值按值传递，远程对象引用除外。

与 APDUConnection 一样，使用 JavaCardRMIConnection 实例需要权限；您需要在应用程序的 JAD 文件或清单中声明`javax.microedition.jcrmi`权限。与 APDUConnection 一样，此权限的使用仅限于运营商、制造商或第三方可信域中的应用程序。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**422**

第 15 章 ■ 保护 Java ME 应用程序安全

**利用 SATSA 高级 API 进行加密** 虽然您已经看到的 SATSA 定义的两个可选包用于与加密硬件交互，但剩下的两个包提供了当今许多移动应用程序所需的常见加密操作的实现。SATSA-CRYPTO 包包括`java.security`包的一个子集、`java.security.spec`包的一个子集、`javax.crypto`包的一个子集以及`javax.crypto.spec`包的一个子集，以提供用于公钥和私钥管理、消息摘要、签名验证和数据加密的 API。SATSA-PKI 安全包包括`javax.microedition.pki`和`javax.microedition.securityservice`，它们定义了支持基本用户证书管理的类。

使用 SATSA-CRYPTO API，让我们看看您在应用程序开发过程中可能执行的两个常见操作：创建消息摘要和加密（或解密）消息。

使用 SATSA-CRYPTO API 创建消息摘要

移动应用程序可能需要执行的最常见的加密操作之一是创建消息摘要。如今许多 Web 服务 API 使用消息摘要作为防止篡改 Web 服务请求或响应负载的手段；消息摘要可能作为单独的 HTTP 头或 Web 服务请求的参数出现，或者简单地附加到 Web 服务头中。使用 SATSA-CRYPTO API 可以非常轻松地实现这一点，如清单 15-3 所示。

**清单 15-3.** *创建消息摘要*

String webRequest = "…";

byte[] message = webRequest.getBytes();

static String digestAlgorithm = "MD5";

static int digestLen = 16;

byte[] digest = new byte[digestLen];

try {

java.security.MessageDigest md;

md = java.security.MessageDigest.getInstance(digestAlgorithm); md.update(message, 0, message.length);

md.digest(digest, 0, digestLen);

} catch (Exception e) {

// 处理 NoSuchAlgorithmException 或 DigestException

…

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**423**

SATSA-CRYPTO API 提供了 MessageDigest 类，该类既提供了用于创建消息摘要算法实现的静态工厂，也提供了消息摘要算法的接口。您可以通过调用其`getInstance`函数并传递您想要实现的摘要函数名称（`"MD5"`或`"SHA-1"`之一）来获取 MessageDigest 的具体实例。生成的 MessageDigest 对象通过调用其`update`方法消耗您传递给它的字节数组，并在您调用其`digest`方法时提供生成的消息摘要并重置算法。

这段代码假设您手头有一个 Web 请求，并且您希望为其生成一个 MD5 摘要，存储在`webRequest`中。由于 MessageDigest 接口处理字节数组，代码首先将 Web 请求表示为字节数组，然后声明变量来存储生成的摘要。完成此操作后，它获取 MD5 MessageDigest 实现的一个实例，并通过调用`md.update`将构成`webRequest`的字节传递给它。对于长文档，您可以多次调用此方法。一旦使用`update`将消息提供给 MessageDigest 实例，代码就会要求它使用`digest`方法计算摘要，该方法接受一个用于存储输出的字节数组以及数组中的字节数。

SATSA-CRYPTO 提供的 MessageDigest 实现通常包括 MD5 和 SHA-1 算法，但不能保证两者都可用。因此，您的应用程序应准备好处理`NoSuchAlgorithmException`。

您还可以使用 SATSA-CRYPTO API 通过创建源文档的消息摘要并将其与提供的消息摘要进行比较来验证消息摘要。



没有用于比较消息摘要的 API；只需遍历每个摘要中的字节并比较各个字节，即可判断消息摘要是否相等。

使用 SATSA-CRYPTO API 进行加密和解密

在大多数情况下，唯一需要加密数据的时候是当数据离开设备进行网络传输时。在这种情况下，HTTPS 通常足以满足您的数据安全需求；偶尔您可能会发现需要以加密形式在本地存储数据，或使用 HTTPS 以外的协议交换数据。SATSA-CRYPTO API 提供了用于加密和解密数据的接口，但有一个限制：虽然您可以使用公钥（也称为*非对称*）加密技术通过 SATSA-CRYPTO API 加密数据，但您无法解密使用公钥加密技术加密的数据。

最好将 SATSA-CRYPTO 密码 API 视为一个接口，而不是具体的实现。对于给定的目标，无法明确保证您会遇到*哪种*密码实现；一台设备可能支持数据加密标准（DES）、Rivest 密码 4（RC4）和加密标准（AES），而另一台设备可能只支持 DES。无论支持什么，使用 SATSA-CRYPTO 密码接口的一般方法保持不变：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**424**

第 15 章 ■ 保护 Java ME 应用程序安全

**1.** 将要加密的明文生成为一个字节数组。

**2.** 生成用于加密明文的密钥，同样作为一个字节数组。

**3.** 获取所需密码实现的实例，该实例将是`java.security.Cipher`的一个实例。

**4.** 初始化密码。

**5.** 使用 Cipher 的`update`方法逐步加密明文，或使用 Cipher 的`doFinal`方法在单次操作中完成加密。

可以预见，解密是加密的逆过程；不是在步骤 3 中指定密码，而是指定密码并指示它应在解密模式下使用。不是在步骤 5 中传递明文，而是传递密文。请参考清单 15-4，它演示了如何加密一条消息。

**清单 15-4.** *加密一条消息*

```java
String algo= "RC4";
byte[] secretKey = { … };
String plainText = "Here there be treasure";
byte[] plainTextBytes = plainText.getBytes();
try {
    java.security.Key key =
        new SecretKeySpec(secretKey, 0, secretKey.length, algo);
    java.security.Cipher cipher;
    cipher = Cipher.getInstance(algo);
    cipher.init(Cipher.ENCRYPT_MODE, key);
    int ciphertextLength = plainText.length();
    byte[] cipherTextBytes = new byte[ciphertextLength];
    cipher.doFinal(plainTextBytes, 0, plainText.length, cipherTextBytes, 0);
} catch (Exception e) {…}
```

这严格遵循了我逐步概述的算法；特别注意在初始化 Cipher 实例时使用`ENCRYPT_MODE`作为选定模式。解密将是加密的逆过程，代码只需在`init`的相应参数中传递`DECRYPT_MODE`即可。此代码运行后，假设没有异常，字节数组`cipherTextBytes`将包含加密后的文本。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**425**

密码在处理输入方面可能有所不同；有些密码，如 DES，是*分组密码*，要求输入以规则大小的块形式出现，例如 64 字节的块。使用分组密码时，必须将输入填充到块对齐的边界；更复杂的是，某些分组密码是非对称的，这意味着输入块大小和输出块大小不同。其他密码，如 RC4，是流密码：它们只接收字节流（明文或密文）并施展其魔法。使用分组密码时，请确保将完整的块传递给`update`和`doFinal`方法。

使用 Cipher 接口进行加密或解密时，可能会发生多种情况；这些情况通过以下异常之一发出信号：

• 当输入未正确填充或块大小错误时，API 会为分组密码抛出`BadPaddingException`或`IllegalBlockSizeException`。
• 当对密码使用无效密钥时，API 会抛出`InvalidKeyException`。
• 当您在初始化密码之前尝试使用它加密或解密消息时，API 会抛出`IllegalStateException`。
• 如果您尝试为不支持的算法获取 Cipher 实例，API 会抛出`NoSuchAlgorithmException`。

■**注意** 在现实世界中，您不应如此轻率地将密码的私钥嵌入源代码或其他易于读取的资源（如 JAR 文件的组件）中。恶意用户可以通过反编译您的应用程序或 JAR 来找到密钥。相反，您可以在分发时创建密钥，为每个应用程序使用不同的密钥，并且仅通过安全的 HTTP 分发应用程序 JAD。

然而，这意味着您的应用程序无法由 Java 验证计划的参与者进行签名和验证。您还可以提供一个 Web 服务，通过 HTTPS 为您的应用程序提供安全的密钥生成和交付。

**探索 Bouncy Castle 解决方案**
**以应对安全挑战**

虽然像 SATSA 这样的可选解决方案可以解决许多设备的问题，但依赖可选解决方案并不能实现 Java 所承诺的“一次编写，随处运行”。幸运的是，有一个可在 Java ME 上运行的解决方案：Bouncy Castle Java 加密 API。该 API 在澳大利亚开发，并在宽松的开源许可下提供，它为 Java SE 的 JCE 和 JCA 提供了洁净室实现的提供者，

[www.it-ebooks.info](http://www.it-ebooks.info/)

**426**

第 15 章 ■ 保护 Java ME 应用程序安全

以及一个非常适合 Java ME 的轻量级加密 API。该 API 实现了许多流行的密码，包括 DES、AES、Blowfish、国际数据加密算法（IDEA）等，以及一系列消息摘要算法，包括 MD5 和 SHA。该 API 还提供了大量实用程序，包括对抽象语法标记一（ASN.1）编码和解码、X.509 加密证书和公钥交换文件，以及安全/多用途互联网邮件扩展（S/MIME）和 OpenPGP 编解码器的支持。

您可以从 http://www.bouncycastle.org/java.html 获取该 API 的副本；在我撰写本文时，当前版本是 1.39。您需要下载该版本，解压，并将 ZIP 文件放入项目 lib 目录下的 zips 目录中。

Bouncy Castle API 由多个包组成；以下是您最可能使用的包：

• `org.bouncycastle.crypto`：包含表示加密引擎、摘要和其他基本构造的基类
• `org.bouncycastle.crypto.digests`：包含实现消息摘要算法的类
• `org.bouncycastle.crypto.engines`：包含实现密码算法的类
• `org.bouncycastle.crypto.generators`：包含实现密钥生成器的类
• `org.bouncycastle.crypto.params`：包含实现特定密码算法选项表示的类

值得注意的是，用于 Java ME 的 Bouncy Castle 完整实现是*庞大*的：接近 1.5 兆字节！当然，几乎没有应用程序需要 API 提供的所有功能。为了管理依赖该 API 的应用程序的大小，您需要使用混淆器——我在第 3 章“构建 CLDC/MIDP 应用程序”一节中讨论过这一点。NetBeans 包含 ProGuard (http://proguard.sourceforge.net) 混淆器，它在管理输出应用程序大小方面表现出色。图 15-2 显示了与 Bouncy Castle API 一起使用的适当设置；在“构建”属性下选择“混淆”。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00004.png)

第 15 章 ■ 保护 Java ME 应用程序安全

**427**


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


**图 15-2.** *使用 Bouncy Castle API 的 Java ME 项目的代表性混淆设置*

图 15-2 中显示的设置指示混淆器使用以下选项：

• –keep public class *.* extends javax.microedition.midlet.MIDlet: 强制 MIDlet 子类保留其名称

• -dontusemixedclassnames: 不允许在生成混淆类名时使用混合大小写，以解决 Microsoft Windows 中的一个问题

• -defaultpackage '': 重命名除实现 MIDlet 接口的包之外的所有包

• -overloadaggressively: 尽可能为不同的方法使用相同的名称（即，只要不同的方法具有不同的参数和返回值），从而创建更小的包

[www.it-ebooks.info](http://www.it-ebooks.info/)

**428**

第 15 章 ■ 保护 Java ME 应用程序

■**注意** 当混淆使用 Bouncy Castle API 的包时，-defaultpackage '' 指令尤其重要，因为 Bouncy Castle 在 java 包中提供了一些类，例如 java.math.BigInteger。CLDC 的类加载器不会加载系统包中定义的类，因为这样做是一种安全威胁（它会使恶意应用程序能够用自己的类替换系统类）。通过使用 -defaultpackage '' 指令，混淆器会重命名该包中的任何类，因此这不会成为问题。

让我们看看使用 Bouncy Castle API 与使用 SATSA API 创建消息摘要和加密消息的比较。

**使用 Bouncy Castle API 创建消息摘要** 消息摘要函数由 org.bouncycastle.crypto.digests 包提供，该包以与 JCA 一致的方式实现了各种消息摘要算法。摘要算法必须实现由 org.bouncycastle.crypto.Digest 定义的接口；这类似于在 SATSA-CRYPTO API 中也存在的 JCA 的 MessageDigest 接口。您可以编写清单 15-5 中的代码来计算 Web 请求的 MD5 摘要。

**清单 15-5.** *计算 Web 请求的 MD5 摘要* String webRequest = "…";

byte[] message = webRequest.getBytes();

byte[] digest;

org.bouncycastle.crypto.Digest md =

new org.bouncycastle.crypto.digests.MD5Digest();

md.update(message, 0, message.length);

digest = new byte[md.getDigestSize()];

md.doFinal(digest, 0);

这段代码的逻辑与您使用 JCA 或 SATSA-CRYPTO API 编写的代码非常相似。从高层次来看，方法是相同的：创建摘要算法的实例，使用 update 方法向其传递用于计算摘要的字节，然后使用 doFinal 方法计算摘要。但是，存在一些差异：

• 没有用于摘要算法的通用工厂；相反，您需要显式创建所需摘要算法的实例（这有助于在您链接 Bouncy Castle 实现时限制应用程序中包含的类数量）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序

**429**

• 消息摘要算法不会抛出异常来指示错误。

• Digest 接口提供了一个方法，告诉您摘要将有多少字节长。

除了 Digest 接口提供的方便的 getDigestSize 方法之外，您还可以通过调用 Digest 方法 getAlgorithmName 来获取消息摘要算法的人类可读名称，您可以通过应用程序的 UI 将其呈现给用户。Bouncy Castle API 提供的大多数消息摘要算法实际上都实现了 ExtendedDigest 接口，该接口实现了 Digest 并添加了 getByteLength 方法。您可以调用 getByteLength 来了解摘要应用其算法的内部缓冲区的大小。

**使用 Bouncy Castle API 进行加密和解密** Bouncy Castle API 通过加密引擎（位于 org.bouncycastle.crypto.engines 中）提供密码实现，这些引擎实现了特定的接口，例如 AsymmetricBlockCipher、BlockCipher 或 StreamCipher（所有这些接口都可以在 org.bouncycastle.crypto 包中找到）。这些接口都有一个共同的目的：它们允许您初始化密码，以字节数组的形式提供要加密或解密的数据（作为块或消息流的一部分），然后执行加密或解密操作。与 JCA 和 SATSA-CRYPTO API 一样，当您初始化密码时，您需要指明您是希望实现执行加密还是解密，以及操作的密钥详细信息。使用 Bouncy Castle API 执行 RC4 加密，您可以编写如清单 15-6 所示的代码。

**清单 15-6.** *使用 Bouncy Castle API 执行 RC4 加密*

byte[] secretKey = { … };

String plainText = "Here there be treasure";

byte[] plainTextBytes = plainText.getBytes();

org.bouncycastle.crypto.StreamCipher cipher =

new org.bouncycastle.crypto.engines.RC4Engine();

cipher.init( true,

new org.bouncycastle.crypto.params.KeyParameter(secretKey)); byte[] cipherTextBytes = new byte[plainTextBytes.length]; try {

cipher.processBytes( plainTextBytes, 0,

plainTextBytes.length,

cipherTextBytes, 0 );

} catch( Exception e ) {…}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**430**

第 15 章 ■ 保护 Java ME 应用程序

与消息摘要一样，Bouncy Castle API 的接口在概念上类似于 JCA 和 SATSA-CRYPTO API，但并不完全相同。同样，您无需使用工厂来创建所需的密码引擎，而是直接实例化该引擎。您以类似的方式初始化生成的引擎，指定您希望引擎加密（传递 true）还是解密（传递 false），以及引擎的 init 方法的选项。最后，将要加密或解密的字节传递给引擎进行处理。引擎如何接受您希望它处理的字节取决于引擎实现的是分组密码还是流密码。您可以使用 processBlock 方法将字节传递给分组密码或非对称分组密码进行处理，每次传递一个完整的块。您可以通过调用其 getBlockSize 方法来确定分组密码的块大小，或者通过分别调用 getInputBlockSize 或 getOutputBlockSize 来确定非对称分组密码的输入或输出块大小。您可以使用 processBytes 方法将字节传递给流密码。

无论哪种情况，密码引擎都会将结果返回给您为处理而调用的方法。此方法可能会抛出多种异常，包括数据数组长度无效时的 DataLengthException，或者您未能初始化密码时的 IllegalStateException。

尽管此示例使用了（相对较弱的）RC4 算法，但 Bouncy Castle API 中还打包了许多其他受支持的密码。与 SATSA-CRYPTO API 不同，这些算法是为对称和非对称密码实现的，因此需要公钥密码加密和解密的应用程序受益于使用 Bouncy Castle API 而不是 SATSA-CRYPTO API。

除了支持许多不同的密码之外，Bouncy Castle API 还为其支持的密码提供密钥生成算法。这是该 API 的一个重要特性，因为在编写应用程序时，安全的密钥生成和分发是一个挑战。org.bouncycastle.crypto.generators 中包含的类包括受支持密码的生成器；创建密钥就像创建适当的生成器并调用方法一样简单。例如，要为 DES 创建随机密钥，您可以编写如清单 15-7 所示的代码。

**清单 15-7.** *生成随机密钥*

org.bouncycastle.crypto.generators.DESKeyGenerator generator =



`new org.bouncycastle.crypto.generators.DESKeyGenerator(); byte[] key = generator.generateKey();`

某些密钥生成器必须先进行初始化，因此请务必查阅所选密码系统和密钥生成器的文档。这些密钥生成器通常是为公钥密码学提供密钥的，其中使用一对密钥——一个用于分发给其他方的公钥，另一个用于解密的私钥——来进行消息的加密和解密。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**431**

■**警告** 虽然安全密钥生成是 Bouncy Castle API 提供的一大福音，但这并非故事的终点。应用程序如何处理其加密密钥，与如何创建它们同样重要；弱存储或交换（例如通过不安全的网络信道共享对称密码的密钥）首先就违背了拥有良好密钥生成算法的初衷。

**利用非接触式通信创建安全商务**

非接触式通信——包括*近场*通信，如 RFID 标签和条形码——正成为移动市场的一个重要组成部分。

有了对非接触式通信的适当支持，您可以创建多种应用程序，例如“刷手机支付”（将手机用作移动钱包）、“移动购买”（扫描条形码并启用远程购买）或“比价购物”（扫描条形码以了解更多产品信息，包括比价）。JSR 257 定义了非接触式通信 API，这是一套可选包，为 RFID 标签（也称为*邻近标签*）和条形码（也称为*视觉标签*）提供支持。

■**提示** 在我撰写本文时，最适合您开发非接触式连接 API 的开发环境是诺基亚近场通信 (NFC) SDK，您可以在网上通过访问 http://www.forum.nokia.com/main/resources/technologies/nfc/ 找到它。

非接触式通信 API 提供了五个包，其中四个可能存在于 API 的任何特定实现中，也可能不存在：

• `javax.microedition.contactless`：始终存在，并提供允许您发现附近近场设备的类。

• `javax.microedition.contactless.ndef`：如果支持与支持 NFC 数据交换格式 (NDEF) 的卡进行通信，则提供此包。

• `javax.microedition.contactless.rf`：如果支持与通用 RFID 卡进行通信，则提供此包。

• `javax.microedition.contactless.sc`：如果支持与符合 ISO 144443 邻近标准的 RFID 卡进行通信，则提供此包。

• `javax.microedition.contactless.visual`：如果实现支持条形码识别和显示，则提供此包。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**432**

第 15 章 ■ 保护 Java ME 应用程序安全

这些包依赖于设备上存在 CLDC 1.1 或更高版本，或来自其他 Java 配置的等效支持。

非接触式通信设备有多种类型，但都基于某种通常围绕 RFID 标签构建的无线协议。非接触式通信 API 目前支持以下类型的非接触式设备：

• *符合 NDEF 的设备*：通过交换包含单个 NDEFRecord 对象的 NDEFMessage 对象进行通信。

• *符合 ISO 144443 的设备*：通过一个 Connection 子类进行通信，该子类允许交换表示为字节数组的 APDU。

• *通用 RFID 标签*：与这些设备的通信通过一个 Connection 子类进行，该子类允许您交换命令向量。

并非非接触式通信 API 的每个实现都支持所有这些选项。

使用非接触式通信 API 时，您的应用程序通常会执行以下一项或多项操作：

• *注册以在非接触式目标可用时接收通知*：您可以使用 MIDP 推送注册表（参见第 14 章了解如何使用 MIDP 推送注册表）或 API 提供的 `javax.microedition.contactless.DiscoveryManager` 类来执行此操作。

• *使用非接触式连接 API 提供的适当* Connection *子类与非接触式通信设备进行通信*：您可以在设备进入范围时执行此操作——该事件报告为 MIDP 推送或来自 DiscoveryManager 的事件。

• *如果需要视觉标签识别，则使用 MMAPI 捕获图像*：有关如何执行此操作的详细信息，请参见第 16 章。您将捕获的图像传递给 VisualTagConnection 实例进行识别。

• *如果需要视觉标签生成，则创建要编码为条形码的字节流，并将字节传递给* VisualTagConnection *以生成图像*：生成图像后，将其呈现给用户。

**发现非接触式目标**

非接触式通信带来一个挑战，即您的应用程序不知道*何时*会遇到可以与之通信的目标。您的应用程序可能需要始终监听目标（用于销售点确认付款或用于安全识别机制的应用程序通常属于此类），或者可能仅在应用程序处于活动状态时监听目标——例如，一旦用户构建了要发送给目标的消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**433**

要始终监听非接触式目标，您的应用程序只需向 MIDP 推送注册表注册此意图。当支持 NDEF 或非接触式目标进入 Java ME 设备的近场范围时，API 会提供推送通知。要注册以在此发生时接收推送通知，您需要构造一个 URI，指示应用程序寻找的 NDEF 记录类型，并将此 URI 放在 JAD 文件中名为 `MIDlet-Push-*n*` 的字段中。

该 URI 可以指定当 Java ME 设备发现包含特定 MIME 编码数据或特定记录类型的目标时，您的应用程序是否应启动。无论哪种情况，推送注册表 URI 都以协议 `ndef` 开头，然后继续使用 MIME 类型声明或记录类型声明，如下所示：`ndef:mime?name=text/x-uri`

每当 Java ME 设备遇到一个带有 URI（即一个 MIME 类型为 `text/x-uri` 的数据块）的支持 NDEF 的目标时，此推送注册表 URI 将启动您的应用程序。这是另一个示例：

`ndef:rtd?name=urn:nfc:wkt:Sp`

在此示例中，URI 的前两个字段——`urn` 和 `nfc`——指示该记录是一个 NDEF 记录，而 `wkt` 指示该记录的命名空间。最后，`Sp` 指示正在寻找的目标是一个智能海报。

如果您的应用程序使用注册表来接收附近目标的通知，它还应实现 `javax.microedition.contactless.ndef.NDEFRecordListener` 接口，该接口为平台提供了一种将数据从目标传输到应用程序的方法。此方法接受一个参数，即由目标生成的 `NDEFRecord`。（我将在下一节更详细地讨论这些记录。）

一旦您的应用程序启动，您还可以使用 `javax.microedition.contactless` 包提供的 `DiscoveryManager` 类来监听目标。



所有符合非接触式通信 API 规范的设备都必须使用此接口，它允许你枚举支持的目标类型，并允许你在 Java ME 设备遇到特定目标时注册监听器。该监听器必须实现 `javax.microedition.contactless.TargetListener` 接口，该接口定义了一个名为 `targetDetected` 的单一方法，当设备遇到新目标时，API 会调用该方法。API 会向此方法传递一个 `TargetProperties` 数组，用于描述设备遇到的目标。例如，清单 15-8 为支持 NDEF 的目标注册了一个监听器。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**434**

第 15 章 ■ 保护 Java ME 应用程序

**清单 15-8.** *为支持 NDEF 的目标注册监听器* import javax.microedition.contactless.*;

import javax.microedition.contactless.ndef.*;

import javax.microedition.pim.*;

import java.util.Enumeration;

import javax.microedition.io.Connector;

public class NDEFExample implements TargetListener {

private DiscoveryManager dm;

NDEFTagConnection conn;

public void registerForDiscovery() {

TargetType[] targets = DiscoveryManager.getSupportedTargetTypes(); boolean supported = false;

for (int i=0; i<targets.length; i++) {

if (targets[i].equals(TargetType.NDEF_TAG)) {

supported = true;

}

}

if (supported) {

dm = DiscoveryManager.getInstance();

try {

dm.addTargetListener(this, TargetType.NDEF_TAG);

}

catch (ContactlessException e) { … }

}

}

public void targetDetected(TargetProperties[] prop) {

TargetProperties target = prop[0];

String url = target.getUrl();

try {

conn = (NDEFTagConnection)Connector.open(url);

if (conn != null) {

readMessage();

}

}

catch (IOException e) { }

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序

**435**

public static void readMessage()

{

…

}

public static void writeMessage()

{

…

}

}

注册过程首先需要确定支持哪些目标类型，这一点非常重要；你可以通过调用 `DiscoveryManager` 的静态方法 `getSupportedTargetTypes` 来获取 API 特定实现所支持的目标类型列表。

■**提示** 务必枚举支持的类型！某些版本的非接触式通信 API 可能支持除本文讨论之外的*其他*类型的近场设备；详情请参阅特定 Java ME 设备及 API 实现附带的文档。

代码在测试确认实现支持 NDEF 类型后，会使用 `addTargetListener` 方法注册一个监听器。如果你想监听多种目标类型，可以使用单个 `DiscoveryManager` 来实现；你可以指定不同的对象来响应 `targetDetected` 事件，也可以在 `targetDetected` 方法内部处理检测到的目标类型。

注册完成后，当设备遇到相应类型的目标时，API 实现会调用你的监听器的 `targetDetected` 方法；此时，你的代码应开始与设备进行通信。这可能是你的应用程序需要启动单独线程的地方，尤其是在通信交换不仅仅包含一两条命令的情况下。

**与非接触式目标通信**

不出所料，与目标的通信是通过无处不在的 GCF 进行的；与各种目标通信的方式体现在 `Connection` 的子类中，如图 15-3 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**436**

第 15 章 ■ 保护 Java ME 应用程序

**图 15-3.** *用于与非接触式目标通信的支持类* 遗憾的是，实现 `Connection` 正是各个子类之间统一性终结的地方。该 API 采用面向消息的方式与非接触式目标通信，但不同目标类型的方法有所不同：

• 支持 NDEF 的设备使用 `javax.microedition.contactless.ndef.NDEFTagConnection` 类，通过连接的 `readNDEF` 和 `writeNDEF` 方法来交换 `NDEFMessage` 实例。



• 支持 ISO 14443 的设备使用 `javax.microedition.contactless.sc.ISO14443Connection` 类及其 `exchangeData` 方法来交换原始字节数组。

• 通用 RFID 设备使用 `javax.microedition.rf.PlainTagConnection` 类。该类的实例通过 `tranceive` 方法交换 Java 对象向量。这些 Java 对象由封装特定应用数据的 API 扩展定义。

• 视觉标签可以使用 `javax.microedition.contactless.visual.VisualTagConnection` 类进行编码和解码，该类实际上并不连接任何设备。（我将在下一节中详细介绍这个类。）

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**437**

NDEF 定义了由一条或多条*记录*组成的*消息*，每条记录采用轻量级二进制消息格式，包含有效载荷类型、可选的有效载荷标识符以及有效载荷本身。例如，要读取任何待处理的 NDEF 记录，你可以编写如清单 15-9 所示的代码。

**清单 15-9.** *读取待处理的 NDEF 记录*

```java
public static void readMessage()
{
    try
    {
        NDEFMessage message = conn.readNDEF();
        if(message == null)
        {
            return;
        }
        NDEFRecord[] records = message.getRecords();
        if(records == null)
        {
            return;
        }
        System.out.println("Got "+records.length+" records.");
        for (int i = 0; i < records.length; i++)
        {
            NDEFRecordType type = records[i].getRecordType();
            System.out.println("id: " + new String(records[i].getId()) );
            System.out.println("type: " + type.getName() );
            System.out.println("payload: " + new String(records[i].getPayload()));
        }
    } catch (Exception e) { … }
}
```

这段代码遍历返回的记录列表，打印每条消息的 ID 和有效载荷。消息也可能带有类型，可通过调用记录的 `getRecordType` 函数获取，该函数返回一个 `NDEFRecordType` 实例。此类型定义了多种记录类型，包括 `EMPTY`、`NFC_FORUM_RTD`、`MIME`、`URI`、`EXTERNAL_RTD` 和 `UNKNOWN`。一种常见的类型是 URI 类型，它表示消息的有效载荷是一个 URL，例如智能海报设备提供的 URL。

如果需要向 NDEF 目标写入记录，可以创建一个新的 `NDEFRecord` 并使用 `NDEFTagConnection` 的 `writeNDEF` 方法。定义新的 `NDEFRecord` 时，必须提供记录的**类型**、**ID** 和**有效载荷**，如清单 15-10 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**438**

第 15 章 ■ 保护 Java ME 应用程序安全

**清单 15-10.** *定义一个新的 NDEF 记录*

```java
public static void writeMessage()
{
    try {
        PIM pim = PIM.getInstance();
        ContactList cl =
            (ContactList)pim.openPIMList(PIM.CONTACT_LIST, PIM.READ_ONLY);
        Enumeration items = cl.items();
        Contact item = (Contact) items.nextElement();
        String name = item.getString(Contact.FORMATTED_NAME,0);
        NDEFRecord[] records = new NDEFRecord[1];
        records[ 0 ] = new NDEFRecord(
            new NDEFRecordType(NDEFRecordType.UNKNOWN, "name"),
            new String("F_M_NAME").getBytes(),
            name.getBytes());
        NDEFMessage message = new NDEFMessage(records);
        conn.writeNDEF(message);
    } catch (Exception e) { … }
}
```

这段代码传输 PIM 联系人列表中的第一个名字。首先，它需要使用 PIM API（参见第 7 章）获取 PIM 管理器的实例，以确定该名字。它使用 PIM 管理器获取联系人列表，然后使用联系人列表提供的枚举来获取联系人第一条记录中存储的名字。接着，代码创建一个新的 NDEF 记录数组，其中包含一条记录。

该记录被分配一个新的 `NDEFRecord`，类型为未知，带有 ID `F_M_NAME` 以及从联系人列表中获取的格式化名称。然后使用记录数组创建一条新消息，并通过连接写入。

对其他非接触式目标（如 ISO 14443）的读写操作类似，不过不是使用连接器提供的单独的读写方法，而是直接交换消息，如清单 15-11 所示。

**清单 15-11.** *直接交换消息*

```java
import java.io.IOException;
import javax.microedition.contactless.*;
import javax.microedition.contactless.sc.*;
import javax.microedition.io.Connector;
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**439**

```java
public class ISO14443Example implements TargetListener {
    byte[] commandAPDU = ...;
    public ISO14443Example () {
        try {
            DiscoveryManager dm = DiscoveryManager.getInstance();
            dm.addTargetListener(this, TargetType.ISO14443_CARD);
            dm.addTransactionListener(this);
        }
        catch (ContactlessException e) { … }
    }
    public void targetDetected(TargetProperties[] properties) {
        TargetProperties target = properties[0];
        Class[] classes = target.getConnectionNames();
        for (int i=0; i<classes.length; i++) {
            try {
                if (classes[i].equals(Class.forName(
                    "javax.microedition.contactless.sc.ISO14443Connection")
                )) {
                    String url = target.getUrl(classes[i]);
                    ISO14443Connection smc =
                        (ISO14443Connection)Connector.open(url);
                    byte[] responseAPDU = smc.exchangeData(commandAPDU);
                    // 对响应进行处理
                }
            }
            catch (Exception e) { … }
        }
    }
}
```

ISO 14443 目标（或其他 RFID 目标）的检测过程与支持 NDEF 的目标完全相同；只需在 `DiscoveryManager` 的 `addTargetListener` 接口中指定 `TargetType` 为 `ISO14443_CARD`。一旦检测到目标，平台会调用已注册的监听器 `targetDetected`，然后该监听器可以与目标交换 APDU 数据包。

如果这个接口看起来很眼熟，那很正常；因为 ISO 14443 设备与本章前面讨论的可选 SATSA API 支持的有线智能卡是近亲。

■**注意** 由于大多数非接触式目标设备的硬件限制，你一次只能打开一个到非接触式目标的连接。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**440**

第 15 章 ■ 保护 Java ME 应用程序安全

**识别和生成视觉标签**

*视觉目标*——这是条形码的别称，你经常会看到接口规范将其称为*标签*——为移动电子商务应用提供了一条有趣的途径，因为它们如今在产品包装上无处不在。反过来，这意味着这些目标的读取器也很常见，从而支持诸如将凭证传输到读取器以实现无纸化门票等应用。

非接触式连接 API 对视觉标签的支持与其他非接触式目标略有不同，因为可能没有自动发现过程。虽然你的应用程序可以向 `DiscoveryManager` 注册，但你也可以启动对视觉标签的手动扫描。如果用户想要扫描视觉标签，则需要由她看到条形码，启动你的应用程序，让应用程序使用 MMAPI 接口捕获条形码图像，并将图像提交给非接触式 API 进行识别。类似地，你可以将任意数据编码到条形码中并显示在屏幕上，这样用户就可以将条形码呈现给条形码扫描器。

条形码有不同的类型，称为*符号体系*。有些符号体系，例如欧洲商品编号/通用产品代码（EAN/UPC），是一维的，包含单条信息条纹。其他较新的条形码，例如快速响应码（QR 码），是二维的信息方块或矩形。这些二维条形码可以编码更多信息，应用程序通常使用它们来携带 URL 或其他信息。使用视觉标签接口时，首先需要确定你想要的符号体系是否被实现所支持。

通过调用静态方法 `SymbologyManager.getReadSymbologies` 可以确定支持的符号体系列表，该方法返回一个字符串数组，命名了支持的符号体系。表 15-1 列出了可能支持的符号体系。



除了与符号体系无关外，非接触式通信 API 也与图像格式无关。`SymbologyManager` 维护了一个受支持图像类的列表，实现可以通过这些类进行解码或编码；你可以通过静态方法 `SymbologyManager.getImageClasses` 获取这些列表。该方法返回一个受支持的 `Class` 实例数组；然后你可以仔细查看该列表，以确定实现是否支持特定的图像类型。

一旦确认支持所需的符号体系和图像类型，就可以执行编码或解码操作了。你需要使用一个 `VisualTagConnection` 对象来完成此操作，该对象通过打开指向 `"vtag://"` URI 的 `Connector` 来获取。得到的 `VisualTagConnection` 实例可以执行必要的编码或解码。使用 `Connection` 子类来做建立连接之外的事情有点奇怪，但非接触式连接 API 就是这样定义接口的；这至少提供了与其他非接触式接口在概念上的一致性。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**441**

**表 15-1.** *非接触式通信 API 通常支持的符号体系* **符号体系名称**

**负责该标准的机构**

aztec-code

ANSI/AIM B13 ITS/97/002

code-16k

ANSI/AIM BC7/1995

code-39

ANSI/AIM BCI-1995, ISO/IEC 16388

code-49

ANSI/AIM BC6-1995

code-93

ANSI/AIM BC5-1995

code-128

ANSI/AIM BC4-1999, ISO/IEC 15417

codebar

ANSI/AIM BC3-1995

data-matrix

ISO/IEC 16022

ean-upc

ISO/IEC 15420

interleaved-2-of-5

ANSI/AIM BC2-1995, ISO/IEC 16390

maxicode

ANSI/AIM BC10, ISO/IEC 16023

pdf417

ISO/IEC 15438

qr-code

AIM ITS/97/001, ISO/IEC 18004

清单 15-12 展示了一个封装了 QR 码编码和解码的类。

**清单 15-12.** *编码和解码 QR 码* import java.io.IOException;

import javax.microedition.lcdui.*;

import javax.microedition.io.Connector;

import

javax.microedition.contactless.ContactlessException;

import javax.microedition.contactless.visual.*;

public class ProcessVisualTag {

private final String sym ="qr-code";

private Class imageClass;

private byte[] data;

private Image image;

public ProcessVisualTag(Object im) {

image = (Image)im;

imageClass = im.getClass();

decode();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**442**

第 15 章 ■ 保护 Java ME 应用程序安全

public ProcessVisualTag(byte[] d) {

data = d;

imageClass = new String("javax.microedition.lcdui.Image"); encode();

}

public byte[] getData() {

return data;

}

public Image getImage() {

return image;

}

private boolean isSupported() {

boolean supportedSym = false;

boolean supportedImage = false;

try {

String[] symbologies = SymbologyManager.getReadSymbologies(); for (int i=0; i<symbologies.length; i++) {

if (symbologies[i].equals(sym)) {

supportedSym = true;

break;

}

}

Class[] images = SymbologyManager.getImageClasses();

for(int i=0; i<images.length; i++) {

if (images[i].equals(imageClass) ) {

supportedImage = true;

imageClass = images[i].getClass();

break;

}

}

}

catch (Exception e) { return false; }

return supportedSym && supportedImage;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序安全

**443**

private void decode() {

try {

if ( isSupported() ) {

VisualTagConnection conn =

(VisualTagConnection)Connector.open("vtag://"); data = conn.readVisualTag(image, imageClass, sym);

conn.close();

}

}

catch (IOException ioe) { ... }

catch (VisualTagCodingException ce) { ... }

}

private void encode() {

try {

if ( isSupported() ) {

ImageProperties props =

SymbologyManager.getImageProperties(sym);

VisualTagConnection conn =

(VisualTagConnection)Connector.open("vtag://"); image = (Image)conn.generateVisualTag(data, imageClass, props); conn.close();

}

}

catch (ContactlessException ce) {…}

catch (IOException ioe) {…}

catch (VisualTagCodingException ce) {…}

}

}



创建时，此辅助工具接受一个图像对象（例如使用 MMAPI 获取的对象，如第 16 章所示）或一个表示字节流的字节数组，并创建相应的表示形式，可以是字节数组或编码后的二维码。如果你获取到编码后的二维码，你将收到一个 `Image` 类的实例作为数据。`isSupported` 方法负责发现支持的符号体系和图像表示形式，向 `SymbologyManager` 查询其支持的符号体系和图像类列表。（当代码找到匹配的图像类时，我会将其复制到 `imageClass` 实例变量中，因为生成视觉标签的方法接受一个 `Class` 实例，而不是类名。）

[www.it-ebooks.info](http://www.it-ebooks.info/)

**444**

第 15 章 ■ 保护 Java ME 应用程序

`VisualTagConnection` 接口提供了两个方法：`readVisualTag` 和 `generateVisualTag`。`readVisualTag` 方法（你可以在清单 15-12 的 `decode` 方法中看到）很直接，它接受源图像、源图像的类以及编码符号体系；成功扫描后生成的字节以字节数组形式返回。此方法可能抛出 `IOException` 或 `VisualTagCodingException`，后者表示编码操作失败。

`generateVisualTag` 方法（你可以在清单 15-12 的 `encode` 方法中看到）是 `readVisualTag` 的编码对应方法，它接受要编码的数据、数据应编码到的类（推测是 `Image` 或其子类），以及一组描述编码操作中符号体系特定属性的属性。你应该始终通过调用 `SymbologyManager` 的 `getImageProperties` 方法来获取特定符号体系的这些图像属性；生成的属性定义了诸如生成的条形码尺寸以及像素间距等内容。

**总结**

Java ME 平台为应用程序开发者提供了一定数量的安全相关接口，例如包含用于安全网络交易的 HTTPS。然而，构建一个安全的应用程序不仅仅是加密网络流量；一个安全的应用程序可能需要防御各种威胁，包括未经授权的使用或对其私有数据的访问。

幸运的是，针对安全挑战的解决方案是存在的，它们提供了用于数据隐藏的密码、用于防御数据篡改和证明消息来源的摘要和签名，以及用于提供身份的证书。Java 社区通过多种方式实现了这些安全工具来响应需求，包括 JSR 177 定义的 SATSA；开源 Bouncy Castle API，它提供了一套全面的安全和加密接口；以及用于构建安全商业应用的更高级构建模块，例如 JSR 257 定义的非接触式通信 API。

某些设备上可用的 SATSA 可选接口为安全问题提供了各种硬件和软件解决方案，包括访问加密智能卡的 API、用于安全管理公钥的基础设施，以及用于加密和解密数据的 JCA 的有限子集。使用 SATSA 可选接口，你的应用程序可以通过 GCF 在单个字节级别访问各种智能卡，如果设备支持 Java 智能卡，则可以通过 Java RMI 访问。你的应用程序还可以使用此 API 使用对称密码执行加密和解密。

开源 Bouncy Castle API 提供了一套全面的密码学解决方案来应对安全问题，它支持多种不同类型的密码和消息摘要。它还提供了其他数据表示形式的实现，包括 ASN.1 和 S/MIME，以及支持 OpenPGP 编码和解码。其接口类似于 SATSA 为常见操作（如加密、解密和创建消息摘要）定义的接口，并且其纯 Java 的洁净室实现使其成为任何需要使用其功能，同时保持对可能支持或不支持 SATSA 的硬件的可移植性的应用程序的理想选择。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 15 章 ■ 保护 Java ME 应用程序

**445**

如今的移动应用程序以多种方式利用安全解决方案，但电子商务在推动安全技术进入移动设备方面正扮演着越来越重要的角色。为了支持安全的商业应用，可选的非接触式通信 API 提供了与多种无线数据设备（如近场无线设备和条形码）通信的手段。该 API 既支持在低级层面（交换单个字节）操作接近卡，也支持 NDEF，并且还允许你解码或生成条形码图像。

安全是一个大话题，本章只是浅尝辄止。这不像用香料烹饪；虽然你可以往菜里扔一把辣椒让它变辣，但你不能往应用程序里扔一把安全 API 就让它变得安全。（实际上，它们比你想象的更相似：最终做出来的菜可能和你应用程序的不安全性一样难以下咽！）设计一个安全的应用程序需要细心和深思熟虑，如果你正朝着这个目标努力，请务必查阅本章中提到的一个或多个优秀的互联网和书籍参考资料以获取更多细节。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章

渲染多媒体内容

**当**今的消费者越来越渴望他们在个人电脑或其他设备（包括移动无线终端和便携式媒体播放器）上所习惯的丰富多媒体体验。与此同时，为了满足这种需求并使产品在竞争中脱颖而出，制造商们越来越多地投入资金为其产品开发丰富的多媒体界面。虽然专家们可能会争论说，这些投资中有很多是多余的——例如，如今手机用户界面中的许多花哨和魅力对于打电话来说并非真正必要——但其中很多并非如此。多媒体应用，从普通的音频和视频播放到复杂的数据可视化应用，既可以区分产品，也可以增加价值。通过诸如定义 MMAPI 的 JSR 135 和定义对可缩放二维矢量图形支持的 JSR 287 等标准化工作，Java ME 提供了丰富的接口，使你能够构建富多媒体应用程序。

在本章中，我将展示这些 JSR 定义的接口。我首先讨论 MMAPI，因为它对音频和视频的支持使其成为当今许多应用程序的重要组成部分。我将解释 MMAPI 是如何组织的，以及它如何利用 Java 运行时和来自主机硬件的原生支持来渲染来自设备本地存储和网络的内容。接下来，我将注意力转向 Java ME 对 SVG 的支持——这是 W3C 正在开发的一个令人兴奋的标准，并得到了浏览器以及移动设备的广泛支持。我将向你展示如何使用可选的可缩放二维矢量图形 API（我将其简称为 SVGAPI）提供的类来渲染 SVG 图像和动画，以及如何构建利用 SVG 内容作为其用户界面一部分的 Java ME 应用程序。在介绍了所有可用的多媒体支持类的基础知识之后，我将展示一个示例 MIDlet，它可以播放音频、视频和 SVG 内容，并让你与设备的摄像头交互以捕获图像。

**447**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**448**

第 16 章 ■ 渲染多媒体内容

**介绍 MMAPI**

从移动 Java 历史的相对早期开始，硬件供应商和 Sun 就共同探索 Java 应如何支持多媒体内容的渲染。



然而，当 Java 社区的参与者们正在敲定 MMAPI 的细节时，当时的硬件设备几乎只能勉强处理数字音频，更不用说视频了。很快，所有人都认识到多媒体支持将对许多 Java ME 应用发挥重要作用。

MMAPI 提供了一个模块化的 API，使你既能够通过指定频率和音调数据来创建自己的简单音频媒体，也能够使用多种编解码器来渲染多媒体音频和视频。由于 MMAPI 是可扩展的，硬件制造商可以添加新的编解码器和传输协议，这样当新的数据格式出现时，你就可以使用相同的接口来渲染它们。

■**注意** 由于篇幅限制，我无法详尽地介绍 MMAPI 的所有功能。

如果你在阅读本章后想进一步了解 MMAPI，我建议你阅读 Vikram Goyal 所著的 *Pro Java ME MMAPI: Mobile Media API for Java Micro Edition*（Apress, 2006），该书更详细地介绍了如何在 Java ME 应用中使用 MMAPI。

**理解基本多媒体概念**

要成功使用 MMAPI，你必须理解其划分媒体渲染职责的三个关键概念。第一个概念涉及创作者和分发者如何打包和交付多媒体内容。内容创作者（无论是大公司还是个人在 YouTube 等社交网站发布内容）在交换多媒体内容时使用特定的*媒体类型*。如今的媒体类型通常是复杂的文件格式，充当高度压缩的视听数据流的*容器*。例如，流行的运动图像专家组版本 4（MPEG-4）标准不仅定义了一种，而是一整套音频和视频编码格式；一个 MPEG-4 文件可能包含一个与 H.263 视频流同步的高级音频编码（AAC）音频流，也可能包含一个与 MPEG-4 视频流同步的 PureVoice 音频流，或者完全是其他组合。因此，当设备供应商指定其硬件可以渲染的多媒体类型时，他们通常会同时根据容器格式*和*编解码器来提供信息。通常，当今大多数移动设备依赖于数字信号处理器（DSP）、专用集成电路等硬件与软件的复杂组合，来实现渲染多媒体所需的编解码器。特定的 MMAPI 栈实现会与这些硬件和软件互联，为渲染各种多媒体格式提供高效的机制。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**449**

■**警告** 容器文件格式与数据文件格式之间的界限常常很模糊，尤其是当许多编码格式本身也是容器时。在与内容提供商合作时，仅仅说“请给我一个 MPEG-4 文件”是不够的，就像你索要一个 PNG 图像那样。更糟糕的是，不同设备支持的编码方案往往不同，因此你可能会发现，一个能在某台设备上播放的 MPEG-4 文件，在另一台设备上却无法播放，因为文件*内部*的数据流在第一台设备上使用了兼容的编解码器（例如，在支持 AAC 的设备上使用了 AAC），而在第二台设备上则不兼容。稍后，我将向你展示如何查询设备的 MMAPI 实现，以确定哪些编解码器可用。

其次，内容提供商可以通过文件交付多媒体内容——这是一种一次性完成的过程，设备将整个文件（例如一首歌或一个视频）存储在设备的文件系统中——或者使用诸如实时流协议（RTSP）等协议来流式传输数据。大多数多媒体系统的接口将数据源视为一个独立的实体，恰当地称之为*数据源*。数据源隐藏了设备如何获取编码数据的所有细节；它可能从文件中获取数据，也可能通过网络使用多种开放或专有技术中的任何一种来获取数据。

第三，对于普通应用程序开发者来说，编解码器这一底层概念通常处于错误的抽象层次。在应用层工作时，最容易想到的是*播放器*：一个引擎，给定一个数据源以及关于该数据源数据如何编码的一些信息，它就能将数据渲染为音频或视频流。播放器通常要么直接提供管理渲染的接口，要么（像 MMAPI 提供的某些平台那样）提供一个接口来获取一个或多个*控制器*，这些控制器可以控制特定播放器对来自数据源的数据进行渲染。

■**注意**

有经验的读者无疑会看到 MVC 模式与许多多媒体 API 的源-播放器-控制器组织方式之间的相似之处。这并非巧合；MVC 提供的职责划分非常适合渲染多媒体流所需的工作，至少从我们作为应用程序开发者的角度来看是这样。

最后，虽然你可能最感兴趣的是从数据源（如 Web 服务或文件）渲染多媒体，但重要的是要认识到，当今的设备可能提供自己的数据源。例如，拍照手机有一个成像传感器；许多 MMAPI 实现允许你将这个成像传感器视为一个数据源，你可以从中渲染视频甚至捕获图像。在考虑多媒体应用时，务必记住，源-播放器-控制器的职责划分是一种通用的划分方式，几乎可以应用于任何代表多媒体数据流的事物。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**450**

第 16 章 ■ 渲染多媒体内容

**理解 MMAPI 的组织结构**

图 16-1 展示了 MMAPI 提供的关键类。在该图中，`DataSource` 类扮演数据源的角色，`Player` 接口扮演播放器的角色，而 `Control` 接口的各种实现则扮演控制器角色，共同完成在 Java ME 设备上渲染多媒体所需的职责分工。所有这些类都包含在三个包中：`javax.microedition.media`、`javax.microedition.media.control` 和 `javax.microedition.media.protocol`。

**图 16-1.** *MMAPI 提供的关键类*

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**451**

MMAPI 提供了以下类及接口（以及其他）：

*   **Manager**：`Manager` 类提供了一个访问点，用于获取依赖于系统的资源，例如 `Player` 接口的实现。`Manager` 的方法是静态的，因此无需获取该类的实例。
*   **Player**：`Player` 接口提供了与多媒体片段交互的方法。实现 `Player` 的子类封装了特定的渲染编解码器，例如一个 MP3 音频播放器。
*   **Control**：`Control` 接口定义了一些媒体处理功能。`Control` 的实现与实现 `Controllable` 接口的对象（例如 `Player` 实例）交互，以允许通过编程方式和用户控制媒体。例如，`VolumeControl` 允许你调整播放音量，而 `VideoControl` 允许你控制视频在显示屏上的可见性和位置。



• **DataSource**：`DataSource`抽象类的实现为`Player`实例提供媒体数据。它们提供一个容器，用于指示媒体对象的内容类型和位置，并通过`SourceStream`实例提供源数据流，这些流允许对单个媒体流进行随机访问。

• **ContentDescriptor**：`ContentDescriptor`类的实例包含一个字符串，用于指示特定`DataSource`的类型。

• **TimeBase**：`TimeBase`接口代表一个持续计时的时钟源。您的应用程序可以在多个播放器之间共享一个`TimeBase`实例，以同步多个媒体流。

• **PlayerListener**：`PlayerListener`接口描述了`Player`实例可以向应用程序提供的异步事件。您可以实现此接口来监听有关媒体渲染的通知。

• **MediaException**：MMAPI 实现使用`MediaException`类的实例来指示错误。

■**注意** `Control`接口包含许多子接口，此处不逐一讨论。如需详细了解每个子接口的功能，请参阅 JSR 135 或 MMAPI 实现附带的 Javadoc。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**452**

第 16 章 ■ 渲染多媒体内容

使用 MMAPI 渲染多媒体非常简单，您需要遵循以下四个步骤：**1.** 使用`Manager`类创建`Player`实例，并引用特定媒体文件的路径。

**2.** 允许`Player`实例获取渲染媒体所需的任何必要硬件资源。

**3.** 获取任何必要的`Control`实例来控制或配置`Player`实例。

**4.** 播放媒体。

由于 MMAPI 实现通常会封装设备上其他进程可能需要的专用硬件资源（例如 RAM、专用 DSP 或专用协处理器），因此仅实例化`Player`实例是不够的。相反，`Player`实例可以处于五种状态之一，这使您能够以编程方式控制获取必要系统资源这一可能耗时的过程（并允许您更好地与设备上的其他应用程序共享这些稀缺资源）。图 16-2 展示了这些状态如何交互。

**图 16-2.** *Player 实例的状态*

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**453**

这些状态包括：

• ***未实现***：未实现的`Player`实例不具备其运行所需获取的资源信息。如果在此状态下调用`Player`的许多方法（包括`getContentType`、`setTimeBase`、`getTimeBase`、`setMediaTime`、`getControls`和`getControl`），这些方法将失败，并且`Player`会抛出`IllegalStateException`。

• ***已实现***：当您调用未实现播放器的`realize`方法时，它会转换到已实现状态。此转换可能是一个漫长的过程，因为`Player`实例需要获取系统资源，并与媒体内容协作以确定其内容类型或执行其他操作。您可以通过调用实例的`deallocate`方法来停止实现过程，但必须在`Player`实例完成向已实现状态转换*之前*执行此操作；一旦实现，`Player`实例无法返回到未实现状态。

• ***预取***：已实现的播放器可能仍需要进行额外的耗时处理，例如从网络填充媒体缓冲区或获取独占资源（如硬件编解码器）。为此，您需要调用`Player`实例的`prefetch`方法。

• ***已启动***：一旦`Player`实例已实现，并且其内容和资源已预取，您可以通过调用`Player`实例的`start`方法开始播放¹。这会使`Player`实例转换到已启动状态。

在已启动状态下，实例运行并渲染多媒体数据，直到您通过调用`stop`停止它，或者直到数据渲染完毕。对已启动的`Player`实例调用`stop`会使其返回到预取状态。



•   *关闭*：从任何其他状态，你可以通过调用 Player 实例的 `close` 方法强制其释放资源并进入关闭状态。一旦进入此状态，你绝不能再次尝试使用该实例；它已准备好被垃圾回收器回收，你唯一要做的就是将所有指向它的引用设置为 null。

俗话说，细节决定成败，所以让我们来看一些实际渲染多媒体并控制其播放的代码。

1.  奇怪的是，你通常可以通过调用 `start` 方法，直接将一个已实现（realized）的 Player 实例带到启动状态。你在网上看到的许多教程示例——包括 Sun 公司的示例——都这样做，但这与 MMAPI JSR 提供的文档相悖，因此我不推荐这样做。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**454**

第 16 章 ■ 渲染多媒体内容

**启动渲染过程**

作为 Player 实例的工厂，Manager 类拥有一组简洁的接口。

你可以播放单音（我将在本章后面的“播放单音”部分进一步讨论），为特定的媒体获取 Player 实例，列出支持的内容类型和协议，以及获取用于渲染所有媒体的系统时间基准。当然，你最常使用的方法是 `createPlayer` 方法来获取 Player 实例。

Manager 实际上有三种不同的 `createPlayer` 方法。最简单易用的是接受一个 `InputStream` 和一个表示媒体 MIME 类型的 `String` 的方法，但这会限制你只能播放那些能获取到 `InputStream` 的多媒体，例如 JAR 文件中的静态资源。例如，你可以编写清单 16-1 中的代码，使用本地资源来创建、实现、预取并启动一个 MP3 音频播放器：

**清单 16-1.** *播放 MP3 音频文件的伪代码*
```java
Player player;
try {
    InputStream in = getClass().getResourceAsStream("/res/sound.mp3");
    player = Manager.createPlayer(in, "audio/mp3");
    player.realize();
    player.prefetch();
    player.start();
} catch (MediaException e) {}
catch (IOException e) {}
```

当然，你可以使用任何其他方式为你的媒体源打开一个 `InputStream`；例如，你可能想使用我在第 7 章中描述的 FCOP 来播放文件系统上用户加载的媒体文件。

然而，很多时候，你可能希望为媒体指定一个不同的来源，例如网络源或设备的其他组件（如成像传感器）。Manager 类也支持*定位器*的概念，这很像你传递给 GCF 的 URL。定位器是一个传递给 `Manager.createPlayer` 的 `String`。该字符串必须包含以下部分：

```
scheme://scheme-part
```

具体来说，`scheme` 是协议的名称，例如 `http` 代表 HTTP，`rtp` 代表实时传输协议 (RTP)，或 `rtsp` 代表 RTSP。`scheme-part` 是以特定于协议的方式表示的媒体位置，例如 Web 上资源的主机和路径。

表 16-1 展示了一些常用的协议及其用途。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**455**

■**注意** 并非所有设备都支持所有协议。例如，RTSP 支持是 MMAPI 中相对较晚才加入的；在我撰写本文时，只有少数设备支持它。

**表 16-1.** *一些常用的 MMAPI 协议*
| **协议名称** | **用途** | **备注** |
| --- | --- | --- |
| `capture` | 从设备传感器捕获实时媒体 | `scheme-part` 指示捕获设备。 |
| `http` | 通过 HTTP 获取 | 通常在*渲染之前*下载整个文件。 |
| `rtp` | 通过 RTP 流式传输 | `scheme-part` 指示服务器地址、端口和内容类型。 |
| `rtsp` | 通过 RTSP 流式传输 | `scheme-part` 指示服务器地址、端口和要流式传输的资源。 |

清单 16-2 展示了一些语法上有效的定位器示例。然而，仅仅因为一个定位器在语法上是正确的，并*不*意味着设备拥有必要的协议和编解码器支持来播放该定位器所指定的媒体。



**清单 16-2.** *一些有效的定位器*

http://www.noplace.com/loon-hoot.mp3

http://www.noplace.com/loon-display.3gp

capture://video

capture://audio

capture://radio

rtp://www.noplace.com:1224/audio

在底层，当你将一个定位器传递给 `Manager.createPlayer` 时，它会在解析定位器后设置所需的任何协议引擎。然后它会创建一个 `DataSource`，用于将媒体数据从源传递到播放器。如果你希望为特定设备上 MMAPI 实现不支持的协议提供支持，你可以编写一个实现该协议的 `DataSource` 子类，并将其与 `Manager.createPlayer` 一起使用，如清单 16-3 中的伪代码所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**456**

第 16 章 ■ 渲染多媒体内容

**清单 16-3.** *将自定义 DataSource 与 MMAPI Manager 结合使用* Player player;

try {

DataSource ds = (DataSource)new CleverStreamingDataSource(

"proto://noplace.com/audio.mp3" );

player = Manager.createPlayer(ds);

player.realize();

player.prefetch();

player.start();

} catch (MediaException e) {}

catch (IOException e) {}

将 `CleverStreamingDataSource` 实现为 `DataSource` 的子类，允许 `Manager` 创建一个连接到你的数据源以获取数据的播放器。

■**提示** 编写自定义 `DataSource` 并非易事，且超出了本书的范围。有关更多详细信息，我建议你从 Vikram Goyal 关于 Java ME 内容流媒体的文章《Java ME 中的流媒体内容实验》开始，该文章位于 http://today.java.net/pub/a/today/2006/08/22/experiments-instreaming-java-me.html。特别要查看“创建自定义 DataSource”部分。

无论你如何创建和启动 `Player` 实例，你都应该在与主应用程序线程不同的线程中执行此操作，因为这是一个耗时的过程。

我将在本章后面的“将 MMAPI 和 SVGAPI 付诸实践”部分向你展示如何在实际中做到这一点。

完成媒体渲染后，在某个时刻你将需要停止播放并回收 `Player` 实例已消耗的资源。清单 16-4 展示了通常的过程。

**清单 16-4.** *停止媒体播放并回收资源* if (player != null) {

player.stop();

player.close();

player = null;

}

有趣的是，`Player` 的 `stop` 方法实际上并不会停止播放；它只是暂停播放。这意味着你可以通过调用 `Player` 实例的 `stop` 方法来实现用户发起的暂停，并通过再次调用其 `play` 方法从暂停处恢复播放。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**457**

MMAPI 非常模块化；为了实现在最广泛的平台上可用的目标，并非所有功能在所有实现中都可用。你可以通过调用 `Manager.getSupportedContentTypes` 获取支持的内容类型列表，或者通过将支持的内容类型传递给 `Manager.getSupportedProtocols` 来获取每种内容类型支持的协议列表。清单 16-5 枚举了给定设备上支持的类型和协议，并将每种类型和协议写入调试控制台。

**清单 16-5.** *枚举支持的媒体和协议类型* private void showSupportedMedia() {

String[] contentTypes = Manager.getSupportedContentTypes(null); for (int i=0; i<contentTypes.length; i++) {

String protocols[] = Manager.getSupportedProtocols(contentTypes[i]); for (int j=0; j<protocols.length; j++) {

String s = contentTypes[i] + ":" + protocols[j]; System.out.println(s);

}

}

}

除了确定给定的 MMAPI 实现是否支持特定的内容类型或协议之外，你可能还需要其他信息，例如目标设备是否支持同时播放多个音频通道（音频混音）、平台是否可以捕获音频或视频等。MMAPI 定义了一组系统属性，你可以使用 `System.getProperty` 获取这些属性的值；这些属性如表 16-2 所示。



**表 16-2.** *描述目标设备 MMAPI 实现的属性* **属性**

**返回值**

microedition.media.version

所实现的 MMAPI 规范版本

supports.mixing

若设备支持混音则返回 true；否则返回 false

supports.audio.capture

若设备支持音频捕获则返回 true；否则返回 false supports.video.capture

若设备支持视频捕获则返回 true；否则返回 false supports.recording

若至少一种返回的 Player 类型支持录制则返回 true；否则返回 false

audio.encodings

指定支持的捕获音频格式的字符串 video.encodings

指定支持的捕获视频格式的字符串 video.snapshot.encodings

指定 VideoControl.getSnapshot 支持的视频快照格式的字符串

streamable.contents

指定 Player 实例在接收数据时可播放的内容类型的字符串

[www.it-ebooks.info](http://www.it-ebooks.info/)

**458**

第 16 章 ■ 渲染多媒体内容

**控制渲染过程**

对于除最简单媒体播放器之外的所有播放器，您通常希望对媒体播放拥有更多控制权。虽然 Player 接口本身允许您启动和停止播放，但通过获取特定 Control 子类的实例，您可以做更多事情来改变 Player 的行为。要获取特定类型控件的实例，您可以将所需类的名称（如果不是 javax.microedition.media.control 包中的类，则需包含包名）传递给 Player 的 getControl 方法，如下所示：

VolumeControl vc = (VolumeControl)player.getControl("VolumeControl"); 每个 Control 子类都有不同的方法，反映了它对媒体渲染过程提供的控制类型。表 16-3 描述了一些在编写多媒体应用程序时非常有用的 Control 子接口。当然，并非每个子接口都适用于渲染所有媒体类型的 Player 实例；例如，能够为音频文件获取 VideoControl，或为视频文件获取 TempoControl，这几乎毫无意义。

**表 16-3.** *可用于控制媒体渲染的 Control 实现者* **接口**

**用途**

**示例方法**

FramePositioningControl 跳转到特定视频帧

seek

skip

mapFrameToTime

mapTimeToFrame

GUIControl

提供一个对象将媒体渲染到 GUI

initDisplayMode

MetaDataControl

获取媒体文件中的元数据

getKeys

getKeyValue

MIDIControl

控制内部合成器

各种方法

PitchControl

在不改变播放速度的情况下改变合成或采样音频的音高

getMaxPitch

getMinPitch

getPitch

setPitch

RateControl

改变播放速率

getMaxRate

getMinRate

getRate

setRate

RecordControl

从播放器录制媒体

commit

setRecordLocation

setRecordStream

startRecord

stopRecord

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**459**

**接口**

**用途**

**示例方法**

StopTimeControl

设置预设停止时间

getStopTime

setStopTime

TempoControl

改变合成音频的节奏

getTempo

setTempo

ToneControl

重放单声道音调序列

setSequence

VideoControl

控制视频显示

getSnapshot

setDisplayFullScreen

setDisplayLocation

setDisplaySize

setVisible

GUIControl 及其子接口 VideoControl 值得特别关注，因为通过它们，您可以从文件或设备摄像头等多媒体源获取视频数据并将其显示在屏幕上。GUIControl 接口指定了一个单一方法——initDisplayMode——您可以使用它来获取一个能够在 Displayable 对象上绘制的对象。VideoControl 扩展了此接口，为您提供了全屏显示视频、指定视频播放位置等方法。清单 16-6 展示了如何结合使用 VideoControl 实例和 Player 实例来播放视频片段。

**清单 16-6.** *使用 VideoControl 和 Player 实例播放视频* Player player;

Form viewer;

VideoControl vc;

try {

InputStream in = getClass().getResourceAsStream("/res/video.3g2"); player = Manager.createPlayer(in, null);



vc = (VideoControl) player.getControl("VideoControl"); player.realize();

player.prefetch();

if (vc != null) {

Item vi = (Item) vc.initDisplayMode(

VideoControl.USE_GUI_PRIMITIVE, null);

viewer.append(vi);

}

player.start();

} catch (MediaException e) {}

catch (IOException e) {}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**460**

第 16 章 ■ 渲染多媒体内容

就我个人而言，我觉得向 Control 请求一个对象来放置到显示屏上有点诡异，但这就是它的工作方式：VideoControl 实例的 `initDisplayMode` 方法接受一个标志和一个 Object 作为参数，并返回一个用于显示的可见组件。你传入 `VideoControl.USE_GUI_PRIMITIVE` 值，以指示该实例应返回设备 GUI 层次结构中的一个对象实例。此示例运行在基于 CLDC 的 MIDP 平台上，因此 `initDisplayMode` 返回的对象实际上是 Item 的一个子类。支持 AWT 的平台（例如某些 CDC 平台）则返回 Component 子类的对象实例。

■**注意** 在 MIDP 设备上，如果你想全屏渲染视频，可以将 `VideoControl.USE_DIRECT_VIDEO` 值传递给 `initDisplayMode`，以指示它直接将视频绘制到 Canvas 对象上。将目标 Canvas 对象的引用作为第二个参数传入。使用 `Display.getDisplay.setCurrent` 将你传递给 `initDisplayMode` 的 Canvas 设置为当前的 Displayable 项，将 Player 实例置于已启动状态，然后大功告成！

正如我在上一节中指出的，Player 对象最好在其自己的线程中使用。它们所做的大部分工作都是异步的，并且通常跨越设备上整个硬件和软件栈。你需要有一种方法来获取 Player 实例正在做什么的信息，这一点很重要。一个简单的方法是使用其 `getState` 方法，该方法返回 Player 实例当前所处的状态。这对于粗粒度的控制来说已经足够，尤其是当你从不同线程管理一个实例时，但为了真正了解发生了什么，你可以使用其 `addPlayerListener` 方法向 Player 实例添加一个监听器。传递给 `addPlayerListener` 的对象必须实现 `PlayerListener` 接口，该接口指定了 `playerUpdate` 方法，Player 实例会调用该方法并传递有关媒体渲染的信息。MMAPI 定义的事件系统是可扩展的；`PlayerListener` 接口定义了许多事件，如表 16-4 所示。`PlayerListener` 接口还允许各个 Player 对象以字符串形式传递专有事件。你的应用程序可能会使用其中一些事件来向其用户界面提供细粒度的更新——例如，指示设备正在缓冲流。

**表 16-4.** *Player 对象提供的事件* **事件**

**用途**

PlayerListener.BUFFERING_STARTED

Player 实例进入缓冲模式。

PlayerListener.BUFFERING_STOPPED

Player 实例退出缓冲模式。

PlayerListener.CLOSED

Player 实例已关闭。

PlayerListener.DEVICE_AVAILABLE

系统占用的独占设备已归还给 Player 实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**461**

**事件**

**用途**

PlayerListener.DEVICE_UNAVAILABLE

系统已抢占 Player 实例先前使用的独占设备。

PlayerListener.DURATION_UPDATED

Player 实例渲染的媒体时长已更新。

PlayerListener.ERROR

处理媒体时发生错误。

PlayerListener.RECORD_ERROR

录制媒体时发生错误。

PlayerListener.RECORD_STARTED

录制已开始。

PlayerListener.RECORD_STOPPED

录制已停止。

PlayerListener.SIZE_CHANGED

正在渲染的视频尺寸已更改。

PlayerListener.STARTED

Player 实例已进入已启动状态。

PlayerListener.STOPPED

Player 实例已响应停止调用而停止渲染媒体。

PlayerListener.STOPPED_AT_TIME



Player 实例因 StopTimeControl 而停止渲染媒体。

PlayerListener.VOLUME_CHANGED

音频设备的音量已更改。

**捕获媒体**

越来越多的 Java ME 设备具备捕获音频和视频的能力，这令人兴奋，因为它催生了新型应用，例如执行音乐识别的应用，以及通过网络进行的不同社交互动。MMAPI 使得从内置图像传感器捕获图片变得异常简单；你只需打开一个连接到设备视频传感器的 Player，并调用 VideoControl 的 getSnapshot 方法即可。清单 16-7 展示了实现此功能的伪代码。

**清单 16-7.** *从成像传感器捕获快照* Player player;

VideoControl vc;

Form viewer;

private void startCamera() {

try {

player = Manager.createPlayer("capture://video"); vc = (VideoControl) player.getControl("VideoControl"); player.realize();

player.prefetch();

[www.it-ebooks.info](http://www.it-ebooks.info/)

**462**

第 16 章 ■ 渲染多媒体内容

if (vc != null) {

Item vi = (Item) vc.initDisplayMode(

VideoControl.USE_GUI_PRIMITIVE, null);

viewer.append(vi);

}

Display.getDisplay(this).setCurrent(viewer);

player.start();

} catch (MediaException e) {}

catch (IOException e) {}

}

private void stopCamera() {

try {

if (player!=null) {

player.stop();

player.close();

player = null;

}

} catch (MediaException e) {}

}

private void capture() {

if (vc != null) try {

byte[] imageBytes = vc.getSnapshot(null);

// 对 imageBytes 进行处理...

} catch (MediaException e) {}

}

■**注意** 所有这些方法都应在它们自己的线程上调用；在 UI 线程上运行它们可能会阻塞 UI 线程过长时间，导致应用程序停滞，这是不可接受的。在某些平台上，这还可能引发其他问题，例如应用程序挂起，尤其是当 Java 平台的安全管理器想要提示用户批准应用程序使用 getSnapshot 方法时。要了解如何在真实的多线程应用程序中实现这一点，请阅读即将到来的“将 MMAPI 和 SVGAPI 投入实践”部分，特别是“捕获图像”小节。

到目前为止，你可能已经大致理解了 startCamera 的用途。它获取一个新的 Player 实例，该实例将设备的成像传感器用作视频源，实现并初始化该实例，并获取一个 VideoControl 实例以在屏幕上显示为相机取景器。stopCamera 方法也很简单；它只是销毁 Player 实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**463**

要捕获图像，capture 方法会调用 VideoControl 实例的 getSnapshot 方法。此方法接受所需的图像格式名称和编码选项，并以字节数组的形式返回按所需图像格式编码的图像。默认情况下，getSnapshot 使用系统的默认编码，该编码是 video.snapshot.encoding 系统属性值中的第一个字段。

你可以将编码选项指定为 URL 编码的字符串，包含以下参数：

• encoding：指示所需的编码，例如 JPEG、PNG 或 GIF

• width：指示编码图像的所需宽度（以像素为单位）

• height：指示编码图像的所需高度（以像素为单位） 例如，你可以将 encoding=jpeg&width=160&height=120 传递给 getSnapshot，以接收编码为 JPEG 的邮票大小图像。

getSnapshot 可能会抛出异常；如果应用程序没有拍摄快照的权限，则预期会抛出 SecurityException；如果所需的格式不受支持或 Player 不支持拍摄快照，则预期会抛出 MediaException。如果尝试在尚未调用 initDisplayMode 的 VideoControl 实例上调用 getSnapshot，则还可能抛出 IllegalStateException。

不幸的是，虽然代码本身很简单，但在实践中，此操作充满了困难。首先是你可能预料到的明显问题：你的应用程序可能运行在没有成像传感器或 MMAPI 与成像传感器未集成的设备上。这通常很容易发现；正如你在表 16-2 中回忆的那样，调用 System.getProperty 并传递 supports.video.capture 值会告诉你目标设备是否真的可以执行此操作。然而，你可能会发现其他问题，包括以下内容：

• *不同的定位器*：某些设备保留定位器 capture://image 用于捕获静态图像，而 capture://video 用于使用 RecordControl 实例捕获视频（我稍后会详细介绍）。

• *渲染相机取景器的预览操作不可预测*：在某些设备上，只有使用 initDisplayMode 并通过 VideoControl.USE_GUI_PRIMITIVE 指定 GUI 基元，才能获得相机取景器。其他设备则只能通过使用 VideoControl.USE_DIRECT_VIDEO 并提供一个 Canvas 对象（MMAPI 在该对象上绘制传感器提供的视图）才能工作。

• *寻找通用编码方案*：不要假设所有 Java ME 设备都会提供相同的图像格式或分辨率，或者提供你请求的格式或分辨率来构建你的应用程序。某些设备可以以多种格式编码，而有些则不能。有些可以缩放编码后的图像，而有些则不能——而且有些设备只有在同时提供宽度和高度参数时才会缩放你的图像。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**464**

第 16 章 ■ 渲染多媒体内容

• *怪异行为*：某些设备制造时，图像传感器是倒置安装的，或者为了满足特定的工业设计要求而向某个方向旋转了 90 度。不幸的是，在这种设备上，经常会发现原生相机软件知道旋转情况并进行了校正，而 Java ME MMAPI 实现却不知道，导致捕获的图像是旋转或倒置的。

MMAPI 的相机支持可能会令人抓狂，因此请使用 System.getProperties 检查设备的系统属性，仔细操作，并记录你的结果。如有疑问，请查阅制造商的开发者支持论坛；除非是全新设备，否则你很可能不是第一个遇到特定问题的人。不要绝望：帮助正在路上。JSR 234，即高级多媒体补充（AMMS）API，定义了一组补充的 MMAPI 接口，包括几个特定于相机的 Control 子接口。虽然在我撰写本文时尚未在设备上广泛部署，但 AMMS API 有望标准化成像子系统的行为，并提供一套新的用于成像的 Control 子接口，包括以下内容：

• javax.microedition.amms.control.camera.CameraControl：使用此接口来确定和更改快门反馈、相机旋转、曝光模式和图像分辨率等内容。

• javax.microedition.amms.control.camera.ExposureControl：使用此接口来确定和更改曝光设置。

• javax.microedition.amms.control.camera.FlashControl：使用此接口来确定和设置相机闪光灯的闪光模式。

• javax.microedition.amms.control.camera.FocusControl：使用此接口来确定和设置可变焦成像传感器的对焦模式和特定对焦设置。

• javax.microedition.amms.control.ImageFormatControl：使用此接口来设置所需的图像格式。

• javax.microedition.amms.control.camera.SnapshotControl：使用此接口来控制连拍静态图像捕获，在一次捕获请求中拍摄多张图像。

• javax.microedition.amms.control.camera.ZoomControl：使用此接口来确定和设置可变焦成像传感器的数字和光学变焦选项。

你可以通过调用 System.getProperty 并传递字符串 microedition.amms.version 来了解特定设备是否支持 JSR 234。支持 AMMS 的设备



API 的符合性要求比支持 MMAPI 的更为严格，因此提供 AMMS API 的设备之间很可能具有更高的一致性行为。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**465**

■**提示** JSR 234 定义的 AMMS API 提供的功能远不止对成像传感器的更好控制。它还包括用于高级音频功能的接口，例如 3D 音频定位、混响和均衡器；收音机调谐器控制；用于分层和分组 Player 实例的效果；以及媒体后处理。如果你正在为高端 Java ME 设备开发多媒体应用程序，请务必检查你的目标设备是否支持此 API。

`getSnapshot` 方法显然仅适用于从视频数据源捕获单帧。你的应用程序可能希望录制其他数据源，例如来自麦克风的音频流，用于录音机或音乐识别应用程序。

要捕获其他类型的媒体，你可以使用 `RecordControl` 接口，该接口将录制媒体的副本写入 `OutputStream` 实例。清单 16-8 展示了一个源自 JSR 135 文档的示例。

**清单 16-8.** *演示 RecordControl 接口的伪代码* try {

// 创建一个捕获实时音频的 DataSource。

ByteArrayOutputStream baos = new ByteArrayOutputStream(); Player p = Manager.createPlayer("capture://audio"); p.realize();

RecordControl rc = (RecordControl)p.getControl("RecordControl"); rc.setRecordStream(baos);

rc.startRecord();

p.start();

Thread.currentThread().sleep(5000);

p.stop();

rc.stopRecord();

rc.commit();

byte[] b = baos.toByteArray();

} catch (IOException ioe) {}

catch (MediaException me) {}

catch (InterruptedException e) {}

此示例创建一个 `Player` 实例，该实例捕获当前音频五秒钟，并获取捕获的编码数据的字节数组。然后，你可以在其他地方使用此内容。例如，你可以将其用作写入文件、上传到 Web 服务器进行处理或通过 WMA 作为 MMS 附件发送的数据（我将在第 14 章中讨论）。

使用 `RecordControl` 实例时，MMAPI 通常以与源相同的格式对结果数据进行编码。从捕获设备录制时，你可以通过 `audio.encodings` 和 `video.encodings` 系统属性的值来了解支持的编码方案。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**466**

第 16 章 ■ 渲染多媒体内容

■**提示** 与 `getSnapshot` 方法一样，不同的设备可能提供不同的编码方案，并且在一个设备上录制的媒体可能无法在另一个供应商的设备上播放。在设计应用程序时，请仔细考虑不同设备对特定媒体类型的需求，并记住你的应用程序可能需要一个特定组件（通常托管在某个服务器上）来将媒体从一种格式转码为另一种格式。

**播放单个音调**

我编写多媒体应用程序的早期经历之一并非在计算机上，而是在可编程计算器上。它有一个 `BEEP` 指令，可以播放 ROM 中编码的四音调蜂鸣声，还有一个 `TONE` 指令，可以播放具有预设频率的音调。当时我认为这非常了不起；能够播放单个音调对于某些应用程序仍然有用。

播放一两个音调的最简单方法是使用 `Manager` 的 `playTone` 方法，该方法接受音符、持续时间（毫秒）和音量，如下所示：`Manager.playTone( 60, 3000, 100 );`

音符值实际上是一个乐器数字接口（MIDI）音符选择器。中央 C 被任意赋予值 60，你可以从中央 C 开始以半音为单位向上或向下计数（例如，中央 C 上方的 A 为 69）。尽管很简单，但 `playTone` 可能会抛出 `MediaException`，就像任何播放器一样，因为另一个应用程序可能正在使用声音硬件。如果你提供的音调超出范围，它也可能抛出 `IllegalArgumentException`。

如果你想按顺序播放音调，最好使用带有关联 `ToneControl` 实例的 `Player` 实例，因为这样你可以使用 `Player` 来控制每个音调的顺序。请按照以下步骤播放一系列音调：**1.** 使用定位器 `Manager.TONE_DEVICE_LOCATOR`（其值为 `device://tone`）创建一个 `Player` 实例。

**2.** 实现播放器。

**3.** 通过调用 `player.getControl("ToneControl")` 获取 `ToneControl` 实例。

**4.** 使用 `ToneControl` 的 `setSequence` 方法，将编码为字节数组（`byte[]`）的音调序列传递给上一步中获得的 `ToneControl` 实例。

**5.** 启动 `Player` 实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**467**

如果你觉得这有点奇怪，请放心，你并非唯一有此感觉的人。通过控件将音调序列馈送到播放器完全打破了源-播放器-控制器的抽象。

更糟糕的是，编码音调序列的方案是一种拜占庭式的复杂事务，让人想起 MIDI 标准定义的音符控制序列，这很可能是因为大多数移动设备的声音硬件都接受 MIDI 命令。编码音调序列的字节流由一个初始版本号（指示你正在使用的 `ToneControl` 实例的版本）组成，后跟成对的音符和持续时间。与 `playTone` 不同，你为 `ToneControl` 实例指定的持续时间不是以毫秒为单位，而是 `ToneControl` 实例的*分辨率*（或基本速度）的倍数。默认情况下，分辨率是四拍一小节（如果你是音乐家，则为 4/4 拍）的 1/64。因此，在默认配置中，持续时间为 64 表示全音符（四拍），持续时间为 16 表示四分音符（一拍），持续时间为 8 表示八分音符，依此类推。清单 16-9 展示了一个用于 `ToneControl` 实例的简单音符序列示例。

**清单 16-9.** *ToneControl 实例的一些示例数据* byte tempo = 30; // 将速度设置为 120 bpm

byte d = 8;

// 八分音符

byte C4 = ToneControl.C4;;

byte D4 = (byte)(C4 + 2); // 两个半音，一个全音 byte E4 = (byte)(C4 + 4); // 四个半音，一个大三度 byte G4 = (byte)(C4 + 7); // 七个半音，一个五度

byte rest = ToneControl.SILENCE;

byte block1 = 0;

byte block2 = 1;

byte[] song = new byte[] {

// 如 JSR-135 中所转录

ToneControl.VERSION, 1,

ToneControl.TEMPO, tempo,

ToneControl.SET_VOLUME, 50,

ToneControl.BLOCK_START, block1,// 开始定义一个块 E4,d, D4,d, C4,d, E4,d, // 块 1 的内容

E4,d, E4,d, E4,d, rest,d,

ToneControl.BLOCK_END, block1, // 结束块定义 ToneControl.BLOCK_START, block2,

D4,d, D4,d, D4,d, rest,d, // 块 2 的内容

E4,d, G4,d, G4,d, rest,d,

ToneControl.BLOCK_END, block2,

ToneControl.PLAY_BLOCK, block1, // 播放第一个块 ToneControl.PLAY_BLOCK, block2, // 播放下一个块

D4,d, D4,d, E4,d, D4,d, C4,d // 播放最后一部分

};

[www.it-ebooks.info](http://www.it-ebooks.info/)

**468**

第 16 章 ■ 渲染多媒体内容

此示例展示了你可以放置在 `ToneControl` 实例序列中的其他几个命令。这些命令影响以下选项：

*   **速度**：你可以随时使用 `ToneControl.TEMPO` 参数更改播放速度，后跟以每分钟节拍数除以 4 的新速度。
*   **音量**：你可以随时使用 `ToneControl.VOLUME` 参数更改音量，后跟新音量（0 到 100 之间的数字）。



•   *音符块*：你可以定义最多 255 个可重复使用的音符块。要开始定义块，请使用 `ToneControl.BLOCK_START` 参数，后跟一个块 ID。接着输入块中的命令，并使用你开始块时所用的 ID 配合 `ToneControl.BLOCK_START` 参数来关闭该块。之后，你可以在序列中的任何时间通过指定 `ToneControl.PLAY_BLOCK` 参数和块 ID 来播放该块。

•   *休止符*：音乐不仅由音符构成，音符之间的停顿也同样重要。

要指定一个休止符，请使用 `ToneControl.SILENCE` 音符值，后跟休止符的持续时间。

•   *重复*：你可以通过指定 `ToneControl.REPEAT` 后跟重复次数，再跟音符和持续时间，来多次重复单个音符。（例如，使用默认分辨率时，序列 `ToneControl.REPEAT, 4, 60, 16` 会演奏四个四分音符的中音 C。）

•   *分辨率*：你可以使用 `ToneControl.RESOLUTION` 将默认的 1/64 分辨率更改为其他值，只需在其后跟上新分辨率的分母即可。

但是，你只能在序列的开头执行此操作一次。

清单 16-10 基于清单 16-1 和 16-9 的伪代码，演奏了《玛丽有只小羊羔》。

**清单 16-10.** *使用 ToneControl 实例演奏简单歌曲的伪代码*
Player player;

ToneControl control;

byte tempo = 30; // 设置速度为 120 bpm

byte d = 8;

// 八分音符

byte C4 = ToneControl.C4;;

byte D4 = (byte)(C4 + 2); // 两个半音，一个全音
byte E4 = (byte)(C4 + 4); // 四个半音，一个大三度
byte G4 = (byte)(C4 + 7); // 七个半音，一个五度

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**469**

byte rest = ToneControl.SILENCE;

byte block1 = 0;

byte block2 = 1;

byte[] song = new byte[] {

// 如 JSR-135 中所转录
ToneControl.VERSION, 1,
ToneControl.TEMPO, tempo,
ToneControl.SET_VOLUME, 50,
ToneControl.BLOCK_START, block1,// 开始定义块
E4,d, D4,d, C4,d, E4,d, // 块 1 的内容
E4,d, E4,d, E4,d, rest,d,
ToneControl.BLOCK_END, block1, // 结束块定义
ToneControl.BLOCK_START, block2,
D4,d, D4,d, D4,d, rest,d, // 块 2 的内容
E4,d, G4,d, G4,d, rest,d,
ToneControl.BLOCK_END, block2,
ToneControl.PLAY_BLOCK, block1, // 播放第一个块
ToneControl.PLAY_BLOCK, block2, // 播放下一个块
D4,d, D4,d, E4,d, D4,d, C4,d // 播放最后一部分

};

try {

player = Manager.createPlayer(Manager.TONE_DEVICE_LOCATOR);
player.realize();
player.prefetch();
control = (ToneControl)player.getControl("ToneControl");
control.setSequence(song);
player.start();

}

catch (MediaException pe) {}

catch (IOException ioe) {}

catch (Exception e) {}

像这样在应用程序中指定音符序列既繁琐又笨重，因此我建议，如果你采用这种方法，请生成 `ToneControl` 序列的数组，并将其作为资源存储在 JAR 文件中，然后你可以使用 `getClass().getResourceAsStream()` 加载它们。

最后，关于使用 `playTone` 方法还是 `ToneControl` 实例的一个提示：`playTone` *不会*阻塞当前正在执行的线程。如果你想使用 `playTone` 来排序多个音调，你应该在单独的线程中执行，并让线程在每次调用之间休眠。否则，一个 `playTone` 可能会覆盖下一个；最坏的情况下，你只会听到静音，但也可能得到混乱的噪音。因此，如果你想播放多个音调，我建议你使用由 `ToneControl` 实例控制的 `Player` 实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**470**

第 16 章 ■ 渲染多媒体内容

**介绍 Java 可缩放 2D 矢量图形 API**

如今，多媒体的概念涵盖了来自模拟源的音频和视频，例如好莱坞电影、感恩而死乐队私制录音的 MP3 文件，或来自你拍照手机的相片快照。然而，全数字多媒体正以 SVG 格式的形式蓬勃发展。SVG 是一种开放的 XML 规范，用于描述二维矢量图形图像，这些图像可以是静态的，也可以包含动画并支持用户交互。为了与 Adobe 的 Shockwave Flash (SWF) 格式竞争，移动行业正在广泛采用 SVG 格式的一个子集，称为 SVG Tiny，用于无线终端和其他设备。

**理解基本 SVG 概念**

SVG 格式允许你使用标签、圆形、线条、多边形和曲线等对象来表示图像。与你可能习惯的图像（如 PNG、GIF、BMP 或 JPEG 图像，它们被称为*位图*或*光栅*图像，因为它们由单个图像元素的行组成）不同，SVG 格式是一种*矢量*格式，它使用构成图像的形状的数学描述。此外，SVG 图像也可以包含位图数据。这些信息使用 W3C 定义的 XML 表示；有关具体标准的信息，请查阅 http://www.w3c.org/TR。

由于 SVG 是一种矢量格式，使用 SVG 表示的图像在不同分辨率下渲染时，不会像位图图像那样出现缩放伪影。此外，该标准明确支持动画图像，让艺术家能够创建在不同尺寸屏幕上都能良好渲染的动画。更有用的是，SVG 包含对事件模型的支持，允许 SVG 图像与渲染图像的容器交换事件，因此可以使用 SVG 来表达整个用户界面的外观和感觉。SVG 文档还可以包含处理事件的脚本，使其本身成为一个成熟的图形创作环境。许多浏览器和其他应用程序已经提供了对 SVG 的支持，使其成为一个被广泛行业采用的有力标准。

JSR 287 定义了一组可选的包，支持渲染和创建符合 SVG Mobile 1.2 标准（也称为 SVG Tiny 1.2，由 W3C 在 http://www.w3.org/TR/2003/REC-SVGMobile-20030114/ 描述）的 SVG 图像。该标准消除了 SVG 的两个关键特性——对脚本的支持，以及渲染器可应用于矢量图形以生成修改后的位图输出的滤镜——以及对裁剪和透明度的其他一些限制。

SVG 是一种 XML 应用，因此你只需使用文本编辑器即可创建 SVG 内容。清单 16-11 显示了一个示例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00012.jpeg)

第 16 章 ■ 渲染多媒体内容

**471**

**清单 16-11.** *一个简单的 SVG Tiny 文档*

<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE svg [
<!ENTITY smile "
<rect x='0' y='0' width='128' height='128' fill='gray' stroke='black'/>
<g transform='translate(0, 0)'>
<circle cx='64' cy='64' r='64' fill='yellow'/>
<circle cx='40' cy='40' r='6.5' fill='black'/>
<circle cx='88' cy='40' r='6.5' fill='black'/>
<path d='M 40 88 L 64 96 88 88' stroke='black' stroke-width='2'/>
</g>
">
]>
<svg version="1.2" baseProfile="tiny">
<title>笑脸</title>
⌣
</svg>

这段代码产生了你在图 16-3 中看到的图像。

**图 16-3.** *一个简单的 SVG 图像，已渲染*

然而，在实践中，大多数有用的 SVG 图像都过于复杂，无法手工编码；复杂图像中大量的对象手动制作需要太长时间。幸运的是，大多数矢量图形应用程序，从重量级的 Adobe Illustrator 到众多由小型团队和开源团队（如 Inkscape (http://www.inkscape.org/)）编写的较小应用程序，都允许你将图像导出为 SVG；任何制作数字内容的艺术家都应该熟悉这些工具。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**472**

第 16 章 ■ 渲染多媒体内容



全面探讨 SVG 和 SVG Tiny 格式超出了本章的范围；如果你对标准的具体细节感兴趣，可以从 W3C 标准网站 http://www.w3c.org/TR 以及互联网上丰富的信息资源入手。在纸质书籍方面，Kurt Cagle 在其著作 *SVG 编程：图形化网络*（Apress, 2002）中对标准的讨论是一个很好的起点。

**理解 SVGAPI 的组织结构**

Java ME 对 SVG 的支持始于 JSR 226。该规范兼容 SVG Tiny 1.1，提供了一组包，支持 SVG 图像和动画图像的基本渲染，以及处理来自 SVG 图像的用户事件。标准社区很快用 JSR 287 取代了 JSR 226，后者为动画和 SVG 文档的 DOM 提供了更好的支持。

■**提示** 在我撰写本文时，NetBeans 6.1 以及大多数支持 SVG 的设备仅支持 JSR 226 提供的 SVGAPI。然而，展望未来，我相信大多数支持 SVG 的设备及开发环境将支持 JSR 287，因此在本章剩余部分，我将重点介绍 JSR 287。

JSR 226 和 JSR 287 所描述的 SVGAPI 为矢量图形渲染的多个方面提供了支持。当然，关键在于 JSR 287 提供的高级渲染 API。然而，JSR 287 还提供了封装 SVG DOM 的接口。它还提供了 SVG 事件、通过同步多媒体集成语言（SMIL）实现的 SVG 动画，以及一个可选包，该包定义了高性能即时模式渲染（即 API 不保留被渲染对象的完整模型）的接口，供应商可以实现这些接口，使应用程序能够访问硬件渲染引擎。

SVGAPI 提供以下包：

• javax.microedition.m2g：用于文档渲染的高级接口
• javax.microedition.vectorgraphics：SVGAPI 和其他应用程序可使用的可选高性能即时模式渲染 API
• org.w3c.dom：提供 DOM Level 3 Core API 子集以管理 XML DOM 的接口
• org.w3c.dom.events：DOM Level 3 Core API 的事件接口
• org.w3c.dom.smil：用于渲染 SMIL 动画的接口
[www.it-ebooks.info](http://www.it-ebooks.info/)
第 16 章 ■ 渲染多媒体内容
**473**
• org.w3c.dom.svg：SVG DOM API 的接口和类
• org.w3c.dom.views：JSR 287 定义的用于与 SVG DOM 交互的 DOM Views API

图 16-4 展示了一个类图，说明了 SVGAPI 相关部分之间的关系，我将在后续章节中讨论这些部分。为简洁起见，图中省略了包名。

**图 16-4.** *SVGAPI 关键部分之间的关系*

[www.it-ebooks.info](http://www.it-ebooks.info/)
**474**
第 16 章 ■ 渲染多媒体内容

■**注意** SVGAPI 非常庞大，尤其是考虑到 javax.microedition.vectorgraphics 包提供的 API。图 16-4 仅展示了你在渲染 SVG 图像时可能用到的类之间的关系。

如图 16-4 所示，根级类和接口的层次结构实际上相当扁平。请注意以下类和接口：

• ScalableGraphics：用于 2D 矢量渲染的基础类
• ScalableImage：表示矢量格式的图像；是 SVGImage 类的父类
• SVGImage：表示符合 SVG Tiny 1.2 标准的 SVG 图像
• SVGAnimator：使用 SVGImage 处理动画 SVG 图像中更新的自动渲染
• Document：表示一个 XML 文档
• SVGDocument：表示一个 SVG 文档；是 Document 的子类
• Element：表示 SVG 文档 DOM 中的通用项；是各个 SVG 元素的父类
• Event：表示特定接口的上下文信息；是 SVG 事件类的父类



• EventListener：表示事件监听器，指定 SVGAPI 框架用于向对象发送事件时所使用的 handleEvent 方法

**渲染 SVG 图像**

你可以通过两种方式使用 SVGAPI 渲染 SVG 图像。第一种方式简单易用，最适合静态图像（即不需要动画的图像）。第二种方式稍微复杂一些，因为它要求你完成第一种方式的所有操作，然后再额外做一些工作；但作为回报，你将获得一个能够管理动画 SVG 图像的对象。

渲染静态 SVG 图像

第一种方式很简单：使用一些初始数据创建一个 SVGImage 进行绘制，然后将其交给 ScalableGraphics 实例来绘制。清单 16-12 展示了这种绘制 SVG 图像方式的伪代码。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**475**

**清单 16-12.** *渲染 SVG 图像*

InputStream in = …

SVGImage image;

ScalableGraphics sg = ScalableGraphics.createInstance(); Graphics g;

int w, h;

try {

image = (SVGImage)ScalableImage.createImage(in, null);

} catch( IOException e) {};

sg.bindTarget(g);

image.setViewportWidth(w);

image.setViewportHeight(h);

sg.render(0, 0, image);

sg.releaseTarget();

当你创建 SVGImage 实例时，可以将源数据指定为 InputStream 子类的实例或 URL。创建实例时，你还可以指定一个外部资源处理器，SVGImage 会使用它来加载子元素。通常你不需要这样做——直接传递 null 即可。不过，例如当你的图像包含需要从网络获取的组件时，你可能需要这样做；在这种情况下，你需要提供一个包装了 HttpConnection 的 ExternalResourceHandler 实现。

当然，清单 16-12 并不能很好地适用于 MIDP 图形环境。

清单 16-13 在清单 16-12 的基础上进行了扩展，展示了一个简单的 SVGImageItem 类，它扩展了 MIDP 的 CustomItem 类（我在第 5 章中首次讨论过）。SVGImageItem 类的实例适合添加到 MIDlet 中的 Form 项中。

**清单 16-13.** *用于在 Form 上渲染静态 SVG 图像的 SVGImageItem 类*

import java.io.*;

import javax.microedition.lcdui.*;

import javax.microedition.m2g.*;

public class SVGImageItem extends CustomItem

{

private ScalableGraphics sg;

private SVGImage image;

private int prefWidth, prefHeight;

[www.it-ebooks.info](http://www.it-ebooks.info/)

**476**

第 16 章 ■ 渲染多媒体内容

public SVGImageItem( String l, InputStream in, ExternalResourceHandler h ) {

super(l);

sg = ScalableGraphics.createInstance();

if (in != null) {

try {

image = (SVGImage)ScalableImage.createImage(in, h);

} catch( IOException e) { e.printStackTrace(); };

} else {

image = (SVGImage)SVGImage.createEmptyImage(h);

}

SVGAnimator animator = SVGAnimator.createAnimator(image); Canvas svgCanvas = (Canvas)animator.getTargetComponent(); prefWidth = svgCanvas.getWidth();

prefHeight = svgCanvas.getHeight();

image.setViewportWidth(prefWidth);

image.setViewportHeight(prefHeight);

}

protected int getPrefContentHeight(int w) {

return minContentHeight();

}

protected int getPrefContentWidth(int h) {

return minContentWidth();

}

protected int getMinContentHeight() {

return prefHeight;

}

protected int getMinContentWidth() {

return prefWidth;

}

public void paint(Graphics g, int w, int h) {

g.setColor(255, 255, 255);

g.fillRect(0, 0, w, h);

sg.bindTarget(g);

image.setViewportWidth(w);

image.setViewportHeight(h);

sg.render(0, 0, image);

sg.releaseTarget();

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**477**

该类必须满足 CustomItem 接口的最低要求：实现一个调用继承构造函数的构造函数，通过 getPref 和 getMin 维度方法向布局管理器提供布局提示，并提供用于绘制项内容的 paint 方法。



清单 16-13 中的`SVGImageItem`构造方法比清单 16-12 中的伪代码稍显复杂，因为它使用`SVGAnimator`来获取 SVG 图像的优选边界。我创建了一个`SVGAnimator`实例（它会在`Canvas`上渲染），然后获取该`Canvas`实例的优选边界。完成此操作后，我便丢弃了`Canvas`实例和`SVGAnimator`，因为后续不再需要它们。最后，我将图像的*视口*（即其边界）设置为从`SVGAnimator`的`Canvas`实例获取的优选边界。

■**注意** 虽然可以通过`SVGDocument`方法直接访问 SVG 图像的 DOM 来确定其边界，但使用`SVGAnimator`更为清晰，并且能为你理解如何制作 SVG 图像动画（下一节将介绍）铺平道路。

MIDP 布局管理器使用`getPrefContentHeight`和`getPrefContentWidth`方法分别获取由构造方法计算出的 SVG 图像优选高度和宽度。当布局管理器需要获取宽度或高度的优选边界时，它会向相应方法传递另一个轴上的期望边界。

`getMinContentHeight`和`getMinContentWidth`方法返回图像的优选边界。

`paint`方法执行与清单 16-12 中伪代码相同的基本工作。

首先，它在将要绘制 SVG 图像的位置绘制一个空白矩形，以防该位置屏幕上已有其他内容。接着，它将`ScalableGraphics`实例绑定到包含该图像的`Canvas`提供给`paint`方法的`Graphics`实例上。这为`ScalableGraphics`实例提供了一个`GraphicContext`，用于渲染 SVG 图像。之后，它将图像的实际宽度和高度设置为包含该图像的`Canvas`提供的宽度和高度，然后指示`ScalableGraphics`实例在项的起点处渲染图像。最后，它通过`ScalableGraphics`的`releaseTarget`方法释放绑定的`Graphics`上下文。

**渲染动画 SVG 图像**

第二种渲染`SVGImage`的方式是使用`SVGCanvas`，这需要更多工作，但能让你轻松渲染动画 SVG 图像或处理事件的 SVG 图像。其策略与你之前在清单 16-13 中看到的类似：

**1.** 创建一个代表你想要绘制的图像的`SVGImage`实例。

**2.** 使用该`SVGImage`创建一个`SVGAnimator`实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**478**

第 16 章 ■ 渲染多媒体内容

**3.** 获取`SVGAnimator`将用于渲染图像的组件（在 MIDP 中为`Canvas`）。

**4.** 将`SVGImage`的边界设置为`SVGAnimator`组件的边界。

**5.** 使`SVGAnimator`的组件可见。

**6.** 使用`SVGAnimator`开始播放动画。

在渲染动画 SVG 时，`SVGAnimator`实现会使用所需的 SVG 图像以及由平台实际 GUI 层级结构（如 MIDP 或 AWT）提供的某种容器。`SVGAnimator`类的设计者假设它将在 CLDC 和 CDC 平台上使用，因此它不会返回 MIDP 类的特定实例（如`Canvas`）。相反，`SVGAnimator`提供`getTargetComponent`方法返回一个组件，它在该组件中渲染 SVG，这与 MMAPI 的`VideoControl`实例通过其`initDisplayComponent`方法返回组件的方式类似。在 MIDP 平台上使用 SVGAPI 时，`getTargetComponent`返回的对象是`Canvas`实例；在其他平台上，它是`Component`的子类。因此，在 MIDP 平台上，你可以使用`Display`类的`setCurrent`方法将生成的`Canvas`设置为要绘制的`Displayable`；在其他平台上，你可以将生成的组件插入到 GUI 视图层级中的适当容器中。

`SVGAnimator`类实现了一个更新线程，用于将 SVG 图像渲染为动画图像。该线程本身实现了一个简单的状态机，具有你在图 16-5 中看到的三种状态。

**图 16-5.** *SVGAnimator 状态机*

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**479**

一个`SVGAnimator`实例可以处于以下状态之一：

• *已停止*：`SVGAnimator`在生命周期开始时处于已停止状态，在此状态下它不会绘制任何内容。当处于其他状态时，你可以通过调用其`stop`方法将`SVGAnimator`实例置于已停止状态。

• *播放中*：具有正在运行且正在向其组件绘制的更新线程的`SVGAnimator`处于播放中状态。你可以通过调用其`pause`方法暂停正在播放的`SVGAnimator`，或通过调用其`stop`方法停止它。

• *已暂停*：`SVGAnimator`可能被暂停，在此期间动画不会更新也不会继续。你可以在其处于已暂停状态时通过调用其`play`方法恢复播放`SVGAnimator`，或通过调用其`stop`方法停止它。

清单 16-14 展示了设置`SVGAnimator`并开始播放的伪代码。

**清单 16-14.** *播放动画 SVG*

```java
SVGAnimator svgAnimator = null;
String mediaName = …;
Display display = …;

private void initAndStartSvgPlayer()
    throws IOException {
    InputStream in = getClass().getResourceAsStream(mediaName);
    SVGImage svgImage = (SVGImage)ScalableImage.createImage(in, null);
    svgAnimator = SVGAnimator.createAnimator(svgImage);
    Canvas svgCanvas = (Canvas)svgAnimator.getTargetComponent();
    svgImage.setViewportWidth(svgCanvas.getWidth());
    svgImage.setViewportHeight(svgCanvas.getHeight());
    display.setCurrent((Displayable)svgCanvas);
    svgAnimator.play();
}
```

由于`SVGAnimator`使用单独的线程来执行动画，因此在图像动画播放期间，你不能直接访问 SVG 图像本身。相反，如果你需要更改图像的内容（请参阅下一节“修改 SVG 图像”），则只能使用`SVGAnimator`的`invokeAndWait`或`invokeLater`方法进行更改。

■**注意** 渲染 SVG 图像*不*使用 MMAPI 定义的相同 API 和概念。这很遗憾，因为稍加扩展数据源和播放器的概念本可以创建一个简洁的接口。在处理媒体时，不要将 SVGAPI 与 MMAPI 混淆。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**480**

第 16 章 ■ 渲染多媒体内容

通过其目标对象（`Canvas`或`Component`子类）以及 SVG 内容本身，`SVGAnimator`可以获取并生成事件。为了让你的应用程序逻辑接收这些事件，它必须实现一个继承`SVGEventListener`接口的类，该接口指定了用户事件的方法，例如按键事件、按键释放事件、指针按下事件、指针释放事件，以及平台隐藏或显示动画本身时的通知。你可以手动处理这些事件，但大多数情况下，当你需要与 SVG 图像的事件交互时，是为了创建特定于 SVG 的控件。幸运的是，NetBeans 环境提供了一些类来为你完成这项工作，我将在后面的“将 NetBeans 与 SVG 图像结合使用”一节中讨论。

■**注意** JSR 226 定义了一种机制，你可以通过该机制使用 MMAPI 播放 SVG 图像。这基本上与播放其他视频相同：获取一个指定了 SVG 图像定位器的`Player`实例，然后获取一个`Control`子接口`javax.microedition.media.control.SVGControl`。使用该`Control`实例，你可以获取一个`Item`或`Component`对象，将其添加到你的视图层级中，并正常播放媒体。JSR 287 未指定设备是否会继续支持用于 SVG 播放的 MMAPI。

**修改 SVG 图像**



虽然你不太可能想编写一个创建 SVG 图像而非显示它们的应用程序，但这确实是可行的，并且 JSR 287 定义的 SVGAPI 让你能够做到这一点。由于 SVGAPI 提供了对 SVG DOM 的支持，你完全可以创建一个空的 SVG 图像并向其中添加新对象。要做到这一点，你必须掌握 XML DOM、SVG DOM 以及 SVG 标准本身的知识；这些内容大多超出了本节的范围。在这里，我只是概述基本原理，以便你知道如果确实有需要，这是可以实现的。我在本节中所述的所有内容仅适用于支持 JSR 287 的设备；JSR 226 缺少许多类以及一些直接操作 SVG DOM 所需的方法。

清单 16-15 展示了源自 JSR 287 的伪代码，它演示了如何使用`SVGAnimator`向应用程序逻辑提供用户事件，从而修改现有的 SVG 图像。反过来，这些用户事件会触发代码向应用程序正在显示的 SVG 图像中添加新的圆形。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**481**

**清单 16-15.** *创建并填充 SVG 图像*
class MIDPSVGEditor implements SVGEventListener, Runnable {

    private static final String svgNS = "http://www.w3.org/2000/svg";
    private SVGDocument svgDocument;

    private SVGAnimator svgAnimator;

    private Vector addToTree = new Vector();

    private int cx, cy;

    public MIDPSVGEditor (SVGImage svgi) {

        svgDocument = svgi.getDocument();

        svgAnimator = SVGAnimator.createAnimator(svgi);

        svgAnimator.setSVGEventListener(this);

    }

    public Object getTargetComponent() {

        return svgAnimator.getTargetComponent();

    }

    public void pointerPressed(int x, int y) {

    }

    public void pointerReleased(int x, int y) {

        cx = x;

        cy = y;

    }

    public void keyPressed(int keyCode) {

        SVGElement circle = svgDocument.createElementNS(svgNS, "circle");
        circle.setFloatTrait("cx", (float)cx);

        circle.setFloatTrait("cy", (float)cy);

        synchronized (addToTree) {

            addToTree.addElement(circle);

        }

        svgAnimator.invokeLater(this);

    }

    public void keyReleased(int keyCode) {

    }

[www.it-ebooks.info](http://www.it-ebooks.info/)

**482**

第 16 章 ■ 渲染多媒体内容

    public void run() {

        synchronized (addToTree) {

            for (int i = 0; i < addToTree.size(); i++) {

                svgDocument.getDocumentElement().appendChild(

                    (SVGElement) addToTree.elementAt(i));

            }

            addToTree.removeAllElements();

        }

    }

    public void hideNotify() {

    }

    public void showNotify() {

    }

}

■**注意** 清单 16-15 *不*适用于 JSR 226，因为 JSR 226 不包含`SVGDocument`类，不支持 SVG DOM，也无法创建带有交互提示的空 SVG 图像。虽然可以在 Java ME 设备上创建 SVG XML 并使用 JSR 226 进行渲染，但您将无法获得`SVGDocument`和其他 JSR 287 类所提供的灵活性，这些类允许您操作单个 SVG DOM 元素。

你需要用一个预先存在的`SVGImage`来初始化这个调用。该图像可能来自其他来源，或者你也可以通过调用`SVGImage`的静态方法`createEmptyImage`来创建一个空的`SVGImage`，如下所示：

SVGImage svgImage = SVGImage.createEmptyImage( null, hints );
`createEmptyImage`的第一个参数是可选的`ExternalResourceHandler`，就像你为现有图像创建`SVGImage`时一样。第二个参数是关于`SVGImage`实例应期望从用户和 SVG 内容本身获得的交互类型的信息。它是一个位掩码，可以包含以下值（按位或运算）：

• `SVGImage.INTERACTION_MODE_DISABLED`：表示不会发生与 SVG 图像的用户交互
• `SVGImage.INTERACTION_MODE_SCRIPT_EXECUTION`：表示 SVG 图像内可能执行脚本（在大多数 API 实现中，此选项无效）

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**483**



• SVGImage.INTERACTION_MODE_FOCUS_HIGHLIGHT：表示 SVG 图像可能包含焦点高亮，用户可更改获得焦点的元素。

• SVGImage.INTERACTION_MODE_IN_PLACE_TEXT_EDIT：表示 SVG 图像可能包含就地文本编辑（例如在文本字段内）。

• SVGImage.INTERACTION_MODE_OTHER：表示实现应支持与 SVG 图像的其他用户交互。

在清单 16-15 中，`keyPressed` 和 `run` 方法展示了如何修改 SVG 图像。当您在 SVGAnimator 的目标组件获得焦点时按下某个键，SVGAnimator 会调用 `keyPressed`，该方法会创建一个新的圆形元素，并将在后续第一个线程安全的机会中将其添加到目标文档中。由于 SVGAnimator 使用多线程，代码需要将圆形元素的添加操作与 SVGAnimator 同步；代码将新圆形添加到一个向量中，并使用 `invokeLater` 安排更新。`run` 方法（由 Java 虚拟机在适当时机调用）实际上会将所有待处理的圆形添加到 SVG 文档中。

一般来说，`Document` 和 `SVGDocument` 类允许您与特定的 SVG 文档进行交互；本示例使用 `Document` 方法 `createElementNS` 创建一个单独的圆形以绘制在图像上。要正确使用 `createElementNS`，您需要对 SVG 标准有相当深入的掌握，因为您要用它来创建 SVG 语言中的基本图元。如果您在应用程序中采用此方法，我建议您仔细研究我之前引用的 W3C 标准。

当然，使用这些类，您还可以查询特定文档的 DOM；清单 16-15 中的 `run` 方法正是通过这种方式获取 SVG XML 的文档元素，根据 SVG 的定义，该元素为图像中的每个形状都包含一个子元素。您还可以使用 `SVGDocument` 继承的 `Node` 接口来更改 DOM 的元素；`run` 方法通过调用 `appendChild` 并传入 `createElementNS` 创建的新对象来实现这一点。

**在 NetBeans 中使用 SVG 图像**

SVGAPI 提供 SVG 支持的方法对于许多应用程序来说是不错的，但工作量稍大，尤其是对于许多应用程序开发者希望做的事情，例如简单地播放 SVG 动画图像、将 SVG 图像显示为启动屏幕或等待屏幕，或将其用作菜单。虽然您当然可以自己编写代码来实现这一点——这无非是组合一个使用 `SVGAnimator` 的 `Displayable`——但这将是您、您的虚拟邻居以及许多其他 Java ME 开发者在未来几年内反复编写的代码。幸运的是，有更好的方法。

NetBeans IDE 的 Mobility Pack 提供了一个小包 `org.netbeans.microedition.svg`，其中包含一些用于构建 SVG 用户界面的实用类。这些类都扩展了 MIDP 的 `Canvas` 类，因此您只需使用 `Display` 类的 `setCurrent` 方法来显示其实例。更棒的是，这些类与 NetBeans IDE 集成，因此如果您使用可视化设计器构建应用程序，可以像在第 3 章中处理传统 MIDP 组件一样，将要在应用程序中使用的组件拖拽出来。`org.netbeans.microedition.svg` 类包含以下类：

• `SVGPlayer`：提供围绕 SVGAPI 的简单封装，用于播放动画 SVG 图像。它作为 `Canvas` 实现接收事件，并将其转发给 SVGAPI。

• `SVGSplashScreen`：提供一个屏幕，用于显示单个 SVG 动画，并过渡到另一个屏幕。

• `SVGWaitScreen`：提供一个插屏，在阻塞后台任务执行时显示 SVG 动画。

• `SVGMenu`：为包含多个可选项目的 SVG 提供播放器。菜单项是单个 SVG 图像中的独立 SVG 元素。



**注意** 这些类包含在 NetBeans 6.1 附带的 `org.netbeans.microedition.svg` 包版本中。早期版本的 NetBeans 也有类似的类，但在那些版本中，`SVGPlayer` 并不存在；取而代之的是 `SVGAnimatorWrapper`。如果你使用的是旧版 NetBeans，建议升级或改用 `SVGAnimatorWrapper`。升级可能是更合适的选择，因为在该包的较新版本中，`SVGAnimatorWrapper` 已被弃用。

使用 NetBeans 的可视化 GUI 构建器可以轻松创建使用这些类的应用程序，当然你也可以不这样做；你也可以直接导入 `org.netbeans.microedition.svg` 包并使用这些类。

这些类及其他类正由 NetBeans 开发者社区不断优化；如果你有兴趣在比 SVGAPI 更高的层次上处理 SVG 内容，应查阅 http://wiki.netbeans.org/ 上的最新文档。

**将 MMAPI 和 SVGAPI 付诸实践**

你可以围绕 MMAPI 和 SVGAPI 构建一些有趣的天气应用程序，例如一个服务器端应用程序，以 SVG 动画形式向 Java ME 客户端提供天气预报；或者一个天气博客应用程序，允许用户查看、分享和交互天气数据。然而，这些应用都会相当复杂。因此，与其将 MMAPI 或 SVGAPI 集成到我在本书中提到的 WeatherWidget 示例中，不如在本节中介绍一个简单的媒体播放器应用程序，它同时使用 MMAPI 和 SVGAPI 播放媒体。该应用程序打开时会显示一个媒体文件列表

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00030.png)

第 16 章 ■ 渲染多媒体内容

**485**

你可以从中选择一个进行播放。图 16-6 显示了应用程序的主屏幕——一个媒体播放器——正在显示电影的一帧画面。

**图 16-6.** *MultimediaMIDlet 应用程序的媒体播放器视图，显示* Duck and Cover2 *中的一帧画面*

**注意** 正如我之前在“捕获媒体”部分提到的，Java ME 设备对媒体捕获的支持时好时坏，模拟器也不例外。更糟的是，NetBeans Mobility Pack 提供的模拟环境并不支持当今普通 Java ME 设备所能渲染的全部媒体文件。在调试媒体应用程序时，我发现最好的做法是先在模拟器中测试 WAV 音频文件或 MPEG-1 视频文件，然后转移到目标手机上，因为我知道该手机上实际可用的音频和视频格式。当今 Java ME 设备上的多媒体支持是 Java ME 生态系统中快速变化的一部分，因此在构建应用程序时，请准备好花时间研究特定设备支持的媒体类型并进行测试。

2\. *Duck and Cover*，由 Anthony Rizzo 执导（1951 年；联邦民防管理局，Archer Productions 制作）。现已进入公共领域；参见 http://en.wikipedia.org/wiki/Duck_and_Cover_(film)、http://www.imdb.com/title/tt0213381/ 和 http://www.archive.org/details/DuckandC1951。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**486**

第 16 章 ■ 渲染多媒体内容

清单 16-16 显示了完整的 MultimediaMIDlet 类，它提供了整个实现。作为一个 MIDlet，其目标平台自然是 MIDP；你也可以使用 MMAPI 和 SVGAPI 编写类似的代码，在包含这些 API 以及 AWT 或 Swing 的 CDC 平台上运行。

**清单 16-16.** *MultimediaMIDlet 类*

package com.apress.rischpater.multimediamidlet;

import java.io.*;

import javax.microedition.lcdui.*;

import javax.microedition.midlet.*;

import javax.microedition.media.*;

import javax.microedition.media.control.*;

import javax.microedition.m2g.*;

public class MultimediaMIDlet

extends MIDlet

implements CommandListener, Runnable {

private Display display;

private List choiceScreen;

private Form mediaScreen;

Displayable viewerDisplayable;

private Item videoItem;



private ImageItem capturedImage;

private VideoControl videoControl;

private Command captureCommand;

private Command selectCommand;

String mediaName = null;

String mediaType = null;

private Player player = null;

SVGAnimator svgAnimator;

boolean endNow;

private static final String SVG_IMAGE_PATH = "/res/image.svg"; private static final String SVG_IMAGE_TYPE = "image/svg+xml"; private static final String WAV_SOUND_PATH = "/res/sound.wav"; private static final String WAV_SOUND_TYPE = "audio/x-wav"; private static final String MPG_MOVIE_PATH = "/res/movie.mpg"; private static final String MPG_MOVIE_TYPE = "video/mpeg"; private static final String CAP_VIDEO_PATH = "capture://video"; private static final String CAP_VIDEO_TYPE = "video/x-capture";

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**487**

private void init() {

showSupportedMedia();

display = Display.getDisplay(this);

if (choiceScreen == null) {

choiceScreen = new List("MultimediaMIDlet", List.IMPLICIT); choiceScreen.addCommand(new Command("Exit", Command.EXIT, 0)); selectCommand = new Command("Play", Command.ITEM,1); choiceScreen.setSelectCommand(selectCommand);

choiceScreen.addCommand(selectCommand);

choiceScreen.setCommandListener(this);

choiceScreen.append("Sound", null);

choiceScreen.append("Video", null);

choiceScreen.append("SVG", null);

choiceScreen.append("Camera Capture",null);

}

display.setCurrent(choiceScreen);

}

public void startApp() {

init();

}

private void showSupportedMedia() {

String[] contentTypes = Manager.getSupportedContentTypes(null); for (int i=0; i<contentTypes.length; i++) {

String protocols[] =

Manager.getSupportedProtocols(contentTypes[i]);

for (int j=0; j<protocols.length; j++) {

String s =

contentTypes[i] + ":" + protocols[j];

System.out.println(s);

}

}

}

public void pauseApp() {

endNow = true;

if (svgAnimator != null) {

svgAnimator.stop();

}

if (player!=null) {

player.close();

}

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**488**

第 16 章 ■ 渲染多媒体内容

public void destroyApp(boolean unconditional) {

endNow = true;

if (player != null)

{

player.close();

}

}

public void commandAction(Command c, Displayable s) {

if (c.getCommandType() == Command.EXIT) {

destroyApp(true);

notifyDestroyed();

} else

if (display.getCurrent() == viewerDisplayable) {

if (c == captureCommand) {

synchronized(this) {

this.notify();

}

}

} else

if (display.getCurrent() == choiceScreen ) {

if (c == selectCommand ) {

Form waitForm = new Form("Loading...");

display.setCurrent(waitForm);

int itemIndex = choiceScreen.getSelectedIndex();

switch(itemIndex) {

case 0:

mediaName = WAV_SOUND_PATH;

mediaType = WAV_SOUND_TYPE;

break;

case 1:

mediaName = MPG_MOVIE_PATH;

mediaType = MPG_MOVIE_TYPE;

break;

case 2:

mediaName = SVG_IMAGE_PATH;

mediaType = SVG_IMAGE_TYPE;

break;

case 3:

mediaName = CAP_VIDEO_PATH;

mediaType = CAP_VIDEO_TYPE;

break;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**489**

Thread t = new Thread(this);

t.start();

}

}

}

public void run() {

playFromResource();

while(!endNow) {

synchronized(this) {

try {

this.wait();

} catch (Exception e) {}

if (!endNow) {

try {

byte[] raw = videoControl.getSnapshot(null);

Image image = Image.createImage(raw, 0, raw.length);

capturedImage.setImage(image);

} catch (MediaException e) {continue;}

}

}

}

}

private void initMediaPlayer()

throws IOException, MediaException {

if (mediaScreen == null) {

mediaScreen = new Form("MultimediaMIDlet");

mediaScreen.addCommand(new Command("Exit", Command.EXIT, 0)); mediaScreen.setCommandListener(this);

}

viewerDisplayable = (Displayable)mediaScreen;

if (mediaType.equals(CAP_VIDEO_TYPE))

player = Manager.createPlayer(mediaName);

else {

InputStream in = getClass().getResourceAsStream(mediaName); player = Manager.createPlayer(in, mediaType);

}

player.realize();

player.prefetch();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**490**

第 16 章 ■ 渲染多媒体内容

private void initSvgPlayer()

throws IOException {

InputStream in = getClass().getResourceAsStream(mediaName); SVGImage svgImage = (SVGImage)ScalableImage.createImage(in, null); svgAnimator = SVGAnimator.createAnimator(svgImage);

Canvas svgCanvas = (Canvas)svgAnimator.getTargetComponent(); viewerDisplayable = (Displayable)svgCanvas;

svgImage.setViewportWidth(svgCanvas.getWidth());

svgImage.setViewportHeight(svgCanvas.getHeight());

}

private void configViewSound() {

// 无操作

}

private void configViewSvg() {

// 无操作

}

private void configViewVideo() {

captureCommand = new Command("Capture", Command.ITEM, 0); mediaScreen.addCommand(captureCommand);

videoControl = (VideoControl) player.getControl("VideoControl"); if (videoControl != null) {

videoItem = (Item) videoControl.initDisplayMode(

VideoControl.USE_GUI_PRIMITIVE, null);

mediaScreen.append(videoItem);

}

capturedImage = new ImageItem(null, null,

ImageItem.LAYOUT_DEFAULT, null);

mediaScreen.append(capturedImage);

}

private void playFromResource() {

try {

if ( mediaType.equals("image/svg+xml")) {

initSvgPlayer();

configViewSvg();

svgAnimator.play();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**491**

else

{

initMediaPlayer();

if (mediaType.startsWith("audio")) {

configViewSound();

} else if (mediaType.startsWith("video")) {

configViewVideo();

}

player.start();

}

display.setCurrent(viewerDisplayable);

}

catch (Exception e) {

showException(e);

}

}

private void showException(Exception e) {

Alert a = new Alert("Exception", e.toString(), null, null); a.setTimeout(Alert.FOREVER);

display.setCurrent(a, viewerDisplayable);

}

}

该 MIDlet 源代码以几个导入指令开头，因为它依赖于 MIDP API 以及 MMAPI 和 SVGAPI。该 MIDlet 遵循本书中所有 MIDlet 的基本组织结构，避免在构造函数中进行初始化，而是采用一个显式的 `init` 方法，当 AMS 启动 MIDlet 时，`startApp` 方法会调用该方法。以下方法负责 MIDlet 的行为：

• `startApp`、`pauseApp` 和 `destroyApp`：这些方法共同管理 MIDlet 的生命周期。启动 MIDlet 会显示一个要播放的媒体列表，而暂停或退出 MIDlet 则会停止任何待处理的媒体播放。

• `init`：如前所述，`init` 创建初始的 `List` 实例，显示媒体播放的选项。它还使用 `showSupportedMedia` 方法将支持的媒体类型列表输出到系统控制台。

• `commandAction`：此方法处理来自初始 `List` 屏幕的用户选择，并允许您在视频播放期间捕获视频帧。

• `run`：应用程序使用一个单独的线程（我将其称为*播放器线程*）进行媒体播放；该线程执行 `run` 方法，该方法启动媒体播放并等待用户捕获媒体的请求。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**492**

第 16 章 ■ 渲染多媒体内容

• `initMediaPlayer` 和 `initSvgPlayer`：这些方法分别初始化 MMAPI 和 SVGAPI 中的类。

• `configViewSound`、`configViewSvg` 和 `configViewVideo`：这些方法对显示媒体的 `Canvas` 子类实例执行额外的配置。其中一些是空操作，在此实现中不执行任何操作；它们仅用于说明目的。

• `playFromResource`：此方法使用您选择的媒体类型来决定是初始化 MMAPI 还是 SVGAPI，启动媒体播放，并显示包含渲染后媒体的 `Canvas` 子类实例。播放器线程的 `run` 方法会调用此方法。

• `showException`：此方法在 `Alert` 中显示异常。



`showSupportedMedia` 方法是你从未想过需要编写，却又会反复编写的实用工具之一。它通过 `getSupportedContentTypes` 函数向 MMAPI 管理器查询支持的内容类型列表。接着，利用内容类型（例如 `audio/wav`），它再次查询管理器以确定 MMAPI 实现所支持的协议（例如 HTTP）。最后，它将每一对支持的内容类型与协议输出到控制台。清单 16-17 展示了模拟器中的典型输出。

**清单 16-17.** *支持的媒体内容类型与协议* video/mpeg:http

video/mpeg:file

image/gif:http

image/gif:file

audio/x-wav:http

audio/x-wav:file

audio/x-wav:capture

audio/amr:http

audio/amr:file

audio/x-tone-seq:http

audio/x-tone-seq:file

audio/x-tone-seq:device

video/vnd.sun.rgb565:capture

audio/sp-midi:http

audio/sp-midi:file

audio/midi:http

audio/midi:file

audio/midi:device

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**493**

你可能会认为不需要这些信息——毕竟，大多数 Java ME 设备附带的厂商信息会提供支持的媒体类型列表——但通常你确实需要它，因为不能保证一台 Java ME 设备的固件与 MMAPI 正确连接，仅仅因为它能通过原生应用程序渲染某种媒体类型。事实上，有时 MMAPI 管理器提供的媒体列表可能是不正确的。

■**提示** 虽然在任何设备上测试 Java ME 应用程序都是开发周期中的关键环节，但对于大多数通过 JSR 文档化的 Java ME 扩展来说尤其如此，MMAPI 也不例外。始终在开发周期早期就在目标硬件上测试你的应用程序，并经常重复测试。

UI 流程的核心是 `commandListener` 方法和 MIDlet 的字段 `mediaName` 与 `mediaType`。当你选择要播放的媒体类型时，`commandListener` 使用一个简单的 switch 语句，用 JAR 文件中的文件名和你选择的媒体类型来填充这些变量。接着，它启动播放器线程，并将 MIDlet 类本身作为 `Runnable` 对象。生成的线程从 MIDlet 类的 `run` 方法开始，该方法负责初始化和启动播放。

接下来的部分将描述 MIDlet 如何播放音频和视频、从视频流中捕获图像，以及播放动画 SVG 内容。

**播放音频和视频**

当播放器线程启动时，其 `run` 方法首先调用 `playFromResource`。该方法使用 `commandListener` 预先设置的关于你所选媒体类型的信息。

随后，

实现会利用这些信息来决定是初始化 SVGAPI 还是 MMAPI，以及设置 MIDlet 的 `viewerDisplayable` 字段中的下一个 `Displayable` 项。此外，如果你选择的文件不是 SVGAPI，该类将创建 MIDlet 所需的任何额外控件。此逻辑由一系列嵌套的 if-else 语句组成，对于这个示例来说已经足够，但或许值得将其拆分为具有公共接口（返回 `Displayable`）的独立子类。

`initMediaPlayer` 负责初始化 MMAPI 的繁重工作；它与清单 16-1 非常相似。它创建一个 `Form` 实例，该实例将成为 MIDlet 在渲染媒体时显示的 `Displayable`。`initMediaPlayer` 添加一个 `Command` 实例，允许你在将新表单的命令监听器设置为 MIDlet 之前退出应用程序。

然后，该方法创建一个 `Player` 实例，如果你选择了“摄像头捕获”选项，则使用摄像头的定位器；否则，使用从 `commandAction` 方法中你选择的 JAR 资源中获取的 `InputStream`。最后，它通过先实现（realizing）播放器，然后预取（prefetching）内容来配置新的 `Player` 实例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**494**

第 16 章 ■ 渲染多媒体内容



一旦 `initMediaPlayer` 完成其工作，控制权将返回给 `playFromResource`，该方法会调用 `configViewSound` 或 `configViewVideo` 来对用户界面进行任何额外的后期配置，然后才开始媒体播放。`configViewSound` 方法不执行任何操作；它只是一个占位符。你可以尝试修改代码，在播放器字段上创建各种 `Control` 子类，例如 `VolumeControl`。

然而，`configViewVideo` 必须执行一些操作；它创建了另一个 `Command` 实例，用于触发视频捕获，并在创建 `VideoControl` 实例之前将其添加到 `Form` 实例中。生成的 `VideoControl` 向该方法提供一个 `Item` 实例以添加到 `Form` 中。最后，该方法创建了第二个额外的空 `ImageItem` 实例，MIDlet 使用该实例来显示你在选择“捕获”选项时捕获的图像。

然后，`playFromResource` 方法启动新创建的 `Player` 实例，并将当前的 `Viewable` 实例设置为 `viewerDisplayable`，然后将控制权返回给播放器线程的 `run` 方法。在 MIDlet 执行的这个阶段，MMAPI 会渲染你选择的媒体，而播放器线程则会在此处阻塞，等待你选择“捕获”选项。

**捕获图像**

MIDlet 存储在 `videoControl` 字段中的 `VideoControl` 实例可以在其 `Player` 实例开始播放后捕获快照，尽管不建议在主线程上执行此操作，因为这可能会导致 MIDlet UI 或 MMAPI 本身停滞。通过使用 Java 的 `synchronized` 关键字和 `Object` 的 `notify` 方法将 MIDlet 实例本身作为显式锁，MIDlet 可以从 UI 线程向播放器线程发送信号，表明你想要捕获一帧。此外，使用 `synchronized` 关键字可以确保在应用程序执行捕获操作时无法触发另一次捕获，这无论如何都可能是一个错误。

一旦 Java 虚拟机使用 `notify/synchronized` 来唤醒播放器线程以捕获一帧，播放器线程就会通过调用 `videoControl` 的 `getSnapshot` 方法（指定默认编码）来执行捕获。此时，播放器拥有捕获图像数据的 `byte[]`；代码仅使用这些数据来设置 `Form` 上的另一个图像，以显示你捕获的图像。

你可能还想对应用程序捕获的图像数据做另一件事，那就是与他人分享。你可以通过修改我在第 14 章中展示的 `MMSSender` 类来实现这一点，使其接受一个可选的编码图像数据 `byte[]`，如清单 16-18 所示，并使用清单 16-18 定义的新 `MMSSender` 方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**495**

**清单 16-18.** *对 MMSSender 类的修订，使其能够通过 MMS 发送捕获的图像*

public class MMSSender implements Runnable {

private byte[] imageBytes;

public void sendMsg(String r, String id, String m, byte[] bi) {

if (sending) return;

receiver = r;

appId = id;

msg = m;

imageBytes = bi;

encoding = System.getProperty("microedition.encoding"); Thread t = new Thread(this);

t.start();

}

public void sendMMS() {

String address = "mms://" + receiver + ":" + appId; System.out.println(address);

MessageConnection c = null;

try {

c = (MessageConnection) Connector.open(address);

MultipartMessage mpm = (MultipartMessage) c.newMessage(

MessageConnection.MULTIPART_MESSAGE);

mpm.setSubject("An Image");

if (image != null) {

InputStream is = getClass().getResourceAsStream(image); imageBytes = new byte[is.available()];

is.read(imageBytes);

}

if (imageBytes != null) {

mpm.addMessagePart(

new MessagePart(

imageBytes, 0, imageBytes.length,

"image/png", "id1", null, null));

}

if (msg != null) {

byte[] bMsg = msg.getBytes();

mpm.addMessagePart(

new MessagePart(bMsg, 0, bMsg.length,

"text/plain", "txt1", null, encoding));

}

c.send(mpm);

[www.it-ebooks.info](http://www.it-ebooks.info/)

**496**

第 16 章 ■ 渲染多媒体内容

} catch (Exception e) {}

finally {

if (c != null) {

try {

c.close();

} catch (IOException e) {}

}

}

}

…其他字段和方法与第 14 章相同…

}

■**注意** 清单 16-18 中的实现假设 `VideoControl` 返回的图像是 PNG 图像；如果你是为生产应用程序编写代码，则应扩展 `MMSSender` 以接受你提供的图像数据的 MIME 类型，并为 `getSnapshot` 方法指定特定的 MIME 类型。

**播放 SVG 内容**

回到 `MultimediaMIDlet`，播放 SVG 内容的流程是类似的。`playFromResource` 方法调用 `initSvgPlayer` 和 `configViewSvg`，然后使用生成的 `svgAnimator` 的 `play` 方法开始播放。由于 SVGAPI 处理媒体渲染的方式与 MMAPI 略有不同，`initSvgPlayer` 中的流程也不同，尽管 `configViewSvg` 仍然是一个空方法，就像 `configViewSound` 一样。`initSvgPlayer` 方法与你在清单 16-12 中首次看到的伪代码非常相似。

`initSvgPlayer` 方法首先获取 JAR 文件中 SVG 图像的 `InputStream` 实例，然后使用该数据和默认的 SVGAPI `ExternalResourceHandler` 创建一个新的 `SVGImage` 实例。有了图像后，该方法接下来创建它将用于播放动画的 `svgAnimator`；如果 SVG 是静态图像，那也没问题，因为当 `playFromResource` 调用 `svgAnimator.play` 时，它会被 `svgAnimator` 实例渲染。使用 `svgAnimator`，`initSvgPlayer` 获取 `SVGAnimator` 的 `Canvas` 实例，并将其保存为当 `playFromResource` 设置下一个 `Displayable` 时 MIDlet 应显示的 `Displayable`。最后，此方法获取 `svgCanvas` 的默认边界，并使用它们初始化 `svgImage` 的视口，以获取并设置图像在显示器上的最佳渲染矩形。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 渲染多媒体内容

**497**

**总结**

通过 JSR 135 和 JSR 287 中记录的可选 API，Java ME 平台为你的应用程序提供了以多种不同格式（包括 WAV、MPEG 和 SVG）渲染丰富音频和视频的能力。尽管 JSR 135 定义的 MMAPI 与 JSR 287 定义的 SVGAPI 有根本不同，但它们共同为你的应用程序 UI 设计提供了广泛的自由度。

MMAPI 使用一种让人联想到许多用户界面框架（如 Swing）所使用的 MVC 范式的范式。你的应用程序获取或向播放器提供数据源，该播放器可以由一个或多个控件控制，这些控件可以以某种方式影响媒体渲染。MMAPI 提供了一个 `Manager` 类，该类在给定数据源（例如 `InputStream` 实例或指定数据源位置的定位器）时提供单独的 `Player` 实例。定位器可以指向设备上的媒体、来自设备上的传感器或来自网络上的设备；许多（但不是所有）MMAPI 实现都支持通过 HTTP、RTP 或 RTSP 进行某种形式的远程媒体访问。

由于 MMAPI 为专用硬件资源提供了 Java ME 包装器，使用 MMAPI 的应用程序需要仔细考虑何时使用这些资源。`Player` 接口实现了一个状态机，有助于限制对设备上有限资源的访问。`Player` 对象可以处于五种状态之一：未实现、已实现、预取、已启动或已关闭。`Player` 接口提供了在这些状态之间转换的方法；通常，你的应用程序会创建一个 `Player` 实例，并且仅在通过 `start` 开始播放之前调用其 `realize` 和 `prefetch` 方法。



MMAPI Player 对象同时也是 Control 对象的工厂；Control 可以改变 Player 的行为（例如调整音量），或提供额外的功能，例如提供一个接口，从中获取可用于在媒体文件中显示视频的用户界面组件。在 Java 设备上从音频和视频传感器进行捕获就是这样工作的；你为设备指定一个定位器，然后可以使用 VideoControl 的 `getSnapshot` 方法获取图像快照，或者使用 RecordControl 类来录制音频或视频数据流。然而，并非所有设备都支持音频或视频捕获，Java ME 运行时提供了系统属性，这些属性精确枚举了特定 MMAPI 实现所支持的媒体类型和传感器。某些设备可能支持 JSR 234，它定义了额外的 Control 子类，你可以与 MMAPI 一起使用来控制捕获传感器以及执行额外的多媒体操作。

另一方面，SVGAPI 支持 W3C 定义的 SVG Tiny 1.2 标准。使用 SVG，你可以定义静态或动画图像，这些图像在几乎任何渲染尺寸下都显得清晰且无像素化。实际上，你可以通过在 SVG 文档中指定事件来构建应用程序用户界面的整个部分，你的 Java ME 应用程序可以接收这些事件以响应用户操作，例如焦点变化。SVG 基于 XML；许多基于矢量的绘图程序都支持这一广泛采用的标准，使其广泛适用于移动内容开发者。通过其包和类，SVGAPI 拥有一套丰富的功能，包括能够通过 SVG DOM 访问 SVG 文档的各个部分。事实上，你甚至可以即时创建 SVG 图像，让用户在你的应用程序中直接在 Java ME 设备上创建新的 SVG 图像。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**498**

第 16 章 ■ 渲染多媒体内容

你可以通过两种方式之一使用 SVGAPI 来渲染图像：你可以使用 `SVGImage` 类来渲染静态图像，或者使用 `SVGAnimator` 类来渲染动态图像。这些类与 MIDP Canvas 子类或 AWT Component 子类配合使用，具体取决于目标设备的 Java ME 配置。通常，创建 `SVGImage` 实例并将渲染委托给 `SVGAnimator` 是最简单的方法，`SVGAnimator` 拥有自己的渲染线程和状态机来管理动画 SVG 图像以及 SVG 图像所需的任何用户交互。最后，如果你只是使用 NetBeans 快速开发一个应用程序，并且需要一个简单的容器来容纳动画 SVG（例如，用于启动画面或插屏显示），NetBeans 本身提供了一个包含一些简单组件的包，用于渲染静态或动画 SVG 图像。你可以直接从 NetBeans GUI 构建器中使用这些组件，从而轻松地将基于 SVG 的功能添加到你的应用程序中。

无论你的应用程序将要渲染哪种多媒体，多媒体应用程序中的线程管理都至关重要。你绝不应尝试在主应用程序或 MIDlet 线程上使用 MMAPI 或 SVGAPI 渲染多媒体，因为执行渲染所需的工作可能会阻塞 UI 线程，使设备感觉迟钝或无法使用。MMAPI 和 SVGAPI 都为你提供了在主线程上调度操作的接口，并且你可以使用简单的 Java 原语（例如通过 `synchronized` 关键字或监视器变量进行同步）来协调主 UI 线程与任何多媒体线程之间的活动。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章

寻找方向

**对**于许多开发者来说，基于位置的服务（LBS）应用程序的潜力是当今 Java ME 开发中最令人兴奋的方面。将用户位置与网络可用数据（用于地理编码的商业位置、定位其他用户、实时交通、天气和其他事件信息）结合使用的新应用程序的潜力简直令人震惊。全新的市场正在涌现，例如经济高效的实时位置感知导航；涉及朋友位置的社交网络；以及透明地利用设备位置的调度和紧急服务应用程序。JSR 179 定义了位置 API，它提供了一个简单的可选 API，用于确定设备的位置并与其他应用程序共享位置偏好。

在本章中，我首先为之前未接触过 LBS 背后技术的读者做一些简要介绍。接下来，我将介绍位置 API，描述 JSR 179 为在 Java ME 设备上启用 LBS 而定义的包和类，并向你展示如何在开发和调试应用程序时使用 Sun Java Wireless Toolkit 模拟位置数据。在本章结尾，我将向你展示 WeatherWidget 应用程序的一个扩展，该扩展允许用户在获取天气数据时指定其当前位置，而不是输入位置。

**理解基于位置的服务**

多年来，包括美国在内的许多国家的应急管理官员已经能够确定电话呼叫者的位置。（在美国，这是 911 系统的关键部分；在任何电话上拨打 911 都会接通紧急调度员，该调度员拥有你拨打电话所在位置的信息。）在 20 世纪 90 年代中期，许多国家的政府要求运营商为无线电话用户提供相同的信息，使应急响应人员拥有与处理有线电话紧急呼叫时相同的能力。这推动了多种移动手机定位技术的创建，包括辅助全球定位系统（A-GPS）和各种蜂窝基站三角测量系统。最初主要用于紧急目的，网络运营商热衷于寻找一种方式来对此服务收费，因为提供基于位置的信息在网络基础设施上产生了高昂的成本，这是由于需要对其网络基础设施进行硬件改造。与此同时，许多企业迅速认识到位置感知无线终端在车队管理和社交网络等多种应用中的市场潜力。

LBS 服务的首次商业推出是 2001 年日本 KDDI 公司；日本早期的服务获得了广泛赞誉和快速采用。在其他地方，采用速度较慢，但随着包括 Java ME、高通公司的无线二进制运行时环境（BREW）、苹果的 iPhone 和谷歌的 Android 在内的移动平台为 LBS 应用程序提供 API，现在正达到临界规模。此外，运营商已在其网络上为应用程序提供该服务。

如今，有几种商用系统允许无线设备高精度地确定其位置，前提是它们能够访问一个或多个广域网（WAN）服务。最著名的是 GPS，设备利用从轨道卫星接收的无线电信号中得出的信息来三角测量其位置。虽然商用 GPS 接收器以低成本用于工业和私人目的已有十多年，但商用 GPS 有两个主要缺点，使其不适合作为移动设备定位的唯一手段。首先，GPS



接收器需要清晰的天空视野，才能接收服务所使用的卫星发出的无线电信号。其次，GPS 的精度会因大气效应和一项名为“选择可用性”（SA）的特定 GPS 功能而有所变化，该功能允许美国政府战时降低民用 GPS 的精度。幸运的是，SA 目前已被停用，并且随着民用 GPS 的广泛普及，它不太可能被重新启用，但其他误差仍可能导致数米的偏差。如今，出于政治和经济原因，全球各地都在持续努力开发其他全球导航辅助系统，为 GPS（可在全球任何地方工作）提供国际替代方案。

为了解决 GPS 固有的问题，人们提出了其他几种方案，包括 A-GPS 和各种形式的网络三角测量。A-GPS 通过利用从设备可能使用的蜂窝基站数据中收集到的关于设备位置的额外提示来工作，并将确定位置所需的大量数学处理工作委托给网络资源。三角测量技术则依赖于根据从本地蜂窝基站（以及最近的本地 Wi-Fi 基站）接收到的信号来确定设备的位置。为了确保快速的定位时间以及高精度和高可用性，当今的无线手持设备通常集成了多种定位方案。

虽然确定设备位置的具体方式可能有所不同，但定位系统提供的结果是相同的：设备的坐标，通常是在特定时间点根据一个*基准面*（地球表面的数学模型）计算出的经纬度。由于定位系统是近似的——大气效应、多径效应等无线电传播效应以及其他因素即使是最好的定位系统也会引入误差——系统提供的位置信息会包含一个位置*精度*的概念，通常是一个半径。坐标和半径定义了一个设备可能位于其中的圆形区域。在设计应用程序时，务必记住位置数据是近似的——即使考虑了位置圆也是如此。设备有可能落在定位圆之外，尽管在实践中这种情况并不常见。这种定位误差通常被称为位置的*不确定性*，在某些系统中，您可以用位置不确定性来换取其他因素，例如确定设备位置所涉及的时间或财务成本。根据设备和网络的能力，位置信息还可能附带设备的海拔高度、速度和航向（运动方向），但这并非总是可用。

由于定位系统本身的特性，从电池电量、时间和用户成本的角度来看，获取设备位置是一项昂贵的操作。定位可能需要稀缺的设备资源以及与远程服务器的网络事务，因此设备提供的是近实时的位置数据，而非实时数据。

**介绍位置 API**

Java ME 位置 API 定义了 `javax.microedition.location` 包，其中包含一组类，允许应用程序请求和获取位置结果。位置 API 旨在与 CLDC 和 CDC 设备一起使用，但它仅适用于 CLDC 1.1 及更高版本的设备，因为定位框架需要浮点数学支持才能向您的应用程序提供经纬度信息。

位置 API 将设备的定位子系统从您的应用程序中抽象出来，为您提供一种以最能满足应用程序约束（例如位置获取时间、不确定性和用户成本）的方式来确定设备位置的方法。您可以只请求一次位置（通常称为*单次*定位请求），或者您可以请求 API 在应用程序运行时发送设备位置的通知。所有位置 API 实现都必须在位置响应中提供经纬度坐标、设备确定位置的时间以及精度度量。根据设备的硬件，位置数据还可能包括设备的方向（指南针、俯仰角和横滚角）、设备移动的速度，以及更人性化的数据，例如位置的街道地址。

位置 API 提供了一个大多数其他平台上的 API（例如，高通公司的 BREW）所不具备的额外功能，那就是访问系统范围的地标数据库以及这些地标的位置。这允许设备的原生软件和设备上的所有应用程序共享一个通用的用户指定位置存储库，例如用户的居住地和工作地点。这使得用户能够在设备上的所有 LBS 应用程序中获得一致的用户体验。

**理解位置 API**

`javax.microedition.location` 包定义了九个类、两个接口和两个异常。除了继承自 `Exception` 的异常之外，类层次结构几乎是完全扁平的，这体现了 API 及其所抽象领域的简洁性。该包提供了以下类：

*   `LocationProvider`：表示设备内能够确定设备位置的硬件或软件模块。它提供了一个静态工厂方法，该方法返回配置为响应特定位置请求的特定 `LocationProvider` 实例。
*   `Criteria`：表示适用于位置请求的特定标准，例如所需的精度和用户成本。
*   `Location`：表示关于位置的基本信息集合，包括时间戳、坐标、精度、速度、航向以及关于用于确定位置的方法的信息，以及可选的文本地址。
*   `Coordinates`：表示位置的纬度、经度和海拔高度，并提供了在坐标的浮点表示和人类可读的文本表示之间进行相互转换的方法。
*   `QualifiedCoordinates`：表示与坐标精度测量相关联的纬度、经度和海拔高度三元组。它是 `Coordinates` 的子类。
*   `AddressInfo`：表示关于位置的文本信息，例如街道地址。`AddressInfo` 类封装了一个通过清单常量访问的字段集合。
*   `Orientation`：表示终端在空间中的物理方向，包括方位角（水平指向方向）、俯仰角（垂直仰角）和横滚角（围绕设备自身纵轴的方向）。
*   `LandmarkStore`：表示单个用户地标的存储库。始终存在一个默认的地标存储库，并且实现可以提供额外的命名地标存储库供应用程序使用。
*   `Landmark`：表示一个具有用户提供名称的已知位置，由名称、一个 `QualifiedCoordinates` 对象和一个 `AddressInfo` 对象组成。

该 API 定义了两个接口：`LocationListener` 和 `ProximityListener`。`LocationListener` 为您的应用程序提供了一种通过其 `locationUpdated` 方法定期从 API 实现接收位置报告的方式，并且它允许


API 实现，用于在位置数据源发生变化时（例如，由于从一个网络切换到另一个网络）通知您的应用程序。当您希望持续接收位置通知时（例如，在设备移动时提供实时位置报告的应用程序），可以使用 `LocationListener` 接口。`ProximityListener` 接口则允许您的应用程序等待终端进入特定坐标附近范围的临近通知。使用 `ProximityListener` 来确定设备何时到达预定位置比使用 `LocationListener` 更高效，因为定位硬件的底层实现可能具有类似的能力。它也更容易；您无需为了判断设备是否到达目标位置而接收和管理常规的位置报告。

`LocationException` 和 `LandmarkException` 是实现在定位子系统或地标存储发生严重故障时可能抛出的异常。对于这两种异常，与异常关联的字符串提供了关于故障细节的额外信息。

您可以使用位置 API 做两件事：确定设备的位置，以及管理用户为 LBS 应用程序创建的地标。

**使用位置 API 确定设备位置**

使用位置 API 需要您执行三个步骤：**1.** 建立位置请求的（可能由用户指定的）条件。

**2.** 获取一个 `LocationProvider` 实例，该实例能够以满足您条件的方式向您的应用程序提供设备的位置。

**3.** 确定位置的坐标。

清单 17-1 中的伪代码展示了这一基本事件序列。

**清单 17-1.** *确定设备位置* try {

Criteria cr = new Criteria();

cr.setHorizontalAccuracy(10);

LocationProvider lp = LocationProvider.getInstance(cr); Location l = lp.getLocation(60);

Coordinates c = l.getQualifiedCoordinates();

if (c != null) {

// 对位置信息进行处理

}

} catch (LocationException e) {}

catch (Exception e) {}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**504**

第 17 章 ■ 寻路

您创建的 `Criteria` 对象指定了您对应用程序将要请求的位置有任何特定要求。位置 API 实现使用这些条件来配置和选择一个特定的 `LocationProvider` 实例。表 17-1 显示了您可以在 `Criteria` 对象中指定的具体条件。如果实现无法明确满足您的条件，它将尽最大努力进行选择，前提是生成的 `LocationProvider` 实例满足您的特定成本标准。`Criteria` 类提供了常量 `NO_REQUIREMENT`、`POWER_USAGE_LOW`、`POWER_USAGE_MEDIUM` 和 `POWER_USAGE_HIGH` 来描述不适用任何条件的情况以及描述特定的功耗标准。清单 17-1 为 `LocationProvider` 实例设置了一个单一条件：位置确定结果应在设备可能位置的十米范围内。

**表 17-1.** *位置提供者配置的条件* **条件**

**单位**

**默认值**

**设置方法**

**访问方法**

水平

米

NO_REQUIREMENT setHorizontalAccuracy

getHorizontalAccuracy

精度

垂直

米

NO_REQUIREMENT setVerticalAccuracy

getVerticalAccuracy

精度

首选

毫秒

NO_REQUIREMENT setPreferredResponseTime

getPreferredResponseTime

响应

时间

功耗

int

NO_REQUIREMENT setPreferredPowerConsumption getPreferredPowerConsumption 消耗

成本

布尔值

true (允许 setCostAllowed

isAllowedToCost

允许

产生成本

对用户)

速度和

布尔值

false

setSpeedAndCourseRequired

isSpeedAndCourseRequired

航向

要求

海拔

布尔值

false

setAltitudeRequired

getAltitudeRequired

要求

地址

布尔值

false

setAddressInfoRequired

isAddressInfoRequired

要求

`LocationProvider` 类既是 `LocationProvider` 实例的工厂，也是 `Location` 对象的工厂，同时还是位置和临近信息的通知器。您通过该类的 `LocationProvider.getInstance` 方法获取一个特定的 `LocationProvider` 实例，并传入一个描述 `LocationProvider` 执行定位工作时应遵循的限制条件的 `Criteria` 实例。一旦您获得了

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻路

**505**

`LocationProvider` 实例，您就可以通过分别调用 `LocationProvider` 的 `getLocation` 或 `getLastKnownLocation` 方法来确定当前位置或最后已知位置（可能为 null）。许多 `LocationManager` 方法可能会抛出异常——通常是针对基于位置故障的 `LocationException`，或者如果用户或平台不允许该操作，则抛出 `SecurityException`。

■**提示** 位置确定可能是一个耗时的过程，并且 `LocationProvider` 实现可能会阻塞当前线程。因此，最好在独立于主线程的线程中调用 `getLocation`，以避免阻塞应用程序的用户界面。在本章后面的“定位用户”部分，我将向您展示如何在实际的 MIDlet 中执行此操作。

`LocationProvider` 类的 `getLocation` 方法返回的特定 `Location` 实例提供了设备在实现使用特定定位方法确定的特定时间点的位置。当您调用 `getLocation` 时，必须传入一个数字，指示您的应用程序愿意等待定位请求完成的最大秒数。

通过 `Location` 实例，您可以使用以下方法来获取更多关于设备位置的信息：

*   `isValid`：如果 `Location` 有效且包含坐标，则返回 true
*   `getQualifiedCoordinates`：返回 `Location` 的坐标
*   `getTimestamp`：返回位置数据收集的时间
*   `getAddressInfo`：返回街道地址（作为 `AddressInfo` 对象）
*   `getCourse`：返回相对于真北的航向（以度为单位）
*   `getSpeed`：返回测量时设备当前的地面速度（以米/秒为单位）
*   `getLocationMethod`：返回一个描述实现如何确定位置的位掩码
*   `getExtraInfo`：返回关于请求的任何额外信息
表 17-2 描述了 `getLocationMethod` 用于报告实现如何确定设备位置的位字段值。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**506**

第 17 章 ■ 寻路

**表 17-2.** *定位方法和位置位字段值* **方法**

**位掩码中断言位**

**说明**

通过其他方式辅助

MTA_ASSISTED

MTY_NETWORKBASED 和

MTY_TERMINALBASED 表示辅助源

未使用辅助

MTA_UNASSISTED

到达角

MTE_ANGLEOFARRIVAL

使用天线上的入射辐射角

小区 ID

MTE_CELLID

在确定中使用基站塔 ID

卫星

MTE_SATELLITE

使用卫星系统（例如 GPS）

短距离

MTE_SHORTRANGE

使用蓝牙或其他无线网络

系统

时间差

MTE_TIMEDIFFERENCE

使用接收到的地面信号之间的

时间差

到达时间

MTE_TIMEOFARRIVAL

使用接收到的地面信号的

到达时间

基于网络的方法

MTY_NETWORKBASED

在辅助请求中使用网络

基于终端的方法

MTY_TERMINALBASED

位置确定使用基于终端

的方法



Location API 提供了 `Coordinates` 和 `QualifiedCoordinates` 类来表示空间中的特定位置。`Coordinates` 实例是绝对的，指定了纬度、经度和海拔高度，且没有误差；`QualifiedCoordinates` 扩展了 `Coordinates` 的概念，增加了以米为单位的水平和垂直不确定度信息。`Coordinates` 类提供了获取坐标值的方法（`getAltitude`、`getLatitude` 和 `getLongitude`），以及一个通用的转换方法（`convert`），用于在坐标的浮点表示和字符串表示之间进行转换。

你还可以通过调用一个 `Coordinates` 对象的 `azimuthTo` 和 `distance` 方法，并传入第二个坐标，来获取坐标之间的方位角和距离。

由于 `LocationProvider` 始终提供真实的测量值，因此它总是返回一个 `QualifiedCoordinate`；你可以使用结果的 `getHorizontalAccuracy` 和 `getVerticalAccuracy` 方法来确定特定位置测量的不确定度。

你的应用程序可能需要不止一次的位置测量；你可以重复调用 `LocationProvider` 的 `getLocation` 方法，或者注册一个实现了 `LocationListener` 的对象来接收位置事件。通过 `setLocationListener` 方法向 `LocationProvider` 注册一个 `LocationListener` 需要四个参数：

• `listener`：接收位置事件的 `LocationListener` 实现

• `interval`：指定确定位置并向监听器发送位置事件的频率

• `timeout`：指定在间隔时间过后，位置信息最多可以延迟多久

• `maximumAge`：指定在设置事件时，允许的位置结果的最大时效（以秒为单位）

类似地，你的应用程序可以通过使用 `addProximityListener` 方法添加一个 `ProximityListener`，在设备接近特定位置时接收通知。

当你这样做时，你需要指定实现 `ProximityListener` 接口的实例、目标位置的坐标，以及围绕目标位置定义临近区域的半径（以米为单位）。但请注意，虽然你可以通过单个 `LocationProvider` 拥有多个临近通知，但你只能拥有一个 `LocationListener`。

**使用 Location API 管理地标**

Location API 包含两个类，允许具有位置感知能力的应用程序通过一个公共存储进行交互：`Landmark` 和 `LandmarkStore`。任何具有安全权限且支持 LBS 的应用程序都可以向此存储添加内容，以及查看和修改此存储中的元素。这两个类使你能够在一个应用程序（例如，旅行指南）中添加一个地标，并从另一个应用程序（例如，逐向导航应用程序）中访问同一个地标。

顾名思义，`LandmarkStore` 包含 `Landmark` 对象的表示。

`LandmarkStore` 是持久化的，尽管没有指定具体的实现方式；它可能将记录保存在闪存中的 MIDP 记录存储中，或者完全使用其他存储方案。

Location API 要求任何实现至少有一个 `LandmarkStore`，称为*默认* `LandmarkStore`，尽管该实现可能支持额外的存储，你可以像访问 MIDP 记录存储一样通过名称来访问它们。要访问一个存储，你必须首先获取一个 `LandmarkStore` 的实例，方法是调用其静态方法 `getInstance` 并传入你寻找的存储名称（对于默认存储则传入 `null`）。

你可以通过调用 `LandmarkStore.listLandmarkStores` 方法来获取存储列表，该方法返回一个字符串数组，其中包含设备上每个存储的名称。`LandmarkStore` 还提供了静态方法 `createLandmarkStore` 和 `deleteLandmarkStore`，用于创建和删除存储。

与我向你展示的第 7 章中的 PIM 包类似，Location API 支持 `LocationStore` 的类别；类别只是一个附加到存储中条目的字符串，允许用户通过该字符串对相关项目进行分组（例如“工作”或“假期”）。你可以使用以下方法与存储中的类别进行交互：

• `getCategories`：枚举存储中的类别，返回一个 `Enumeration`

• `addCategory`：添加一个类别

• `deleteCategory`：删除一个类别，同时保留该类别中的记录

请注意，你可以将一个类别与存储中的多个条目关联，但每个条目只能与一个类别关联。

`LandmarkStore` 提供了以下用于在存储中添加、删除和更新 `Landmark` 的方法：

• `addLandmark`：向存储中添加一个新的地标

• `deleteLandmark`：删除存储中的一个地标

• `updateLandmark`：更新存储中一个已有的地标

• `removeLandmarkFromCategory`：移除分配给特定 `Landmark` 的类别

添加一个 `Landmark` 需要你同时传入要添加的 `Landmark` 实例和一个类别名称（或者传入 `null` 表示无类别）。

你可以使用其定义的三个 `getLandmarks` 方法之一来搜索存储中已有的 `Landmark` 实例，所有这些方法都返回一个 `Landmark` 的 `Enumeration`。不传参数调用 `getLandmarks` 会枚举存储中的*所有* `Landmark`。向 `getLandmarks` 传入一个类别名称（或 `null`）和一个由纬度和经度构成的边界矩形，会返回该矩形内 `Landmark` 实例的枚举列表；而仅传入一个类别（或 `null`）和一个名称，则会返回你指定的类别中具有你提供名称的 `Landmark` 实例。

`Landmark` 本身只是一个容器，用于保存用户的名称、描述、地址信息和坐标；该类通过访问器和修改器公开这些信息：

• `getName` 和 `setName`：获取和设置地标名称

• `getDescription` 和 `setDescription`：获取和设置地标描述

• `getAddressInfo` 和 `setAddressInfo`：获取和设置地标地址，该地址是 `AddressInfo` 的一个实例

• `getQualifiedCoordinates` 和 `setQualifiedCoordinates`：获取和设置地标的位置，作为 `QualifiedCoordinates` 的一个实例

**理解安全在 LBS 中的作用**

许多用户对位置和网络服务之间的相互关系*非常*敏感，这是可以理解的；隐私通常是一个关键问题。与大多数可选 API 一样，Location API 在支持它的平台（如 MIDP 2.0）上需要权限。因此，你的应用程序在分发后，可能需要在移动设备上运行时获得运营商签名。表 17-3 列出了 Location API 中的权限名称和受保护的方法。该 API 不仅为确定位置提供权限，还为访问地标存储提供权限。

**表 17-3.** *Location API 权限*

**权限名称**

**受此权限保护的方法**

`javax.microedition.location.Location`

`LocationProvider.getLocation`
`LocationProvider.setLocationListener`
`LocationProvider.getLastKnownLocation`

`javax.microedition.location.Orientation`

`Orientation.getOrientation`

`javax.microedition.location.ProximityListener`

`LocationProvider.addProximityListener`

`javax.microedition.location.LandmarkStore.read`

`LandmarkStore.getInstance`
`LandmarkStore.listLandmarkStores`

`javax.microedition.location.LandmarkStore.write`

`LandmarkStore.addLandmark`
`LandmarkStore.deleteLandmark`
`LandmarkStore.removeLandmarkFromCategory`
`LandmarkStore.updateLandmark`

`javax.microedition.location.LandmarkStore.category`

`LandmarkStore.addCategory`
`LandmarkStore.deleteCategory`

`javax.microedition.location.LandmarkStore.management`

`LandmarkStore.createLandmarkStore`
`LandmarkStore.deleteLandmarkStore`



用户若想使用 LBS 应用程序，就必须同时信任该应用及其供应商。建立这种信任并非仅仅像表 17-3 那样开启或关闭各种 MIDlet 权限。在构建应用时，你需要仔细思考位置数据为何相关，以及用户分享位置信息会承担哪些风险。你提供的价值必须大于用户感知或实际面临的风险。此外，你的应用应像对待其他隐私数据一样对待位置数据，仅在应用功能所需的范围内进行暴露。一个明显的例子是社交网络应用：用户可能希望分享自己的位置，但也希望能在分享程度上有所控制。例如，用户可能希望控制公开显示，并仅与特定好友圈子分享信息。

**使用位置 API**

为新闻和天气应用添加位置信息是位置 API 的一个明显用途，这正是我在本节中通过本书一直使用的 WeatherWidget 示例所做的事情。基于第 13 章使用 kXML 解析器的 WeatherWidget，清单 17-2 所示的 WeatherWidget MIDlet 包含了将当前位置用作天气预报位置的功能。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**510**

第 17 章 ■ 寻找方向

**清单 17-2.** *使用位置 API 的 WeatherWidget 类* package com.apress.rischpater.weatherwidget;

import javax.microedition.midlet.*;

import javax.microedition.lcdui.*;

import javax.microedition.location.*;

public class WeatherWidget extends MIDlet

implements CommandListener, Runnable {

private Form wxForm;

private Alert locatingAlert;

private StringItem wxItem;

private Command exitCommand;

private Command screenCommand;

private Command settingCommand;

private Command okCommand;

private Command backCommand;

private Command locateCommand;

private List locationList;

private TextBox locationTextBox;

private Alert cannotAddLocationAlert;

private WeatherFetcher fetcher;

private WeatherLocation wxlocation;

private WeatherLocationStore locationStore;

private void initialize() {

locationStore = new WeatherLocationStore();

String[] locations = locationStore.getLocationStrings(); try {

if (locations.length > 0 )

wxlocation = locationStore.getLocation(locations[0]);

}

catch(Exception e){}

fetcher = new WeatherFetcher(wxlocation, this);

getDisplay().setCurrent(get_wxForm());

}

public void startApp() {

initialize();

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻找方向

**511**

public void pauseApp() {

}

public void destroyApp(boolean unconditional) {

}

public void update() {

get_wxItem().setText(get_forecast());

try

{

locationStore.updateLocation(wxlocation);

}

catch(Exception e){}

}

public void determineLocation() {

getDisplay().setCurrent(get_locatingAlert());

Thread t = new Thread(this);

t.start();

}

public void commandAction(Command command, Displayable displayable) {

if (displayable == wxForm) {

if (command == exitCommand) {

exitMIDlet();

} else if (command == settingCommand) {

getDisplay().setCurrent(get_locationList());

}

} else if

(displayable == locationList) {

if (command == locateCommand) {

determineLocation();

} else if (command == screenCommand) {

getDisplay().setCurrent(get_locationTextBox());

} else if (command == List.SELECT_COMMAND) {

int index = get_locationList().getSelectedIndex();

set_location(get_locationList().getString(index));

fetcher.cancel();

fetcher = new WeatherFetcher(wxlocation, this);

getDisplay().setCurrent(get_wxForm());

} else if (command == backCommand) {

getDisplay().setCurrent(get_wxForm());

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**512**

第 17 章 ■ 寻找方向

} else if (displayable == locationTextBox) {

if (command == locateCommand) {

determineLocation();

} else if (command == backCommand) {



getDisplay().setCurrent(get_locationList());

} else if (command == okCommand) {

add_location(locationTextBox.getString());

getDisplay().setCurrent(get_locationList());

}

} else if (displayable == cannotAddLocationAlert) {

if (command == backCommand) {

getDisplay().setCurrent(get_locationList());

}

}

}

public String get_forecast() {

if (wxlocation == null) {

return "unknown forecast";

} else {

return wxlocation.getForecast();

}

}

public String get_location() {

if (wxlocation == null) {

return "unknown";

} else {

return

wxlocation.getLocation();

}

}

public void set_location( String l ) {

try {

wxlocation = locationStore.getLocation(l);

}

catch(Exception e) {}

get_locationTextBox().setString(l);

get_wxForm().setTitle(l);

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻找方向

**513**

public void add_location( String l ) {

wxlocation = new WeatherLocation(l);

try {

locationStore.addLocation( wxlocation );

} catch (Exception e) {

getDisplay().setCurrent(get_cannotAddLocationAlert());

}

locationList = null;

}

public Display getDisplay() {

return Display.getDisplay(this);

}

public void exitMIDlet() {

getDisplay().setCurrent(null);

destroyApp(true);

notifyDestroyed();

}

public StringItem get_wxItem() {

if (wxItem == null) {

wxItem = new StringItem("Forecast", get_forecast());

}

return wxItem;

}

public Form get_wxForm() {

if (wxForm == null) {

wxForm = new Form(get_location(), new Item[] {

get_wxItem()

});

wxForm.addCommand(get_exitCommand());

wxForm.addCommand(get_settingCommand());

wxForm.setCommandListener(this);

}

return wxForm;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**514**

第 17 章 ■ 寻找方向

public Alert get_locatingAlert() {

if (locatingAlert == null) {

locatingAlert = new Alert("Locating", "Finding your location", null, null);

locatingAlert.setTimeout(Alert.FOREVER);

}

return locatingAlert;

}

public TextBox get_locationTextBox() {

if (locationTextBox == null) {

locationTextBox = new TextBox("Add Location", "", 80, 0); locationTextBox.addCommand(get_locateCommand());

locationTextBox.addCommand(get_backCommand());

locationTextBox.addCommand(get_okCommand());

locationTextBox.setCommandListener(this);

}

return locationTextBox;

}

public List get_locationList() {

if (locationList == null) {

String[] locations;

locations = locationStore.getLocationStrings();

locationList = new List("Where", List.IMPLICIT, locations, null); locationList.addCommand(get_screenCommand());

locationList.addCommand(get_backCommand());

locationList.addCommand(get_locateCommand());

locationList.setCommandListener(this);

}

return locationList;

}

public Alert get_cannotAddLocationAlert() {

if (cannotAddLocationAlert == null)

{

cannotAddLocationAlert = new Alert("Cannot Add Location"); cannotAddLocationAlert.setString("An error occurred adding➥

the location you entered. It has not been added."); cannotAddLocationAlert.addCommand(get_backCommand());

}

return cannotAddLocationAlert;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻找方向

**515**

public Command get_settingCommand() {

if (settingCommand == null) {

settingCommand = new Command("Settings", Command.OK, 1);

}

return settingCommand;

}

public Command get_okCommand() {

if (okCommand == null) {

okCommand = new Command("OK", Command.OK, 1);

}

return okCommand;

}

public Command get_locateCommand() {

if (locateCommand == null) {

locateCommand = new Command("Use this location", Command.ITEM, 1);

}

return locateCommand;

}

public Command get_exitCommand() {

if (exitCommand == null) {

exitCommand = new Command("Exit", Command.EXIT, 1);

}

return exitCommand;

}

public Command get_screenCommand() {

if (screenCommand == null) {

screenCommand = new Command("Add Location", Command.SCREEN, 1);

}

return screenCommand;

}

public Command get_backCommand() {

if (backCommand == null) {

backCommand = new Command("Back", Command.BACK, 1);

}

return backCommand;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

**516**

第 17 章 ■ 寻找方向

private class LocationUpdater implements Runnable {

String location;

public LocationUpdater(String l) {

location = l;

}



public void run() {

add_location(location);

set_location(location);

getDisplay().setCurrent(get_locationTextBox());

}

}

public void run() {

Criteria criteria = new Criteria();

LocationProvider lp;

StringBuffer cl = new StringBuffer();

String location;

criteria.setCostAllowed(true);

criteria.setAddressInfoRequired(true);

criteria.setHorizontalAccuracy(100);

criteria.setVerticalAccuracy(100);

try {

lp = LocationProvider.getInstance(criteria);

Location l = lp.getLocation(60);

if ( l != null && l.isValid() ) {

AddressInfo ai = l.getAddressInfo();

QualifiedCoordinates c = l.getQualifiedCoordinates();

if ( ai != null ) {

cl.append(ai.getField(AddressInfo.CITY));

cl.append(", ");

cl.append(ai.getField(AddressInfo.STATE));

cl.append(", ");

cl.append(ai.getField(AddressInfo.COUNTRY));

} else {

int r = QualifiedCoordinates.DD_MM;

String s;

cl.append("GPS 坐标, ");

s = QualifiedCoordinates.convert(c.getLatitude(), r);

cl.append(s);

cl.append(", ");

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻找方向

**517**

s = QualifiedCoordinates.convert(c.getLongitude(), r);

cl.append(s);

}

}

} catch(Exception e) {};

if ( cl.length() > 0 ) {

location = cl.toString();

} else {

location = "无法确定位置。";

}

getDisplay().callSerially(new LocationUpdater(location));

}

}

核心 UI 实现保持不变，但为了清晰起见，我做了一些改动。

最明显的改动是，我将`Location`、`LocationStore`和`LocationParser`类分别重命名为`WeatherLocation`、`WeatherLocationStore`和`WeatherLocationParser`。

这并非绝对必要，因为我可以通过在`WeatherWidget`类及其他地方使用包限定符，来解决本书中一直将`Location`用作类名与 Location API 的`Location`类之间的命名冲突，但这样做可能会引起混淆。

为了支持定位功能，我添加了`locatingAlert`，以便在 MIDlet 使用 Location API 时提供状态信息。我还在`locationList`和`locationTextBox`屏幕中添加了`Command`实例`locateCommand`。采用与 NetBeans 相同的 UI 代码组织方案，我为这些项目添加了延迟构造器-获取器方法。我本可以直接使用 NetBeans GUI 构建器添加它们，但像这样的小改动，手动编辑代码通常更快。

■**注意**

是依赖 NetBeans GUI 构建器，还是手动构建 GUI（全部或部分），这是一个艰难的决定。我发现使用 NetBeans 布局 GUI 可以快速原型化应用程序的流程，尽管有时手动进行小调整更快。在本书中，我选择在初始示例中使用 GUI 构建器，然后手动编辑这些示例，以尽量减少添加到项目中的额外代码量（无论是通过我还是 NetBeans 本身）。这样做可能存在风险，因为 NetBeans 代码生成器不喜欢你更改其自动生成的代码。在实践中，最好要么完全依赖代码生成工具，要么完全手动构建代码，不要混合使用这两种方法。

为了向你展示 MIDlet 通过 Location API 确定的位置，我稍微调整了`set_location`方法的实现。除了更新 MIDlet 的`wxlocation`字段外，`set_location`方法还将`locationTextBox`的内容设置为 MIDlet 通过 API 确定的位置。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**518**

第 17 章 ■ 寻找方向

`WeatherWidget`使用一个单独的线程来获取你的位置，尽管该线程的代码位于`WeatherWidget`类本身内部，而不是一个单独的类。当你从位置列表或位置输入屏幕中选择“使用此位置”时，`determineLocation`方法会创建并启动这个线程。当该线程执行完毕时，它会在主线程上调度一个`Runnable`任务，将新位置添加到位置列表中，并设置 MIDlet 显示`locationTextBox`，以便你查看位置请求的结果。



**定位用户**

该 MIDlet 使用一次性定位请求来确定您的位置；当您调用`determineLocation`时，定位线程会通过生成一个新线程来执行清单 17-2 中的`run`方法，从而完成这项工作。这段代码是您在清单 17-1 中看到的伪代码的扩展，仅存在几个重要差异。

应用程序为定位请求设置了特定标准，表明允许定位请求产生费用，请求应附带`AddressInfo`信息，并且允许设置相对较高的误差范围（100 米）。`run`方法验证生成的`Location`实例，如果可用，则利用生成的`AddressInfo`结构为`WeatherLocation`结构提供真实的城市、州和国家信息，就像您直接输入这些信息一样。然而，在大多数情况下——尤其是在 Sun Java Wireless Toolkit 中——您只会收到一个`QualifiedCoordinates`实例，代码便直接使用该实例，构建一个由标签`GPS Coordinates`后跟经纬度组成的字符串。这无疑是一种权宜之计，但也是合理的：它利用了 MIDlet 中其他类的现有实现，使我能够专注于向您展示 Location API，而不是如何重构其他类以同时处理坐标数据及其人类可读的对应形式。

一旦`run`方法构建了包含位置数据的字符串，它会使用一个独立的`Runnable`在主线程上更新 UI。这并非严格必要，因为`locatingAlert`会遮挡`locationTextBox`的输入，因此两个线程之间不可能发生冲突。然而，这向您演示了如何在主 UI 线程上调度一个操作。私有内部类`LocationUpdater`实际执行更新 UI 所需的工作，它本可以是一个匿名类，因为仅在此处使用。其构造函数接收新位置，而`run`方法则将新位置添加到位置列表中，将当前位置设置为新位置，并将显示从`locationAlert`切换到`locationTextBox`，后者会显示已确定的位置。

**在 Sun Java Wireless Toolkit 中模拟 Location API 数据** Sun Java Wireless Toolkit 支持在模拟环境中使用 Location API；您可以指定一个固定位置，或指定一个模拟器在其生命周期内将使用的位置脚本。

图 17-1 显示了配置屏幕（从模拟器主菜单中选择 MIDlet ➤ External Events，然后选择 Location 选项卡）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00040.png)

第 17 章 ■ 寻路

**519**

**图 17-1.** *模拟设备位置*

从图中可以看出，您不仅可以模拟设备的位置，还可以模拟其方向、海拔、速度和航向。您还可以通过更改 Location Provider 组合框来模拟定位失败，从而以可预测且可重复的方式测试您的应用程序。

虽然手动指定位置是测试使用一次性定位的简单应用程序的好方法，但在测试更复杂的应用程序（尤其是那些使用多次定位请求或接近检测的应用程序）时，您需要随时间变化模拟位置。您可以使用包含以下两个 XML 标签的简单 XML 脚本来实现这一点：

• `waypoints`：此顶级标签包含一个单独的航点项列表，每个项代表一个独立的位置。
• `waypoint`：此标签使用`time`（毫秒）、`latitude`、`longitude`和`altitude`（米）属性包含一个特定的位置和时间戳。

清单 17-3 显示了一个简单的示例。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**520**

第 17 章 ■ 寻路

**清单 17-3.** *在 Sun Java Wireless Toolkit 中模拟运动的简单脚本*

<?xml version="1.0"?>

<waypoints>

<waypoint time="0"

latitude="37.87" longitude="-122.22" altitude="10" />

<waypoint time="50000"

latitude="38.0" longitude="-122.30" altitude="12" />

</waypoints>

通过单击 Browse…按钮并选择包含`waypoint`元素的脚本，您就可以使用 Location 选项卡中 Location 窗格底部的回放控件，在 Sun Java Wireless Toolkit 中回放位置数据。

■**提示** 需要大量测试数据？带有 USB 或串口的 GPS 接收器价格低廉，而且几乎所有接收器都按照美国国家海洋电子协会（NMEA）规定的标准格式输出数据。使用一个接收器，您可以徒步、驾车或骑自行车在城镇中穿行，并在笔记本电脑上捕获位置信息，然后将数据转换为一系列航点条目，用于测试您的应用程序。NMEA 使用逗号分隔的纯文本格式；将文件转换为 Sun Java Wireless Toolkit 可用的格式，可以简单到编写一个快速的 Emacs 宏或一个小的 Java 命令行工具。

**总结**

可选的 Location API 为当今设备使用的众多定位技术提供了一个通用封装。`javax.microedition.location`包中包含一个`LocationProvider`类，它允许您一次性或按时间序列获取设备的位置；您还可以使用它在设备接近某个位置时获取事件。

`LocationProvider`类是`LocationProvider`和`Location`实例的工厂，也是`locationUpdated`、`proximityEvent`、`providerStateChanged`和`monitoringStateChanged`事件的通知器。要使用该接口，您需要通过其静态方法`getInstance`获取一个`LocationProvider`实例，并传入一个描述您对所需位置信息要求的`Criteria`对象。然后，您可以向`LocationProvider`实例注册`LocationListener`或`ProximityListener`实现，以接收连续的位置信息或接近事件，或者根据需要简单地通过调用其`getLocation`方法来确定设备的位置。当然，所有这些都应在单独的线程上完成，以避免阻塞 UI 线程。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 17 章 ■ 寻路

**521**

该 API 将位置数据表示为`Location`类的实例，这些实例包含表示坐标、街道地址、时间戳、航向、速度以及定位方式的字段。为了标准化对这些数据的访问，该 API 还包含了诸如`AddressInfo`和`QualifiedCoordinates`等类，它们分别表示街道地址和带有相关误差圆的位置。

该 API 还允许不同的应用程序（可能包括设备上的嵌入式应用程序）通过一个或多个地标存储库相互共享位置数据。您通过`LandmarkStore`类访问地标存储库，该类提供了用于在持久文件系统中创建和删除存储库以及访问特定存储库的静态方法。一旦创建了`LandmarkStore`实例，您就可以使用它来枚举存储库的内容，获取表示特定`Location`实例和用户提供数据（如实例的名称、描述和类别）的单个`Landmark`对象。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章

寻求通用

平台



**Java ME 平台的分裂化**无疑是其最薄弱的环节。正如你在本部分各章节中所见，许多可选 API 使你能够为该平台创建引人入胜的应用程序。不幸的是，通常很难确定哪些设备支持这些 API 中的哪些，并且如果你在应用程序中使用了多个可选 API，你将需要管理因市场上目标设备可能支持或不支持的不同可选 API 数量而导致的组合爆炸问题。这种碎片化是 JCP 的一个不良副作用；当供应商试图扩展 Java ME 以支持其设备功能时，他们会向 Java ME 平台提出新的扩展。JCP 在确保不同供应商差异化产品时，新功能可通过通用 API 获得方面做得相当不错。然而，它却因存在太多可供选择的可选 API 而备受困扰。许多活跃在 JCP 中的组织都认识到了这个问题，并提出了几个 JSR 来解决它。

在本章中，我将更详细地讨论 Java ME 的碎片化问题，帮助你理解其发生的原因，以及你在开发工作中可以如何规划以应对它。我将进一步讨论典型 JSR 的实际内容，因为要成为一名真正熟练的 Java ME 开发者，你需要能够理解 JSR 提供了什么以及没有提供什么。然后，我将详细审视当前解决 Java ME 碎片化问题的三个关键 JSR：JSR 185，它定义了无线行业 Java 技术（JTWI）；JSR 248，它定义了移动服务架构（MSA）1.0；以及 JSR 249，它定义了 MSA2。

**理解 JSR 在碎片化中扮演的角色**

正如你在第 1 章中所见，Java ME 作为小型计算设备平台的根源，驱使其创建者采取了一种极简的、最小公分母的方法，即基础平台只包含很少的功能。通过向平台添加新模块，Sun 和其他供应商能够以可预测且一致的方式提供额外功能。配置、简表和包的引入，使得 Sun 能够提供满足特定类别设备能力的 Java ME 版本，并让开发者能够针对这些设备进行开发。

**导致碎片化与统一化**

Sun 将 Java ME 授权给一个由平台和设备供应商组成的开放市场，其中许多供应商希望提供额外的软件或硬件功能来使其产品差异化。JCP 通过让相互竞争的供应商共同协作来描述 Java ME（以及整个 Java）的扩展来解决这个问题。例如，在 FCOP 之前，美国几家运营商的一些手机拥有基于 Java SE 的专有文件系统访问接口。虽然这些接口在架构上没有问题，但一家运营商支持来自多个供应商、使用不同接口访问文件系统文件的手机这一事实本身就是一个大问题：如果你想在该运营商上通过一个需要文件系统访问的应用程序获得广泛的市场份额，你需要编写并分发多个版本的应用程序。FCOP 通过提供一个所有平台供应商都提供的单一 API 解决了这个问题。现在，你只需使用 FCOP 提供的 API 编写代码，即可在任何提供文件系统访问的设备上运行。

这种解决方案对于单一的 API 集（如 FCOP 或 MMAPI）效果很好，但在处理不同设备供应商提供不同可选 API 集这一事实时则显得不足。如果你正在编写一个只需要一两个可选 API 的单一应用程序，这可能对你来说不是一个大问题，但日益复杂的移动应用程序需要支持不止一两个可选 API。这些应用程序常常面临无法获得足以支持其开发的市场份额的风险，原因很简单，因为对于平台供应商在众多现有可选 API 中*实际*会支持哪些，缺乏明确性。

解决这个问题的一个明显方法是将 Sun 用于描述 Java ME 版本的配置/平台/包模型应用于可选包，从而定义新的配置和平台。本质上，这正是 JCP 成员通过其他 JSR 所做的事情。一些 JSR——例如我在本章中讨论的 JSR 185、JSR 248 和 JSR 249——并不描述可选 API；相反，它们描述的是可选 API 的集合，并在必要时对特定可选 API 的可接受行为提供额外说明。本质上，这些 JSR 试图通过规定一组可选 API 是应用特定名称或标签所必需的，来统一平台。例如，正如你将在下一节“理解 JTWI”中看到的，一个符合 JSR 185 描述的 JTWI 设备必须包含 CLDC 1.0、MIDP 2.1 和 WMA 1.1；它也可以包含 MMAPI 1.1。设备供应商可以——也应该——选择符合 JTWI 标准，以使他们的产品对市场更具吸引力，而运营商可以要求设备在允许其接入网络之前符合 JTWI 标准。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 18 章 ■ 寻求通用平台**

**525**

诚然，这种解决方案也遭受着它试图治愈的同样问题：没有任何个人或组织能够真正命令供应商必须支持哪些 JSR。因此，这些统一化的尝试本身并不完美；它们依赖于市场力量来取得成功。然而，这些 JSR 背后的专家组通常包括主要的制造商和运营商——包括诺基亚、沃达丰、摩托罗拉、Orange 和 Sun 等公司——这为这些遏制平台碎片化的努力提供了可信度和动力。

**阅读 JSR**

既然你已经读到了这本书的这个部分，我要告诉你一个小秘密：我在本书中告诉你的很多内容都可以在记录你所读到的 API 的 JSR 中找到。¹ 事实上，Java ME 变化如此之快，以至于在实践中，熟悉定义平台的 JSR 比仅仅读一两本关于 Java ME 的书对你更有利。（当然，实际编写平台代码的经验也是无可替代的！）这样，当供应商为新兴技术引入新 API 时，你将准备好学习这些 API 以及如何在你的应用程序中使用它们。

广义上讲，大多数 JSR 都包含以下内容（通常使用这些标题）：

• *引言*：说明 JSR 的目的。这个引言通常会告诉你 JSR 的商业案例；对于可选 API，它会告诉你起草该 JSR 的专家组为何认为 Java 开发者社区需要这个可选 API。

• *贡献者*：列出创建该 JSR 的公司（通常还有个人）。

• *术语表*：定义 JSR 使用的领域特定术语，并且通常会重述*必须*、*不得*、*应该*、*不应*和*可以*的定义，如请求评论（RFC）2119“用于 RFC 中指示需求级别的关键词”所述。

• *包*：描述 JSR 引入 Java 环境的包。

• *API*：描述 JSR 引入的包中的类和接口。



• *示例*：展示如何使用 JSR 引入的 API。如果幸运的话，JSR 会包含示例。

1.  话虽如此，我仍努力让你在应用 Java ME API 时获得实践经验，同时也让内容比阿塔卡马沙漠少一些枯燥。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**526**

第 18 章 ■ 寻求通用平台

许多 JSR 实际上都包含由 Javadoc 生成的 HTML 格式的完整包文档；有些 JSR，例如 JSR 135，则以这种形式提供*所有*信息。因此，要成功使用 JSR 中的信息，你既需要具备关于 JSR 试图解决的问题的领域经验，也需要清晰理解 JSR 所定义的包以及这些包中包含的类。

JSR 本身具有清晰的生命周期，包括以下阶段：**1.** *草案*：JSR 通常会经历多个早期草案，其中许多由提出 JSR 的专家组进行内部审查。在此阶段，你可能甚至不知道 JSR 的存在；即使知道，也必须认识到其内容几乎肯定会在未来某个时刻发生变化。

**2.** *公开审查*：在此期间，任何人都可以查看或评论 JSR，只需将意见提交给负责该 JSR 的专家组。此阶段在 JCP 成员进行的批准投票中结束；如果文档未通过批准投票，专家组可以提交修订版以回应社区的关切。一旦 JSR 通过公开审查阶段，除维护外，其未来发生变化的可能性很小。

**3.** *维护*：一旦 JSR 被社区接受，提供该 JSR 的专家组可以对其进行小幅修改，以修正需要更正的项目。

**4.** *休眠*：不再有专家组支持的 JSR 被称为休眠状态。休眠的 JSR 很可能描述的是在社区中未获得广泛支持的 API，或者已被新倡议明确取代的 API。

了解 JSR 处于其生命周期的哪个阶段，可以帮助你确定在产品规划中是否可以依赖其提供的 API。例如，将产品建立在处于早期草案阶段的 JSR 所描述的 API 上可能存在风险。在将产品建立在休眠 JSR 之上之前，最好先了解该 JSR 为何休眠。

随着时间的推移，我总结出了一种方法，使我能够相当快速地学习和处理 JSR 中的信息。诀窍不仅在于查看 API，还在于尝试获取关于 API 设计者试图解决的问题的领域经验。例如，在查看 JSR 177 中的 SATSA-CRYPTO API 时，我花了一些时间研究智能卡行业中的各种技术，尽管我正在进行的项目不太可能使用特定供应商的产品。我还花时间研究了其他社区开发的、实现相同目标的其他 API；观察不同的人如何解决相同的资源访问抽象问题，可以揭示 JSR 实际文档化实现中所做的决策。始终要记住，在某种程度上，JSR 定义的 API 实际上只是对某个物理系统的抽象；人们很容易过于沉浸在类和对象模型中，以至于忘记了现实世界的基础。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章 ■ 寻求通用平台

**527**

另一个有助于理解 JSR 的方法是，为 JSR 包提供的接口和类绘制类图——即使 JSR 作者已经贴心地提供了图表。如果你能通读一组类和方法的描述，并绘制出这些类和方法之间关系的图表，那么你就离真正理解它们如何协同工作不远了。

然而，有必要提出一个警告。JSR 是用英语编写的，这是一种充满细微差别和不确定性的自然语言。与任何软件系统规范一样，使用自然语言时很难消除歧义；更糟糕的是，JSR 通常旨在描述多个实际实现，并且往往不够规范，无法涵盖所有可能的歧义。描述 MMAPI 的 JSR 135 就是一个痛苦的例证；你只需考虑我在第 16 章“捕获媒体”部分中提出的众多注意事项，就会意识到 JSR 并非全部真相。

有些 JSR 实际上试图解决其他 JSR 的缺陷；描述 MSA 的两个 JSR 就是 JSR 试图修补先前 JSR 缺陷的例子。

Sun（偶尔也包括其他公司）为关键包（包括 MMAPI）向供应商提供参考实现（RI）。RI 通常是某个或多个 JSR 中包的实现，并最终被纳入 Sun Java Wireless Toolkit 和相应的 NetBeans Mobility Pack 中；Sun 也可能将 RI 提供给供应商，作为其实现的基础起点。此外，Sun 还为许多这些关键的可选 API 提供技术兼容性套件（TCK）；供应商可以使用 TCK 的测试来确定*他们的*实现与 RI 的匹配程度。当然，所有这些都假设 RI 是完美 JSR 的完美实现，且不存在任何歧义（无论是在 RI 实现者*还是*你的眼中！）——这在当今的软件世界中是一种相当不可能的事件组合。

当然，正如我在本书中一直敦促你的那样，解决方案是对你使用的任何 API 进行实现和测试你的假设。虽然 API 或工具损坏或不存在的可能性极低，但 API 相关文档的某些元素完全有可能不够清晰（无论对你还是对基于该文档进行实现的人来说都是如此）。如果你正在学习一个新的 API，构建一个使用该 API 关键部分的应用程序原型，是巩固你对 API 的理解并确定其是否足够可靠的好方法。此外，你可以在不同的设备目标上使用同一个原型，以探索设备对 API 实现的任何细微差别。与大多数事情一样，经验确实是无可替代的。

**自行处理碎片化问题**

你可能会发现自己处于一种尴尬的境地，即必须使用没有对应 JSR 的 API 来开发应用程序；更糟糕的是，不同平台上的 API 可能不同。这种情况可能发生，尤其是在与设备制造商密切合作，或依赖社区尚未达成共识的前沿特性时。在这种情况下，

[www.it-ebooks.info](http://www.it-ebooks.info/)

**528**

第 18 章 ■ 寻求通用平台

你可以做的一件事是提供一个隔离层来抽象非标准 API，并针对该隔离层进行实现。这让你可以替换符合特定 API 实现的隔离层实现，从而在将代码从一个非标准设备移植到另一个设备时，最大限度地减少应用程序中针对非标准 API 的更改。

如果接口仅由逻辑组成，你也可以自己制作一个参考实现；例如，考虑如何在那些不支持通过 JSR 172 进行 XML 解析的设备上使用 kXML 解析器。当然，你也可以这样做来弥补那些未提供特定 API 实现的设备的不足。这种方法在使用 Sun Java Wireless Toolkit 时也很有帮助，因为如果没有仿真中缺失的可选 API，你可能无法运行应用程序，从而使开发和调试变得更加困难。



您还应考虑加入 JCP 并亲自参与标准制定过程，成为解决方案的一部分。

**理解 JTWI**

JTWI 由 JSR 185 定义，是首批尝试统一 Java ME 设备（特别是手机）可选 API 的规范之一。该规范于 2003 年最终确定，实际上仅要求在当时大多数支持 Java 的手机所包含的 CLDC/MIDP 堆栈基础上增加极少内容。然而，它设定了一些针对手机 Java ME 实现的明确目标，因此成为关键里程碑。

在硬件方面，JTWI 提出了一些强烈建议，主要包括：

• *彩色屏幕*：设备必须拥有至少 125×125 像素的屏幕，像素宽高比为 1:1。屏幕必须为彩色，色彩深度至少 12 位。

• *堆内存*：设备必须为 Java 虚拟机提供至少 256KB 的堆内存。

因此，虽然你*可能*会遇到屏幕更小或堆内存更少的 JTWI 设备，但这种可能性极低。

JTWI 设备必须满足一些严格规范，包括：

• *JAR 文件大小*：AMS 必须能够支持至少 64KB 的 JAR 文件。

• *JAD 文件大小*：AMS 必须能够支持至少 5KB 的 JAD 文件。

• *记录存储*：记录存储实现必须能够存储至少 30KB 数据。

• *线程*：Java 虚拟机必须允许 MIDlet 套件创建至少 10 个同时运行的线程。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章 ■ 寻求通用平台

**529**

• *JPEG 支持*：Image 类必须能够支持 JPEG 图像以及 MIDP 2.0 要求的 PNG 图像。

• *GSM 短信*：WMA 实现必须支持 GSM 短信，包括通过推送注册表将短信传递给 MIDlet 的能力。

• *HTTP 1.1*：每个设备必须为所有支持的媒体类型提供 HTTP 1.1，这是 MIDP 2.0 的要求。

此外，JTWI 要求 CLDC 1.0 或 1.1、MIDP 2.0 和 WMA 1.1。厂商也可包含 MMAPI 1.1。

**审视 JTWI 必需元素**

为覆盖最低端设备（从而涵盖大量目标设备），JTWI 要求的包相对较少。但为确保这些设备间的兼容性，JTWI 要求以下元素：

• *CLDC*：JTWI 要求 JSR 30 定义的 CLDC 1.0。厂商可用 JSR 139 定义的 CLDC 1.1 替代，因为 CLDC 1.1 是 CLDC 1.0 的严格超集。CLDC（1.0 或 1.1）为所有应用程序提供了稳健的核心基础（见第 2 章）。

• *MIDP*：JTWI 要求 JSR 30 定义的 MIDP 2.0。这提供了许多应用程序所需的 UI、持久存储和通信层（见第 2、3、4、5 章）。

• *WMA*：JTWI 要求 JSR 120 定义的 WMA 1.1（包括短信），使应用程序能够利用 JTWI 兼容设备运行的无线网络（见第 14 章）。

正如我在上一节所述，JTWI 为这些 JSR 中的每一个提供了澄清说明。好奇者（或过度严谨者）应参阅 JSR 185 了解详情。

**审视 JTWI 可选元素**

在编写 JSR 185 时，多媒体接口刚刚开始出现在一些中高端手机上。为鼓励采用 MMAPI（我在第 16 章中描述过），JTWI 要求：如果设备通过 Java ME 支持多媒体访问，则必须通过 MMAPI 1.1 提供该功能。如果 JTWI 设备上存在 MMAPI 实现，则该设备必须支持以下特性：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**530**

第 18 章 ■ 寻求通用平台

• *HTTP*：设备必须支持所有支持媒体格式的 HTTP 1.1 下载（*非流式*）。这是因为 MMAPI 本身不强制任何协议，也不指定哪些媒体类型需要特定协议。

• *MIDI*：设备必须支持 MIDI 文件播放。这是因为许多游戏通过合适的音频得到增强；MIDI 提供了音频的紧凑表示。此外，设备还必须支持 MIDI 播放的 VolumeControl。

• *音调序列*：设备必须支持音调序列，作为为游戏和其他应用提供音频的额外途径。

• *快照格式*：如果 MMAPI 实现支持视频功能集和视频图像捕获，则快照格式必须包含 JPEG 编码作为编码选项。

令人沮丧的是，JTWI 对 MMAPI 有很多*未提及*之处，例如是否应支持流式传输以及 WAV 或 MP3 等数字音频格式。

然而，通过将门槛设得如此之低，它涵盖了许多商用设备，并帮助 Java ME 开发者社区标准化了无线游戏多媒体开发的许多方面。

**理解 MSA**

JTWI 是统一手机市场 Java ME API 的极佳第一步。然而，它已严重过时；移动设备硬件和软件开发的快速步伐决定了，当 JTWI 被广泛采用和引用时，它已无法解决因 FCOP、额外 MMAPI 和 SVG 支持、蓝牙和 Mobile 3D Graphics API（本书中我甚至未详细讨论过）等附加功能带来的进一步碎片化问题。

因此，Java ME 社区制定了 JSR 248 和 249 来定义 MSA——一种比 JTWI 定义的平台架构更广泛的平台架构，但仍针对高销量设备。此外，虽然 JTWI 主要反映了其通过时 Java ME 设备市场的状态，但 MSA 旨在不仅描述某些设备上已有的功能，还描述专家预计在可预见的未来将普及的功能。

MSA 已有两个版本：JSR 248 定义的 MSA 1.0，以及 JSR 249 定义的 MSA2。MSA 1.0 已获 JCP 完全接受；在我撰写本章时，JSR 248 的维护版本已发布。相比之下，JSR 249 还是个雏形；在其前身 JSR 248 获批时，JSR 249 仍处于私人草案状态，甚至未向整个社区公开。然而，两者都值得讨论，因为尚不清楚市场最终会采用哪一个，而且两者都为讨论 Java ME 平台的近期未来提供了优秀框架。

与之前的 JTWI 类似，MSA 定义了符合设备必须支持的 API 集合，并针对许多 API 的歧义性提供了具体澄清。MSA 1.0 和 MSA2 共同定义了五种不同的可选 Java ME API 组合，适用于使用当前或近期可用技术和 API 的大众市场手机。

**理解 MSA 1.0**

制定 MSA 1.0 的努力始于 JTWI 完成后约两年，从它要求符合性所需可选接口的数量和类型可以清楚看出这一点。定义 MSA 1.0 的 JSR 规定了必须满足的强制组件和要求，以及如果满足 JSR 中设定的特定条件则必须满足的*条件强制*要求。



JSR 包含五个关键部分，分别描述了强制性组件 JSR、对组件 JSR 的额外说明、超越 JSR 的额外平台要求、给开发者的各种建议，以及一份详述 Java ME 和 MSA 暂定未来规划的路线图。该规范背后的专家组由 Java ME 市场的主要参与者组成，包括摩托罗拉、诺基亚、RIM（黑莓）、三星、西门子和索尼等硬件制造商，以及 Cingular Wireless（现为 AT&T 移动部门）、NTT docomo、Orange、Sprint、T-Mobile 和沃达丰等网络运营商，当然还有 Sun 公司。该规范定义了两个平台：*MSA* 和 *MSA 子集*。（为避免与 JSR 249 定义的 MSA2 混淆，我将它们称为 MSA 1.0 和 MSA 子集 1.0。）顾名思义，MSA 1.0 是一个完整的软件栈，而 MSA 子集 1.0 则为了适应成本更低、性能更弱的设备，删除了几个可选的软件包。为了满足这两个平台中任何一个的要求，符合规范的实现必须实现或遵守以下内容：

• *JSR*：必须实现 MSA 1.0 或 MSA 子集 1.0 指定为该平台必需组件的每一个 JSR。
• *对 JSR 的额外说明*：必须遵守 JSR 相关“额外说明”部分中详述的任何 MSA 1.0 或 MSA 子集 1.0 的额外要求。
• *额外要求*：必须遵守 JSR 248 中列出的所有额外 MSA 要求。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**532**

第 18 章 ■ 寻求通用平台

一些要求对 MSA 1.0 和 MSA 子集 1.0 是通用的，包括以下类别的要求：

• *JTWI*：符合 MSA 规范的实现必须满足 JTWI 的要求。
• *屏幕*：符合 MSA 规范的实现应配备至少 128✕128 像素、16 位色深、支持 65,536 种颜色的屏幕。（你可能会遇到不符合此要求的设备，但这不太可能。）
• *堆内存*：MIDlet 可用的最小 Java 堆内存应至少为 1024KB；MSA 建议堆内存至少为 2048KB。
• *JAD 和 JAR 文件限制*：支持的最小 MIDlet JAR 大小为 300KB。支持的最小 MIDlet 套件 JAD 文件大小为 10KB。平台必须支持最多包含 512 个属性的 JAD 清单。
• *记录存储*：每个 MIDlet 套件支持的最小记录存储大小为 64KB，并且每个 MIDlet 套件最多可使用 10 个独立的记录存储。
• *安全性*：MSA 定义了需要权限的可选包 API 与相关权限名称之间的明确对应关系。
• *系统属性*：MSA 定义并提供了系统属性的通用参考，用于确定特定 API（例如 FCOP）的可用性和版本。
• *网络协议*：HTTP、基于安全套接字层 (SSL) 3.0 的 HTTP、TCP、SMS 和 MMS 是 MSA 实现必须提供的强制性协议。

此外，MSA 1.0（而非 MSA 子集 1.0）必须提供对会话发起协议 (SIP) 的支持。其他协议，如基于 TLS 的 HTTP、TCP 服务器支持、TLS 1.0 和 UDP，则是可选的。一些协议，包括蓝牙和红外数据协会 (IrDA) 的多个配置文件，是有条件强制性的。

• *内容格式*：MSA 要求支持多种常见内容格式，包括 PNG、JPEG、Mobile 3D Graphics API、SVG Tiny 1.1、8kHz 脉冲编码调制 (PCM) 格式的 WAV、自适应多速率窄带 (AMR-NB) 音频压缩、MIDI 和可扩展和弦 MIDI (SP-MIDI) 音频。

审视 MSA 子集 1.0

要被视为符合 MSA 子集 1.0 规范，产品必须提供以下包：

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章 ■ 寻求通用平台

**533**

• *CLDC 1.1 和 MIDP 2.1*：提供 JSR 139 和 JSR 118 中规定的基础平台。



• *FCOP 和 PIM*：提供对本地文件系统和个人信息管理器的访问。FCOP 必须提供属性，用于指定相机拍摄的照片和视频、壁纸图像、铃声、音乐和录音机的存储目录的默认 URL，以及每个 MIDlet 的私有存储目录。PIM 必须支持 PIMList 的子类 ContactList、ToDoList 和 EventList，并支持只读、读写和只写模式。ContactList 必须支持传真、家庭、移动、首选和工作号码，以及街道地址、电子邮件、备注、URL 和照片字段。ToDoList 必须包含摘要、优先级、完成日期、截止日期和已完成字段。与 MSA Subset 1.0 的其他部分一样，这些要求同样适用于 MSA 1.0。

• *蓝牙*：如果设备支持蓝牙技术，则必须包含基于 JSR 82 描述的 GCF 的蓝牙 API。通过此 API，应用程序可以使用蓝牙在设备之间交换媒体。

• *MMAPI*：必须包含 MMAPI，以允许开发人员创建引人入胜的多媒体应用程序。实现必须支持与系统相机应用程序相同的从图像传感器捕获图像的分辨率。

• *移动 3D 图形*：移动 3D 图形是游戏、一些丰富的 GUI 和其他图形密集型应用程序的重要元素。符合规范的设备必须提供 JSR 184 指定的接口。

• *WMA*：符合规范的设备必须提供 WMA 2.0，因为消息传递是必须可供 Java ME 开发人员使用的关键手机功能（参见第 14 章）。

• *SVG*：图形仍然是当今和未来移动应用程序的关键组成部分；包含对 JSR 226 的支持可确保应用程序能够显示 SVG 图像。

审视 MSA 1.0

MSA 1.0 在 MSA Subset 1.0 的基础上增加了以下包：

• *J2ME Web 服务*：JSR 172 描述的 J2ME Web 服务规范——包括 XML 解析器和 Web 服务可选包——必须包含在内，以促进支持 Web 服务的标准方式（参见第 13 章）。

• *安全与信任服务*：SATSA 是各种移动应用程序和服务中的重要元素。必须提供 SATSA-CRYPTO，而对 SATSA 和 SATSA-PKI 中的 APDU 接口的支持则是条件性强制要求（参见第 15 章）。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**534**

第 18 章 ■ 寻求通用平台

• *位置*：如果设备具有 GPS 接收器或其他位置定位采集子系统以支持基于位置的应用程序，则必须包含 JSR 179 描述的位置 API。

• *SIP*：SIP 是基于 IP 的电话网络建立和管理语音呼叫处理的首选协议。MSA 1.0 要求符合规范的设备包含一个包，该包通过提供 JSR 180 指定的 SIP 来增强 GCF 支持。

• *内容处理*：JSR 211 定义的内容处理 API 允许一个 Java 应用程序（或 MIDlet）根据其支持的内容类型启动另一个应用程序。

• *高级多媒体*：必须包含高级多媒体补充（AMMS）API（我在第 16 章中简要提及），以允许开发人员使用增强的相机支持、3D 音频、对内部收音机的支持以及卓越的图像编码和处理能力。

• *国际化*：Java ME 的 MIDlet 和应用程序难以国际化；没有好的方法将本地化的应用程序资源与源代码隔离，或在运行时动态识别这些资源。JSR 238 提供了一种实现此目的的标准方法，并且必须包含在内。

**面向未来的演进：MSA2**

JSR 249 定义了 MSA2，这是一组与 MSA 1.0 向后兼容的需求。由与 JSR 248 组成基本相同的专家组编写，MSA2 解决了 JSR 248 开发开始和结束期间 Java ME 市场发生的变化。它定义了三个平台：

• *MSA2 Limited*：面向低端大众市场手机。

• *MSA2 Subset*：面向中端大众市场手机，是 MSA2 Limited 的超集，并包含额外需求。

• *MSA2*：面向高端设备，是 MSA2 Subset 的超集，并包含额外需求。

与 JSR 248 一样，JSR 249 使用强制需求和条件性强制需求的概念来定义这些平台，它不仅指定了每个平台必须支持的一组 JSR，还提供了关于这些 JSR 的额外需求，以及针对整个目标平台的额外需求。

其中一些需求继承自 MSA 1.0 和 JTWI；其他需求则是 MSA2 新增的。硬件需求（例如屏幕和堆容量）与

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章 ■ 寻求通用平台

**535**

MSA 1.0 保持一致；安全需求在原则上保持相似，但已大幅扩展，以包含 MSA2 所需的额外 API 的权限。

■**注意** 在我撰写本文时，JSR 249 仍处于草案形式，可能会发生变化。在依赖我在此提供的材料之前，请访问 JCP 网站上关于此 JSR 的页面 http://jcp.org/en/jsr/detail?id=249 以获取最新副本，并查看是否有任何可能影响您开发计划的更改。

审视 MSA2 Limited

MSA2 Limited 本质上是 MSA Subset 1.0，仅包含最少数量的新增内容，以确保跨多个设备的最低层级具有通用 API。它包括 MSA 1.0 要求的移动国际化 API（JSR 238），以及 JSR 256 定义的移动设备传感器管理通用框架。

移动传感器 API 使您可以访问强制性的硬件传感器（电池和网络接收信号强度），如果设备硬件有的话，还可以访问 3D 加速度计。

审视 MSA2 Subset

MSA2 Subset 建立在 MSA2 Limited 之上；实际上，它是 MSA2 Limited 的超集。它必须提供对以下包的支持：

• *MIDP 3.0*：JSR 271 定义的 MIDP 3.0 是 MSA2 Subset 和 MSA2 的基本组成部分。目前由 JCP 成员开发，它将扩展已有的 MIDP 2.1 API，增加对共享库代码、更好的应用程序生命周期管理、用于 MIDlet 间记录存储交换的安全二进制接口以及增强的用户界面（包括对 MMAPI 的强制支持）的支持。

• *移动广播服务*：当今许多设备可以接收广播内容，例如数字电视。JSR 272 定义了访问服务指南（允许应用程序搜索节目内容数据库）以及使用基于 MMAPI 的 API 渲染广播内容（包括与广播内容相关的字幕或其他元数据）的接口。

• *非接触式通信*：JSR 257（我在第 15 章中提到）描述了执行与 RFID 卡或视觉标签的非接触式通信的方法。如果底层硬件提供这些功能，则需要支持此 API。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**536**

第 18 章 ■ 寻求通用平台

• *移动用户界面定制*：设备个性化——壁纸、铃声和设备主题——是当今许多移动设备的重要功能，也是内容提供商的主要收入来源。JSR 258 定义了一个允许访问个性化数据并为用户提供重新定制 Java ME 应用程序外观的 API；此 API 是强制要求的。

• *XML*：XML 可以说是当今网络的*通用语言*；JSR 280 定义了替代 JSR 172 所定义接口的标准接口。JSR 280 实现了更好的 XML 解析，并包含一个流式 XML 解析器和对 XML DOM 的支持。此 API 是强制要求的，以使应用程序开发人员能够使用 Web 服务。

审视 MSA2



基于 MSA2 子集，MSA2 要求包含对 IP 多媒体子系统（IMS）服务的支持。IMS 使用包括 SIP、Diameter（RADIUS 的超集）、MMS 和 RTSP 在内的标准协议，以实现基于 IP 的实时消息、聊天、一键通（PTT）和多人在线游戏，这些功能正迅速成为全球 3G 无线基础设施的标准组成部分。为了充分利用这些网络上的 IMS 骨干网，下载的应用程序必须能够通过标准 API 访问 IMS 功能；JSR 281 定义了此 API。

JSR 281 以 Java 为中心的方式支持 IMS 的诸多方面，包括定义可选包以支持以下 IMS 功能：

• *推送（Push）*：MIDlet 可以注册以响应传入的 IMS 连接，就像它们可以响应其他消息一样。

• *服务质量（QoS）*：QoS 保证是 IMS 标准的重要组成部分，IMS API 允许在 QoS 保证下进行音频和视频流传输。

• *编解码器（Codecs）*：IMS API 实现必须包含对 AMR-NB（以及 AMR-WB，即 AMR 的宽带版本，如果设备是宽带设备）的支持，并且如果设备支持，还需包含用于流视频的 H.263。

IMS API 建立在 GCF 和 MMAPI 提供的概念框架之上。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 18 章 ■ 寻求通用平台

**537**

**总结**

Java ME 作为平台的强大之处在于其“一次编写，随处运行”的特性；不幸的是，这一值得称赞的目标始终受到 Java 社区开放性的威胁，这种开放性鼓励并拥抱增长，导致了 API 和平台碎片化。Java 社区致力于扩展 Java ME 可用的 API，并通过 JCP 遏制 Java ME 平台的碎片化，JCP 发布 JSR 来记录额外的 API 或 API 组合，以支持新的应用程序和服务。有三个关键的 JSR 正致力于通过定义应尽可能多地包含在设备上的 Java ME API 集合来减少 Java ME 平台的碎片化。

第一个是 JSR 185，它定义了 JTWI，这是一个支持 CLDC、MIDP 和 WMA 的极简移动手机。它也可能通过 MMAPI 支持一些音频多媒体。目前市场上大多数支持 Java ME 的设备都明确符合 JTWI，无论是设备制造商有意为之并验证的目标，还是顺其自然的结果。

另外两个，JSR 248 和 JSR 249，建立在 JTWI 之上，并定义了需要更多 API 的附加平台。这些 JSR 允许你依赖这些必需的 API 的存在，用于文件系统访问、MMAPI、SATSA、Mobile 3D Graphics API、WMA 和 SVG API，以及实现新网络功能（如 SIP 和 IMS）的 API。

[www.it-ebooks.info](http://www.it-ebooks.info/)

附录

查找 Java API

**在**本书中，我重点介绍了特定的必需和可选 Java ME API 以及如何使用它们。在某些情况下，我引导你查阅了记录这些 API 的特定 JSR，而在其他情况下则没有。此外，我只讨论了可选 Java ME API 的一个子集；许多其他可选 API 在某些支持 Java ME 的设备上也是可用的。

关于 Java ME API 的 JSR 数量之多确实令人不知所措，尤其是在你刚开始接触时。不可避免地，会有人问你：“Java ME 设备能做*X*吗？”通常，问这个问题的是重要人物，比如正在设计公司下一代产品的产品经理，而你将在接下来的六个月里致力于开发该产品。准确回答这个问题并不像听起来那么容易，尤其是如果你不立即熟悉*X*的话。你需要查阅与 Java ME 相关的 JSR，找到与*X*相关的 API，并查看它们是否能满足你的需求。这就是本附录的目的：它包含表 A-1，该表将重要的移动技术映射到描述该技术 API 的相关 JSR。有了这些信息，你可以访问 http://www.jcp.org，查找相关的 JSR，并形成你所提问题的答案。

最后一点：JSR 告诉你*如何*用 Java ME 设备做某事。它们不会告诉你是否有任何 Java ME 设备*实际*上以那种方式做。请注意，我并非在抨击 JCP；这是一个伟大的流程。我只是指出围绕 Java ME 的自由市场的一个现实。某些 JSR 描述的 API 仅由少数设备制造商实现，这通常是因为这些设备制造商的客户（无论是网络运营商还是最终消费者）不理解 JSR 中 API 集合的重要性。其他 JSR 包含绝妙的想法，但实现成本过高。还有一些很快被取代或扩展，因此 JSR 只是一个化石，展示了 API 演进过程中某一时刻的快照。出于这些原因，你应该将 JSR 视为回答“Java ME 设备能做*X*吗？”这个问题的第一步。JSR 中的信息让你能够回答：“理论上，可以，”或者“没有标准方式”（因为可能在某些设备上存在执行*X*的专有方式）。为了完整回答这个问题——其真正含义可能是“有多少 Java ME 设备能做*X*？”以及“我们是否有可行的商业案例来构建需要*X*的应用程序？”——你还需要查看你产品所涵盖的时间范围内市场上的设备。这在许多情况下更加困难；你需要考虑你的产品将如何分发，是否仅涉及单一网络运营商的客户，等等。获得这些答案需要付出努力。预计要花时间**539**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**540**

附录 ■ 查找 Java API

搜索互联网，阅读特定设备的产品数据表和开发文档，并仔细查阅关于相关设备的开发者论坛，以便就特定功能或 API 的实际市场可用性提供清晰的答案。

**表 A-1.** *按技术分类的 Java ME JSR*

**技术**

**JSR**

**备注**

3D 图形

184, 239, 297

MSA 既要求又澄清了 JSR 184。

自组网

高级 GUI

应用程序部署

这描述了针对 CDC 设备的安全应用程序部署机制。

条码识别

MSA 既要求又澄清了 JSR 257。

蓝牙

这种基于 GCF 的实现可以提供客户端和服务器端点。

广播

JSR 272 定义的接口基于 MMAPI 和 GCF。MSA 澄清了 JSR 272。

CLDC

30, 139

JWTI 和 MSA 对 CLDC 实现有澄清说明。

CDC

36, 218

非接触式通信

MSA 既要求又澄清了 JSR 257。

内容处理

MSA 既要求又澄清了 JSR 211。

加密

MSA 既要求又澄清了 JSR 177。

设备传感器

MSA 既要求又澄清了 JSR 256。

数字机顶盒

文件系统

MSA 既要求又澄清了 JSR 75。

基础框架

46, 219

游戏

IMS

MSA 既要求又澄清了 JSR 281。

国际化

MSA 既要求又澄清了 JSR 238。

Java 数据库连接

JTWI

位置服务

MSA 既要求又澄清了 JSR 179。

消息

120, 205, 266



JSR 205 引入了 MMS。JTWI

既要求又澄清了 JSR 120。而

MSA 既要求又澄清了 JSR 205。

*续*

[www.it-ebooks.info](http://www.it-ebooks.info/)

附 录 ■ 查找 Java API

**541**

**技术**

**JSR**

**备注**

MIDP

37, 118, 271

JWTI 和 MSA 对 MIDP 的实现有澄清说明。

MMS

205, 266

JSR 205 引入了 MMS。MSA 既要求又澄清了 JSR 205。

移动广播

接口基于 MMAPI 和 GCF。MSA 既要求又澄清了 JSR 272。

MSA

248, 249

多媒体

135, 226, 234, 287

JSR 226 和 287 描述了 SVG 渲染。

支付

PDA 可选包

MSA 既要求又澄清了 JSR 75。

用于 J2ME

个人基础框架

129, 217

个人信息

MSA 既要求又澄清了 JSR 75。

管理器访问

个人框架

62, 216

远程方法调用

RFID

MSA 既要求又澄清了 JSR 257。

安全与信任

MSA 既要求又澄清了 JSR 177。

传感器

MSA 既要求又澄清了 JSR 256。

SMS

120, 205, 266

JWTI 既要求又澄清了 JSR 120。MSA 既要求又澄清了 JSR 205。

SIP

JSR 180 定义了基于 GCF 的类实现，以提供 SIP 支持。

SVG

226, 287

MSA 既要求又澄清了 JSR 226。

远程信息处理

电话

电视

这定义了基于 MMAPI 和 GCF 的接口。MSA 既要求

又澄清了 JSR 272。

主题

MSA 既要求又澄清了 JSR 258。

UI 定制

MSA 既要求又澄清了 JSR 258。

视觉标签识别

MSA 既要求又澄清了 JSR 257。

Web 服务 API

MSA 既要求又澄清了 JSR 172。

无线消息

120, 205, 266

JSR 205 引入了 MMS。JTWI

（SMS, MMS）

既要求又澄清了 JSR 120。MSA 既要求又澄清了 JSR 205。

XML

172, 280

JSR 280 取代了 JSR 172。MSA 既要求

又澄清了 JSR 172 和 JSR 280。

[www.it-ebooks.info](http://www.it-ebooks.info/)

索引

■**数字**

Activatable 类, 278

2D 游戏, 193–218, 540

活动状态

3D 图形

Xlet 的, 224

JSR 和, 540

MIDlet 的, 85

MSA 要求和, 533

自组网, JSR 和, 540

911 紧急系统, 基于位置的服务和, 499

add 方法 (Container 类), 262

addAddress 方法, 383, 384

addBinary 方法, 182

addBoolean 方法, 183

■**符号**

addCategory 方法, 184, 507

< > 表示纯文本 XML 元素,

addCommand 方法, 106, 195

addContact 方法, 189

< /> 表示空元素, 在 XML 中,

addDate 方法, 182

addFileSystemListener 方法, 168

< 小于号, 339

addInt 方法, 182

表示 XML 文档中的注释

addLandmark 方法, 508

addLocation 方法, 154, 160

</ > 表示起始标签, 在 XML 中, 339

addMessagePart 方法, 383

<? ?> 表示 XML 文档的前言, 339

addPlayerListener 方法, 460

> 大于号, 339

addProximityListener 方法, 507

& 与符号, 339

addRecord 方法, 138, 141

/ 斜杠字符, 163, 165

addRecordListener 方法, 145

地址, MMS 消息和, 383

‘ ’ 单引号, 339

AddressInfo 类, 502

addStore 方法, 140

■

addString 方法, 183

**A**

addStringArray 方法, 183

A-GPS (辅助全球定位系统), 499

Adobe

AAC 格式, 448

Illustrator, 471

抽象窗口工具包。*参见* AWT

Shockwave Flash, 470

acceptAndOpen 方法, 303

高级图形和用户界面。*参见* AGUI

访问模式, 297

AES (加密标准), 423

accessor 字段键, 177

AGUI (高级图形和用户界面), 230

ActionEvent 类, 266

使用其开发用户界面, 266–271

ActionListener, 265

局限性, 269–271

actionPerformed 方法, 266

操作, 用于 WeatherApplet 示例应用程序, 61–75

**543**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**544**

■索引

AID (应用程序标识符), 417, 421

安全性, 413–445

alarmFired 方法, 95

签名, 55

警报, 90–96

从应用程序使用 Web 服务的步骤, 341

Alert 类, 99, 114, 116

ARGS 属性, 242

AlertType 枚举, 116

ARPU (每用户平均收入), 3, 4

AlreadyBoundException, 245

辅助全球定位系统 (AGPS), 499



AMS（应用程序管理软件）

非对称密码学，423

JAR/JAD 文件与，89

AsymmetricBlockCipher 接口，429

MIDlet 与，86

原子性，记录存储与，135, 142

动画 SVG 图像，477

每用户平均收入（ARPU），3, 4

动画，205–207

AWT（抽象窗口工具包）

Ant（Apache），36

使用其开发用户界面，

APDU（应用协议数据单元），

260–266

个人基础框架与，14

APDUConnection 接口，418

个人框架与，15

API 权限，54

用户界面模型与，99

append 方法，106, 114, 202

AWT 类层次结构，用于用户界面

appendChild 方法，483

组件，261

<APPLET>，258

azimuthTo 方法，506

Applet 描述符部分，54

applet 模型，14

AppletContext 类，257

■**B**

applet，253–260

BadPaddingException，425

之间的通信，258

条形码。*参见* 视觉标签

生命周期，254

BD-J 栈，231

vs. MIDlet/Xlet，254, 258

Berners-Lee, Tim，306

命名，258

BinaryMessage 接口，376, 379, 381

应用程序标识符（AID），417, 421

位图图像，470

应用程序管理软件（AMS）

分组密码，425

JAR/JAD 文件与，89

BlockCipher 接口，429

MIDlet 与，86

笔记块，468

应用协议数据单元（APDU），

蓝光光盘 Java（BD-J），231

蓝牙

应用程序屏幕，用于用户界面。

JSR 与，540

*参见* 屏幕

MSA 要求与，533

应用程序

–bootpath 选项，52

向其中添加位置信息，509–520

BorderLayout 管理器，262

构建/运行，43–53

Bouncy Castle API，425–431

部署，JSR 与，540

通过其创建的消息摘要，428

分布式面向对象，273

vs. SATSA-CRYPTO API，430

在其中生成 XML，343–355

广播服务

营销/销售你的，17

JSR 与，540

打包/执行，53–56

MSA2 与，535

示例。*参见* 示例应用程序；

构建目录，36

源代码

build.xml 文件，36

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**545**

蝴蝶/猫游戏（示例应用程序），

CDMA 网络，378

207–217

手机。*参见* 移动电话

Button 类，264

单元格，201

按钮，236–240

证书，55, 415

ByteArrayInputStream 类，95, 139, 140,

第三方认证，17

142, 362

changePin 方法，420

ByteArrayOutputStream 类，95, 139, 140

字符实体，339

字符事件，357, 360

子元素，XML 与，336

■**C**

Choice 类，112

日历约会，161, 174

ChoiceGroup 类，112

创建，183

选择

PIMItem 类与，177

显示，120

移除，184

管理，112

Calendar 类，28

Cipher 接口，424

callSerially 方法，396

密码，415

相机

类加载器，242

捕获媒体与，462

CLDC（有限连接设备配置），8, 10

成像传感器与，449, 461, 464

Connection 实现与，299

MMAPI 支持与，464

cancel 方法，318

非接触式通信 API

canRead 方法，166

包与，432

Canvas 类，99, 122, 193, 460, 463

GCF 与，293

vs. CustomItem 类，125

历史与未来，19–22

在 Java ME 与 Java SE 中，122

JSR 与，540

NetBeans IDE 移动包与，483

JTWI 要求与，529

渲染 SVG 图像与，477

MSA 要求与，533

vs. Screen 类，114

CLDC 1.0，22

画布，122–131

CLDC 1.1，19

canWrite 方法，166

CLDC/MIDP 应用程序。*参见* MIDlet

capture 方法，463

close 方法，173, 295

捕获方案，455

关闭状态，312, 453

捕获媒体，461–466, 494

closeRecordStore 方法，137

CardLayout 管理器，262

closeStore 方法，153

大小写敏感性，XML 与，339

码分多址（CDMA）

猫/蝴蝶游戏（示例应用程序），

网络，378

207–217

代码示例。*参见* 源代码

类别，PIM 数据库与，176, 184

代码签名，190

CDC（连接设备配置），

编解码器，448–453

8, 12

Collections API（Java SE），28

AGUI 可选包与，266

collidesWith 方法，207, 217

GUI 工具包与，230

碰撞检测，207, 217

历史，23

Command 类，101

JSR 与，540

commandAction 方法，101, 122, 325,

CDC 应用程序。*参见* Xlet

491, 493

[www.it-ebooks.info](http://www.it-ebooks.info/)

**546**

■索引

CommandListener 类，101, 122

ContactLoaderThread 类（示例），189

commandListener 方法，493

联系人，161, 174, 189



commands, 101, 106, 123

creating, 183

comments

PIMItem 类与, 177–182

NetBeans IDE 与, 50

removing, 184

in XML documents, 339

Container 类, 234, 235, 256, 261

commit 方法, 183

containers, 234, 256

compare 方法, 143

AWT, 261–263

Component 类, 234, 261, 263

multimedia content 与, 448

components, 234, 260–266, 270

content

Concurrent Versions System (CVS), 36

JSRs 与, 540

configurations, 8, 10

multimedia. *参见* multimedia content

configViewSound 方法, 492, 494

remote, 309

configViewSvg 方法, 492, 496

content bounds, 125

configViewVideo 方法, 492, 494

content ID, MMS 消息与, 383

CONNECT 方法, 307

ContentConnection 类, 295, 309–315

Connected Device Configuration, *参见* CDC

vs. HttpConnection 类, 311–315

Connected Limited Device Configuration.

StreamConnection 接口与, 311

*参见* CLDC

ContentDescriptor 类, 451

connected state, HTTP 通信

Control 接口, 450, 451

与, 312

control loop, 195, 197

Connection 类, 296–299, 378

Control 子类, 458, 494

Connection 实现 (表格), 299

controllers, multimedia content 与, 449

Connection 接口, 295

cookies, 335

Connection 方案 (表格), 297

Coordinates 类, 502, 506

Connection 子类, contactless

countermeasures, security 与, 414, 415

connections 与, 432, 435, 440

createAnimatedTile 方法, 205

ConnectionNotFoundException, 165, 295,

createBoard (示例) 方法, 216, 217

createButterflies (示例) 方法, 216,

Connector 类, 162, 295

datagram

communication 与, 304

createCat (示例) 方法, 216, 217

socket communication 与, 300

createContact 方法, 183, 189

Connector.READ 访问模式, 297

createElementNS 方法, 483

Connector.READ_WRITE 访问模式,

createEmptyImage 方法, 482

createEvent 方法, 183

Connector.WRITE 访问模式, 297

createLandmarkStore 方法, 507

consumer-producer 模型, 259

createPlayer 方法, 454

consumers, Java ME 市场视角

createToDo 方法, 183

与, 3, 5

creating

Contact 类, 181

calendar appointments, 183

Contactless Communication API, 431

contacts, 183

contactless communications, 431–444

custom items, 125

contactless targets 与, 432, 435–439

directories, 165

JSRs 与, 540

events, 183

ContactList 类, 189, 533

files, 165

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**547**

lightweight components, 236–240

DataLengthException, 430

message digests, 422, 428

DataOutput 接口, 295, 305

messages, 379

DataOutputStream 类, 95, 139

MIDlets, 37–56

reading files 与, 173

PIM records, 183

sockets 与, 301

to-do items, 183

DataSource 类, 450, 451, 455

user interfaces, 97–131

date and time, 110, 138

WeatherWidget 示例应用程序,

Date 类, 28

38–51

date-book appointments, 161, 174

Xlets, 57–77, 225, 227–233

creating, 183

Criteria 类, 502, 504

PIMItem 类与, 177

cryptographic signatures, 17

removing, 184

cryptography, 414–425

DateField 类, 110

asymmetric, 423

deallocate 方法, 453

JSRs 与, 540

decode 方法, 444

CustomItem 类, 107, 122, 125–131

decrypting messages

CVS (Concurrent Versions System), 36

Bouncy Castle API 与, 429

SATSA-CRYPTO API 与, 423

DefaultHandler 类, 357

■**D**

defense in depth, 415

daemon threads, 27

defineReferencePixel 方法, 206

data communication, 293–329

DEFRecordListener 接口, 433

HTTP communication 与, 306–326

DELETE 方法, 307, 334

permissions 与, 327

delete 方法

socket/datagram communications 与,

Choice 类, 114

300–306

FileConnection 类, 167

Data Encryption Standard (DES), 423

TextField 类, 110

data representation, XML for, 336–341

deleteAll 方法, 114

data services, 4

deleteCategory 方法, 184, 507

data sharing, Xlets 与, 243–251

deleteLandmark 方法, 508

data sources

, multimedia content 与,

deleteLandmarkStore 方法, 507

deleteRecord 方法, 138, 144

database connectivity, JSRs 与, 540

deleteRecordStore 方法, 137

database of landmarks, location-based

DES (Data Encryption Standard), 423

services 与, 501

deserialization, 140, 150

Datagram 类, 304–306

destroy 方法, 254

datagram communication, 300, 304–306

destroyApp 方法, 85, 87, 325, 491

Datagram 接口, 295

destroyed state

DatagramConnection 类, 304

of MIDlets, 85



DatagramConnection 接口，295

Xlet 的，225

DataInput 接口，295，305

destroyXlet 方法，225，233

DataInputStream 类，95，139，140，142，

determineLocation 方法，518

开发规划，16–18

以及打开文件，166

设备制造商，Java ME 市场

以及读取文件，173

视角与，3

[www.it-ebooks.info](http://www.it-ebooks.info/)

**548**

■索引

设备传感器，JSR 与，540

文档类型定义 (DTD)，341

DeviceKeyEvent 类，270

doFinal 方法，425

设备

DOM 解析器，342

代码签名与，190

双缓冲，124

开发规划，16

下载

FCOP 与，162

Bouncy Castle API，426

监听文件系统变更，168

kXML 解析器，365

JTWI 要求/选项与，529

Mobility 包，34

基于位置的服务，499–521

NetBeans IDE，34

MMAPI 补充接口与，

绘图

Canvas 类与，123

MMAPI 系统属性定义

通过 Canvas 类控制，122–125

以及，457

GameCanvas 类与，195–200

MSA 强制要求

Item 类与，106

组件/要求与，

DSP（数字信号处理器），

多媒体内容与，448

个性化，536

DTD（文档类型定义），341

解决碎片化问题，523–537

入站套接字/数据报通信的

动态注册，390

支持的媒体/协议类型，

枚举，457

■**E**

Digest 接口，429

EAN/UPC（欧洲商品编号/

数字信号处理器 (DSP)，

通用产品代码），

多媒体内容与，448

数字签名，415

EclipseME，34，53

目录

Element 接口，474

创建，165

XML 中的空元素，以 < /> 表示，

删除，167

directorySize 方法，166

enablePIN 方法，420

disablePIN 方法，420

encode 方法，444

DiscoveryManager 类

，432，433，440

以及编码 MMS 消息，383

显示画布，122–131

加密消息

Display 类，98，483

Bouncy Castle API 与，429

Displayable 类，99，107

SATSA-CRYPTO API 与，423

GameCanvas 类与，195，199

加密标准 (AES)，423

示例游戏应用与，209

endDocument 事件，357，361

Displayable 接口，85

endElement 事件，357，360

dist 目录，36

enterPIN 方法，420

distance 方法，506

enumerateRecords 方法，142

分布式面向对象应用，

错误处理，无线消息与，

分发考量，17

欧洲商品编号/通用

Document 类，474，483

产品代码 (EAN/UPC)，440

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**549**

事件处理

exportObject 方法，284

Canvas 类与，123

ExtendedDigest 接口，429

GameCanvas 类与，195–200

可扩展性，7，15

Item 类与，106

ExternalResourceHandler，475，482，496

Event 接口，474

EventList 类，533

EventListener，474

■**F**

事件，161，174，264

FCOP（文件连接可选包），

创建，183

161–173

PIMItem 类与，177

判断是否存在，164

PlayerListener 接口与，460

Java ME 碎片化与，524

移除，184

LocationStore 示例类与，

异常

169–173

addFileSystemListener 方法与，

MSA 要求与，533

字段键，PIM 包与，177

addRecord 方法与，141

字段类型，141

创建接口与，165

文件访问，161–191

数据通信与，295

FCOP 与，161–173

deleteRecord 方法与，144

PIM 包与，174–190

deleteRecordStore 方法与，137

文件连接可选包。*参见*

加密/解密消息与，

FCOP

file:/// 协议前缀，163

getNextRecordID 方法与，142

文件系统

getNumRecords 方法与，145

添加/移除，168

getRecord 方法与，142

FCOP 与，161–173

items 方法与，177

JSR 与，540

listRoots 方法与，169

监听变更，168

基于位置的服务与，503

FileConnection 类，162–173

消息摘要，423

集成到 WeatherWidget，173

open 方法与，165，296

vs. RecordStore 类，173

openRecordStore 方法与，136

文件，操作，165–167

PIM 包与，175

fileSize 方法，166

removeFileSystemListener 方法与，

FileSystemRegistry 类，168，169

finalize 方法，26

setRecord 方法与，144

平面字符数据，XML 与，339

verifyPIMSupport 方法与，189

浮点数学，CLDC 与，



可视化标签，444

12, 26

exchangeAPDU 方法，419

Flow Designer，38–50

exchangeData 方法，436

FlowLayout 管理器，262

执行

焦点事件，126

MIDlet，53–56

FocusListener 接口，265

Xlet，75

Form 类，99

exit 方法，27

通过其收集可见项，114

exitMIDlet 方法，95, 325

向其中添加项目，104

[www.it-ebooks.info](http://www.it-ebooks.info/)

**550**

■索引

FP（基础框架），12, 14, 231

getAttributeValue 方法，371

AGUI 可选包与，266

getAudioClip 方法，257

JSR 与，540

getBinary 方法，179

碎片化，Java ME 与，523–537

getBlockSize 方法，430

Frame 类，234

getBoolean 方法，180

Frame 容器，262

getByteLength，429

FramePositioningControl 接口，458

getCaretPosition 方法，110

帧，201

getCategories 方法，184, 507

freeMemory 方法，27

getChars 方法，110

fromBytes 方法，150, 355

getCipherSuite 方法，326

fromXml 方法，361, 372

getClassLoader 方法，226

getComponent 方法，263

getComponentAt 方法，263

■**G**

getConditions 方法，131

GameCanvas 类，99, 122, 194, 195–200

getContainer 方法，226

Canvas 类与，195

getContent 方法，383

SpriteCanvas 类与，210–217

getContentAsStream 方法，383

游戏，193–218

getContentID 方法，383

猫/蝴蝶示例游戏与，207–217

getContentLocation 方法，383

JSR 与，540

getContentType 方法，453

管理其执行，197

getControl 方法，453, 458

垃圾回收，gc 方法用于，27

getControls 方法，453

Gauge 类，111

getCourse 方法，505

gc 方法，27

getDate 方法，110, 179, 312

GCF（通用连接框架），

getDescription 方法，508

11, 163, 293–329

getDeviceKeyCode 方法，270

APDU 层次结构与，417

getDigestSize 方法，429

非接触式目标与，435

getDisplay 方法，98

数据报通信与，304–306

getEncoding 方法，311, 383

层次结构，295

getExpiration 方法，312

HTTP 通信与，309–315

getExtraInfo 方法，505

权限与，327

getFile 方法，313

套接字通信与，300–303

getFilter 方法，391

使用，295

getFont 方法，108

generateVisualTag 方法，444

getForecast 方法，281

通用连接框架。*参见* GCF

getGraphics 方法，124

GET 方法，307, 311, 313, 319

getHeader 方法，383, 385

getAddress 方法，301, 380

getHeaderField 方法，312

getAddressInfo 方法，505, 508

getHeaderFieldKey 方法，312

getAlgorithmName 方法，429

getHorizontalAccuracy 方法，506

getAltitude 方法，506

getHost 方法，313

getApplet 方法，257

getImage 方法，118, 257

getAppletContext 方法，257

getImageProperties 方法，444

getApplets 方法，257

getIndicator 方法，118

getAppProperty 方法，87, 89

getInitialReference 方法，421

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**551**

getInputBlockSize 方法，430

getPreferredWidth 方法，107

getInputMode 方法，110

getProperty 方法，27, 242, 406, 419,

getInstance 方法，397, 423, 504, 507

457, 463, 464

getInt 方法，180

getProtocol 方法，313

getInteractionModes 方法，125

getProtocolName 方法，326

getKeyStates 方法，196

getProtocolVersion 方法，326

getLabel 方法，107

getQualifiedCoordinates 方法，505, 508

getLandmarks 方法，508

getReadSymbologies 方法，440

getLastKnownLocation 方法，505

getReason 方法，175

getLastModified 方法，138, 312

getRecord 方法，142

getLatitude 方法，506

getRecordType 方法，437

getLayerAt 方法，202

getResource 方法，242

getLength 方法，311, 314

getResourceAsStream 方法，242, 469

getLocalAddress 方法，301

getResponseCode 方法，312, 314

getLocalPort 方法，301

getResponseMessage 方法，312

getLocation 方法，154, 263, 281, 505, 506

getRuntime 方法，27

getLocationMethod 方法，505

getSecurityInfo 方法，326

getLocationOnScreen 方法，263

getSelectedFlags 方法，114

getLocationStrings 方法，153, 160

getSelectedIndex 方法，114

getLongitude 方法，506

getServerCertificate 方法，326

getMessage 方法，403

getSize 方法，138, 202, 263

getMessageParts 方法，383

getSizeAvailable 方法，注意，146

getMidlet 方法，391

getSnapshot 方法，461–465, 494

getMIMEType 方法，383

getSocketOption 方法，301

getMin 方法，477



getSpeed 方法, 505

getMinContentHeight 方法, 125, 477

getStartContentId 方法, 382

getMinContentWidth 方法, 125, 477

getState 方法, 460

getMinimumHeight 方法, 107

getString 方法, 110, 118, 180

getMinimumSize 方法, 240

getStringArray 方法, 180

getMinimumWidth 方法, 107

getSubject 方法, 383

getName 方法, 166, 508

getSupportedContentTypes 方法, 457,

getNextRecor

dID 方法, 142

getNumRecords 方法, 138, 145

getSupportedFields 方法, 179

getOutputBlockSize 方法, 430

getSupportedProtocols 方法, 457

getPath 方法, 166

getSupportedTargetTypes 方法, 435

getPayloadData 方法, 381

getSystemResource 方法, 242

getPayloadText 方法, 380

getSystemResourceAsStream 方法, 242

getPort 方法, 301, 313, 326

getTargetComponent 方法, 478

getPref 方法, 477

getText 方法, 371

getPrefContentHeight 方法, 125, 131,

getTimeBase 方法, 453

getTimeout 方法, 118

getPrefContentWidth 方法, 125, 131,

getTimestamp 方法, 380, 382, 505

getType 方法, 311, 360

getPreferredHeight 方法, 107

getURL 方法, 167, 313

getPreferredSize 方法, 240

getValue 方法, 360

[www.it-ebooks.info](http://www.it-ebooks.info/)

**552**

■索引

getVerticalAccuracy 方法, 506

HTTP 通信, 298, 306–326, 334

getXletProperty 方法, 226, 242

GCF 与, 309–315

get_alarmAlert 方法, 96

方法, 307

get_exitCommand 方法, 96

打开连接与, 295

get_helloStringItem 方法, 95

通过 HTTPS 进行安全通信, 325

get_infoForm 方法, 95

套接字/数据报通信与,

get_listContacts 方法, 189

get_mainForm 方法, 105

状态码, 308

全球定位系统 (GPS), 500

WeatherWidget 示例应用程序与,

全球移动通信系统 (GSM), 374, 378

315–325

GPS (全球定位系统), 500

HTTP 方法, 334

Graphics 类, 122, 125, 235, 257

http 方案, 455

图形操作, 124

HttpConnection 类, 311–315

图形。*参见* 图像

HTTPS 通信, 299, 325, 334

GridBagLayout 管理器, 262

证书与, 415

GridLayout 管理器, 262

与 SATSA-CRYPTO API 对比, 423

GSM (全球移动通信系统), 374, 378

HttpsConnection 类, 326

GUIControl 接口, 458, 459

GUI, JSR 与, 540

■**I**

IllegalArgumentException, 136

■**H**

open 方法与, 165, 296

H.263 视频流, 448

playTone 方法与, 466

hasPointerEvents 方法, 123

IllegalBlockSizeException, 425

hasPointerMotionEvents 方法, 123

IllegalStateException

HAVi (家庭音频视频互操作性), 231

密码与, 425, 430

HEAD 方法, 307

Player 实例与, 453, 463

头部

Illustrator (Adobe), 471

HTTP 通信与, 308

ImageItem 类, 111

MMS 消息与, 383, 385

图像

堆

在消息中发送, 384, 398–407

JTWI 对堆的建议, 528

视觉标签识别与, 432

MSA 对堆的要求, 532

成像传感器, 449, 461, 464

IMS (IP 多媒体子系统)

重量级组件, 264, 270

JSR 与, 540

“Hello World!”

MSA2 与, 536

数据报通信与, 304

索引, 用于记录, 135, 138

HTTP 通信与, 307

init 方法, 254, 491

无线消息与, 381

initComponents 方法, 263

Hello World MIDlet, 83

initDisplayMode 方法, 459, 460, 463

“Hello Xlet”, 229, 232

initialize 方法, 95, 159, 325

HelloMidlet 方法, 85

initMediaPlayer 方法, 492, 493

高级事件, 264

initSvgPlayer 方法, 492, 496

家庭音频视频互操作性 (HAVi), 231

initXlet 方法, 225, 233

Inkscape, 471

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**553**

输入约束, 109

■**J**

输入设备, AGUI 与, 269

J2ME Polish, 17

输入模式, 109

J2ME Web 服务规范, 331,

InputConnection 接口, 295

355–365

InputStream 类, 295, 301, 315

JAD 文件, 6

InputStreamReader 类, 30

JTWI 设备与, 528

insert 方法, 106, 202

MIDlet 与, 53–56, 87, 89, 387–390, 532

Choice 类, 114

JAD/JAR 对 (套件), 53

TextField 类, 110

JAR 文件

集成开发环境, 33, 53

MIDlet 与, 53, 87, 89

Xlet 间通信 (IXC), 243

Xlet 与, 75, 242

基于 XML 的 RPC 的 Java API (JAX-RPC), 355

国际标准化组织 (ISO), 417

Java API, 539–541

国际化, MSA 与, 534, 540



Java AWT（抽象窗口工具包），用户界面模型，99

interrupt 方法，27

Java Community Process（JCP，Java 社区进程），523–528，

invalidate 方法，239

530，535–537

InvalidKeyException（无效密钥异常），425

Java 数据库连接，JSR 与，540

InvalidRecordIDException（无效记录 ID 异常），142

Java ME（Java 平台，微型版）

invokeAndWait 方法，269，479

架构，10–16

invokeLater 方法，269，479，483

开发规划与，16–18

IOException（输入/输出异常），165，295，296，444

碎片化与，523–537

IP 多媒体子系统（IMS），MSA2

市场，3

与，536

应用程序的营销/销售与，17

isAncestorOf 方法，263

组成的平台/库，6

isCategory 方法，184

安全与，6，413–445

isDirectory 方法，167

用于 Web 服务的 XML 支持与，

isDoubleBuffered 方法，124，270

341–372

isHidden 方法，167

ISO（国际标准化组织），417

Java ME 设备表，300

ISO14443Connection 类，436

Java ME 可缩放 2D 矢量图形。*参见*

isOpen 方法，167

SVGAPI

isSelected 方法，114

Java 移动游戏 API，193–218，540

isSupportedField 方法，179

Java 网络启动协议（JNLP），

isValid 方法，505

Java 平台，微型版。*参见* Java ME

Item 类，99，106

Java 远程方法协议（JRMP），275

items，104–114

Java RMI 栈。*参见* RMI 栈

自定义，122–131

Java SE（Java 标准版）

PIM 数据库类别与，184

与 CDC 对比，23

items 方法，177

与 CLDC 1.1 对比，20

itemsByCategory 方法，184

集合 API，28

itemStateChanged 方法，116

生成存根类与，284

IXC（Xlet 间通信），243，246

实现远程服务与，283

IxcRegistry，246

RMI OP 与，279

rmic 编译器与，281

[www.it-ebooks.info](http://www.it-ebooks.info/)

**554**

■索引

Java SE 类库

JSR 30 - J2ME 连接有限设备配置，529

CDC 与，31

JSR 36 - 连接设备配置，23

CLDC 与，24–30

JSR 63 - Java XML 处理 API 1.1，

Java SE Java 开发工具包，34

JSR 62 - 个人概要规范，

Java 智能卡，416–421

Java 规范请求。*参见* JSR

Java SE（Java 标准版）

Java 流，139

无线行业 Java 技术（JTWI），528，540

Java 验证程序，17，415

JSR 66 - RMI 可选包规范 1.0 版，278

Java 虚拟机（JVM），10

JSR 75 - J2ME 平台 PDA 可选包，161，174，190

Java Web Start，75

JSR 82 - Java 蓝牙 API，533

java.io 包，为适应 CLDC 所做的更改，29

JSR 118 - 移动信息设备概要 2.0，533

java.lang 包，为适应 CLDC 所做的更改，

JSR 120 - 无线消息 API，373，

24–28

375，529

java.util 包，为适应 CLDC 所做的更改，

JSR 135 - 移动媒体 API，447，451，

JSR 139 - 连接有限设备配置 1.1，529，533

JavaCardRMIConnection 接口，418，

JSR 172 - J2ME Web 服务规范，331，355

JavaScript 对象表示法（JSON），331

JSR 177 - J2ME 安全与信任服务 API，416

JAX-RPC（基于 XML 的 RPC 的 Java API），

JSR 179 - J2ME 位置 API，499

JComponent 类，270

JSR 184 - J2ME 移动 3D 图形 API，533

JCP（Java 社区进程），523–528，

JSR 185 - 无线行业 Java 技术，524，528–529

530，535–537

JSR 197 - J2SE 通用连接框架（GCF）可选包，294

JCRMI 接口，420

JSR 205 - 无线消息 API 2.0，

JFrame 类，269

373，375，386

JMenuBar 类，270

JSR 206 - Java XML 处理 API（JAXP）1.3，386

JNLP（Java 网络启动协议），

JSR 209 - AGUI 可选包，266

JOptionPane 类，269

JSR 216 - 个人概要 1.1，253

JPopupMenu 类，269

JSR 218 - 连接设备配置 1.1，23

JRMP（Java 远程方法协议），275

JSR 226 - J2ME 可缩放 2D 矢量图形 API，472，533

JSON（JavaScript 对象表示法），331

JSR 234 - 高级多媒体补充，464

JSR（Java 规范请求），8，

JSR 238 - 移动国际化 API，534–535

539–541

AGUI 与，230

■**L**

支持 CDC 的设备，用户界面包与，230

标签，101

如何阅读，525

Landmark 类，502，507

Java ME 碎片化与，523–528

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**555**




LandmarkException, 503

JSR 248 - 移动服务架构,

landmarks

524, 530

基于位置的服务与, 501

JSR 249 - 移动服务架构 2,

管理, 507

524, 530, 534

LandmarkStore 类, 502, 507

JSR 256 - 移动传感器 API, 535

lastModified 方法, 167

JSR 257 - 非接触式通信

Layer 类, 194, 200

API, 431, 535

LayerManager 类, 194, 200–205

JSR 258 - 移动用户界面

的方法, 202

定制 API, 536

示例游戏应用与, 216

JSR 271 - 移动信息设备

layers, 200–205

Profile 3, 535

布局标志, 107

JSR 272 - 手持终端移动广播服务 API

布局管理器, 261, 262

, 535

布局首选项, Item 类与, 106

JSR 281 - IMS 服务 API, 536

LBS。*参见* 基于位置的服务

JSR 287 - 可缩放二维矢量图形

小于 (<), 339

Java ME 2.0 API, 447, 470, 472,

轻量级组件, 233–241, 264,

生命周期, 526

List 类, 99, 113

JTextComponent 类, 269

复杂用户界面与, 114

JTWI (Java 无线工业技术), 528, 540

通过其显示选择, 120

JVM (Java 虚拟机), 10

list 方法, 167

listConnections 方法, 303, 389

监听器, 265

■**K**

listLandmarkStores 方法, 507

K 虚拟机 (KVM), 10

listRecordStores 方法, 137

键码, 123, 196

listRoots 方法, 169

按键事件, 270

load 方法, 173

密钥生成, 430

位置 API, 499, 501–521

按键状态, 轮询, 195, 196

通过其确定设备位置,

KeyEvent 类, 270

503–507

KeyListener, 265

JSR 与, 540

keyPressed 方法, 123, 126, 483

管理地标与, 507

keyReleased 方法, 123, 126

权限与, 508

keyRepeated 方法, 123, 126

Sun Java 无线工具包与, 518

密钥, 密码与, 415

WeatherWidget 示例应用与,

按键输入, 轮询, 196

509–520

KVM (K 虚拟机), 10

kXML 解析器, 365–372, 509

KXmlParser 类, 365, 371

[www.it-ebooks.info](http://www.it-ebooks.info/)

**556**

■索引

Location 类, 154, 502, 504, 517

MediaException 类, 451, 463

实现, 146–150

内存, 27

集成到 WeatherWidget,

菜单优先级, 270

155–160

菜单类型, 270

修改器和访问器与, 246

Message 类, 376, 380

RMI OP 与, 281

消息计数器, 403

XML 与, 343–355

消息摘要, 335, 415

基于位置的服务 (LBS), 499–521

Bouncy Castle API 与, 428

确定设备位置与,

SATSA-CRYPTO API 与, 422

503–507

MessageConnection 接口, 376–380,

地标与, 501, 507

安全与, 508

MessageDigest 类, 423

WeatherWidget 示例应用与,

MessageListener 接口, 376, 385

509–520

MessagePart 类, 376

Location 接口, 282

与 MultipartMessage 类对比, 380, 383

LocationException, 503, 505

sendMMS 方法与, 406

LocationImpl 类 (示例), 281, 283, 284

消息。*参见* 无线消息传递

LocationListener 接口, 502, 506

MetaDataControl 接口, 458

LocationParser 类, 367–372, 517

麦克风, 465

LocationParserHandler 类, 357–361, 370

MIDIControl 接口, 458

LocationProvider 类, 502–507

MIDlet 套件, 133–137

LocationStore 类, 146, 169–173, 325,

MIDlets, 13, 83–96。*另请参见*

507, 517

WeatherWidget

实现, 150–155

与 applets 对比, 254, 258

集成到 WeatherWidget,

属性, 87

155–160

构建/运行, 43–53

locationUpdated 方法, 502

创建, 37–56

LocationUpdater 类, 518

执行, 53–56

定位器, 多媒体内容与, 454,

退出, 87

463, 493

GameCanvas 类, 绑定到, 199

低级事件, 264

Hello World 示例, 83

传入消息, 动态注册,

JAD/JAR 对与, 53

390

生命周期, 85, 97

■

方法, 85

**M**

清单, 87

Mac OS X, NetBeans 移动包与, 34

移动包, 34, 37

Manager 类, 451, 454

打包, 53–56, 87

清单, 87

属性, 89

营销考量, 17

记录存储与, 133

MarshalledObject 类, 277

资源与, 89

数据编组, 342, 355

示例游戏应用与, 209

match 方法, 143

启动事件/闹钟与, 90–96

Math 类, 26

状态, 85

媒体播放器 (示例应用),

484–496

媒体类型, 多媒体内容与, 448

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**557**



MIDletStateChangeException, 87

mobile phones, 6

MIDP (移动信息设备

location-based services and, 499

Profile), 9, 13

MIDP and, 9

2D games and, 193

MSA and, 530–536

Connection implementations and, 299

Mobile Sensor API, 535

HTTPS and, 325

mobile sensors, JSRs and, 541

JSRs and, 541

Mobile Service Architecture (MSA),

JTWI requirements and, 529

530–536, 541

MIDlets and, 83–96

Mobile Station International Subscriber

MIDP 3.0 and, 535

Directory Number (MSISDN), 391

MSA requirements and, 533

Mobile User Interface Customization API,

MIME type, MMS messages and, 383

MMAPI (移动媒体 API), 407, 448–469

mobile-originated (MO) messages, 374, 379

classes/interfaces of, 451

mobile-terminated (MT) messages, 374,

JTWI requirements and, 529

MMAPI system property definitions

Mobility Pack for NetBeans, 483, 485

and, 457

Mobility Packs, for MIDlets and Xlets, 34,

MSA requirements and, 533

37, 57

organization of, 450

model-view-controller (MVC), 99

sample media player and, 484–496

Moore’s law, 21

supplemental interfaces for, 464

more than (>), 339

using, 452–469

MouseListener, 265

MMAPI schemes, 454

MouseMotionListener, 265

MMS Center, 375

MouseWheelListener, 265

MMS messages, 374

moveButterflies (sample) method, 217

contents of, 383

moveCat (sample) method, 199, 217

JSRs and, 541

MP3 players, 454

parts and, 376, 380, 382

MPEG-4, 448

sending/receiving, 398–407

MSA (移动服务架构),

MMSMIDlet class, 399–404

530–536, 541

MMSSender class, 404, 494

MSA2 (移动服务架构 2), 534

MO (mobile-originated) messages, 374,

msgAvail message counter, 403

MSISDN (移动台国际用户

Mobile 3D graphics, MSA requirements

目录号码), 391

and, 533

MT (mobile-terminated) messages, 374,

mobile broadcast service

JSRs and, 541

multimedia content, 447–498

MSA2 and, 535

capturing, 461–466

mobile devices

IMS and, 536

development planning and, 16

JSRs and, 541

Java ME platform and, 6–10

MMAPI and, 448–469, 484–496

Moore’s law and, 21

packaging and delivery of, 448

Mobile Information Device Profile. *参见*

playing audio/video and, 493

MIDP

playing SVG content and, 496

Mobile Media API. *参见* MMAPI

rendering, 452–469

SVGAPI and, 470–484

[www.it-ebooks.info](http://www.it-ebooks.info/)

**558**

■索引

Multimedia Messaging Service. *参见* MMS

nextRecord method, 143

messages

nextTag method, 365

MultimediaMIDlet class, 486–492, 496

nextText method, 365

MultipartMessage interface, 376, 379

nextToken method, 365

managing multiple parts of, 382

NFC (诺基亚近场通信)

message headers and, 385

SDK, 431

multithreading, 27

NFC Data Exchange Format (NDEF), 431,

MVC pattern

MIDlets and, 99

Nokia Near Field Communication (NFC)

multimedia content and, 449

SDK, 431

NoSuchAlgorithmException, 423, 425

NoSuchMethod exception, 260

■**N**

NotBoundException, 246

namespaces, XML and, 340

notifyActive method, 227

Naming class, 277

notifyDestroyed method, 87, 227

naming conventions

notifyIncomingMessage method, 385, 403

for applets, 258

notifyPaused method, 87, 227

for record stores, 135

notifyStateChanged method, 127

NanoXML parser, 372

NullPointerExecption, 169

nbproject directory, 36

numberOfSegments method, 380

NDEF (NFC 数据交换格式), 431,

NDEF-enabled targets, registering

■**O**

listeners for, 433

obfuscation, 52

NDEFTagConnection class, 436

Object class, 26

near-field communications, 431

object-oriented applications, 273

NetBeans, 105, 107

ODMs (原始设计制造商), 3

adding/removing file systems and, 169

OEMs (原始设备制造商), 3

containers and, 263

OMA (开放移动联盟), 386

GUI builder and, 517

one-shot positioning requests, location-

Mobility Pack for, 483

based services and, 501, 518

PIM package and, 189

one-to-many protocols, 375

RMI applications and, 282

open method, 164, 173

SVG images and, 483

Connector class and, 295, 300, 378, 385

NetBeans IDE, 33–77

HttpConnection class and, 311

comments in, 50

Open Mobile Alliance (OMA), 386

home page of, 34

openDataInputStream method, 166, 301

installing, 34

openDataOutputStream method, 166, 301

MIDlets and, 37–56

opening files, 166

reasons for using, 33

Xlets and, 57–77



openInputStream 方法，第 166 页

newMessage 方法，第 379、384 页

openOutputStream 方法，第 166、314 页

newSAXParser 方法，第 362 页

openPIMList 方法，第 176 页

next 方法，第 365 页

openRecordStore 方法，第 136 页

nextFrame 方法，第 206 页

openStore 方法，第 153 页

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**559**

优化，第 52 页

个人基础框架 (PBP)，第 12、14 页

选项标识符，第 302 页

223–252

OPTIONS 方法，第 307 页

AGUI 可选包与，第 266 页

方向类，第 502 页

开发轻量级用户界面

原始设计制造商 (ODM)，第 3 页

通过，第 233–241 页

原始设备制造商

JSR 与，第 541 页

(OEM)，第 3 页

其系统属性，第 241 页

OutputConnection 接口，第 295 页

个人数字助理。*参见* PDA

OutputStream 类，第 301、313 页

个人识别码 (PIN)，

OutputStreamWriter 类，第 30 页

智能卡与，第 420 页

个人信息管理。*参见*

*PIM 相关条目*

■**P**

个人框架 (PP)，第 12、15 页

包，第 8、15 页

小程序，支持，第 253 页

打包

JSR 与，第 541 页

MIDlet，第 53–56 页

PersonalJava，第 15、23 页

Xlet，第 75 页

PIM（个人信息管理），

paint 方法，第 123–125、239 页

161、174–190、438

Container 类与，第 235、256 页

检查/确保 PIM 包的可用性，第 175、176 页

GameCanvas 类与，第 195 页

JSR 与，第 541 页

Graphics 类与，第 235 页

MSA 要求与，第 533 页

LayerManager 类与，第 202 页

删除条目与，第 184 页

WeatherItem 示例类与，第 131 页

示例应用程序与，第 185–190 页

Panel 类，第 263 页

PIM 类，第 174 页

Panel 容器，第 262 页

<PARAM>，第 258 页

PIM 数据库，第 176 页

parse 方法，第 362、371 页

PIM 记录，第 177–184 页

部件，MMS 消息与，第 376、380、382 页

创建，第 183 页

修改，第 182 页

pauseApp 方法，第 85、86、491 页

从 PIM 数据库读取，第 177 页

暂停状态，第 479 页

PIMException，第 175 页

MIDlet 的，第 85 页

PIMItem 类，第 175、177 页

Xlet 的，第 224 页

其 adder 方法，第 182 页

pauseXlet 方法，第 224、225、230 页

其 setter 方法，第 183 页

支付，JSR 与，第 541 页

PIMList 类，第 174、184、533 页

PBP。*参见* 个人基础框架

PIN（个人识别码），

PDA（个人数字助理），第 6 页

智能卡与，第 420 页

CDC 与，第 12 页

乒乓缓冲，第 124 页

JSR 与，第 541 页

PitchControl 接口，第 458 页

权限，第 6、319 页

像素，参考像素与，第 206 页

APDU 与，第 420 页

纯文本 XML 元素，由 < > 指示，

授予网络连接，第 327 页

JCRMI 与，第 421 页

基于位置的服务与，第 508 页

PlainTagConnection 类，第 436 页

MIDP 与，第 13 页

明文，第 415 页

play 方法，第 496 页

[www.it-ebooks.info](http://www.it-ebooks.info/)

**560**

■索引

Player 接口，第 450、451 页

项目配置，第 52 页

PlayerListener 接口，第 451、460 页

属性，Xlet 的，第 242 页

播放器，多媒体内容与，第 449 页

协议，多媒体内容与，第 454 页

playerUpdate 方法，第 460 页

邻近标签，第 431 页

playFromResource 方法，第 492、493、496 页

ProximityListener 接口，第 502、507 页

播放音频/视频，用于多媒体

公钥密码学，第 423 页

内容，第 493 页

拉解析器，第 342、365 页

播放状态，第 479 页

推解析器，第 342、355 页

playTone 方法，第 466–469 页

推送注册表，第 54、303 页

点对区域协议，第 375 页

闹钟与，第 91 页

指针事件，第 123 页

非接触式通信与，第 432 页

pointerDragged 方法

传入消息，第 387 页

Canvas 类，第 123 页

PushRegistry 类，第 303、387 页

CustomItem 类，第 126 页

PUT 方法，第 307 页

pointerPressed 方法

putClientProperty 方法，第 269 页

Canvas 类，第 123 页

CustomItem 类，第 126 页

pointerReleased 方法

■**Q**

Canvas 类，第 123 页

QR 码（快速响应码），第 440 页

CustomItem 类，第 126 页

QualifiedCoordinates 类，第 502、506、508 页

轮询

事件，第 197 页

快速响应码 (QR 码)，第 440 页

按键状态，第 195 页

引号，单引号（‘ ’），第 339 页

按键，第 196 页

POST 方法，第 307、313、319 页

PP。*参见* 个人框架

■**R**

前言，XML 文档的，第 339 页

光栅图像，第 470 页

prefectch 方法，第 453 页

RateControl 接口，第 458 页

预取状态，第 453 页

RC4（Rivest 密码 4），第 423 页

preverify 工具，第 11、52 页

RC4 加密，使用 Bouncy Castle API

prevFrame 方法，第 206 页

进行，第 429 页

previousRecord 方法，第 143 页

read 方法，第 140、301 页

优先级，第 101 页

readBoolean 方法，第 140 页

私有记录存储，第 133 页

readByte 方法，第 140 页

特权，第 6 页

readChar 方法，第 140 页

第三方认证与，第 17 页

readChars 方法，第 140 页

WMA 与，第 386 页

readDouble 方法，第 140 页

processBlock 方法，第 430 页

readFloat 方法，第 140 页

processBytes 方法，第 430 页



readInt 方法，141

processMouseEvent 方法，240

readLong 方法，141

processRequest 方法，259

readNDEF 方法，436

生产者-消费者模型，259

readShort 方法，141

配置文件，8, 12–15

readUTF 方法，141

ProGuard 混淆器，52, 426

readVisualTag 方法，444

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**561**

read *X* 方法，305

可远程对象，使其可供

realize 方法，453

其他 Xlet 使用，246

realized 状态，453

Remote 类，245

receive 方法

读取远程内容，309

Connector 类，385

Remote 接口，243, 277

MessageConnection 接口，379

远程方法调用。*参见*

接收消息，385

RMI 相关条目

MMS 消息与，398–407

远程对象，243, 275

SMS 消息与，391–398

访问，249

记录 ID，135, 138

调用，286

删除记录与，144

远程过程调用（RPC）服务，333

检索记录与，142

远程服务，280, 285

更新记录与，144

RemoteException，246, 251, 277, 282

记录存储，133–160

remove 方法，184, 202

访问记录存储中的记录，138–146

removeToDo 方法，184

监听记录存储中的变化，145

removeAddresses 方法，383

获取记录存储信息，137

removeCommand 方法，106, 195

打开/关闭记录存储，136

removeContact 方法，184

记录存储的平台限制，145

removeEvent 方法，184

删除记录存储，137

removeFileSystemListener 方法，169

WeatherWidget 示例应用程序与，

removeLandmarkFromCategory 方法，

146–160

recordAdded 方法，145

removeMessagePart 方法，383

recordChanged 方法，145

removeMessagePartId 方法，383

RecordComparator 接口，143

removeMessagePartLocation 方法，383

RecordControl 接口，458, 465

removeRecordListener 方法，145

recordDeleted 方法，145

renameCategory 方法，184

RecordEnumeration 接口，143, 153

渲染多媒体内容，452–469

RecordFilter 接口，143

控制渲染过程，458–461

RecordListener 接口，145

启动渲染过程，454–457

记录，133

SVG 图像与，474–484

访问记录存储中的记录，138–146

repaint 方法，123, 125, 235

记录大小限制，记录存储与，146

重复，多媒体内容与，468

RecordStore 类，135, 173

表述性状态转移（REST），333

RecordStoreException，136, 137

reset 方法，306

RecordStoreFullException，136, 141

分辨率，多媒体内容与，467

RecordStoreNotFoundException，136, 137

RecordStoreNotOpenException，138, 141,

Xlet 的资源，242

resources 目录，36

参考实现（RI），527

进一步阅读的资源

参考像素，206

对策，416

registerAlarm 方法，91

密码学，415

registerConnection 方法，303, 390

编写自定义 DataSource，456

Registry 接口，246

MMAPI，448

releaseTarget 方法，477

SVG/SVG Tiny，472

RemoteException，246, 251, 277, 282

XML，336

[www.it-ebooks.info](http://www.it-ebooks.info/)

**562**

■索引

REST（表述性状态转移），333

■**S**

RESTful Web 服务，333, 356, 362

SA（选择可用性），500

resume 方法（已弃用），27

示例应用程序

resumeRequest 方法，87

猫/蝴蝶游戏，207–217

版本控制软件，36

ContactLoaderThread 类与，189

RFID 设备，436, 541

Location 类与，146–150, 154

RFID 标签，431

LocationStore 类与，146, 150–155,

RI（参考实现），527

169–173

风险，安全与，414

媒体播放器，484–496

Rivest Cipher 4（RC4），423

PIM 包与，185–190

RMI（远程方法调用），

RMI 服务器，285

273–287, 333

WeatherApplet，57–76, 281

优点/缺点，276

WeatherItem 示例类与，127–131

架构，274

WeatherWidget。*参见* WeatherWidget

JSR 与，541

示例代码。*参见* 源代码

Personal Basis Profile 与，14

SATSA（J2ME 安全与信任服务 API），

使用 RMI，280–286

416–425, 533

RMI 接口，277, 282

SATSA-CRYPTO API，416, 422–425

RMI OP（RMI 可选包），278

与 Bouncy Castle API 对比，430

基于 Internet Inter-Orb 协议的 RMI

加密/解密消息与，

（RMI-IIOP），278

消息摘要创建与，422

RMI 服务器应用程序（示例），285

MSA 与，533

RMI 协议栈，243

SATSA-Java Card RMI（SATSA-JCRMI），416

RMI-IIOP（基于 Internet Inter-Orb 协议的 RMI），278

SATSA-公钥基础设施（SATSA-



rmic 编译器，276, 281, 284

PKI（公钥基础设施），416

RMISecurityManager 类，277

SAX 解析器，357, 360

rms 接口，133

可缩放二维矢量图形。*参见* SVGAPI

根元素，XML 与，336

ScalableGraphics 类，474, 477

根文件系统，169

ScalableImage 类，474

rootChanged 方法，168

scheduleMIDlet 方法，95

圆角按钮，236–240

方案，MMAPI 与，454, 463

RPC（远程过程调用）服务，333

SCP（安全复制协议），56

rtp 方案，455

Screen 类，99, 114, 122

rtsp 方案，455

屏幕设计器，39–43

run 方法

屏幕转换，用于 WeatherWidget，39

WeatherWidget 示例应用程序与，

屏幕，114–122

JTWI 对屏幕的建议，528

接收消息与，403

MSA 对屏幕的要求，532

SVG 图像与，483

MSA2 对屏幕的要求，534

媒体播放与，491, 493

用于 WeatherWidget，41

位置与，518

SD（安全数字）卡，77

run. sendMsg 方法，397

安全复制协议（SCP），56

Runnable 接口，318, 395

安全数字（SD）卡，77

Runtime 类，27

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**563**

安全性，13, 413–445

setDoubleBuffered 方法，270

Java ME 安全模型与，6

setFont 方法，108

小程序与，253

setForecast 方法，281

理解安全需求，413–416

setFrame 方法，206

J2ME 安全与信任服务 API

setHeader 方法，383, 385

（SATSA），416–425, 533

setHidden 方法，167

SecurityException，141, 165

SetImage 类，404

LocationManager 方法与，505

setImage 方法，118

open 方法与，296

setIndicator 方法，118

Player 实例与，463

setInitialInputMode 方法，109

SMS 消息与，378

setInputMode 方法，110

SecurityInfo 接口，326

setInt 方法，183

seed 方法，189

setItemCommandListener 方法，106,

段，SMS 消息与，380

Selective Availability（SA），500

setItemSateListener 方法，116

发送消息，380–385

setLabel 方法，107, 108

MMS 消息与，398–407

setLayout 方法，107

SMS 消息与，391–398

setLocation 方法，281

sendMMS 方法，406, 495

setLocationListener 方法，506

sendMsg 方法，396, 397, 406

setMediaTime 方法，453

序列化，139, 150

SetMessage 类，392, 396

ServerSocketConnection 类，302

setName 方法，508

服务代理，332

setPaused 方法，216

服务提供商，332

setPayloadData 方法，381

服务请求者，332

setPayloadText 方法，380

面向服务的架构（SOA）

setPreferredSize 方法，107

服务，333

setQualifiedCoordinates 方法，508

机顶盒，5

setReadable 方法，167

CDC 与，12

setRecord 方法，138, 144

JSR 与，540

setRequestMethod 方法，312, 314, 334

setAddress 方法，380

setRequestProperty 方法，312, 336

setAddressInfo 方法，508

setSelectedFlags 方法，114

setAnimatedTile 方法，205

setSelectedIndex 方法，114

setBinary 方法，183

setSocketOption 方法，301

setBoolean 方法，183

setStartContentId 方法，382

setCell 方法，204

setString 方法，110, 118, 183

setChars 方法，110

setStringArray 方法，183

setCommandListener 方法，103, 116,

setSubject 方法，383, 384

setText 方法，108

setConditions 方法，131

setTimeBase 方法，453

setConstraints 方法，109

setTimeout 方法，116, 118

setCurrent 方法，118, 199, 478, 483

setTransform 方法，206

setDate 方法，110, 183

设置状态，HTTP 通信与，

setDescription 方法，508

[www.it-ebooks.info](http://www.it-ebooks.info/)

**564**

■索引

setViewWindow 方法，201

套接字通信，300–303

setWritable 方法，167

套接字选项，302

set_location 方法，517

SocketConnection 类，300

共享对象，243–251

软键，270

实现，244

斜杠（/）字符，163, 165

使用，249

源代码

共享记录存储，133

小程序，255, 258, 265

Shockwave Flash（SWF）格式，470

actionPerformed 方法，61, 74

短代码，374

警报，91

小区广播短消息服务

警报，配置，117

（SMS-CB），375

ByteArrayInputStream，创建，140

短消息服务。*参见* SMS 消息

ByteArrayOutputStream，创建，139

showDocument 方法，257

命令对象/可显示对象，

showException 方法，492

之间的关系

showStatus 方法，257

演示，103

showSupportedMedia 方法，491, 492

联系人，184, 189



签名应用程序，55

内容，通过...下载

静音，多媒体内容与，468

ContentConnection，309

简单对象访问协议（SOAP），

控制循环，实现，198

332，355

Cookie，335

单引号（‘ ’），339

数据，发送到服务器，313

用于 J2ME 的 SIP API，541

DataSource，455

SIP 协议，MSA 与，534

DataInputStream，创建，140

size 方法，114

DataOutputStream，创建，139

骨架，276

设备位置，确定，503

智能卡，416–421

加密消息，424

smartcardslots 属性，419

FCOP，向系统查询

SMS 聚合器，374

版本，164

短信中心（SMSC），374

表单，向其中添加项目，205

SMS 消息，374

帧序列，206

编码/解码与，381

GameCanvas 类，199

JSR 与，541

“Hello World!”，304，307

消息大小与，374，380

Hello World 小程序，256

分段与，380

Hello World MIDlet，83

发送/接收，391–398

“Hello Xlet”，232

SMS-CB（短消息服务-小区

HTTP 连接，295

广播），375

HTTP 方法，指定，334

SMSC（短信中心），374

HTTPS 连接，326

SMSSender 类，392，396

图像，在消息中发送，384

SOA（面向服务的架构）

成像传感器，从中捕获快照

服务，333

，461

SOAP（简单对象访问协议），

索引，数组，203

332，355

IXC 机制，247

基于 SOAP 的 Web 服务，355

JAD 文件，54

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**565**

Java AWT 组件/容器，256

SAX 解析器，361

Java SE 存根类，284

sendMsg 方法，495

轻量级组件，236–240

ServerSocketConnection，获取，

kXML 解析器，366–372

按键状态，轮询，189

setCell 方法，204

listConnections 方法（PushRegistry

SettingPanel 类，68

类），使用，389

共享对象，244，249

Location 类，146，343–355

智能卡，419，421

Location 接口，282

SMSMidlet 类，392

位置模拟，519

SMSSender 类，396

LocationImpl 类，283

Sprite 类，206

LocationStore 类，实现，150，

SpriteCanvas 类，210

SpriteSampleMIDlet 类，209

LocationParser 类，371

StreamConnection 接口，300

LocationParserHandler 类，357–361

支持的媒体/协议类型，457，

定位器，用于多媒体内容，455

媒体播放，停止/回收

SVG 图像，474，475，479，480

资源，456

SVG Tiny 文档，470

消息摘要，422，428

TextMessage 接口，设置/获取

MIDlet，43–50，208

有效载荷与，380

MIDlet-Push 条目，假设的，388

待办事项，返回摘要，180

MMS 消息，384

toByteArray 方法，调用，140

MMSMIDlet 类，399–404

ToneControl 实例，467，468

MMSSender 类，404

TiledLayer 类，204

MP3 播放器，454

未解析的字符指令，340

MultimediaMIDlet 类，486–492

视频片段，播放，459

支持 NDEF 的目标，注册

WeatherApplet，76

监听器，433

WeatherController 类，72

NDEF 记录，437

WeatherFetcher 示例类，315，

paint 方法，235

319–325，362

PIM 包，185

WeatherWidget 示例应用程序，

QR 码，编码/解码，441

155–160，315，319–325，509–518

随机密钥生成，430

WeatherXlet，62，73

RC4 加密，使用 Bouncy Castle

网页，获取，306，307

API，429

无线消息，438

记录字段，枚举，180

Xlet，225，227

记录存储，过滤，143

XML 文档，336

RecordControl 接口，465

XML 命名空间，340

远程对象，调用，286

Spacer 类，107

RESTful Web 服务，362

特殊字符，339

资源，访问，242

Sprite 类，194，201，205–207

RMI 服务器应用程序，285

碰撞检测与，207

根文件系统，枚举，169

变换与，206

rootChanged 方法，重写，168

SpriteCanvas 类，210–216

[www.it-ebooks.info](http://www.it-ebooks.info/)

**566**

■索引

SpriteSampleMIDlet 类，209

SVGAPI（可缩放二维矢量图形），

src 目录，36

470–496

start 方法，254，453

类/接口层次结构，474

startApp 方法，85，86

JSR 与，541

init 方法与，491

修改图像与，480

initialize 方法与，95

组织，472

startReceive 方法与，395

渲染图像与，474–480

startCamera 方法，462

示例媒体播放器与，484–496



startDocument 事件，357，360

使用 NetBeans 时，483

started 状态，453

SVGCanvas，477

startElement 事件，357，360

SVGDocument 类，474，483

起始内容 ID，MMS 消息与，

SVGImage 类，474，482

SVGImageItem 类，475

起始 XML 标签，由 </ > 表示，339

SVGMenu 类，484

startReceive 方法，395

SVGPlayer 类，484

启动事件，90–96

SVGSplashScreen 类，484

startXlet 方法，224，225，229

SVGWaitScreen 类，484

状态，Player 实例与，452，460

SVN（Subversion），36

静态 SVG 图像，474

SWF 格式（Adobe），470

HTTP 通信的状态码，

Swing，267

AGUI 与，270，271

stop 方法，254，453，456

用户界面模型与，99

stop 方法（在 Java SE 中已弃用），27

SwingUtilities 类，269

stopCamera 方法，462

符号体系，440

stopped 状态，479

SymbologyManager，440，443

StopTimeControl 接口，459

System 类，27

StreamCipher 接口，429

StreamConnection 接口，295，300

StreamConnectionNotifier 类，302

■**T**

StreamConnectionNotifier 接口，295

XML 中的标签，336

String 类，28

targetDetected 方法，433，435

StringBuffer 类

，28，342，355，360

TargetListener 接口，433

StringItem 类，107，108

TargetProperties，433

存根，275，284

TCK（技术兼容性工具包），527

主题，MMS 消息与，383

TCP 连接，295，300，302

Subversion（SVN），36

技术兼容性工具包（TCK），527

套件（JAD/JAR 对），53

远程信息处理，JSR 与，541

Sun Java Wireless Toolkit，43，53，518

电话技术，JSR 与，541

suspend 方法（已弃用），27

电视，JSR 与，541

SVG 图像，MSA 要求与，533

速度，回放与，468

SVG Tiny，470

TempoControl 接口，459

SVGAnimator 类，474，477–480

测试目录，36

SVGAnimatorWrapper 类，484

文本消息。*参见* 无线消息

TextBox 类，99，114，119

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**567**

TextField 类，109，119

UnexpectedException，246

TextListener，265

unexportObject 类，284

TextMessage 接口，376，379，380

UnicastRemoteObject 类，278，279，283

主题化，JSR 与，541

统一资源标识符（URI）

第三方认证，17

发现非接触式目标与，433

线程组，27

智能卡与，418

威胁，安全与，414

通用描述、发现与

TiledLayer 类，194，201

集成（UDDI），332

索引与，203–205

通用产品代码（UPC），440

示例游戏应用与，217

通用用户身份模块

图块，194，201

（USIM），416

时间 API，28

数据解组，342，355

TimeBase 接口，451

未解析字符指令，339

Timer 类，29，91

unrealized 状态，453

TimerTask 类，29，91

unregisterConnection 方法，390

时间戳，MMS 消息与，382

UnsupportedOperationException，269

TimeZone 类，28

UPC（通用产品代码），440

待办事项，161，174

update 方法，235，239，318，325，425

创建，183

updateLandmark 方法，508

PIMItem 类与，177

updateLocation 方法，154

移除，184

URFL 设备描述仓库，300

返回摘要，180

URI（统一资源标识符）

toByteArray 方法，140

发现非接触式目标与，433

toBytes 方法，150，154，355

智能卡与，418

ToDoList 类，533

urlEncode 方法，319

令牌智能卡，417

URL

ToneControl 接口，459，466–469

创建，295

音调，播放，466–469

语法，297，378

顶层窗口，限制，269

用户界面（UI），97–131

totalMemory 方法，27

应用屏幕，114–131

toXml 方法，343，355

项目，104–112

TRACE 方法，307

JSR 与，541

变换，Sprite 类与，206

轻量级，通过 PBP 开发，

遍历操作，126

233–241

traverse 方法，126，131

USIM（通用用户身份模块

truncate 方法，167

），416

可信应用，13

类型，101

■**V**

有效文档，XML 与，341

■**U**

矢量图像格式，470

UDDI（通用描述、发现与

verifyPIMSupport 方法，189

集成），332

VeriSign，55

UDPDatagramConnection 类，304

版本控制，记录存储与，138

UI。*参见* 用户界面

视频片段，播放，459

[www.it-ebooks.info](http://www.it-ebooks.info/)

**568**



■索引

VideoControl 接口, 459, 461, 494

WeatherWidget 类, 518

视口, 477

Web 页面, 获取, 306

可见项, 97–116

Web 服务

视觉标签识别, 432

访问, 331–372

视觉标签（视觉目标）, 431–444

从客户端视角的架构,

编码/解码, 436

331–341

JSR 与, 541

常见 Java ME 编程任务

识别/生成, 440–444

用于, 342

VisualTagCodingException, 444

HTTP 与, 306

VisualTagConnection 接口, 432, 436,

JSR 与, 541

440, 444

XML 支持, 341–372

音量, 播放与, 468

Web 服务描述语言

VolumeControl 接口, 494

(WSDL), 332

webRequest, 423

格式良好的文档, XML 与, 341

■**W**

空白字符, XML 与, 339

W3C, 470

广域网 (WAN), 基于位置的服务与, 500

WAN (广域网), 基于位置的服务与, 500

Window 类, 234, 263

WAP (无线应用协议), 341

Window 容器, 262

WAP-209-MMSEncapsulation 标准,

窗口工具包, 230

实现你自己的组件

WAP 二进制 XML (WBXML), 341

用于, 234

航点/航点元素, 519

PBP 与, 局限性, 240

WBXML (WAP 二进制 XML), 341

无线应用协议 (WAP), 341

WeatherApplet (示例应用程序),

无线消息传递, 373–407

57–76

创建消息与, 379

打包/执行, 75

加密/解密消息与,

RMI OP 与, 281

423, 429

WeatherFetcher (示例) 类, 315–325, 362

直接交换消息, 438

WeatherItem (示例) 类

, 127–131

JSR 与, 540, 541

WeatherWidget (示例应用程序),

接收消息与, 385, 391–398,

37–56, 315–325

398–407

创建, 38–51

发送消息与, 380–385,

FileConnection 类, 集成到,

391–398, 398–407

WMA 用于, 375–387, 391–407

集成 Location 和 LocationStore

无线消息 API。*参见* WMA

类到, 155–160

无线运营商, Java ME 市场

kXML 解析器与, 371

视角与, 3, 4

Location API 与, 509–518

无线通用资源文件

Location 类与, 146–150, 155–160,

(WURFL), 17

343–355

WMA (无线消息 API), 375–387

LocationParserHandler 类与,

JTWI 要求与, 529

357–361

消息编码/解码与, 381

MMAPI/SVGAPI, 484

MSA 要求与, 533

记录存储与, 146–160

权限与, 386

[www.it-ebooks.info](http://www.it-ebooks.info/)

■索引

**569**

推送注册表与, 387

XML 字符实体, 339

使用, 391–407

XML 文件, Xlet 与, 76

版本, 375

XML 解析器, 342, 355, 356

write 方法, 139, 301

XML 模式, 341

writeBoolean 方法, 139

Xparse-J 解析器, 372

writeByte 方法, 139

writeChar 方法, 139

writeChars 方法, 139

■**Y**

writeDouble 方法, 139

YAML (YAML 不是标记语言), 331

writeFloat 方法, 139

writeInt 方法, 139

writeLong 方法, 139

writeNDEF 方法, 436, 437

writeShort 方法, 139

writeUTF 方法, 139

write *X* 方法, 305

WSDL (Web 服务描述语言), 332

WURFL (无线通用资源文件), 17

■**X**

Xlet 应用程序模型, 向后兼容性, 253

Xlet 编程模型, 14

XletContext 接口, 225, 226, 242, 246

Xlet, 223–252

与 applet 对比, 254, 258

开发注意事项, 233

创建, 57–77, 225, 227–233

数据共享与, 243–251

依赖关系与, 230–233

JAR 文件与, 242

生命周期, 224

Mobility Pack 用于, 34, 57

属性/资源用于, 242

状态, 224

XletStateChangeException, 225, 226

XML

用于数据表示, 336–341

在应用程序中生成, 343–355

JSR 与, 541

MSA2 与, 536

示例文档与, 336

Web 服务与, 331, 334, 341–372

[www.it-ebooks.info](http://www.it-ebooks.info/)
