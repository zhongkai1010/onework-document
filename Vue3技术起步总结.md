# Vue3技术起步总结
# 一、参考资料
Vue：[https://v3.cn.vuejs.org/guide/introduction.html](https://v3.cn.vuejs.org/guide/introduction.html)

Vue CLI：[https://cli.vuejs.org/](https://cli.vuejs.org/)

es6：[https://www.bookstack.cn/read/es6-3rd/sidebar.md](https://www.bookstack.cn/read/es6-3rd/sidebar.md)

# 二、基础知识
## （1）指令
### v-bind
```html
<!-- 完整语法 -->
<a v-bind:href="url"> ... </a>
<!-- 缩写 -->
<a :href="url"> ... </a>
<!-- 动态参数的缩写 -->
<a :[key]="url"> ... </a>
```
### v-on
```html
<!-- 完整语法 -->
<a v-on:click="doSomething"> ... </a>
<!-- 缩写 -->
<a @click="doSomething"> ... </a>
<!-- 动态参数的缩写 -->
<a @[event]="doSomething"> ... </a>
```
### v-bind:\[attributeName\]
```html
<a v-bind:[attributeName]="url"> ... </a>
```
### v-model
```html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</div>
```
## （2）Data Property 和方法
### Data
组件的 `data` 选项是一个函数。Vue 会在创建新组件实例的过程中调用此函数。它应该返回一个对象，然后 Vue 会通过响应性系统将其包裹起来，并以 `$data` 的形式存储在组件实例中。

```javascript
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.$data.count) // => 4
console.log(vm.count)       // => 4

// 修改 vm.count 的值也会更新 $data.count
vm.count = 5
console.log(vm.$data.count) // => 5

// 反之亦然
vm.$data.count = 6
console.log(vm.count) // => 6
```
### 方法
我们用 `methods` 选项向组件实例添加方法，它应该是一个包含所需方法的对象：

```javascript
const app = Vue.createApp({
  data() {
    return { count: 4 }
  },
  methods: {
    increment() {
      // `this` 指向该组件实例
      this.count++
    }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4

vm.increment()

console.log(vm.count) // => 5
```
### 防抖和节流
```javascript
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
  Vue.createApp({
    methods: {
      // 用 Lodash 的防抖函数
      click: _.debounce(function() {
        // ... 响应点击 ...
      }, 500)
    }
  }).mount('#app')
</script>
```
## （3）Class 与 Style 绑定
操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是 attribute，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可。

### 绑定 HTML Class
模板：

```html
<!--对象语法-->
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
<!--数组语法-->
<div :class="[activeClass, errorClass]"></div>
```
data：

```javascript
/**对象语法**/
data() {
  return {
    isActive: true,
    hasError: false
  }
}
/**数组语法**/
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```
渲染的结果为：

```html
<!--对象语法-->
<div class="static active"></div>
<!--数组语法-->
<div class="active text-danger"></div>
```
绑定的数据对象不必内联定义在模板里：

```html
<div :class="classObject"></div>
```
```javascript
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```
### 在组件上使用
```javascript
const app = Vue.createApp({})

app.component('my-component', {
  template: `<p class="foo bar">Hi!</p>`
})
```
```html
<div id="app">
  <my-component class="baz boo"></my-component>
</div>
```
HTML 将被渲染为：

```html
<p class="foo bar baz boo">Hi</p>
```
### 绑定内联样式
```html
<div :style="styleObject"></div>
<!--数组语法-->
<div :style="[baseStyles, overridingStyles]"></div>
```
```javascript
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```
## （4）条件渲染
### v-if
```html
<h1 v-if="awesome">Vue is awesome!</h1>

```
### v-else
```html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```
### v-else-if
```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```
### v-show
```html
<h1 v-show="ok">Hello!</h1>
```
## （5）列表渲染
```html
<ul id="array-rendering">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```
```javascript
Vue.createApp({
  data() {
    return {
      items: [{ message: 'Foo' }, { message: 'Bar' }]
    }
  }
}).mount('#array-rendering')
```
## （6）事件处理
### 监听事件
```html
<div id="basic-event">
  <button @click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```
```javascript
Vue.createApp({
  data() {
    return {
      counter: 0
    }
  }
}).mount('#basic-event')
```
### 多事件处理器
```html
<!-- 这两个 one() 和 two() 将执行按钮点击事件 -->
<button @click="one($event), two($event)">
  Submit
</button>
```
```javascript
// ...
methods: {
  one(event) {
    // 第一个事件处理器逻辑...
  },
  two(event) {
   // 第二个事件处理器逻辑...
  }
}
```
### 事件修饰符
```html
<!-- 阻止单击事件继续冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
```
## （7）表单输入绑定
### 基础用法
```html
<!-- 文本 (Text) -->
<input v-model="message" placeholder="edit me" />
<p>Message is: {{ message }}</p>

<!-- 多行文本 (Textarea)-->
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br />
<textarea v-model="message" placeholder="add multiple lines"></textarea>

<!-- 复选框 (Checkbox) -->
<input type="checkbox" id="checkbox" v-model="checked" />
<input type="checkbox" v-model="toggle" true-value="yes" false-value="no" />
<label for="checkbox">{{ checked }}</label>

<!-- 单选框 (Radio) -->
<div id="v-model-radiobutton">
  <input type="radio" id="one" value="One" v-model="picked" />
  <label for="one">One</label>
  <br />
  <input type="radio" id="two" value="Two" v-model="picked" />
  <label for="two">Two</label>
  <br />
  <span>Picked: {{ picked }}</span>
</div>
<input type="radio" v-model="pick" v-bind:value="a" />

<!-- 选择框 (Select) -->
<div id="v-model-select" class="demo">
  <select v-model="selected">
    <option disabled value="">Please select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>
<select v-model="selected">
  <!-- 内联对象字面量 -->
  <option :value="{ number: 123 }">123</option>
</select>
```
### 修饰符
* .lazy
在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了上述输入法组织文字时)。你可以添加 `lazy` 修饰符，从而转为在 `change` 事件之后进行同步：

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" />
```
* .number
如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```html
<input v-model.number="age" type="text" />
```
* .trim
如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```Plain Text
<input v-model.trim="msg" />
```
## （8）组件基础
### 基本示例
```javascript
// 创建一个Vue 应用
const app = Vue.createApp({})

// 定义一个名为 button-counter 的新全局组件
app.component('button-counter', {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
})
```
### 通过 Prop 向子组件传递数据
Prop 是你可以在组件上注册的一些自定义 attribute。

```javascript
const app = Vue.createApp({})

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})

