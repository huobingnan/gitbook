---
title: JavaScript对象的浅拷贝与深拷贝
date: 2021-12-12
tags:
  - JavaScript
---

## 概述

在JavaScript中对象的类型可以分为值类型和引用类型两种，值类型有：`number`，`string`，`boolean`，`null`，`undefined`，`symbol`等。引用类型有：`object`，`function`等。

JavaScript的对象为引用类型，所以对象变量作为右值赋值的操作实际上是引用的拷贝操作，即两个变量引用同一块内存区域。值类型的变量在作为右值赋值给一个左值变量时是对内存区域的拷贝。由此也就引出了JavaScript中对象的拷贝问题。

浅拷贝是指只拷贝对象中值类型变量，引用类型的变量依然采用引用拷贝。深拷贝是讲变量所有的内容重新拷贝一份到新的内存区域中，无论是值类型还是引用类型。

## 浅拷贝

JavaScript对象的浅拷贝实现有多种方式，常用的有

1. 迭代对象
2. Object.assign
3. 对象展开

### 1. 迭代对象

```javascript
function shadowCopy(src) {
	const dst = new src.constructor();
	for (const [prop, val] of Object.entries(src)) {
		dst[prop] = val;
	}
	return dst;
}
```

这里采用构造函数去实例化对象，而不是采用对象字面量`{}`来创建的原因是保留对象的原型链信息。

### 2. Object.assign

```javascript
function shadowCopy(src) {
	const dst = new src.constructor();
	return Object.assign(dst, src);
}
```

### 3. 对象展开

```javascript
function shadowCopy(src) {
	return { ...src };
}
```

## 深拷贝

在有浅拷贝作为前置知识的情况下，深拷贝很容易实现。

深拷贝主要要解决问题时完成对对象中引用类型属性的复制，当属性为值类型时，直接执行对象的浅拷贝，当遇到一个引用类型的属性时，可以递归按照之前设计的对象浅拷贝逻辑进行。

```javascript
function doDeepCopy(src, dst) {
	for (const [prop, val] of Object.entries(dst)) {
		if (typeof val === "object") {
			src[prop] = new val.constructor();
			doDeepCopy(src[prop], val);
		}else {
			src[prop] = val;
		}
	}
}

function deepCopy(src) {
	const dst = new src.constructor();
	doDeepCopy(dst, src);
	return dst;
}
```

除此之外，也可以采用序列化与反序列化的方式进行对象的深拷贝

```javascript
const copy = JSON.parse(JSON.stringify(t));
```

使用序列化和反序列深拷贝对象时，需要注意：

1. 值为`null`和`undefined`的属性会消失
2. 原型链信息会消失
3. 对象的方法会消失

