# Sass使用
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


