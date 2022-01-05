---
title: Android Button防抖操作
date: 2021-10-08
tags:
    - "Android"
---

## 为什么要按钮防抖？

Button按钮作为一个重要的用户交互组件，它通常会接收用户的点击操作，然后响应特定的业务逻辑。

如果Button点击之后的业务逻辑会涉及到IO（网络请求/大文件读写）或者一些其他的耗时操作时，除了给用户提供必要的进度提醒之外，还需要防止用户由于等待心理造成的`Button重复点击`。

那么如何防止这种重复点击行为呢？易于想到的一种解决方案是：在点击之后，禁用Button，当业务处理完毕之后重新恢复Button的默认点击行为。

这种解决方案可行，而且易于实现，但是在编码中易于制造BUG，同时在交互上不是一种好的设计。

1. 业务处理有时会很复杂，同时业务处理可能存在异步操作。若没有在恰当的时候恢复Button的默认状态，那么将带来极其不友好的用户体验。

2. 诸如Button这类控件，其设计本意是与用户的交互，禁用了默认的交互行为，有失设计本意。


## 按钮防抖的一种实现

重新实现`View.OnClickListner`接口。

```kotlin
// @param clickTimeGap : 按钮点击时间间隔
// @param handler : 点击时间处理函数
class MyClickListener(private val clickTimeGap: Long, 
					  val handler: (view: View) -> Unit) : View.OnClickListener {  
  
    private var prevClickTime: Long = 0L // 按钮上一次的点击时间戳
  
 	override fun onClick(p0: View?) {  
        val cur = System.currentTimeMillis()  
        if (cur - prevClickTime >= clickTimeGap) {  
            handler(p0!!)  
        }  
        prevClickTime = cur  
 	}  
  
}
```

使用`MyClickListner`，为Button添加点击事件的监听器。

```kotlin
binding.button.setOnClickListener(MyClickListener(500){  
  
 	val username = binding.usernameEditText.text.toString()  
    val password = binding.passwordEditText.text.toString()  
    if (username == "Hello" && password == "123") {  
        // toast show is a non-blocking operation  
 		binding.usernameEditText.text.clear()  
        binding.passwordEditText.text.clear()  
        val intent = Intent(this, EmptyActivity::class.java)  
        startActivity(intent)  
    } else {  
        Toast.makeText(this@MainActivity, "登录失败", Toast.LENGTH_SHORT).show()  
    }  
})
```

最终效果演示：

<div align="center">
	<img src="https://cdn.jsdelivr.net/gh/huobingnan/my_image_hosting@master/Android-Demo1.44zcm40jjmg0.gif" />
</div>
