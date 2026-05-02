# 10. 游戏开发

游戏与人类历史一样悠久，并在当代社会中持续扮演着重要角色。第一款电子游戏是《乓》（Pong）。这是一款二维体育游戏，本质上仅包含一个从屏幕左侧移动的球，以及两根试图阻止球得分的长条。电子游戏的首次迭代可追溯至 1966 年。

自 1966 年以来，游戏产业不断发展，如今我们已能见到拥有精致画面和玩法的游戏。现在，甚至在手机上也能找到具备 3D 画面的游戏。在本章中，你将了解如何开始用 Scala 创建一个简单的游戏引擎。我们将使用 DSL 创建一组 API。通过这种方式，我们能够以简单易懂的方式为我们的游戏创建调用。

## 游戏团队结构

使用 DSL 意味着我们创建的软件不仅技术人员能够“阅读”和理解。要做到这一点，我们必须了解问题领域。

理解领域的第一步是了解我们软件开发中的结构和参与者。因此，在我们的案例中，第一步是理解游戏团队结构。这在设计和定义我们的 DSL 时至关重要。我们现在尝试定义不同的团队，并观察他们如何相互协作。

### 工程团队

工程团队负责所有工程工作。这包括开发游戏、设计游戏所需的工具以及维护构建流程。

工程师可分为两大类：运行时工程师（或称游戏工程师），负责开发游戏引擎本身；以及工具工程师，负责开发供引擎或其他工程师在游戏开发中使用的工具集。

通常，这些工程类别还有子类别。例如，还有专门定义人工智能（AI）的 AI 工程师，或专注于图形界面的图形工程师。近年来，还出现了其他与游戏相关的工程专业，例如服务器工程师。这些工程师专门负责游戏服务器端的开发。对于越来越多的游戏，尤其是手机游戏，服务器是驱动游戏引擎的重要组成部分。它与客户端协同工作来管理游戏。

所有上述工程师共同协作来构建一款游戏。当然，每种工程师都使用与其具体工作直接相关的特定语言。在游戏领域，创建一个好的 DSL 对于定义和促进团队间的良好沟通至关重要。一些公司会聘请技术总监，负责管理一个或多个项目、协调开发，有时也负责管理团队间的沟通。在此背景下，为设计 DSL 而拥有一个良好的通用词典，可以帮助技术总监管理沟通并更好地完成工作。

与其他软件公司一样，一些工作室设有首席技术官（CTO）。这是一个更高级别的工程师职位，本质上属于高管职位。CTO 为公司内的所有工程师提供技术方向指导。

### 美术团队

美术师是构建优秀电子游戏的另一个重要角色。与工程师一样，美术师也可分为几个不同的类别。基本分类如下：

*   3D 美术师：这些美术师专门从事 3D 工作，例如为游戏中的环境和角色建模。
*   环境美术师：负责设计游戏环境的美术师。
*   光照美术师：专门负责游戏光照设计的美术师。
*   概念美术师：负责设计新游戏或为老游戏开发新概念的美术师。
*   动画师：负责为 3D 模型制作动画，赋予游戏角色“生命”的美术师。
*   贴图美术师：在游戏设计中，模型应非常简洁，多边形数量极低（在多边形网格中尽可能使用最少的多边形）。最终设计中贴图的改进由贴图美术师完成。这些美术师为整个游戏设计贴图。

这些只是美术师种类的一部分。尽管有众多不同类型的美术师，但通常一个人只专注于上述提到的某一类。其他一些美术师可能只参与游戏开发的某个阶段，并且从不与工程师直接接触。然而，为了相互沟通，必须明确各自的角色和职责。

### 其他参与角色

实现一款电子游戏需要团队合作。除了我描述的技术角色之外，其他角色对于管理开发生命周期也至关重要。

#### 制作人

制作人负责管理开发中的非技术方面，包括管理任务、人力资源，并帮助清除开发过程中的障碍。

#### 市场营销与客户服务

