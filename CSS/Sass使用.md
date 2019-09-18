# Sass使用

https://www.sasscss.com/getting-started/

在线转换

[https://www.sassmeister.com](https://www.sassmeister.com/)

阮一峰博客《SASS用法指南》

http://www.ruanyifeng.com/blog/2012/06/sass.html



## 变量
```scss
$highlight-color: #F90;
.selected {
  border: 1px solid $highlight-color;
}

//编译后

.selected {
  border: 1px solid #F90;
}

```
在声明变量时，变量值也可以引用其他变量。
```scss
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}

//编译后

.selected {
  border: 1px solid #F90;
}
```
sass的变量名可以与css中的属性名和选择器名称相同，包括中划线和下划线。

sass并不想强迫任何人一定使用中划线或下划线，所以这两种用法相互兼容。用中划线声明的变量可以使用下划线的方式引用，反之亦然。

在sass的大 多数地方，中划线命名的内容和下划线命名的内容是互通的，除了变量，也包括对混合器和Sass函数的命名。

```scss
$link-color: blue;
a {
  color: $link_color;
}

//编译后

a {
  color: blue;
}
```
> 在sass中纯css部分不互通，比如类名、ID或属性名。

## 嵌套

```scss
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}

 /* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```
### 父选择器的标识符&:

```scss
article a {
  color: blue;
  &:hover { color: red }
}

 /* 编译后 */
article a { color: blue }
article a:hover { color: red }
```
同时父选择器标识符还有另外一种用法，你可以在父选择器之前添加选择器。举例来说，当用户在使用IE浏览器时，你会通过JavaScript在<body>标签上添加一个ie的类名，为这种情况编写特殊的样式如下：
```scss
#content aside {
  color: red;
  body.ie & { color: green }
}

/*编译后*/
#content aside {color: red};
body.ie #content aside { color: green }
```
### 群组选择器的嵌套:

```scss
.container {
  h1, h2, h3 {margin-bottom: .8em}
}

/*编译后*/
.container h1, .container h2, .container h3 { margin-bottom: .8em }
```
```scss
nav, aside {
  a {color: blue}
}

/*编译后*/
nav a, aside a {color: blue}
```

子组合选择器和同层组合选择器：>、+和~:

```scss
// 选择article下的所有命中section选择器的元素
article section { margin: 5px }

// 选择article下紧跟着的子元素中命中section选择器的元素
article > section { border: 1px solid #ccc }

// 用同层相邻组合选择器+选择header元素后紧跟的p元素
header + p { font-size: 1.1em }

// 同层全体组合选择器~，选择所有跟在article后的同层article元素，不管它们之间隔了多少其他元素
article ~ article { border-top: 1px dashed #ccc }
```

这些组合选择器可以毫不费力地应用到sass的规则嵌套中
```scss
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}

// 编译后
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

### 嵌套属性：

```scss
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```
嵌套属性的规则是这样的：把属性名从中划线-的地方断开，在根属性后边添加一个冒号:，紧跟一个{ }块，把子属性部分写在这个{ }块中。就像css选择器嵌套一样，sass会把你的子属性一一解开，把根属性和子属性部分通过中划线-连接起来，最后生成的效果与你手动一遍遍写的css样式一样：
```scss
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```
对于属性的缩写形式，你甚至可以像下边这样来嵌套，指明例外规则：
```scss
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}
```
这比下边这种同等样式的写法要好：
```scss
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

## 导入

css有一个特别不常用的特性，即@import规则，它允许在一个css文件中导入其他css文件。然而，后果是只有执行到@import时，浏览器才会去下载其他css文件，这导致页面加载起来特别慢。

sass也有一个@import规则，但不同的是，sass的@import规则在生成css文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个css文件中，而无需发起额外的下载请求。

### 使用SASS部分文件

当通过@import把sass样式分散到多个文件时，你通常只想生成少数几个css文件。那些专门为@import命令而编写的sass文件，并不需要生成对应的独立css文件，这样的sass文件称为局部文件。对此，sass有一个特殊的约定来命名这些文件。

此约定即，**sass局部文件的文件名以下划线开头。这样，sass就不会在编译时单独编译这个文件输出css，而只把这个文件用作导入**。当你@import一个局部文件时，还可以不写文件的全名，即省略文件名开头的下划线。举例来说，你想导入themes/_night-sky.scss这个局部文件里的变量，你只需在样式表中写@import "themes/night-sky";。

