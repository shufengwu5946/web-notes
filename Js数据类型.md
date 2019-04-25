# JavaScript数据类型

## 7种：6种原始类型和对象（广义的对象）

6种原始类型：

* number
* string
* boolean
* null
* undefined
* symbol

广义的对象可以分为：
* 狭义的对象
* 数组
* 函数

> **注意**
>
>* 首先原始类型存储的都是值，是没有函数可以调用的，比如 undefined.toString()
>* **null不是对象**。虽然 typeof null 会输出 object，但是这只是 JS 存在的一个悠久 Bug。在 JS 的最初版本中使用的是 32 位系统，为了性能考虑使用低位存储变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。


> **对象类型和原始类型不同**的是，原始类型存储的是值，对象类型存储的是地址（指针）。

> **函数参数是对象**会发生什么问题？
>
> JavaScript里面当对象引用做为函数参数传递时候，函数内的操作会被影响到作为参数的对象。

> 解决方法？？？？？

## 数据类型判断

* typeof运算符
* instanceof运算符
* Object.prototype.toString方法

### typeof运算符

* 数值、字符串、布尔值分别返回number、string、boolean。
* undefined返回undefined。
* symbol返回symbol
* null返回object
* 对象中函数返回function，其他都返回object

```javascript
typeof 123 // "number"
typeof '123' // "string"
typeof false // "boolean"
typeof undefined
// "undefined"
typeof Symbol() // 'symbol'
typeof null // "object"
function f() {}
typeof f
// "function"
typeof window // "object"
typeof {} // "object"
typeof [] // "object"
```
> typeof 对于对象来说，除了函数都会显示 object，所以说 typeof 并不能准确判断变量到底是什么类型

### instanceof运算符

内部机制是通过原型链来判断的

对于原始类型来说，你想直接通过 instanceof 来判断类型是不行的
```javascript
var x = [1, 2, 3];
var y = {};
x instanceof Array // true
y instanceof Object // true
var s = 'hello';
s instanceof String // false
undefined instanceof Object // false
null instanceof Object // false
```

### Object.prototype.toString方法

```javascript
console.log(Object.prototype.toString.call("jerry"));//[object String]
console.log(Object.prototype.toString.call(12));//[object Number]
console.log(Object.prototype.toString.call(true));//[object Boolean]
console.log(Object.prototype.toString.call(undefined));//[object Undefined]
console.log(Object.prototype.toString.call(null));//[object Null]
console.log(Object.prototype.toString.call({name: "jerry"}));//[object Object]
console.log(Object.prototype.toString.call(function(){}));//[object Function]
console.log(Object.prototype.toString.call([]));//[object Array]
console.log(Object.prototype.toString.call(new Date));//[object Date]
console.log(Object.prototype.toString.call(/\d/));//[object RegExp]
function Person(){};
console.log(Object.prototype.toString.call(new Person));//[object Object]
```

参考 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString

## 数据类型转换

### 强制类型转换
* Number()
* String()
* Boolean()

#### Number()

原始类型值:
```javascript
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
```
对象：