游戏发布的关键方面是市场营销和客户服务，特别是对于现代手机游戏或 MMO（大型多人在线游戏）的开发而言，这有助于保持与用户的正确沟通，并帮助推动游戏变更的团队实现其目标。同时，市场营销负责广告宣传活动并促进游戏销售。

#### 游戏设计师

游戏设计师负责游戏的设计。每款游戏都有一名或多名游戏设计师负责定义游戏的所有逻辑和故事。这是一个艺术角色，对于定义好故事至关重要。一个好的游戏设计就像电影中的好导演。好导演能拍出好电影。游戏也是如此。

## 游戏引擎的定义

“游戏引擎”是用于创建电子游戏的软件。该术语首次使用是在 20 世纪 90 年代中期，用于游戏《毁灭战士》（Doom）。这是一款第一人称射击（FPS）类型的游戏，主角必须射杀屏幕上移动的一切物体。

《毁灭战士》的创新之处在于完美划分了游戏的不同领域。AI 在一个部分定义，而 3D 图形编程则在另一个部分。这种领域间的分离使得开发者能够创建一个“引擎”，即软件中可被其他游戏复用的部分。

例如，用于计算物理效果的代码区域可以被复用。这种分离催生了第一款游戏引擎。多年来，许多其他引擎相继诞生，例如 Unity 3D 和 Unreal。所有这些引擎的共同点是能够使用引擎的通用库来创建游戏。例如，AI 部分已准备好特定的范式来定义对象及其之间的交互。

多年来，游戏引擎不断发展，现在已用于特定的游戏类型。较新的引擎，如用于 FPS 的 Unreal，或用于通用 3D 目的的 Unity 3D，都具有特定的特性，有时还带有特定的语言。这意味着开发者必须频繁更换引擎，从而需要重新学习基本语法。

例如，针对平台游戏或赛车游戏的特定游戏引擎，会为物理效果以及游戏中不同对象之间的交互定义一些特定规则。这就产生了对不同库的需求。当然，所有引擎都有一些共同的特定特性，例如控制，但每个引擎本质上都是不同的。

我们可以将游戏引擎的基本功能总结如下：

*   在屏幕上渲染对象；可以是 2D 或 3D
*   管理用于对象间交互的 AI
*   允许使用控制来移动对象
*   包含一些游戏的基本物理定律

这些是一些通用且最低限度的指导原则。每个游戏引擎都始于这些简单规则，我们可以据此定义自己的游戏引擎。



### 设计我们的新 DSL 游戏引擎

到目前为止，我已经描述了什么是游戏引擎以及它必须具备的常见功能。首先，我们想要一个具有良好用户界面的游戏引擎，因此我们必须定义这个特性。其次，我们想要定义在屏幕上移动对象所需的控制。最后，我们想要一些与游戏 AI 相关的通用特性。带着这些需求，让我们开始定义一些代码。

我们必须定义的第一个功能是游戏生命周期。这本质上是一个游戏的完整周期。它可能因游戏而异，但通常，每个游戏都有一些必须执行的步骤。

生命周期与故事情节无关。例如，一个游戏的总游戏时长可能是 20 小时。游戏时长是完成游戏所需的总时间，这与故事情节严格相关。生命周期则指示了玩家在游戏执行过程中可能处于的状态。

通常，我们有一个状态“开始”，表示游戏何时开始；“暂停”，用于在游戏动作中插入暂停；“恢复”，用于在暂停游戏后恢复游戏；以及“结束”，例如当玩家死亡时。

通过添加这些基本状态，我们可以包含游戏特定的其他状态。例如，我们可以有一个“菜单”状态，在某些游戏中用于在显示游戏菜单时进入该状态。目前，我们只处理基本状态。

为了定义状态，我们创建一个特定的类。这个类用于定义游戏中的所有状态。当我们考虑游戏软件时，本质上，我们是在考虑一个复杂的状态机。机器的每个状态内部都可能包含更复杂的状态。这基本上就是我们游戏的核心。我们可以定义一些基本状态，如下所示：

*   开始
*   暂停
*   恢复
*   结束

