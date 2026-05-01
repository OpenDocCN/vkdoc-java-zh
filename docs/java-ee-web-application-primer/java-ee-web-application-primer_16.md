# 16. 使用 JavaScript 让网页动起来

JavaScript 是浏览器的语言。它是一种面向对象的编程语言。虽然 JavaScript 看起来很像 Java（因为两者都基于 C 和 C++），但它们之间毫无关联。JavaScript 通常用于以编程方式与 HTML 页面交互，这是通过操作 DOM（文档对象模型）来实现的。所有主流浏览器都支持 JavaScript。

将 JavaScript 包含在网页中，只需将脚本放在 `<script>` 和 `</script>` 标签之间即可。你可以将脚本标签放在 `<head>` 部分或页面底部。如果将 JavaScript 放在页面顶部或 `<head>` 标签之间，用户可能会看到几秒钟的空白页面。不过，一旦页面加载完成，所有功能从第一秒起就能完全正常运行。如果将 JavaScript 放在页面底部，页面加载速度会显得更快，但 JavaScript 需要等到页面（和脚本）完全加载后才会运行。JavaScript 也可以保存在一个文本文件中（这种情况下不需要脚本标签），并在页面的 `<head>` 部分引用它。这样你就可以在多个页面上复用同一个 JavaScript。从维护角度来看，使用链接文件更好，因为所有 JavaScript 都位于一个位置，便于更新。

由于许多开发者觉得 JavaScript 使用起来有挑战性，多年来开发了各种库来简化 JavaScript 的常规任务。jQuery（[`http://www.jquery.com`](http://www.jquery.com)）适用于所有支持 JavaScript 的浏览器，并使 JavaScript 的使用更加一致。jQuery 在 Bootstrap 中被广泛使用。我们稍后会介绍 Bootstrap；它让使用 jQuery、HTML 和 CSS 变得更加有趣。

注意

操作 DOM 是 JavaScript 最强大的用途之一。通过 DOM，你可以遍历并修改整个页面，从简单地添加一个元素到重新排列页面上的多个区域。DOM 将文档分解成一个节点树。

## 使用 JavaScript 验证表单

清单 16-1 中的表单用于向 Bullhorn 提交帖子。在用户尝试提交之前，应对帖子进行验证。JavaScript 允许我们在浏览器端进行验证。清单中展示了用于验证表单的 JavaScript 方法，其中包含一个方法 `validate()`。JavaScript 应放在页面底部的 `<script>...</script>` 标签之间，紧邻结束的 `</body>` 标签之前。将脚本放在它所引用的元素之后，可以确保在执行脚本之前，这些元素已被 DOM 创建。`validate` 方法会查找 ID 为 `posttext` 的元素，如果该文本框的长度为 0（空帖子），则返回 false。返回 false 将阻止表单提交。

```
function validate() {
valid = true;
if ($('#posttext').val().length==0){
alert("你不能提交空帖子。");
valid = false;
}
return valid;
}
清单 16-1
一个用于验证表单的 JavaScript 函数，可以放在网页底部的脚本标签之间，紧邻结束的 body 标签之前
```

将使用上述验证脚本的 HTML 表单放在网页的 `<body>...</body>` 标签内，并位于 JavaScript 之前。当点击提交按钮时，该表单会调用脚本。如果 `validate` 方法返回 false，则表单不会被提交。参见清单 16-2。`form` 标签的 `onsubmit` 属性会调用 JavaScript 函数来验证表单。

```
创建新帖子（141 字符）：

清单 16-2
用于提交帖子的表单
```

### 显示文本框中的字符数

我们还可以使用 JavaScript 来统计剩余字符数，并在用户输入时动态更新网页。这是 JavaScript 强大功能的一个绝佳示例。它展示了 JavaScript 可以在浏览器端操作网页。这个 JavaScript 函数会在文档就绪时加载。文档在完全渲染并且所有 DOM 已从 Web 服务器下载到浏览器后即视为就绪。然后该函数会被创建。此函数会将 ID 为 `textarea_feedback` 的元素的 HTML 属性设置为“剩余 XX 字符”，其中 XX 是从最大长度 141 中减去后的剩余字符数。在 `document.ready` 中，ID 为 `posttext` 的元素的 `keyup` 事件被修改，以包含另一个函数，该函数统计剩余字符数并将其显示在 `textarea_feedback` 元素中。参见清单 16-3。

```
$(document).ready(function() {
var text_max = 141;
$('#textarea_feedback').html
(text_max + ' 字符剩余');
$('#posttext').keyup(function() {
var text_length = $('#posttext').val().length;
var text_remaining = text_max - text_length;
$('#textarea_feedback').html(text_remaining + '
字符剩余');
});
});
清单 16-3
返回文本框中剩余字符数的 JavaScript
```

