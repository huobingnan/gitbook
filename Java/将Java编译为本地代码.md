---
title: 将Java编译为本地代码
date: 2021-11-13
tags:
  - "Java"
---
## 将Java编译为本地代码

通常Java程序的执行流程为：将Java代码编译为Byte Code（字节码），然后JVM执行引擎执行编译好的Byte Code。这是一种中间语言的特性，它的好处就是可以做到平台的无关性，一份代码可以在任意的平台上运行。而且JVM语言采用了JIT（Just In Time）即时编译技术，会将执行中的热点代码（字节码）编译为本地代码运行，提高代码执行性能。

虽然Java的这种中间语言+即时编译的技术有很多优点，同时也有很多缺点。比如JVM执行引擎执行会比较占用资源，而且JIT有热加载的问题，所以执行的性能发挥不太稳定。对于软件的发布来说，我们通常会将JRE连同我们的应用程序一同发布，这样虽然能解决用户PC上JRE版本与要求版本不一致问题，但是也增大了软件包的体积。

针对上述JVM存在的问题，Oracel公司推出了一个名为GraalVM的项目，这个项目可以将Java字节码编译为本地代码。编译生成的本地代码无须JVM，可以直接在目标机器上运行。而且这种AOT（Ahead Of Time）的编译方式并不会对性能造成太大的影响，同时它还能够减少运行时的内存占用与CPU资源消耗。具体的其他特性，可以查看[GraalVM官网](https://www.graalvm.org/java/)。

## GraalVM安装（OSX）

GraalVM JDK可以与你本机的JDK互补的存在，GraalVM并没有提供相应的安装程序，而是以压缩的包的形式进行发布，你可以从Github上进行下载：https://github.com/graalvm/graalvm-ce-builds/releases/tag/vm-21.0.0.2

下载完毕后解压缩至相应目录即可。

安装完毕GraalVM之后，你可以安装`native-image`本地代码编译工具，这个工具需要依赖于GraalVM，所以在安装这个工具前，请先安装GraalVM。`native-image`本地代码编译工具也可以在上文中的Github仓库中进行下载，它也是压缩包的形式进行发布的，下载下来解压即可。但是与GraalVM不同的是这个工具并不是开箱即用，而是需要一些配置。

```shell
sudo xattr -r -d com.apple.quarantine /path/to/GRAALVM_HOME
```

```shell
<GraalVM安装目录>/Contents/Home/bin/gu install native-image
```

执行完这个命令后，`native-image`就会安装到GraalVM的bin目录下。

### 测试编译本地代码

Java源代码：

```java
public class Test
{
  public static void main(String... args)
  {
    System.out.println("Hello world");
  }
}
```

将源代码编译为字节码：

```shell
javac Test.java
```

将字节码编译为本地代码：

```shell
native-image Test
```



