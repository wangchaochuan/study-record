# http协议零碎知识点

## GET和POST的请求的区别
POST 和 GET 是 HTTP 请求的两种方法，其区别如下：
### 应用场景
- GET请求是**一个幂等的请求**,一般用于对服务器资源不会产生影响的场景,比如获取一些静态资源;
- POST请求**不是一个幂等的请求**,一般用于对服务器资源会产生影响的场景,比如注册用户之类的操作;

tips:**幂等是指一个请求方法执行多次和仅执行一次的效果完全相同**
PUT请求也是幂等的。

### 是否缓存
因为两者应用场景不同，浏览器一般会对 Get请求缓存，但很少对 Post请求缓存

### 传参方式不同
GET请求通过查询字符串传参,Post请求一般通过请求体传参,当然也可以通过查询字符串传参,只是一般不这么用

### 安全性
GET请求是通过查询字符串传参的,这种方式将参数放入了url中一起发送到了服务端,这种做法是不太安全的,因为请求的url会被保留在记录中。因此,POST请求安全性比GET请求安全性更高。

### 请求长度
由于浏览器对url长度有限制,所以会影响GET请求发送数据时的长度;而POST请求将请求参数放在了请求体里,所以没有这个限制。

### 参数类型
get参数只允许ASCII字符，post 的参数传递支持更多的数据类型(如文件、图片)。

### 发送请求次数
GET请求只会发送一次请求,而POST会发送两次请求。

tip:**为什么post请求会发送两次请求?**


1、第一次请求为`options`预检请求,状态码为:204
那么发送`options`请求的目的是什么呢?主要有一下两个作用
- 询问服务器是否支持修改的请求头，如果服务器支持，则在第二次中发送真正的请求
- 检测服务器是否为同源请求,是否支持跨域
  
2、第二次为真正的`post`请求


## 常见的HTTP请求头和响应头

### HTTP Request Header

- **Accept**:浏览器能够处理的内容类型
- **Accept-Charset**:浏览器能够显示的字符集
- **Accept-Encoding**：浏览器能够处理的压缩编码
- **Accept-Language**：浏览器当前设置的语言
- **Connection**：浏览器与服务器之间连接的类型
- **Cookie**：当前页面设置的任何Cookie
- **Host**：发出请求的页面所在的域
- **Referer**：发出请求的页面的URL
- **User-Agent**：浏览器的用户代理字符串


### HTTP Responses Header

- **Date**：表示消息发送的时间，时间的描述格式由rfc822定义
- **server**:服务器名称
- **Connection**：浏览器与服务器之间连接的类型
- **Cache-Control**：控制HTTP缓存
- **content-type**:表示后面的文档属于什么`MIME`类型

## 常见的HTTP请求方法
- GET: 向服务器获取数据；
- POST：发送数据给服务器，通常会造成服务器资源的新增修改；
- PUT：用于全量修改目标资源(看接口，也可以用于添加)；
- PATCH：用于对资源进行部分修改
- DELETE：用于删除指定的资源；
- HEAD：获取报文首部，与GET相比，不返回报文主体部分；使用场景是比如下载一个大文件前，先获取其大小再决定是否要下载，以此可以节约宽带资源
- OPTIONS：(浏览器自动执行)、询问支持的请求方法，用来跨域请求、预检请求、判断目标是否安全；
- CONNECT：要求在与代理服务器通信时建立管道，使用管道进行TCP通信；(把服务器作为跳板，让服务器代替用户去访问其他网页，之后把数据原原本本的返回给用户)
- TRACE: 该方法会让服务器原样返回任意客户端请求的信息内容，主要⽤于测试或诊断

## OPTIONS请求方法及使用场景
OPTIONS是除了GET和POST之外的其中一种 HTTP请求方法。(浏览器自动执行)
OPTIONS方法是用于请求获得由`Request-URI`标识的资源在请求/响应的通信过程中可以使用的功能选项。通过这个方法，客户端可以在**采取具体资源请求之前，决定对该资源采取何种必要措施，或者了解服务器的性能**。该请求方法的响应不能缓存。

OPTIONS请求方法的主要用途有两个：
- 获取服务器支持的所有HTTP请求方法；
- 用来检查访问权限。例如：在进行 CORS 跨域资源共享时，对于复杂请求，就是使用 OPTIONS 方法发送**嗅探请求**，以判断是否有对指定资源的访问权限。

## XMLHTTPRequest对象

Ajax的核心是XMLHTTPRequest。它是一种支持异步请求的技术。 XMLHTTPRequest使您可以使用JavaScript向服务器提出请求并处理响应，而不阻塞用户。可以在页面加载以后进行页面的局部更新