### 默认变量值

含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```scss
$fancybox-width: 400px !default;
.fancybox {
width: $fancybox-width;
}
```
在上例中，如果用户在导入你的sass局部文件之前声明了一个$fancybox-width变量，那么你的局部文件中对$fancybox-width赋值400px的操作就无效。如果用户没有做这样的声明，则$fancybox-width将默认为400px。

### 嵌套导入

跟原生的css不同，sass允许@import命令写在css规则内。这种导入方式下，生成对应的css文件时，局部文件会被直接插入到css规则内导入它的地方。举例说明，有一个名为_blue-theme.scss的局部文件，内容如下：
```scss
aside {
  background: blue;
  color: white;
}
```
然后把它导入到一个CSS规则内，如下所示：
```scss
.blue-theme {@import "blue-theme"}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
```
被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。这些变量和混合器不会全局有效，这样我们就可以通过嵌套导入只对站点中某一特定区域运用某种颜色主题或其他通过变量配置的样式。

### 原生的CSS导入

由于sass兼容原生的css，所以它也支持原生的CSS@import

下列三种情况下会生成原生的CSS@import，尽管这会造成浏览器解析css时的额外下载：

* 被导入文件的名字以.css结尾；
* 被导入文件的名字是一个URL地址（比如http://www.sass.hk/css/css.css），由此可用谷歌字体API提供的相应服务；
* 被导入文件的名字是CSS的url()值。

不能用sass的@import直接导入一个原始的css文件，因为sass会认为你想用css原生的@import。但是，因为sass的语法完全兼容css，所以你可以把原始的css文件改名为.scss后缀，即可直接导入了。

## 注释
```scss
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
```
css的标准注释格式/* ... */内的注释内容亦可在生成的css文件中抹去。当注释出现在原生css不允许的地方，如在css属性或选择器中，sass将不知如何将其生成到对应css文件中的相应位置，于是这些注释被抹掉。
```scss
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```

## 混合器
```scss
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

### 何时使用混合器

* 如果你发现自己在不停地重复一段样式，那就应该把这段样式构造成优良的混合器，尤其是这段样式本身就是一个逻辑单元，比如说是一组放在一起有意义的属性。

* 判断一组属性是否应该组合成一个混合器，一条经验法则就是你能否为这个混合器想出一个好的名字。如果你能找到一个很好的短名字来描述这些属性修饰的样式，比如rounded-corners`fancy-font或者no-bullets`，那么往往能够构造一个合适的混合器。如果你找不到，这时候构造一个混合器可能并不合适。

混合器在某些方面跟css类很像。最重要的区别就是类名是在html文件中应用的，而混合器是在样式表中应用的。这就意味着**类名具有语义化含义**，而不仅仅是**一种展示性的描述**：用来描述html元素的含义而不是html元素的外观。而另一方面，混合器是**展示性的描述**，用来描述一条css规则应用之后会产生怎样的效果。


### 混合器中的CSS规则

混合器中不仅可以包含属性，也可以包含css规则，包含选择器和选择器中的属性，如下代码:
```scss
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}

ul.plain {
  color: #444;
  @include no-bullets;
}

// 编译后
ul.plain {
  color: #444;
  list-style: none;
}
ul.plain li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```

### 给混合器传参

```scss
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是：

a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```
当你@include混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，sass允许通过语法$name: value的形式指定每个参数的值。这种形式的传参，**参数顺序就不必再在乎了，只需要保证没有漏掉参数即可**：
```scss
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```
### 默认参数值

参数默认值使用$name: default-value的声明形式，默认值可以是任何有效的css属性值，甚至是其他参数的引用，如下代码：

```scss
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```



## 继承

设计一个页面时常常遇到这种情况：当一个样式类（class）含有另一个类的所有样式，并且它自己的特定样式。处理这种最常见的方法是在HTML同时使用一个通用样式类和特殊样式类。例如，假设我们设计需要一个普通错误的样式和一个严重错误的样式。我们可以类似这样写：

```html
<div class="error seriousError">
  Oh no! You've been hacked!
