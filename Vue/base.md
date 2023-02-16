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