所有这些状态基本上都是管理游戏的条件。每个游戏都有一个起点，用于管理游戏中的其他交互。我们必须编写的第一个类是用于管理状态的核心游戏类。

```
package ch10
trait StateMachine {
def start(): Unit
def resume(): Unit
def pause(): Unit
def end(): Unit
}
```

`StateMachine` 是一个 Scala 特质。特质是 Scala 中的一种接口。特质类似于 Java 接口。特质和 Java 接口的主要区别在于，特质可以被初始化并且不接受参数。

类可以扩展特质并使用它来定义特质中定义的通用方法。在我们的例子中，这个特质定义了游戏引擎的基础。这个特质定义了状态机的基本操作。

一个游戏有界面和声音，因此我们必须定义五个宏区域来构建我们的引擎，如下所示：

*   图形
*   音频
*   系统
*   窗口
*   输入

我们必须定义所有这些类以供游戏引擎使用。例如，图形类可用于定义 2D 或 3D 游戏。在我们的例子中，我们将使用 2D 图形引擎。下一步是定义游戏的主类。

### 定义通用组件

我们现在要做的是定义游戏引擎的基本组件。首先，我们定义音频。

```
package ch10
trait Audio {
abstract class Sound {
type PlaySound
def play(volume: Float): PlaySound
def play(): PlaySound = play(1f)
def loop(volume: Float): PlaySound
def loop(): PlaySound = loop(1f)
def pause(id: PlaySound): Unit
def resume(id: PlaySound): Unit
def stop(id: PlaySound): Unit
def dispose(): Unit
}
type SoundType <: Sound
def loadSoundFromSource(path: String): SoundType
abstract class Music {
def play(): Unit
def pause(): Unit
def stop(): Unit
def setVolume(volume: Float): Unit
def setLooping(isLooping: Boolean): Unit
def dispose(): Unit
}
type MusicType <: Music
def loadMusicFromSource(path: String): MusicType
}
```

`Audio` 文件包含一些特质，用于定义管理音乐的方法和功能。我们可以看到这些方法是不言自明的。我们可以简单地阅读并理解该方法的作用。

另一个我们必须定义的类是与图形相关的类。为了定义图形组件，我们添加了一个辅助类。这用于维护该类的一些通用方法。代码如下：

```
package ch10
trait GraphicHelper {
this: Graphic =>
implicit class UpdateCanvas(canvas: Canvas) {
def RepeatBitmap(bitmap: Bitmap, x: Int, y: Int, width: Int, height: Int): Unit = {
val imageWidth = bitmap.width
val imageHeight = bitmap.height
val columns: Int = width/imageWidth
val rows: Int = height/imageHeight
for(i  0) {
for(i  0) {
for(i  0 && missingHeight > 0) {
canvas.drawBitmap(bitmap, columns*imageWidth, rows*imageHeight, 0, 0, missingWidth, missingHeight)
}
}
def drawBitmap(region: ImageRegion, x: Int, y: Int): Unit = {
canvas.drawBitmap(region.bitmap, x, y, region.x, region.y, region.width, region.height)
}
}
case class ImageRegion(
val bitmap: Bitmap, val x: Int, val y: Int,
val width: Int, val height: Int) {
def this(bitmap: Bitmap) = this(bitmap, 0, 0, bitmap.width, bitmap.height)
}
class Animation(
var frameDuration: Long,
frames: Array[ImageRegion],
var playMode: Animation.PlayMode = Animation.Normal
)
{
def currentFrame(time: Long): ImageRegion = {
val frameNumber: Int = ((time/frameDuration) % Int.MaxValue).toInt
val frameIndex = playMode match {
case Animation.Normal =>
math.min(frames.size - 1, frameNumber)
case Animation.Reversed =>
math.max(frames.size - frameNumber - 1, 0)
case Animation.Loop =>
frameNumber % frames.size
case Animation.LoopReversed =>
frames.length - (frameNumber % frames.size) - 1
frames(frameIndex)
}
def isCompleted(time: Long): Boolean = time > animationDuration
def animationDuration: Long = frames.size*frameDuration
}
object Animation {
sealed trait Play
case object Normal extends Play
case object Reversed extends Play
case object Loop extends Play
case object LoopReversed extends Play
}
}
```

