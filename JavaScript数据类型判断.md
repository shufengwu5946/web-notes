# JavaScript数据类型判断

- [JavaScript 的数据类型](#JavaScript-的数据类型)
- [数据类型判断方法](#数据类型判断方法)
  - [typeof 运算符](#typeof-运算符)
  - [instanceof运算符](#instanceof运算符)
  - [Object.prototype.toString方法](#Object-prototype-toString方法)

## JavaScript 的数据类型

一共7种：

* 数值（number）：整数和小数（比如1和3.14）
* 字符串（string）：文本（比如Hello World）。
* 布尔值（boolean）：表示真伪的两个特殊值，即true（真）和false（假）
* undefined：表示“未定义”或不存在，即由于目前没有定义，所以此处暂时没有任何值
* null：表示空值，即此处的值为空。
* 对象（object）：各种值组成的集合。
* Symbol：ES6 新增。

对象是最复杂的数据类型，又可以分成三个子类型：

* 狭义的对象（object）
* 数组（array）
* 函数（function）

## 数据类型判断方法

JavaScript 有三种方法，可以确定一个值到底是什么类型。

* typeof运算符
* instanceof运算符
* Object.prototype.toString方法

### typeof 运算符

* 数值、字符串、布尔值分别返回number、string、boolean。
* 函数返回function。
* undefined返回undefined
* 对象返回object
* null返回object

```javascript
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"

function f() {}
typeof f // "function"

typeof undefined // "undefined"

typeof window // "object"
typeof {} // "object"
typeof [] // "object"

typeof null // "object"
```

> typeof可以用来检查一个没有声明的变量，而不报错。
```
v // ReferenceError: v is not defined
typeof v // "undefined"
```
> 上面代码中，变量v没有用var命令声明，直接使用就会报错。但是，放在typeof后面，就不报错了，而是返回undefined。
> 实际编程中，这个特点通常用在判断语句。
```
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
```

### instanceof运算符
### Object.prototype.toString方法
