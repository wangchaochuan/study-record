# Vue基础

## Vue的基本原理

当一个`Vue`实例创建时，`Vue`会遍历`data`中的属性，用 `Object.defineProperty`（`vue3.0使用proxy` ）将它们转为 `getter/setter`，并且在**内部追踪相关依赖**，在属性被访问和修改时通知变化。 每个组件实例都有相应的 `watcher` 程序实例，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的`setter`被调用时，会通知`watcher`重新计算，从而致使它关联的组件得以更新。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2369052cf3e244d5ac21c9505da97259~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## Vue的优点

### 轻量级框架
只关注视图层，是一个构建数据的视图集合，大小只有几十 `kb`
### 简单易学
国人开发，中文文档，不存在语言障碍 ，易于理解和学习
### 双向数据绑定
保留了 `angular` 的特点，在数据操作方面更为简单
### 组件化
保留了 `react` 的优点，实现了 `html` 的封装和重用，在构建单页面应用方面有着独特的优势
### 视图，数据，结构分离
使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作
### 虚拟DOM
`dom` 操作是非常耗费性能的，不再使用原生的 `dom` 操作节点，极大解放 `dom` 操作，但具体操作的还是 `dom` 不过是换了另一种方式
### 运行速度更快
相比较于 `react` 而言，同样是操作虚拟 `dom`，就性能而言， `vue` 存在很大的优势

## Vue响应式的原理
### 数据劫持
数据劫持比较好理解，通常我们利用`Object.defineProperty`劫持对象的访问器，在属性值发生变化时我们可以获取变化，从而进行进一步操作.
### 发布/订阅模式
在软件架构中，**发布订阅**是一种消息范式，消息的发送者（称为发布者）不会将消息直接发送给特定的接收者（称为订阅者）。而是**将发布的消息分为不同的类别**，无需了解哪些订阅者（如果有的话）可能存在。同样的，订阅者可以表达对一个或多个类别的兴趣，只接收感兴趣的消息，无需了解哪些发布者（如果有的话）存在。

这里很明显了，区别就在于，不同于观察者和被观察者，**发布者和订阅者是互相不知道对方的存在的，发布者只需要把消息发送到订阅器里面，订阅者只管接受自己需要订阅的内容**

### 响应式原理

`Vue`响应式的原理就是采用**数据劫持**结合**发布者-订阅者模式**的方式，通过`Object.defineProperty()` 来劫持各个属性的`setter`，`getter`，在**数据变动时发布消息给订阅者，触发相应的监听回调**。

核心概念

- `Observe`(被劫持的数据对象) 
- `Compile`(vue的编译器) 
- `Wather`(订阅者) 
- `Dep`(用于收集Watcher订阅者们)

主要分为以下几个步骤：
- 需要给`Observe`的数据对象进行递归遍历，包括子属性对象的属性，都加上`setter`和`getter`这样的属性，给这个对象的某个值赋值，就会触发`setter`，那么就能监听到了数据变化
- `Compile`解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
- `Watcher`订阅者是`Observer`和`Compile`之间通信的桥梁，主要做的事情是:
  
     - 在自身实例化时往属性订阅器(Dep)里面添加自己 
     - 自身必须有一个update()方法 
     - 待属性变动dep.notice() 通知时，能调用自身的update() 方法，并触发Compile中绑定的回调，则功成身退。

- `MVVM`作为数据绑定的入口，整合`Observer`、`Compile`和`Watcher`三者，通过`Observer`来监听自己的`model`数据变化，通过`Compile`来解析编译模板指令，最终利用`Watcher`搭起`Observer`和`Compile`之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(`input`) -> 数据`model`变更的双向绑定效果。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c8b6808985354109a11d2ce29c6903de~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## MVC、MVP、MVVM的区别
- `M`: `model`数据模型
- `V`:`view`视图模型
- `C`: `controller`控制器
- `P`: `Presenter` 控制器
- `VM`:`ViewModel`负责监听`Model`中数据的改变并且控制视图的更新，处理用户交互操作
### MVC