这段代码有点复杂，但在其中，我们找到了管理游戏图形所需的每一个接口片段。特质 `GraphicHelper` 引入了 Scala 2.10 的一个新功能：隐式类。

这个功能非常有用，因为它有助于定义一个类在创建时拥有一个可用的主构造函数。这意味着，例如，我们可以使用这个类通过不同的构造函数创建不同类型的游戏对象，但我们都使用相同类型的调用来调用它们。

注意

隐式类是 Scala 2.10 中引入的一种新型类。隐式类是一个普通的 Scala 类，在 `class` 关键字前加上 `implicit` 关键字创建。隐式类遵循与其他任何类相同的规则。`implicit` 使得该类的主构造函数在作用域内需要隐式转换时可用。隐式类只能定义在对象、特质或类内部。



`GraphicHelper` 定义了一个类 `UpdateCanvas`，用于覆盖游戏的画布。我们可以在所有游戏引擎中看到，那些被设计用于引擎其他部分的元素。`UpdateCanvas` 类被设计用于不同类型的图形上下文中，例如，在 Android 应用或 AWT 图形界面中绘制图形。在我们的案例中，代码的关键部分是我们给方法起的名称。我们必须始终记住，DSL 不仅仅是一种编程技术，更重要的是，我们用来定义方法和软件内部变量的名称。

`GraphicHelper` 内部还定义了其他一些类，例如 `Animation` 类。该类用于管理游戏过程中的动画。

```
class Animation(
var frameDuration: Long,
frames: Array[ImageRegion],
var playMode: Animation.PlayMode = Animation.Normal
)
{
def currentFrame(time: Long): ImageRegion = {
val frameNumber: Int = ((time/frameDuration) % Int.MaxValue).toInt
val frameIndex = playMode match {
case Animation.Normal =>
math.min(frames.size - 1, frameNumber)
case Animation.Reversed =>
math.max(frames.size - frameNumber - 1, 0)
case Animation.Loop =>
frameNumber % frames.size
case Animation.LoopReversed =>
frames.length - (frameNumber % frames.size) - 1
}
frames(frameIndex)
}
def IsCompleted(time: Long): Boolean = time > animationDuration
def Duration: Long = frames.size*frameDuration
}
```

该类有两个方法。`currentFrame` 用于定义动画的帧索引。动画无非是一组在短时间内依次播放的帧。我们可以定义不同类型的动画，这些类型由 `playMode` 标识。帧的计算是通过使用数学库来实现的。我们为每一帧定义了最小值和最大值。

该类的代码稍微有点复杂。首先，我们定义想要创建的动画类型。这是在创建类时完成的，我们传递以下参数：

```
var frameDuration: Long,
frames: Array[ImageRegion],
var playMode: Animation.PlayMode = Animation.Normal
```

这些参数用于初始化该类。现在，我们可以看到用于定义动画的值。动画是一组帧。帧是单个图像。我们可以定义动画的速度，即实现动画所需的每秒帧数。动画的正常速度是 24 fps（每秒帧数）。三个参数用于定义这个值。通常，帧是在所谓的精灵表中设计的。这个文件包含我们想要创建的动画的一组图像。每个图像都有一个尺寸，这个尺寸用于计算动画中有多少帧。`ImageRegion` 本质上定义了帧的“区域”。

这个工具类被图形组件使用。