</div>
```

我们的样式如下

```css
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  border-width: 3px;
}
```

不幸的是，这意味着，我们必须时刻记住使用`.seriousError`的时候需要搭配使用`.error`。
这对于维护来说是一个负担，甚至导致棘手的错误，并且导致无语意的样式。

`@extend` 指令避免这些问题，告诉 Sass 一个选择器的样式应该继承另一选择器。 例如：

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

编译为：

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd;
}

.seriousError {
  border-width: 3px;
}
```

这意味着`.error`说定义的所有样式也适用于`.seriousError`，除了`.seriousError`的特定样式。相当于，每个带有`.seriousError`类的元素也带有`.error`类。

其他使用了`.error` 规则也会同样继承给`.seriousError`，例如，如果我们有特殊错误样式的hack：

```css
.error.intrusion {
  background-image: url("/image/hacked.png");
}
```

然后`<div class="seriousError intrusion">`也同样会使用了 `hacked.png` 背景。

### 它是如何工作的（How it Works）

`@extend`通过在样式表中出现被扩展选择器（例如`.error`）的地方插入扩展选择器（例如`.seriousError`）。比如上面的例子：

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.error.intrusion {
  background-image: url("/image/hacked.png");
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

编译为：

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd; }

.error.intrusion, .seriousError.intrusion {
  background-image: url("/image/hacked.png"); }

.seriousError {
  border-width: 3px; }
```

当合并选择器时，`@extend` 会很聪明地避免不必要的重复，所以像`.seriousError.seriousError` 将转换为 `.seriousError`，此外，她不会生成不能匹配任何元素的选择器（比如 `#main#footer` ）。

### 扩展复杂的选择器（Extending Complex Selectors）

Class 选择器并不是唯一可以被延伸 (extend) 的，Sass 允许延伸任何定义给单个元素的选择器，比如 .special.cool，a:hover 或者 a.user[href^="http://"] 等，例如：

类（class）选择，并不是唯一可以扩展。她可以扩展任何定义给单个元素的选择器，如`.special.cool`, `a:hover`, 或 `a.user[href^="http://"]`。 例如：

```scss
.hoverlink {
  @extend a:hover;
}
```

同带 class 元素一样，这意味着，`a:hover`定义的样式同样也适用于`.hoverlink`。例如：

```scss
.hoverlink {
  @extend a:hover;
}
a:hover {
  text-decoration: underline;
}
```

编译为：

```css
a:hover, .hoverlink {
  text-decoration: underline; }
```

与上面 `.error.intrusion` 的例子一样， `a:hover` 中所有的样式将继承给 `.hoverlink`，甚至包括其他使用到她的样式，例如：

```scss
.hoverlink {
  @extend a:hover;
}
.comment a.user:hover {
  font-weight: bold;
}
```

编译为：

```css
.comment a.user:hover, .comment .user.hoverlink {
  font-weight: bold; }
```

### 多重扩展 (Multiple Extends)

同一个选择器可以扩展多个选择器。这意味着，它继承了被扩展选择器的所有样式。例如：

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.attention {
  font-size: 3em;
  background-color: #ff0;
}
.seriousError {
  @extend .error;
  @extend .attention;
  border-width: 3px;
}
```

编译为：

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd; }

.attention, .seriousError {
  font-size: 3em;
  background-color: #ff0; }

.seriousError {
  border-width: 3px; }
```

每个带`.seriousError`类的元素也有`.error`类和`.attention`类。
因此，定义在文档后面的样式优先级高于定义在文档前面的样式：`.seriousError`的背景颜色是`#ff0`，而非`#fdd`，因为 `.attention` 是在 `.error` 后面定义。

多重扩展也可以用逗号分隔的选择器列表（list）写入。例如，`@extend .error, .attention`等同于`@extend .error; @extend .attention`。

### 链式扩展（Chaining Extends）

一个选择器可以扩展另一个选择器，另一个选择器又扩展的第三选择器选择。 例如：

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
.criticalError {
  @extend .seriousError;
  position: fixed;
  top: 10%;
  bottom: 10%;
  left: 10%;
  right: 10%;
}
```

现在，带 `.seriousError` 类的每个元素将包含 `.error` 类，而带 `.criticalError` 类的每个元素不仅包含 `.criticalError`类也会同时包含 `.error` 类，上面的代码编译为：

```css
.error, .seriousError, .criticalError {
  border: 1px #f00;
  background-color: #fdd; }

.seriousError, .criticalError {
  border-width: 3px; }

.criticalError {
  position: fixed;
  top: 10%;
  bottom: 10%;
  left: 10%;
  right: 10%; }
```

### 选择器序列 (Selector Sequences)

选择器序列，比如`.foo .bar` 或 `.foo + .bar`，目前还不能作为扩展。但是，选择器序列本身可以使用`@extend`。例如：

```scss
#fake-links .link {
  @extend a;
}

