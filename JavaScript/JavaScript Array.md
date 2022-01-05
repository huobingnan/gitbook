---
title: JavaScript Array
date: 2021-12-20
tags:
  - JavaScript
---

![logo](https://cdn.jsdelivr.net/gh/huobingnan/my_image_hosting@master/logo.3ovzks2zysi0.png)

## 摘要

Array（数组）是JavaScript中一个重要的数据类型，本文将介绍不同语言标准下（ES3 ES5 ES6）JavaScript Array的用法与相关操作。


## Array创建方式

Array的创建方式有：

1. 字面量
2. 构造函数
3. 静态方法

### 1. 字面量

Array可以通过`[]`字面量的方式创建。

```javascript
const array = [] // 一个空数组
const array = [1, 2, 3, 4, 5]; // 数组with初始值

// JavaScript的数组可以存储任意类型的元素
const array = ["String", 1, true, {}]
```

使用字面量创建Array时需要注意，谨慎在数组末尾添加`,`。有些编程语言规范中会建议在数组最后一个元素结尾添加`,`。但是这个操作在JavaScript中容易引起歧义。

有些浏览器会默认为`,`后添加一个`undefined`元素。所以数组字面量中以`,`结尾，实际数组元素可能会多一个。

```javascript
const array = [1,2,3,]; // ==> [1,2,3, undefined]
```

同时，数组字面量中没有元素，而是N个`,`，那么会创建一个N个或N+1个`undefined`元素的数组。

```javascript
const array = [,,,,,];
```

### 2. 构造函数

数组类型的构造函数是`Array`，创建Array可以通过`new`的方式，也可以通过直接调用的方式。

```javascript
// 可以传入array的长度信息，也可以缺省
let array = new Array(5);
let array = new Array();
// 也可以采用直接调用构造函数
let array = Array(5);
```

### 3. 静态方法

`Array.from`和`Array.of`也可以创建数组。

#### 3.1 Array#from
Array.from接受一个ArrayLike类型的参数，常见的ArrayLike有
array， string和arguments。

使用Array.from可以打散一个字符串

```javascript
console.log(Array.from("Hello world"));
```

```
[
  'H', 'e', 'l', 'l',
  'o', ' ', 'w', 'o',
  'r', 'l', 'd'
]
```

Array.from也可以较为方便的操作arguments，arguments具有数组的一些特性，但是操作时由于arguments不能使用Array的一些API，可以使用Array.from函数对arguments进行一层包裹。

```javascript

function sublist() {
    //Array.prototype.slice.call(arguments, 2);
    return Array.from(arguments).slice(2);
}
```

当Array.from接收一个数组作为参数时，返回值是原数组浅拷贝。

```javascript
const a = [{ name: "Tom" }];
const b = Array.from(a);
b[0].name = "Steven";
console.log(a);
// [ { name: 'Steven' } ]
const c = [1, 2, 3, 4, 5];
const d = Array.from(c);
d[0] = 100;
console.log(c);
// [ 1, 2, 3, 4, 5 ]
```

#### 3.2 Array#of

Array.of函数接收的是一个变长参数，然后返回由输入参数组成的数组。

```javascript
const a = Array.of(1,2,3,4);
```