`MVC` 通过分离 `Model`、`View` 和 `Controller` 的方式来组织代码结构。其中 `View` 负责页面的显示逻辑，`Model` 负责存储页面的业务数据，以及对相应数据的操作。并且 `View` 和 `Model` 应用了**观察者模式**，当 `Model` 层发生改变的时候它会通知有关 `View` 层更新页面。`Controller` 层是 `View` 层和 `Model` 层的纽带，它主要负责用户与应用的响应操作，当用户与页面产生交互的时候，`Controller` 中的事件触发器就开始工作了，通过调用 `Model` 层，来完成对 `Model` 的修改，然后 `Model` 层再去通知`View`视图更新。

![](https://henleylee.github.io/medias/study/architectural_pattern_mvc.png)

**`MVC` 的通信都是单向的**。`View` 层会从 `Model` 层拿数据，因此 `MVC` 中的 `View` 层和 Model 层还是存在耦合的.

### MVP
`MVP` 模式与 `MVC` 唯一不同的在于 `Presenter` 和 `Controller`。在 `MVC` 模式中使用观察者模式，来实现当 `Model` 层数据发生变化的时候，通知 `View` 层的更新。这样 `View` 层和 `Model` 层耦合在一起，当项目逻辑变得复杂的时候，可能会造成代码的混乱，并且可能会对代码的复用性造成一些问题。

`MVP` 的模式通过使用 `Presenter` 来实现对 `View` 层和 `Model` 层的解耦。MVC 中的Controller 只知道 `Model` 的接口，因此它没有办法控制 `View` 层的更新，`MVP` 模式中，`View` 层的接口暴露给了 `Presenter` 因此可以在 `Presenter` 中将 `Model` 的变化和 `View` 的变化绑定在一起，以此来实现 `View` 和 `Model` 的同步更新。这样就实现了对 `View` 和 `Model` 的解耦，`Presenter` 还包含了其他的响应逻辑。

### MVVM
`Model`和`View`并无直接关联，而是通过`ViewModel`来进行联系的，`Model`和`ViewModel`之间有着**双向数据绑定**的联系。因此当`Model`中的数据改变时会触发`View`层的刷新，`View`中由于用户交互操作而改变的数据也会在`Model`中同步。

这种模式实现了 `Model`和`View`的数据自动同步，因此开发者只需要专注于数据的维护操作即可，而不需要自己操作`DOM`。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/deffd15d0d9647f599de5af394309ff8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## MVVM的优缺点
### 优点
- 分离视图（`View`）和模型（`Model`），**降低代码耦合，提⾼视图或者逻辑的重⽤性**: ⽐如视图（`View`）可以独⽴于`Model`变化和修改，⼀个`ViewModel`可以绑定不同的`View`上，当`View`变化的时候`Model`不可以不变，当`Model`变化的时候`View`也不可以不变。你可以把⼀些视图逻辑放在⼀个`ViewModel`⾥⾯，让很多`view`重⽤这段视图逻辑.
- 提⾼可测试性: `ViewModel`的存在可以帮助开发者更好地编写测试代码
- ⾃动更新`dom`: 利⽤双向绑定,数据更新后视图⾃动更新,让开发者从繁琐的⼿动`dom`中解放

### 缺点

- **`Bug`很难被调试**: 因为使⽤双向绑定的模式，当你看到界⾯异常了，有可能是你`View`的代码有`Bug`，也可能是`Model`的代码有问题。数据绑定使得⼀个位置的`Bug`被快速传递到别的位置，要定位原始出问题的地⽅就变得不那么容易了。另外，数据绑定的声明是指令式地写在`View`的模版当中的，这些内容是没办法去打断点`debug`的
- ⼀个⼤的模块中`model`也会很⼤，虽然使⽤⽅便了也很容易保证了数据的⼀致性，当时⻓期持有，不释放内存就造成了花费更多的内存
- 对于⼤型的图形应⽤程序，视图状态较多，`ViewModel`的构建和维护的成本都会⽐较⾼

## Vue的常用指令及作用

- `v-on` 给标签绑定函数，可以缩写为`@`，例如绑定一个点击函数 函数必须写在`methods`里面
- `v-bind` 动态绑定 作用： 及时对页面的数据进行更改, 可以简写成：冒号
- `v-slot`: 缩写为`#`, 组件插槽
- `v-for` 根据数组的个数, 循环数组元素的同时还生成所在的标签
- `v-show` 显示内容
- `v-if` 显示与隐藏
- `v-else` 必须和v-if连用 不能单独使用 否则报错
- `v-text` 解析文本
- `v-html` 解析html标签

## Vue怎么动态绑定Class 与 Style
```html
v-bind:class="{{ '类名': bool, '类名': bool ......}}"
```
`v-bind:style` 的对象语法十分直观——看着非常像 `CSS`，但其实是一个 `JavaScript` 对象。`CSS property` 名可以用驼峰式 (`camelCase`) 或短横线分隔 (`kebab-case`，记得用引号括起来) 来命名.直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

## vue常用的修饰符

### v-on
- `.stop` - 调用 `event.stopPropagation()`。 阻止默认事件
- `.prevent` - 调用 `event.preventDefault()`。阻止默认行为
- `.native` - 监听组件根元素的原生事件。

### v-model
- `.lazy`- 取代 `input` 监听 `change` 事件
- `.number` - 输入字符串转为有效的数字
- `.trim` - 输入首尾空格过滤

## v-show和v-if的区别
### 相同点
`v-show` 和`v-if`都是`true`的时候显示，`false`的时候隐藏.

### 不同点

`v-if` 是"真正"的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被**销毁和重建**，操作的实际上是`dom`元素的创建或销毁

`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 `CSS` 进行切换 它操作的是`display:none/block`属性。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

### 原理不同
- `v-show` 一定会在`DOM`树中,只不过是通过控制`display`属性控制显隐
- `v-if` 是真正的条件渲染,如果为`false`,就不会出现在`DOM`树中

### 对子组件影响不同
`v-if`的切换会导致子组件或者子元素的卸载和重新创建,而`v-show`不会

### 应用场景不同

频繁切换的场景适合使用`v-show`,不频繁切换的场景适合使用`v-if`

## v-model原理
### 作用在表单元素上 
动态绑定了 `input` 的 `value` 指向了 `messgae` 变量，并且在触发 `input` 事件的时候去动态把 `message` 设置为目标值

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9f554ba199884e8d8eb96d04d97c9db1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 作用在组件上

在自定义组件中，`v-model` 默认会利用名为 `value` 的 `prop`和名为 `input` 的事件

**本质是一个父子组件通信的语法糖，通过`prop`和`$.emit`实现**。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7215208de2124bfbbfb2f617e473507f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 计算属性computed 和watch 的区别

### Computed

- 它**支持缓存**，只有依赖的数据发生了变化，才会重新计算
- **不支持异步**，当`Computed`中有异步操作时，无法监听数据的变化
- `computed`的值会默认走缓存，**计算属性是基于它们的响应式依赖进行缓存的**，也就是基于`data`声明过，或者父组件传递过来的`props`中的数据进行计算的。
- 如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，一般会使用`computed`
- 如果`computed`属性的属性值是函数，那么默认使用`get`方法，函数的返回值就是属性的属性值；在`computed`中，属性有一个`get`方法和一个`set`方法，当数据发生变化时，会调用`set`方法。

### Watch
- 它**不支持缓存**，数据变化时，它就会触发相应的操作
- **支持异步监听**
- 监听的函数接收`两个参数`，第一个参数是最新的值，第二个是变化之前的值
- 当一个属性发生变化时，就需要执行相应的操作
- 监听数据必须是`data`中声明的或者父组件传递过来的`props`中的数据，当发生变化时，会触发其他操作，函数有两个的参数
    - **immediate**：组件加载立即触发回调函数
    - **deep**：深度监听，发现数据内部的变化，在复杂数据类型中使用，例如数组中的对象发生变化。需要注意的是，`deep`无法监听到数组和对象内部的变化。

当想要执行异步或者昂贵的操作以响应不断的变化时，就需要使用`watch`。

### 适用场景

- 当需要进行数值计算,并且依赖于其它数据时，应该使用 `computed`，因为可以利用 `computed` 的缓存特性，避免每次获取值时都要重新计算。
- 当需要在数据变化时执行异步或开销较大的操作时，应该使用 `watch`，使用 `watch` 选项允许执行异步操作 ( 访问一个 `API` )，限制执行该操作的频率，并在得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

### 总结

- `computed` 计算属性 : 依赖其它属性值，并且 `computed` 的值有缓存，只有它依赖的属性值发生改变，下一次获取 `computed` 的值时才会重新计算 `computed` 的值。
- `watch` 侦听器 : 更多的是观察的作用，无缓存性，类似于某些数据的监听回调，每当监听的数据变化时都会执行回调进行后续操作。

## Vue插件
`Vue`插件通常用来为 `Vue` 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：

- 添加全局方法或者属性。如: `vue-custom-element`
- 添加全局资源：指令/过滤器/过渡等。如 `vue-touch`
- 添加全局公共组件 `Vue.component()`
- 添加全局公共指令 `Vue.directive()`
- 通过全局混入来添加一些组件选项。如`vue-router`
- 一个库，提供自己的 `API`，同时提供上面提到的一个或多个功能。如`vue-router`

## Vue组件之间通信方式有哪些

组件传参的各种方式

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf775050e1f948bfa52f3c79b3a3e538~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 组件通信常用方式有以下几种

- props/$emit
- provide/inject
- $parent/$children
- $attr/$listeners
- ref
- $root
- eventbus
- vuex/pinia

### 根据组件之间关系讨论组件通信最为清晰有效

- 父子组件
    - props/$emit/$parent/ref/$attrs
- 兄弟组件
    - $parent/$root/eventbus/vuex/pinia
- 跨层级关系
    - eventbus/vuex/pinia/provide+inject

## 简述 Vue 的生命周期以及每个阶段做的事
每个 `Vue` 组件实例被创建后都会经过一系列初始化步骤，比如，它需要数据观测，模板编译，挂载实例到 `dom` 上，以及数据变化时更新 `dom` 。**这个过程中会运行叫做生命周期钩子的函数，以便用户在特定阶段有机会添加他们自己的代码**。

### 生命周期对比

`Vue` 生命周期总共可以分为8个阶段：**创建前后**, **载入前后**, **更新前后**, **销毁前后**，以及一些特殊场景的生命周期。`vue3` 中新增了三个用于调试和服务端渲染场景。

| Vue2生命周期  | Vue3生命周期      | 描述                                     |
| ------------- | ----------------- | ---------------------------------------- |
| beforeCreate  | beforeCreate      | 组件实例被创建之初                       |
| created       | created           | 组件实例已经完全创建                     |
| beforeMount   | beforeMount       | 组件挂载之前                             |
| mounted       | mounted           | 组件挂载到实例上去之后                   |
| beforeUpdate  | beforeUpdate      | 组件数据发生变化，更新之前               |
| updated       | updated           | 数据数据更新之后                         |
| beforeDestroy | **beforeUnmount** | 组件实例销毁之前                         |
| destroyed     | **unmounted**     | 组件实例销毁之后                         |
| activated     | activated         | keep-alive 缓存的组件激活时              |
| deactivated   | deactivated       | keep-alive 缓存的组件停用时调用          |
| errorCaptured | errorCaptured     | 捕获一个来自子孙组件的错误时被调用       |
| -             | renderTracked     | 调试钩子，响应式依赖被收集时调用         |
| -             | renderTriggered   | 调试钩子，响应式依赖被触发时调用         |
| -             | serverPrefetch    | ssr only，组件实例在服务器上被渲染前调用 |

### 声明周期流程图

#### Vue2生命周流程图

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89deb213d6fe4b64b0724f1cb1e09978~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c77dcde01e14cfc9bcc0c1550d471df~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

#### Vue3声明周期流程图

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/779f7121823d4118a5b6ad2aa4007c28~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 各个阶段所处理的事

- `beforeCreate` ：通常用于插件开发中执行一些初始化任务
- `created` ：组件初始化完毕，可以访问各种数据，获取接口数据等
- `mounted` ：`dom` 已创建，可用于获取访问数据和`dom`元素；访问子组件等。
- `beforeUpdate` ：此时 `view` 层还未更新，可用于获取更新前各种状态
- `updated` ：完成 `view` 层的更新，更新后，所有状态已是最新
- `beforeunmount` ：实例被销毁前调用，可用于一些定时器或订阅的取消
- `unmounted` ：销毁一个实例。可清理它与其它实例的连接，解绑它的全部指令及事件监听器

### 子组件和父组件执行顺序
#### 加载渲染过程
- 父组件 `beforeCreate`
- 父组件 `created`
- 父组件 `beforeMount`
- 子组件 `beforeCreate`
- 子组件 `created`
- 子组件 `beforeMount`
- 子组件 `mounted`
- 父组件 `mounted`

#### 更新过程
- 父组件 `beforeUpdate`
- 子组件 `beforeUpdate`
- 子组件 `updated`
- 父组件 `updated`

#### 销毁过程：
- 父组件 `beforeDestroy`
- 子组件 `beforeDestroy`
- 子组件 `destroyed`
- 父组件 `destoryed`

### 一般在哪个生命周期请求异步数据
我们可以在钩子函数 `created` 、 `beforeMount` 、 `mounted`  中进行调用，因为在这三个钩子函数中， `data` 已经创建，可以将服务端端返回的数据进行赋值。

推荐在 `created` 钩子函数中调用异步请求，因为在 `created` 钩子函数中调用异步请求有以下优点：
- 能更快获取到服务端数据，减少页面加载时间，用户体验更好；
- `SSR` 不支持 `beforeMount` `、mounted` 钩子函数，放在 `created` 中有助于一致性。

## 组件缓存 `keep-alive`
组件的缓存可以在进行动态组件切换的时候**对组件内部数据进行缓存**,而不是走销毁流程

使用场景: 多表单切换,对表单内数据进行保存等

`keep-alive` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。
`keep-alive` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。 当组件在<keep-alive>内被切换，它的 `activated` 和 `deactivated` 这两个生命周期钩子函数将会被对应执行 

### `keep-alive` 的参数( `include` , `exclude` )
- `include` (包含): 名称匹配的组件会被缓存--> `include` 的值为组件的 `name` 。
- `exclude` (排除): 任何名称匹配的组件都不会被缓存。
- `max` - 数量 决定最多可以缓存多少组件。

### `keep-alive` 的使用

- 搭配`<component></component>`使用
- 搭配路由使用 ( 需配置路由`meta`信息的`keepAlive`属性 )
- 清除缓存组件
    - 在组件跳转之前使用后置路由守卫判断组件是否缓存
    - `( beforeRouteLeave( to, from, next ){ from.meta.keepAlive = false }`


### `keep-alive` 的两个钩子函数

| activated                            | deactivated                          |
| ------------------------------------ | ------------------------------------ |
| 在 `keep-alive` 组件激活时调用       | 在`keep-alive` 组件停用时调用        |
| 该钩子函数在服务器端渲染期间不被调用 | 该钩子函数在服务器端渲染期间不被调用 |

使用`keep-alive`会将数据保留在内存中，如果要在每次进入页面的时候获取最新的数据，需要在 `activated`阶段获取数据，承担原来`created`钩子函数中获取数据的任务。

### `keep-alive` 主要流程
- 判断组件 `name` ，不在 `include` 或者在 `exclude` 中，直接返回 `vnode，说明该组件不被缓存。`
- 获取组件实例 `key` ，如果有获取实例的 `key` ，否则重新生成。
- `key` 生成规则， `cid +"∶∶"+ tag` ，仅靠`cid`是不够的，因为相同的构造函数可以注册为不同的本地组件。
- 如果缓存对象内存在，则直接从缓存对象中获取组件实例给 `vnode` ，不存在则添加到缓存对象中。 
- 最大缓存数量，当缓存组件数量超过 `max` 值时，清除 `keys` 数组内第一个组件。

### `keep-alive` 的实现

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/846880f48f1e450198f2e28444d0f6a9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

## 过滤器的作用，如何实现一个过滤器
根据过滤器的名称，过滤器是用来过滤数据的，在`Vue`中使用`filters`来过滤数据，`filters`不会修改数据，而是过滤数据，改变用户看到的输出（计算属性 `computed` ，方法 `methods` 都是通过修改数据来处理数据格式的输出显示）

**使用场景：**

- 需要格式化数据的情况，比如需要处理时间、价格等数据格式的输出 `/` 显示。
- 比如后端返回一个 年月日的日期字符串，前端需要展示为 `多少天前` 的数据格式，此时就可以用`fliters`过滤器来处理数据。

过滤器是一个函数，它会把表达式中的值始终当作函数的第一个参数。过滤器用在插值表达式 `{{ }}` 和 `v-bind` 表达式 中，然后放在操作符` | `后面进行指示。

例如，在显示金额，给商品价格添加单位：

```javascript
<li>商品价格:{{item.price|filterPrice}}</li>

filters:{
    filterPrice(price){
        retrun price ? `￥${price}` : "--"
    }
}
```

## `slot` 插槽
`slot` 又名插槽，是`Vue`的**内容分发机制**，组件内部的模板引擎使用`slot`元素作为承载分发内容的出口。插槽`slot`是子组件的一个模板标签元素，而这一个标签元素是否显示，以及怎么显示是由父组件决定的。

**通过插槽可以让用户可以拓展组件，去更好地复用组件和对其做定制化处理**

通过slot插槽向组件内部指定位置传递内容，完成这个复用组件在不同场景的应用,比如布局组件、表格列、下拉选、弹框显示内容等

### 分类

#### 默认插槽
子组件用`<slot>`标签来确定渲染的位置，标签里面可以放`DOM`结构，当父组件使用的时候没有往插槽传入内容，标签内`DOM`结构就会显示在页面

父组件在使用的时候，直接在子组件的标签内写入内容即可

#### 具名插槽
子组件用`name`属性来表示插槽的名字，不传为默认插槽
父组件中在使用时在默认插槽的基础上加上`slot`属性，值为子组件插槽`name`属性值

#### 作用域插槽
子组件在作用域上绑定属性来将子组件的信息传给父组件使用，这些属性会被挂在父组件`v-slot`接受的对象上
父组件中在使用时通过`v-slot:（简写：#）`获取子组件的信息，在内容中使用

### 小结

- `v-slot`属性只能在`<template>`上使用，但在只有默认插槽时可以在组件标签上使用
- 默认插槽名为`default`，可以省略`default`直接写`v-slot`
- 缩写为`#`时不能不写参数，写成`#default`
- 可以通过解构获取`v-slot={user}`，还可以重命名`v-slot="{user: newName}"`和定义默认值`v-slot="{user = '默认值'}"`

## `Vue`为什么采用异步渲染
`Vue` 是组件级更新，如果不采用异步更新，那么每次更新数据都会对当前组件进行重新渲染，所以为了性能，`Vue` 会在本轮数据更新后，在异步更新视图。核心思想`nextTick` 。

`dep.notify()` 通知 `watcher`进行更新，`subs[i].update` 依次调用 `watcher` 的`update` ，`queueWatcher` 将`watcher` 去重放入队列， `nextTick(flushSchedulerQueue)`在下一`tick`中刷新`watcher`队列（异步）。

##  `$nextTick` 原理及作用

其实一句话就可以把`$nextTick`这个东西讲明白：**就是你放在`$nextTick`当中的操作不会立即执行，而是等数据更新、`DOM`更新完成之后再执行，这样我们拿到的肯定就是最新的了**。

`Vue`的响应式并不是只数据发生变化之后，`DOM`就立刻发生变化，而是按照一定的策略进行`DOM`的更新。

`DOM`更新有两种选择，一个是在本次事件循环的最后进行一次`DOM`更新，另一种是把`DOM`更新放在下一轮的事件循环当中。`Vue`优先选择第一种，只有当环境不支持的时候才触发第二种机制。

虽然性能上提高了很多，但这个时候问题就出现了。我已经把数据改掉了，但是它的更新异步的，而我在获取的时候，它还没有来得及改，这个时候就需要用到 `nextTick`

在以下情况下，会用到nextTick：

- 在数据变化后执行的某个操作，而这个操作需要使用随数据变化而变化的`DOM`结构的时候，这个操作就需要方法在`nextTick()`的回调函数中。
- 在vue生命周期中，如果在`created()`钩子进行`DOM`操作，也一定要放在`nextTick()`的回调函数中。

因为在`created()`钩子函数中，页面的`DOM`还未渲染，这时候也没办法操作`DOM`，所以，此时如果想要操作`DOM`，必须将操作的代码放在`nextTick()`的回调函数中。

## Vue 项目性能优化
### 编码阶段
- 尽量减少 `data` 中的数据，`data`中的数据都会增加`getter`和`setter`，会收集对应的`watcher`
- `v-if`和`v-for`不能一起使用
- 如果需要使用`v-for`给每项元素绑定事件时使用事件代理
- `SPA` 页面采用`keep-alive`缓存组件
- 合理使用 `v-if` 和 `v-show`
- `key`保证唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需导入
- 长列表滚动到可视区域动态加载
- 图片懒加载

### 打包优化
- 压缩代码
- `Tree Shaking/Scope Hoisting`
- 使用`cdn`加载第三方模块
- 多线程打包 `t`hread-loader`
- `splitChunks`抽离公共文件
- `sourceMap`优化

### 用户体验
- 骨架屏
- PWA
- 还可以使用缓存(客户端缓存、服务端缓存)优化、服务端开启gzip压缩等

## Vue的template模版编译原理
`Vue`中的模板`template`无法被浏览器解析并渲染，因为这不属于浏览器的标准，不是正确的`HTML`语法，所有需要将`template`转化成一个`JavaScript`函数，这样浏览器就可以执行这一个函数并渲染出对应的`HTML`元素，就可以让视图跑起来了，这一个转化的过程，就成为模板编译。模板编译又分三个阶段，解析`parse`，优化`optimize`，生成`generate`，最终生成可执行函数`render`。

### 解析阶段
使用大量的正则表达式对`template`字符串进行解析，将标签、指令、属性等转化为抽象语法树`AST`。
### 优化阶段
遍历`AST`，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行`diff`比较时，直接跳过这一些静态节点，优化`runtime`的性能
### 生成阶段
将最终的`AST`转化为`render`函数字符串

## `template` VS `jsx`

对于 `runtime` 来说，只需要保证组件存在 `render` 函数即可，而有了预编译之后，只需要保证构建过程中生成 `render` 函数就可以。在 `webpack` 中，使用`vue-loader`编译`.vue`文件，内部依赖的`vue-template-compiler`模块，在 `webpack` 构建过程中，将`template`预编译成 `render` 函数。与 `react` 类似，在添加了`jsx`的语法糖解析器`babel-plugin-transform-vue-jsx`之后，就可以直接手写`render`函数。

所以，**`template`和`jsx`的都是`render`的一种表现形式**，不同的是：`JSX`相对于`template`而言，具有更高的灵活性，在复杂的组件中，更具有优势，而 `template` 虽然显得有些呆滞。但是 `template` 在代码结构上更符合视图与逻辑分离的习惯，更简单、更直观、更好维护。


