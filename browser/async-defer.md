# 图解 `script` 标签中的 `async` 和 `defer` 属性

我们在工作中经常会碰到 `script` 标签,一般会有以下三种形式
```html
<script src='xxx'></script>
<script src='xxx' async></script>
<script src='xxx' defer></script>
```

那么这三种形式的 `script` 标签有什么区别呢？且容我们一探究竟。

## 前置知识点

浏览器的渲染线程在解析 `HTML` 的过程中如果遇到 `script` 标签会暂停解析 `HTML` ,等带 `Javascript` 脚本下载并执行完成后才会继续解析 `HTML` 。因为解析 `HTML` 的过程其实就是在构建 `DOM` 树的过程,而 `Javascript` 脚本在执行的过程中是有可能修改 `DOM` 树结构的。

![](https://www.growingwiththeweb.com/images/2014/02/26/legend.svg)



## 普通 `script` 标签
对于普通 `script` 标签而言,就是走的正常的解析 `Javascript` 的流程,先暂停 `HTML` 的解析,等待下载并执行 `Javascript` 之后再继续 `HTML` 解析。

以下是普通 `script` 标签执行时机的图解
![](https://www.growingwiththeweb.com/images/2014/02/26/script.svg)

如果某个 `Javascript` 脚本比较大或者网络不好,那么就可能在下载的时候耗费时间比较久,从而造成页面白屏;  `Javascript`  执行时间过长,比如循环遍历了一个很大的数据,也是有可能造成白屏的。

## `async script`
对于有 `async` 属性的 `script` 标签而言,遇到这种 `script` 标签并不会立即停止解析 `HTML` ,而是下载 `JavaScript` 和解析 `HTML` 同时进行,当 `JavaScript` 下载完成之后,再停止解析 `HTML` 转而执行刚下载好的 `JavaScript` ,待 `JavaScript` 执行完成再继续解析 `HTML`。

以下是`async` `script` 标签执行时机的图解

![](https://www.growingwiththeweb.com/images/2014/02/26/script-async.svg)

使用 `async` 属性的 `script` 标签不能保证执行顺序和我们在页面中写入的顺序是一致的,因为我们不能保证先出现的 `script` 标签就一定先下载完成,可能后面的 `script` 标签指向的文件更小,那么它就有可能先下载完成,从而先执行。所以,对于依赖执行顺序的 `JavaScript` 文件最好不要使用 `async` 属性。

## `defer script`
对于有 `defer` 属性的 `script` 标签而言,遇到这种 `script` 标签不会停止解析 `HTML` , 而是同时解析 `HTML` 和 下载 `JavaScript` ,待 `HTML` 解析完成,再根据加入 `script` 的顺序执行 `JavaScript`。

以下是`defer` `script` 标签执行时机的图解

![](https://www.growingwiththeweb.com/images/2014/02/26/script-defer.svg)

使用 `defer` 属性的 `script` 标签不会阻塞 `HTML` 的解析,且能保证 `JavaScript` 的执行顺序，但是这些 `JavaScript` 需要在 `HTML` 解析之后才会执行。

**tips: `IE9` 以下不支持 `defer` 属性**

## 总结
最后，根据上面的分析，不同类型 `script` 的执行顺序及其是否阻塞解析 `HTML` 总结如下：

|script 标签|JS 执行顺序|是否阻塞解析 HTML
|-|-|-|
|`<script>`|在 HTML 中的顺序|阻塞
|`<script async>`|网络请求返回顺序|可能阻塞，也可能不阻塞
|`<script defer>`|在 HTML 中的顺序|不阻塞

