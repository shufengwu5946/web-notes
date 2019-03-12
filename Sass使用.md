# Sass使用

https://www.sasscss.com/getting-started/

## 使用变量
```
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
```
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

```
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

## 嵌套CSS 规则

```
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
父选择器的标识符&:
```
article a {
  color: blue;
  &:hover { color: red }
}

 /* 编译后 */
article a { color: blue }
article a:hover { color: red }
```
同时父选择器标识符还有另外一种用法，你可以在父选择器之前添加选择器。举例来说，当用户在使用IE浏览器时，你会通过JavaScript在<body>标签上添加一个ie的类名，为这种情况编写特殊的样式如下：
```
#content aside {
  color: red;
  body.ie & { color: green }
}

/*编译后*/
#content aside {color: red};
body.ie #content aside { color: green }
```
群组选择器的嵌套:
```
.container {
  h1, h2, h3 {margin-bottom: .8em}
}

/*编译后*/
.container h1, .container h2, .container h3 { margin-bottom: .8em }
```
```
nav, aside {
  a {color: blue}
}

/*编译后*/
nav a, aside a {color: blue}
```

子组合选择器和同层组合选择器：>、+和~:

```css
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
```
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

嵌套属性：
```
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```
嵌套属性的规则是这样的：把属性名从中划线-的地方断开，在根属性后边添加一个冒号:，紧跟一个{ }块，把子属性部分写在这个{ }块中。就像css选择器嵌套一样，sass会把你的子属性一一解开，把根属性和子属性部分通过中划线-连接起来，最后生成的效果与你手动一遍遍写的css样式一样：
```
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```
对于属性的缩写形式，你甚至可以像下边这样来嵌套，指明例外规则：
```
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}
```
这比下边这种同等样式的写法要好：
```
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

## 导入SASS文件

css有一个特别不常用的特性，即@import规则，它允许在一个css文件中导入其他css文件。然而，后果是只有执行到@import时，浏览器才会去下载其他css文件，这导致页面加载起来特别慢。

sass也有一个@import规则，但不同的是，sass的@import规则在生成css文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个css文件中，而无需发起额外的下载请求。

### 使用SASS部分文件

当通过@import把sass样式分散到多个文件时，你通常只想生成少数几个css文件。那些专门为@import命令而编写的sass文件，并不需要生成对应的独立css文件，这样的sass文件称为局部文件。对此，sass有一个特殊的约定来命名这些文件。

此约定即，**sass局部文件的文件名以下划线开头。这样，sass就不会在编译时单独编译这个文件输出css，而只把这个文件用作导入**。当你@import一个局部文件时，还可以不写文件的全名，即省略文件名开头的下划线。举例来说，你想导入themes/_night-sky.scss这个局部文件里的变量，你只需在样式表中写@import "themes/night-sky";。

### 默认变量值
```
$fancybox-width: 400px !default;
.fancybox {
width: $fancybox-width;
}
```
在上例中，如果用户在导入你的sass局部文件之前声明了一个$fancybox-width变量，那么你的局部文件中对$fancybox-width赋值400px的操作就无效。如果用户没有做这样的声明，则$fancybox-width将默认为400px。

### 嵌套导入

跟原生的css不同，sass允许@import命令写在css规则内。这种导入方式下，生成对应的css文件时，局部文件会被直接插入到css规则内导入它的地方。举例说明，有一个名为_blue-theme.scss的局部文件，内容如下：
```
aside {
  background: blue;
  color: white;
}
```
然后把它导入到一个CSS规则内，如下所示：
```
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

## 静默注释
```
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
```
css的标准注释格式/* ... */内的注释内容亦可在生成的css文件中抹去。当注释出现在原生css不允许的地方，如在css属性或选择器中，sass将不知如何将其生成到对应css文件中的相应位置，于是这些注释被抹掉。
```
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```

## 混合器
```
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
```
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

```
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
```
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

```
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

## 使用选择器继承来精简CSS

```
//通过选择器继承继承样式
.error {
  border: 1px red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

以class="seriousError" 修饰的html元素最终的展示效果就好像是class="seriousError error"。

.seriousError不仅会继承.error自身的所有样式，任何跟.error有关的组合选择器样式也会被.seriousError以组合选择器的形式继承，如下代码:
```
//.seriousError从.error继承样式
.error a{  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error { //应用到hl.seriousError
  font-size: 1.2rem;
}
```
如上所示，在class="seriousError"的html元素内的超链接也会变成红色和粗体