```
package ch10
import util.Loader
trait GraphicsSystem extends GraphicHelper {
this: SystemProvider =>
trait Graphics {
def loadImage(path: System.ResourcePath): Loader[Bitmap]
}
val Graphics: Graphics
//定义并抽象类 Bitmap，用于定义我们需要使用的图像大小
abstract class AbstractBitmap {
def height: Int
def width: Int
}
type Bitmap  A): A
def translate(x: Int, y: Int): Unit
def rotate(theta: Double): Unit
def scale(sx: Double, sy: Double): Unit
def clipRect(x: Int, y: Int, width: Int, height: Int): Unit
def drawBitmap(bitmap: Bitmap, x: Int, y: Int): Unit
def drawBitmap(bitmap: Bitmap, dx: Int, dy: Int, sx: Int, sy: Int, width: Int, height: Int): Unit
def drawRect(x: Int, y: Int, width: Int, height: Int, paint: Paint): Unit
def drawOval(x: Int, y: Int, width: Int, height: Int, paint: Paint): Unit
def drawLine(x1: Int, y1: Int, x2: Int, y2: Int, paint: Paint): Unit
def drawCircle(x: Int, y: Int, radius: Int, paint: Paint): Unit = drawOval(x, y, 2*radius, 2*radius, paint)
def drawString(str: String, x: Int, y: Int, paint: Paint): Unit
def drawText(text: TextLayout, x: Int, y: Int): Unit
def drawColor(color: Color): Unit
def clearRect(x: Int, y: Int, width: Int, height: Int): Unit
def clear(): Unit = clearRect(0, 0, width, height)
def renderText(text: String, width: Int, paint: Paint): TextLayout
}
type Canvas <: AbstractCanvas
def getScreenCanvas: Canvas
def releaseScreenCanvas(canvas: Canvas): Unit
}
```

现在我们可以看到 `GraphicsSystem` 是如何使用 `GraphicHelper` 的。它所做的就是定义所有在屏幕上绘制图元的方法。在这种情况下，我们使用英文来定义方法。这有助于我们在需要使用它的时候。

这段代码有时需要解释，我们现在就来解释。该类首先定义了 `GraphicsSystem`。这是对 `GraphicHelper` 特质的扩展。正如我们所见，特质类似于 Java 接口。我们使用的代码如下：

```
trait Graphics {
def loadImage(path: System.ResourcePath): Loader[Bitmap]
}
```

这个特质定义了 `loadImage` 方法。该方法表示我们从某个路径加载图像。这是每个游戏引擎的基础。

现在，引擎必须知道图像的尺寸才能计算动画的持续时间。因此，我们创建了一个名为 `AbstractBitmap` 的抽象类。这个类有两个方法来定义位图的尺寸：高度和长度。这些用于精确标识位图。

对于图形，我们还需要定义字体。为此，我们创建了另一个抽象类来定义我们使用的字体类型。这个类是 `AbstractFont`。现在，我们可以定义字体所需的所有排版属性。

在该类中，我们定义了创建图形所需的所有图形组件。这个类的核心是 `AbstractCanvas` 特质。这个特质本质上是软件的核心。我们使用这个特质来定义我们想要绘制的每种图像类型。例如，我们定义了圆形、矩形等。所有这些元素都用于定义用户界面的元素。需要注意的一点是 `type` 的使用。在 Scala 中，`type` 用于为某些复杂的代码片段创建别名。在我们的案例中，我们使用 `type` 来连接到某种不同的类。

### 其他组件

到目前为止，我们已经定义了图形组件，但在游戏引擎中，我们必须定义其他组件来控制游戏。这个类实际上会拦截键盘按键或鼠标操作，并对特定事件做出响应，如下所示：