app.mount('#blog-post-demo')
```
```html
<div id="blog-post-demo" class="demo">
  <blog-post title="My journey with Vue"></blog-post>
  <blog-post title="Blogging with Vue"></blog-post>
  <blog-post title="Why Vue is so fun"></blog-post>
</div>
```
### 监听子组件事件
同时子组件可以通过调用内建的 **\$emit** 方法并传入事件名称来触发一个事件：

```html
<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
       v-for="post in posts"
       :key="post.id"
       :title="post.title"
       @enlarge-text="postFontSize += 0.1"
    ></blog-post>
  </div>
</div>
```
```javascript
const app = Vue.createApp({
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue'},
        { id: 2, title: 'Blogging with Vue'},
        { id: 3, title: 'Why Vue is so fun'}
      ],
      postFontSize: 1
    }
  }
})

app.component('blog-post', {
  props: ['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button @click="$emit('enlargeText')">
        Enlarge text
      </button>
    </div>
  `
})

app.mount('#blog-posts-events-demo')
```
### 动态组件
```html
<div id="dynamic-component-demo" class="demo">
  <button
     v-for="tab in tabs"
     v-bind:key="tab"
     v-bind:class="['tab-button', { active: currentTab === tab }]"
     v-on:click="currentTab = tab"
   >
    {{ tab }}
  </button>

  <component v-bind:is="currentTabComponent" class="tab"></component>
</div>
```
```javascript
const app = Vue.createApp({
  data() {
    return {
      currentTab: 'Home',
      tabs: ['Home', 'Posts', 'Archive']
    }
  },
  computed: {
    currentTabComponent() {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})

app.component('tab-home', {
  template: `<div class="demo-tab">Home component</div>`
})
app.component('tab-posts', {
  template: `<div class="demo-tab">Posts component</div>`
})
app.component('tab-archive', {
  template: `<div class="demo-tab">Archive component</div>`
})

app.mount('#dynamic-component-demo')
```
# 三、工具
## （1）单文件组件
Vue SFC 是经典的 HTML、CSS 与 JavaScript 三个经典组合的自然延伸。每个 `*.vue` 文件由三种类型的顶层代码块组成：`<template>`、`<script>` 与 `<style>`：

* `<script>` 部分是一个标准的 JavaScript 模块。它应该导出一个 Vue 组件定义作为其默认导出。
* `<template>` 部分定义了组件的模板。
* `<style>` 部分定义了与此组件关联的 CSS。

```html
<script>
export default {
  data() {
    return {
      greeting: 'Hello World!'
    }
  }
}
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```
## （2）TypeScript
待定

# 四、组件使用
### （1）组件命名规则
对组件的命名可能与你打算在哪使用它有关。当直接在 DOM 中 (而不是在字符串模板或[单文件组件](https://v3.cn.vuejs.org/guide/single-file-component.html)中) 使用一个组件的时候，我们强烈推荐遵循 [W3C 规范](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name)来给自定义标签命名：

   1. 全部小写
   2. 包含连字符 (及：即有多个单词与连字符符号连接)

组件名大小写

* 使用 kebab-case

```javascript
app.component('my-component-name', {
  /* ... */
})
```
* 使用 PascalCase

```javascript
app.component('MyComponentName', {
  /* ... */
})
```
### （2）全局注册
注册之后可以用在任何新创建的组件实例的模板中

```javascript
Vue.createApp({...}).component('my-component-name', {
  // ... 选项 ...
})
```
### （3）局部组件
```Plain Text
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
  // ...
}
```
### （4）生命周期
|选项式 API|Hook inside `setup`|
| ----- | ----- |
|beforeCreate|Not needed\*|
|created|Not needed\*|
|beforeMount|onBeforeMount|
|mounted|onMounted|
|beforeUpdate|onBeforeUpdate|
|updated|onUpdated|
|beforeUnmount|onBeforeUnmount|
|unmounted|onUnmounted|
|errorCaptured|onErrorCaptured|
|renderTracked|onRenderTracked|
|renderTriggered|onRenderTriggered|
|activated|onActivated|
|deactivated|onDeactivated|

### （5）组件API
#### **a）setup**
一个组件选项，在组件被创建**之前**，`props` 被解析之后执行。它是组合式 API 的入口。

参数

* {Data} props
* {SetupContext} context

```javascript

export default {
  setup(props, context) {
    // Attribute (非响应式对象，等同于 $attrs)
    console.log(context.attrs)

    // 插槽 (非响应式对象，等同于 $slots)
    console.log(context.slots)

    // 触发事件 (方法，等同于 $emit)
    console.log(context.emit)

    // 暴露公共 property (函数)
    console.log(context.expose)
  }
}
```


# 五、Vue 3 新特性
## （1）单文件组件`<script setup>`
当使用 `<script setup>` 的时候，任何在 `<script setup>` 声明的顶层的绑定 (包括变量，函数声明，以及 import 引入的内容) 都能在模板中直接使用：

```Plain Text
<script setup>
// 变量
const msg = 'Hello!'

// 函数
function log() {
  console.log(msg)
}
</script>

<template>
  <div @click="log">{{ msg }}</div>
</template>
```
# 六、问题质疑
## （1）`createApp(App).mount('#app')`中如何将`App`组件加入到`#app`中,其中`#app`是如何关联到`public`文件夹中的`index.html`？
## （2）defineComponent 具体其说明作用，与` export default {}`写法有什么区别？
defineComponent 本身的功能很简单，但是最主要的功能是为了 ts 下的类型推到。对于一个 ts 文件，如果我们直接写

```Plain Text
export default {}
```
这个时候，对于编辑器而言，{} 只是一个 Object 的类型，无法有针对性的提示我们对于 vue 组件来说 {} 里应该有哪些属性。但是增加一层 defineComponet 的话，

```Plain Text
export default defineComponent({})
```
这时，{} 就变成了 defineComponent 的参数，那么对参数类型的提示，就可以实现对 {} 中属性的提示，外还可以进行对参数的一些类型推导等操作。

> 但是上面的例子，如果你在 vscode 的用 .vue 文件中尝试的话，会发现不写 defineComponent 也一样有提示。这个其实是 Vetur 插件进行了处理。



