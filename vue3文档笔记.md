# vue3文档笔记

与大多数应用方法不同的是，`mount` 不返回应用本身。相反，它返回的是根组件实例。

实际的mvvm？

组件实例的所有 property，无论如何定义，都可以在组件的模板中访问。

Vue 还通过组件实例暴露了一些内置 property，如 `$attrs` 和 `$emit`。这些 property 都有一个 `$` 前缀，以避免与用户定义的 property 名冲突。

> 不要在选项 property 或回调上使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。![实例的生命周期](https://v3.cn.vuejs.org/images/lifecycle.svg)

### v-once的注意事项
通过使用 v-once 指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上的其它数据绑定：

双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用v-html 指令：

### v-bind操作属性
如果绑定的值是 null 或 undefined，那么该 attribute 将不会被包含在渲染的元素上。
```js
<button v-bind:disabled="isButtonDisabled">按钮</button>
```
如果 isButtonDisabled 的值是 truthy[1]，那么 disabled attribute 将被包含在内。如果该值是一个空字符串，它也会被包括在内，与 ```<button disabled="">``` 保持一致


### 指令??
1. 参数， 动态参数
2. 修饰符
   - ```.prevent``` 

### v-on
### v-bind
### v-if用来插入和移除元素


Vue 使用 $ 前缀通过组件实例暴露自己的内置 API。它还为内部 property 保留 _ 前缀。你应该避免使用这两个字符开头的的顶级 data property 名


### data 
> 一个函数
### methods
Vue 自动为 methods 绑定 this，以便于它始终指向组件实例。这将确保方法在用作事件监听或回调时保持正确的 this 指向。在定义 methods 时应避免使用箭头函数，因为这会阻止 Vue 绑定恰当的 this 指向。

### 模板中调用方法
从模板调用的方法不应该有任何副作用，比如更改数据或触发异步进程。如果你想这么做，应该换做生命周期钩子。

### 重点看他的如何在声明周期函数中绑定
```js
app.component('save-button', {
  created() {
    // 用 Lodash 的防抖函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 响应点击 ...
    }
  },
  template: `
    <button @click="debouncedClick">
      Save
    </button>
  `
})
```

### 引入计算属性 computed
> 初衷：模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护

At this point, the template is no longer simple and declarative. You have to look at it for a second before realizing that it performs a calculation depending on author.books.

computed properties are cached based on their reactive dependencies.

```Date.now()``` is not reactive.

### 为计算属性提供settter
> This is most useful when you want to perform asynchronous or expensive operations in response to changing data.
```
```

### 监听器 Watcher
### 样式绑定
> since class list and in-line style are both atrribute we can use
> ```v-bind``` to handle them.


This will render the same result. We can also bind to a computed property that returns an object. This is a common and powerful pattern:

自定义组件上的表现，直接将class混入自定义组件的根元素上
If your component has multiple root elements, you would need to define which component will receive this class. You can do this using $attrs component property:
使用$attr来控制渲染到哪个根元素上去

### 条件渲染
- v-if / v-else-if / v-else
- v-show
The difference is that an element with v-show will always be rendered and remain in the DOM; v-show only toggles the display CSS property of the element.

Generally speaking, v-if has higher toggle costs while v-show has higher initial render costs. 
So prefer v-show if you need to toggle something very often, and prefer v-if if the condition is unlikely to change at runtime

v-if和v-for不推荐一起混合在一个元素上使用
Never use v-if on the same element as v-for
- To filter items in a list (e.g. v-for="user in users" v-if="user.isActive"). In these cases, replace users with a new computed property that returns your filtered list (e.g. activeUsers).

- To avoid rendering a list if it should be hidden (e.g. v-for="user in users" v-if="shouldShowUsers"). In these cases, move the v-if to a container element (e.g. ul, ol).

When they exist on the same node, v-if has a higher priority than v-for. That means the v-if condition will not have access to variables from the scope of the v-for:
v-if的优先级更高，在v-if的作用域中拿不到v-for里面的数据

### 列表的渲染 v-for
- 数组 
- 对象
```js
for in是遍历（object）键名，for of是遍历（array）键值
```

```js
v-for="(name,key) in obj"
v-for="()"
```
### v-for就地更新
当 Vue 正在更新使用 v-for 渲染的元素列表时，它默认使用“就地更新”的策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。

### 需要为v-if提供一个唯一的id(基本类型)

### 数组的变异方法
- push
- pop
- shift
- unshift
- splice
- sort
- reverse

替换数组同样的高效

在组件中使用v-for,任何数据都不会被自动传递到组件里，因为组件有自己独立的作用域。
### 事件处理
> 方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。
- 直接在v-on后写逻辑
- 绑定一个方法
```vue
<button @click="handleClick">
</button>
```
- 语句调用
- 多事件处理
```vue
<button @click="one($event), two($event)">
  Submit
</button>
```
### 事件修饰符
- stop (event.stopPropagation)
- prevent (event.preventDefault)
- capture
- self
- once
- passive

> 不像其它只能对原生的 DOM 事件起作用的修饰符，.once 修饰符还能被用到自定义的组件事件上。如果你还没有阅读关于组件的文档，现在大可不必担心。