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