a {
  color: blue;
  &:hover {
    text-decoration: underline;
  }
}
```

将被编译为：

```css
a, #fake-links .link {
  color: blue; }
  a:hover, #fake-links .link:hover {
    text-decoration: underline; }
```

#### 合并选择器序列 (Merging Selector Sequences)

有时，选择器序列扩展另一个选择器，这个选择器出现在另一选择器序列中。在这种情况下，这两个选择器序列需要合并。例如：

```scss
#admin .tabbar a {
  font-weight: bold;
}
#demo .overview .fakelink {
  @extend a;
}
```

技术上讲能够生成所有匹配条件的结果，但是这样生成的样式表太复杂了，上面这个简单的例子就可能有 10 种结果。所以，Sass 只会编译输出有用的选择器。

当两个列 (sequence) 合并时，如果没有包含相同的选择器，将生成两个新选择器：第一列出现在第二列之前，或者第二列出现在第一列之前。例如：

```scss
#admin .tabbar a {
  font-weight: bold;
}
#demo .overview .fakelink {
  @extend a;
}
```

编译为：

```css
#admin .tabbar a,
#admin .tabbar #demo .overview .fakelink,
#demo .overview #admin .tabbar .fakelink {
  font-weight: bold; }
```

如果两个列 (sequence) 包含了相同的选择器，相同部分将会合并在一起，其他部分交替输出。在下面的例子里，两个列都包含 `#admin`，输出结果中它们合并在了一起：

```scss
#admin .tabbar a {
  font-weight: bold;
}
#admin .overview .fakelink {
  @extend a;
}
```

编译为：

```css
#admin .tabbar a,
#admin .tabbar .overview .fakelink,
#admin .overview .tabbar .fakelink {
  font-weight: bold; }
```

### `@extend`-Only 选择器 (`@extend`-Only Selectors)

有时候你只会想写一个 `@extend` 扩展样式类，不想直接在你的HTML中使用。在写一个 Sass 样式库时，这是特别有用，如果他们需要，在这里你可以提供 `@extend` 扩展样式给用户，如果他们不需要，直接被忽视。

对于这种情况，如果使用普通的样式类，在你你最终生成的样式表中，会有很多额外（愚人码头注：无用）的CSS，并且在HTML被使用时，和其他样式类结合的时候容易造成冲突。这就是 Sass 为什么支持"占位选择器"的原因（例如，`%foo`）。

占位选择器看起来很像普通的 class 和 id 选择器，只是 `#` 或 `.` 被替换成了 `%`。他可以像 class 或者 id 选择器那样使用，而它本身的规则，不会被编译到 CSS 文件中。例如：

```scss
// This ruleset won't be rendered on its own.
#context a%extreme {
  color: blue;
  font-weight: bold;
  font-size: 2em;
}
```

占位符选择器，就像class和id选择器那样可以用于扩展。扩展选择器，将会编译成CSS，占位符选择器本身不会被编译。例如：

```scss
.notice {
  @extend %extreme;
}
```

编译为：

```css
#context a.notice {
  color: blue;
  font-weight: bold;
  font-size: 2em; }
```

### `!optional` 标记（The `!optional` Flag）

通常，当你扩展一个选择器的时候，如果说`@extend`不起作用了，你会收到一个错误提示。
例如，如果没有 `.notice` 选择器,你这么写`a.important {@extend .notice}`,将会报错。如果只有`h1.notice`一个选择器包含了`.notice`，那么也会报错。因为 `h1` 会与 `a` 冲突，并且不会生成新的选择器。

然而，有时候，要想`@extend`不生成任何新的选择器。只是在选择器后添加 `!optional`标志就可以了。例如：

```scss
a.important {
  @extend .notice !optional;
}
```

## 高级用法

### 条件语句

@if...@else

### 循环语句

@for

@each

@while

### 自定义函数

@function...@return