### 使用XHR

- 实例化ajax对象
```javascript
const xhr = new XMLHttpRequest();
```
- 创建HTTP请求
```javascript
/**
 * @params method 请求方法 GET|POST|PUT|DELETE
 * @params url    请求地址 String
 * @params async  是否是异步请求,可选,默认为true Boolean
 */
xhr.open("post", "http://localhost:3008/api/login",true);
```

- 设置请求头
```javascript
/**
 * @params key    请求头的key    String
 * @params value  请求头的value  String
 */
xhr.setRequestHeader("Content-type","application/json");
```

- 注册回调函数

```javascript
// 方式一
// onload事件 ：  接收服务器响应的数（一次请求，只会执行一次）
xhr.onload = function(){
    console.log(xhr.responseText);
}
// 方式二
/**
 * onreadystatechang事件 : 作用与onload事件一致（一次请求，会执行多次）
 * XMLHttpRequest对象的状态码 （xhr.readyState）
 * 0: 请求未建立  (创建了xhr对象，但是还没调用open)
 * 1: 服务器连接已建立
 * 2. 请求已接收  (send之后,服务器已经接收了请求)
 * 3. 请求处理中
 * 4. 请求已完成，且响应已就绪  (4状态码等同于onload事件)
 */
xhr.onreadystatechange = function() {
    if(xhr.readyState === 4){
        console.log(xhr.responseText);
    }
}
```

- 发送请求
```javascript
/**
 * @params body    可选参数,请求主体,如果请求方法是 GET 或者 HEAD，则应将请求主体设置为 null
 */
xhr.send();
```

```javascript
// 1.实例化ajax对象
const xhr = new XMLHttpRequest();
// 2.创建http请求
xhr.open("post", "http://localhost:3008/api/login",true);
// 3.设置请求头
xhr.setRequestHeader("Content-type","application/json");
// 4.设置回调函数
xhr.onreadystatechange = function() {
    if(xhr.readyState === 4){
        console.log(xhr.responseText);
    }
}
// 5.发送请求
xhr.send();
```

### ajax请求如何取消
#### 原生xhr取消请求
```javascript
const xhr = new XMLHttpRequest();
xhr.abort();
```

#### axios取消请求
传递一个 `executor` 函数到 `CancelToken` 的构造函数来创建 `cancel token`
```javascript
const CancelToken = axios.CancelToken;
let cancel;
​
axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // executor 函数接收一个 cancel 函数作为参数
    cancel = c;
  })
});
​
// cancel the request
cancel();
```

### 取消ajax请求的意义
- 已发出的请求可能仍然会到达后端
- 取消后续的回调处理，避免多余的回调处理，以及特殊情况，先发出的后返回，导致回调中的数据错误覆盖
- 取消loading效果，以及该请求的其他交互效果，特别是在单页应用中，A页面跳转到B页面之后，A页面的请求应该取消，否则回调中的一些处理可能影响B页面
- 超时处理，错误处理等都省去了，节约资源

## HTTP 1.0 和 HTTP 1.1 之间有哪些区别？

### 连接方面
http1.0 默认使用**非持久连接**，而 http1.1 默认使用**持久连接**(`Connection: Keep-Alive`)。http1.1 通过使用持久连接来使多个 http 请求复用同一个 TCP 连接，以此来避免使用非持久连接时每次需要建立连接的时延。

### 资源请求方面
在 http1.0 中，存在一些浪费带宽的现象，例如客户端只是需要某个对象的一部分，而服务器却将整个对象送过来了，并且不支持断点续传功能，http1.1 则在请求头引入了 `range` 头域，它允许只请求资源的某个部分，即返回码是 206（Partial Content），这样就方便了开发者自由的选择以便于充分利用带宽和连接。

### 缓存方面
在 http1.0 中主要使用 header 里的 `Expires` 来做为缓存判断的标准，http1.1 则引入了更多的缓存控制策略，例如 `cache-control`、`Etag`、`if-none-matched`、`last-modified`、`if-modified-since` 等更多可供选择的缓存头来控制缓存策略

### http1.1 中新增了 host 字段,用来指定服务器的域名
http1.0 中认为每台服务器都绑定一个唯一的 IP 地址，因此，请求消息中的 URL 并没有传递主机名(`hostname`)。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机，并且它们共享一个IP地址。因此有了 host 字段，这样就可以将请求发往到同一台服务器上的不同网站。
### 新增请求方法
http1.1 相对于 http1.0 还新增了很多请求方法，如 `PUT`、`HEAD`、`OPTIONS` 等
