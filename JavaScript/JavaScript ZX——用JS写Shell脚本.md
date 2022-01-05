---
title: JavaScript ZX——用JS写Shell脚本
date: 2021-11-14
tags:
  - "JavaScript"
---

## ZX库简介

`zx`是Google推出的一个运行在`Node.js`环境下的方便开发人员编写命令行脚本程序的类库。

命令脚本程序是基于特定操作系统，方便完成的一系列批处理工作的脚本程序。在Unix-like的操作系统，通常是使用shell脚本。在Windows系统中使用的则是bat脚本。

这类批处理脚本都有一个共同的特点——语法晦涩不好编写。对于开发人员来说，掌握shell编程的收益并不大。好在Google推出了一款可以在Node运行时环境中进行shell编程的工具类库`zx`

`zx`的Github项目地址为[https://github.com/google/zx](https://github.com/google/zx)，此项目在笔者写下这篇文章时就已经有23k左右的star了，可见这个库确实解决了开发人员的一个痛点。

## ZX库的安装

安装ZX之前，需要确保本机具有Node的运行时环境。如果没有安装，可以去NodeJS官网下载，地址为：[https://nodejs.org/en/](https://nodejs.org/en/)。

NodeJS的安装完毕后，你就可以使用npm（Node Package Manager）来安装zx了。

```shell
sudo npm install -g zx
```

## 使用ZX写一段shell脚本

zx提供了许多封装好的函数方便你进行shell编程。

例如：`question`函数，对`readline`进行了封装，可以更方便的接收shell的输入。同时zx还提供了`fetch`函数，可以更好的进行网络数据的请求。

在zx中以`$`符号开头的代表这是一个shell命令，它会立即执行并返回一个Promise，这个Promise被定义为

```typescript
class ProcessPromise<T> extends Promise<T> {
  readonly stdin: Writable
  readonly stdout: Readable
  readonly stderr: Readable
  readonly exitCode: Promise<number>
  pipe(dest): ProcessPromise<T>
  kill(signal = 'SIGTERM'): Promise<void>
}
```

`ProcessPromise`的返回值为：

```typescript
class ProcessOutput {
  readonly stdout: string
  readonly stderr: string
  readonly exitCode: number
  toString(): string
}
```

其中如果shell命令执行成功，那么`exitCode`字段将被设置为0。同时shell命令执行后的输出将会被保存在`stdout`中。如果执行失败，那么`exitCode`将为一个非零的整数，命令的输出将会被保存在`stderr`中。

同时你也可以对shell命令执行后的输出进行重定向：

```javascript
$`git pull`.pipe(streamVar)
```

Demo：自动提交当前代码

```js
#!/usr/bin/env zx

// 注意$返回的是一个Promise
let { exitCode } = await $"git add ."

if (exitCode != 0) {
	process.exit(-1)
}

let { exitCode } = await $"git commit -m \"update\""

if (exitCode != 0) {
	process.exit(-1)
}
```

写好脚本之后，脚本需要被zx执行，而不是node。

```shell
zx ./script.js
```

也可以在文件开头指明执行当前脚本的程序

```shell
#!/usr/bin/env zx

//script.js
```

然后给当前文件赋予可执行权限后，便可以单独运行此文件

```shell
chmod +x ./script.js
```

关于zx库的更多用法，可以在Github上进行查看[https://github.com/google/zx](https://github.com/google/zx)。