```scala
package ch10
trait InputHelper extends StateMachine {
this: Input =>
//输入助手用于定义游戏中所有输入方法，在此类中我们定义了游戏中可使用的每种输入类型，例如键盘和鼠标
object InputHelper {
import Input._
//此方法用于定义"事件"，事件用于定义游戏中发生的情况，类似于常规图形开发中的事件。存在一个轮询机制和一组事件，软件必须能够处理每个事件，并以正确的功能响应事件
def pollEvent(): Option[InputEvent] = {
val ev = Input.pollEvent()
ev foreach processEvent
ev
}
def processEvents(function: (InputEvent) => Unit): Unit = {
var oev = Input.pollEvent()
while(!oev.isEmpty) {
val ev = oev.get
processEvent(ev)
function(ev)
oev = pollEvent()
}
}
//processEvent 方法用于识别事件，该方法主要检查触发了什么事件，然后创建相应的类来响应事件本身
def processEvent(event: InputEvent): Unit = event match {
case KeyDownEvent(key) => setKeyboardState(key, true)
case KeyUpEvent(key) => setKeyboardState(key, false)
case MouseMovedEvent(x, y) => Inputs.Mouse.position = (x, y)
case MouseDownEvent(x, y, mouseButton) => {
Inputs.Mouse.position = (x, y)
mouseButton match {
case Input.MouseButtons.Left =>
Inputs.Buttons.leftPressed = true
case Input.MouseButtons.Right =>
Inputs.Buttons.rightPressed = true
case Input.MouseButtons.Middle =>
Inputs.Buttons.middlePressed = true
}
}
case MouseUpEvent(x, y, mouseButton) => {
Inputs.Mouse.position = (x, y)
mouseButton match {
case Input.MouseButtons.Left =>
Inputs.Buttons.leftPressed = false
case Input.MouseButtons.Right =>
Inputs.Buttons.rightPressed = false
case Input.MouseButtons.Middle =>
Inputs.Buttons.middlePressed = false
}
}
case MouseScrolledEvent(amount) => ()
case TouchMovedEvent(x, y, pointer) => (
Inputs.Touch.pointerPressed += (pointer -> (x, y))
)
case TouchDownEvent(x, y, pointer) => (
Inputs.Touch.pointerPressed += (pointer -> (x, y))
)
case TouchUpEvent(x, y, pointer) => (
Inputs.Touch.pointerPressed -= pointer
)
}
//此方法用于定义键盘的输入状态，当我们按下某个键时，实际上改变了该键的状态。此类用于拦截该操作并改变状态，状态的改变用于在游戏中产生响应
private def setKeyboardState(key: Input.Keys.Key, down: Boolean): Unit = key match {
case Keys.Left => Inputs.Keyboard.left = down
case Keys.Up => Inputs.Keyboard.up = down
case Keys.Right => Inputs.Keyboard.right = down
case Keys.Down => Inputs.Keyboard.down = down
case Keys.Space => Inputs.Keyboard.space = down
case Keys.ButtonStart => Inputs.Buttons.startPressed = down
case Keys.ButtonSelect => Inputs.Buttons.selectPressed = down
case Keys.ButtonBack => Inputs.Buttons.backPressed = down
case Keys.ButtonMenu => Inputs.Buttons.menuPressed = down
case Keys.A => Inputs.Keyboard.a = down
case Keys.B => Inputs.Keyboard.b = down
case Keys.C => Inputs.Keyboard.c = down
case Keys.D => Inputs.Keyboard.d = down
case Keys.E => Inputs.Keyboard.e = down
case Keys.F => Inputs.Keyboard.f = down
case Keys.G => Inputs.Keyboard.g = down
case Keys.H => Inputs.Keyboard.h = down
case Keys.I => Inputs.Keyboard.i = down
case Keys.J => Inputs.Keyboard.j = down
case Keys.K => Inputs.Keyboard.k = down
case Keys.L => Inputs.Keyboard.l = down
case Keys.M => Inputs.Keyboard.m = down
case Keys.N => Inputs.Keyboard.n = down
case Keys.O => Inputs.Keyboard.o = down
case Keys.P => Inputs.Keyboard.p = down
case Keys.Q => Inputs.Keyboard.q = down
case Keys.R => Inputs.Keyboard.r = down
case Keys.S => Inputs.Keyboard.s = down
case Keys.T => Inputs.Keyboard.t = down
case Keys.U => Inputs.Keyboard.u = down
case Keys.V => Inputs.Keyboard.v = down
case Keys.W => Inputs.Keyboard.w = down
case Keys.X => Inputs.Keyboard.x = down
case Keys.Y => Inputs.Keyboard.y = down
case Keys.Z => Inputs.Keyboard.z = down
case Keys.Num0 => Inputs.Keyboard.num0 = down
case Keys.Num1 => Inputs.Keyboard.num1 = down
case Keys.Num2 => Inputs.Keyboard.num2 = down
case Keys.Num3 => Inputs.Keyboard.num3 = down
case Keys.Num4 => Inputs.Keyboard.num4 = down
case Keys.Num5 => Inputs.Keyboard.num5 = down
case Keys.Num6 => Inputs.Keyboard.num6 = down
case Keys.Num7 => Inputs.Keyboard.num7 = down
case Keys.Num8 => Inputs.Keyboard.num8 = down
case Keys.Num9 => Inputs.Keyboard.num9 = down
}
}
//Input 对象用于定义游戏的输入，输入可以通过键盘或鼠标发送
object Input {
object ButtonPressed {
var left: Boolean = false
var middle: Boolean = false
var right: Boolean = false
var back: Boolean = false
var menu: Boolean = false
var start: Boolean = false
var select: Boolean = false
}
//此对象定义了触摸功能，用于识别玩家在游戏过程中指向和触摸的位置
object Touch {
var pointerPressed: Map[Int, (Int, Int)] = Map()
def pressed: Option[(Int, Int)] = pointerPressed.toSeq.headOption.map(_._2)
def pressed(pointer: Int): Option[(Int, Int)] = pointerPressed.get(pointer)
def allPressed: Seq[(Int, (Int, Int))] = pointerPressed.toSeq
}
//Mouse 对象用于识别和描述鼠标，实际上每个游戏都会使用鼠标进行游玩，因此我们的引擎需要一个 Mouse 对象
object Mouse {
var position: (Int, Int) = (0, 0)
def pressed: Option[(Int, Int)] = leftPressed
def leftPressed: Option[(Int, Int)] = if(Buttons.leftPressed) Some(position) else None
def rightPressed: Option[(Int, Int)] = if(Buttons.rightPressed) Some(position) else None
}
//此对象模拟键盘，我们在此基本上重新创建了键盘的所有布局
//我们使用布尔值定义按键，用于表示按键是否被按下
object Keyboard {
var left: Boolean = false
var right: Boolean = false
var up: Boolean = false
var down: Boolean = false
var space: Boolean = false
var a: Boolean = false
var b: Boolean = false
var c: Boolean = false
var d: Boolean = false
var e: Boolean = false
var f: Boolean = false
var g: Boolean = false
var h: Boolean = false
var i: Boolean = false
var j: Boolean = false
var k: Boolean = false
var l: Boolean = false
var m: Boolean = false
var n: Boolean = false
var o: Boolean = false
var p: Boolean = false
var q: Boolean = false
var r: Boolean = false
var s: Boolean = false
var t: Boolean = false
var u: Boolean = false
var v: Boolean = false
var w: Boolean = false
var x: Boolean = false
var y: Boolean = false
var z: Boolean = false
var num0: Boolean = false
var num1: Boolean = false
var num2: Boolean = false
var num3: Boolean = false
var num4: Boolean = false
var num5: Boolean = false
var num6: Boolean = false
var num7: Boolean = false
var num8: Boolean = false
var num9: Boolean = false
}
//此类用于识别鼠标的使用情况，现在我们看到使用了 Mouse 对象，该对象告诉程序哪个按键被按下
object PointDevice {
def pressed: Option[(Int, Int)] = Mouse.leftPressed.orElse(Touch.pressed)
}
}
}
```

此方法定义了所有事件，用于定义游戏中触发的所有按键和事件。这使得引擎能够响应按下的按键或鼠标点击。此类结构简单，是 DSL 关键原则的另一个示例：定义的方法可以像普通英语一样阅读。


## 结论

到目前为止，你已经看到了 DSL 在不同领域的应用。我最后讨论的是电子游戏领域。在本章中，我着重强调了 DSL 如何在日常开发中使用，不仅用于定义特定模式或使用特定技术，例如，还可以用通俗易懂的英文为方法和变量命名。DSL 可以从简单的步骤开始，融入日常开发。我们通过 DSL 能够实现的最重要的改变，就是开始用通俗易懂的英文来思考方法和变量。之后，我们就可以开始改进所涉及的方法和模式。

