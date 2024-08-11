## Js的 Object.defineProperty 的用法

Object.defineProperty 是 JavaScript 中的一个方法

它允许**精确添加或修改对象的属性**。这个方法接受三个参数:

    1. 对象 (obj):要在其上定义属性的对象。
    
    2. 属性名 (prop):要定义或修改的属性的名称。
    
    3. 属性描述符 (descriptor):一个对象,包含了属性的特性,如 value, writable, enumerable, configurable 等

Object.defineProperty 允许**为属性定义 getter 和 setter 函数**。这些函数可以在访问或修改属性值时被调用。

##  响应式系统

Vue 2.x 版本中,响应式系统的实现依赖于 Object.defineProperty 方法来劫持属性的访问器（getter/setter）。以下是Vue初始化响应式属性的基本步骤:

    1. 递归遍历:Vue 会递归遍历 data 选项中的所有属性。
    
    2. 属性定义:对于每个属性,Vue 使用Object.defineProperty重新定义它,而不是使用默认的赋值方式。
    3. 依赖收集:通过在getter中收集依赖,Vue能够追踪哪些计算属性或者渲染函数依赖了这个属性。
    4. 变更通知:当属性的值发生变化时,在setter中通知所有依赖于这个属性的计算属性和组件,使得它们可以重新计算或重新渲染

在Vue 3中,响应式系统得到了重写,使用了ES6的Proxy来实现,这使得Vue3的响应式系统更加高效和强大

​    Proxy可以直接响应对象属性的访问和修改,不需要使用 Object.defineProperty来定义getter和setter

## Vue的模版语法

插值

​    文本（内容）用 {{}} 插值

​    属性 attribute 用v-bind 指令插值



指令
​    指令 (Directives) 是带有 v- 前缀的特殊 attribute    


## Vue的计算属性、方法和侦听器

计算属性

先来看下计算属性

   ```Vue
  new Vue({
    el: "#app",
    data: {
      a: 1,
      b: 2,
    },
    computed: {
      // 使用对象字面量定义计算属性
      computedValue: {
        // getter 函数
        get: function () {
          return this.a + this.b;
        },
        // 可选的 setter 函数
        set: function (newValue) {
          this.a = newValue - this.b;
        },
      },
    },
  });
   ```

这个是计算属性的完整形式，常用的是他的语法糖形式

    new Vue({
      el: "#app",
      data: {
        a: 1,
        b: 2,
      },
      computed: {
        // 计算属性的 getter
        computedValue: function () {
          return this.a + this.b;
        },
      },
    }) 

  

![Vue_initComputed_code.png](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\img\Vue_initComputed_code.png)

对于这个computed Vue2中 是这个么处理的 ，和data中的类似 都是defineProperty劫持。

## 动态地绑定一个或多个属性(V-BIND)

`v-bind` 是 Vue.js 中的一个指令，用于在模板中动态地绑定 HTML 属性或组件的 props 到表达式。以下是 `v-bind` 的详细用法：

### 基本用法

1. **绑定单个属性**: 使用 `v-bind` 可以绑定 HTML 元素的属性到 Vue 实例的数据属性上。例如，将 `src` 属性绑定到 `imageUrl` 数据属性：

   ```
   html
   <img v-bind:src="imageUrl" alt="图片描述">
   ```

   或者使用缩写形式：

   ```
   html
   <img :src="imageUrl" alt="图片描述">
   ```

2. **对象语法**: 当需要绑定多个属性时，可以使用对象语法。`v-bind` 可以接受一个对象，对象的每个键值对都会被绑定为一个属性：

   ```
   html
   <div v-bind="{ id: someId, title: someTitle }"></div>
   ```

   这相当于：

   ```
   html
   <div id="{{ someId }}" title="{{ someTitle }}"></div>
   ```

3. **数组语法**: 如果属性名是动态的，可以使用数组语法，将属性名和属性值分别放在数组中：

   ```
   html
   <div v-bind:[somePropName]="someDynamicPropValue"></div>
   ```

### 绑定 CSS 类

1. **绑定单个类**: 使用 `v-bind:class` 可以动态地绑定 CSS 类：

   ```
   html
   <div v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
   ```

   这会根据 `isActive` 和 `hasError` 的值动态添加或移除 `active` 和 `text-danger` 类。

2. **绑定多个类**: 可以绑定一个包含类名的数组：

   ```
   html
   <div v-bind:class="['class1', 'class2']"></div>
   ```

   或者使用对象语法来动态地添加类：

   ```
   html
   <div v-bind:class="{ class1: condition1, class2: condition2 }"></div>
   ```

### 绑定内联样式

1. **绑定内联样式**: 使用 `v-bind:style` 可以动态地绑定内联样式：

   ```
   html
   <div v-bind:style="styleObject"></div>
   ```

   其中 `styleObject` 是一个包含 CSS 属性的对象：

   ```
   javascript复制data() {
     return {
       styleObject: {
         color: 'red',
         fontSize: '13px'
       }
     };
   }
   ```

   ![v-bind.png](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\img\v-bind.png)

   ![v-bind-1.png](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\img\v-bind-1.png)

## 条件渲染(V-IF  & V-SHOW)

#### V-IF :指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染

实例：

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

只有在awesome 为 truthy 值时候进行渲染 这个渲染的是一个元素 （请记得什么是一个元素）

#### 用 key 管理可复用的元素

**Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。**例如，如果你允许用户在不同的登录方式之间切换：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

那么在上面的代码中切换 `loginType` 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，`<input>` 不会被替换掉——仅仅是替换了它的 `placeholder`。

这样也不总是符合实际需求，所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”

这样也不总是符合实际需求，所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 `key` attribute 即可：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

注意，`<label>` 元素仍然会被高效地复用，因为它们没有添加 `key` attribute

#### V-SHOW  ：用法大概一样，但是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS property `display`。

> **v-if 和v-show 的对比**
>
> `v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
>
> `v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
>
> 相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
>
> 一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

## 列表渲染（V-FOR)

   我们可以用 `v-for` 指令基于一个数组来渲染一个列表。`v-for` 指令需要使用 `item in items` 形式的特殊语法，其中 `items` 是源数据数组，而 `item` 则是被迭代的数组元素的**别名**。

```html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```

```vue
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```



结果：

- Foo
- Bar

同样的 这个**也可以用来遍历对象中的属性**



> 在遍历对象时，会按 `Object.keys()` 的结果遍历，但是**不能**保证它的结果在不同的 JavaScript 引擎下都一致。



> 在Vue 更新用V-FOR 渲染的列表时 采用“就地更新”的策略。

## 事件处理（V-ON)

可以用 `v-on` 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。

示例：

```html
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
```

```vue
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

然而许多事件处理逻辑会更为复杂，所以直接把 JavaScript 代码写在 `v-on` 指令中是不可行的。因此 `v-on` 还可以接收一个需要调用的方法名称。

示例：

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```

```vue
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
```

除了直接绑定到一个方法，也可以在内联 JavaScript 语句中调用方法：

```html
<div id="example-3">
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>
```

```js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message) {
      alert(message)
    }
  }
})
```



#### 事件修饰符

V-ON 绑定了事件的监听，实际使用上我们可能还会需要 对事件的冒泡进行处理，阻止一下默认的行为

我们可以这样做：

```html
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>
```

```js
// ...
methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

*<u>为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。之前提过，修饰符是由点开头的指令后缀来表示的。</u>*

```js
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>
```

还对键盘监听事件进行了包装 成为 按键修饰符



## 表单输入绑定（V-MODEL)

这个`v-model` 本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。V-MODEL  用来创建双向绑定，所谓的双向绑定 就是**数据状态更新UI** **UI更新数据状态**

实例：

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

`v-model` 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用 `value` property 和 `input` 事件；（v-model 默认是绑定 value到 Property上 默认监听 input监听事件）

- checkbox 和 radio 使用 `checked` property 和 `change` 事件；

  > ![Vue_Getcheckmodel_code.png](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\img\Vue_Getcheckmodel_code.png)
  >
  > 以此为例： checkModel 中chenge事件的回调函数 中 会根据property的类型 选择不同的方式，
  >
  > 对于数组，会根据attribute的checked是否被选中 用attribute中value 来更新 property。
  >
  > 对于非数组，会根据attribute的 checked 是否被选中 更新当时的值

- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

  >![Vue_selectModel_code.png](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\img\Vue_selectModel_code.png)

v-model 实际上就是 v-bind 绑定attribute 和v-on 的语法糖罢了

> Vue 在对多个复选框 （数组 Property）进行双向绑定时
>
> 1. **初始化**: 首先，`v-model` 绑定到一个数组，这个数组用于存储所有选中复选框的 `value`。
> 2. **渲染**: Vue.js 渲染复选框时，会遍历数组，根据数组中的内容来设置每个复选框的 `checked` 属性。如果数组中包含某个复选框的 `value`，则该复选框会被渲染为选中状态。
> 3. **事件监听**: Vue.js 为每个复选框添加 `change` 事件监听器。当用户点击复选框时，事件监听器会被触发。
> 4. **更新数组**: 当 `change` 事件发生时，事件处理函数会检查复选框的 `value` 是否已经在数组中：
>    - 如果复选框被选中，并且它的 `value` 还不在数组中，那么 `value` 会被添加到数组中。
>    - 如果复选框被取消选中，它的 `value` 会在数组中被移除。
> 5. **响应式更新**: 由于数组是响应式的，当数组内容发生变化时，Vue.js 会自动更新所有相关的 DOM，包括复选框的 `checked` 属性。
> 6. **数组更新**: 由于 `v-model` 绑定的数组是响应式的，任何对数组的修改都会触发视图的更新。这意味着，如果数组中的某个值被添加或删除，所有绑定到该数组的复选框都会相应地更新它们的 `checked` 状态

> **另外就是 v-model 还会改变property的类型？**  



## ~~组件一 (组件注册)~~

 ~~组件非常的简单，简单的学习下就可以了~~

~~先来看实例~~

```js
// 定义一个名为 button-counter 的新组件
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

~~组件是可复用的 Vue 实例，且带有一个名字：在这个例子中是 `<button-counter>`。~~

~~我们可以在一个通过 `new Vue` 创建的 Vue 根实例中，把这个组件作为自定义元素来使用：~~

```html
<div id="app">
  <button-counter></button-counter>
</div>
```

```js
new Vue({
    el: '#app' 
})
```

~~因为组件是可复用的 Vue 实例，所以它们与 `new Vue` 接收相同的选项，例如 `data`、`computed`、`watch`、`methods` 以及生命周期钩子等。仅有的例外是像 `el` 这样根实例特有的选项。~~

#### ~~组件名（存疑 dom的模板和字符串模板 目前查到的存在好几种说法 暂时分辨不了 ）~~

~~组件名分 两种形式 单词字母大写或者单词中间短横线 以后记得只用短横即可~~

> ~~`<my-component-name>` 和 `<MyComponentName>` 都是可接受的。注意，尽管如此，直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。~~
>
> 

#### ~~全局注册and局部注册~~

~~全局注册实例：~~

```js
Vue.component('my-component-name', {
  // ... 选项 ...
})
```

```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })
```

~~全局注册就是直接使用Vue.component 进行注册，也就是说它们在注册之后可以用在任何新创建的 Vue 根实例 (`new Vue`) 的模板中~~

~~局部注册实例：~~

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

```js
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

~~对于局部注册  `components` 对象中的每个 property 来说，其 property 名就是自定义元素的名字，其 property 值就是这个组件的<u>***选项对象***</u>~~

#### ~~webpack等工具的模块系统中的 局部注册~~

~~你需要在局部注册之前导入每个你想使用的组件的**<u>*选项对象*</u>**。例如，在一个假设的 `ComponentB.js` 或 `ComponentB.vue` 文件中：~~

```js
 `ComponentB.js` 
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

#### ~~webpack等工具的模块系统中的自动化全局注册~~

~~可能你的许多组件只是包裹了一个输入框或按钮之类的元素，是相对通用的。我们有时候会把它们称为基础组件，它们会在各个组件中被频繁的用到。~~

~~所以会导致很多组件里都会有一个包含基础组件的长列表：~~

```js
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```

~~如果你恰好使用了 webpack (或在内部使用了 webpack 的 Vue CLI 3+，那么就可以使用 `require.context` 只全局注册这些非常通用的基础组件。这里有一份可以让你在应用入口文件 (比如 `src/main.js`) 中全局导入基础组件的示例代码：~~

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```

>  ~~`require.context`是 Webpack 特有的一个 API，它允许你通过执行一个函数来创建自己的上下文模块。这个功能非常强大，因为它可以自动地导入一个目录下的所有模块，这对于代码分割和懒加载非常有用。~~
>
>  ~~`require.context(directory, useSubdirectories, regExp)` 接受三个参数：~~
>
>  1. ~~**directory**: 需要检索的目录的路径。~~
>  2. ~~**useSubdirectories**: 一个布尔值，指示是否搜索子目录。~~
>  3. ~~**regExp**: 一个正则表达式，用来匹配文件名。~~

## ~~组件二（prop）~~

#### ~~Prop的类型（可以通过prop传递什么样的值）~~

~~早些时候，我们提到了创建一个博文组件的事情。问题是如果你不能向这个组件传递某一篇博文的标题或内容之类的我们想展示的数据的话，它是没有办法使用的。这也正是 prop 的由来。~~

~~Prop 是你可以在组件上注册的一些自定义 attribute。当一个值传递给一个 prop attribute 的时候，它就变成了那个组件实例的一个 property。为了给博文组件传递一个标题，我们可以用一个 `props` 选项将其包含在该组件可接受的 prop 列表中：~~

```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

~~一个组件默认可以拥有任意数量的 prop，任何值都可以传递给任何 prop。在上述模板中，你会发现我们能够在组件实例中访问这个值，就像访问 `data` 中的值一样。~~

~~一个 prop 被注册之后，你就可以像这样把数据作为一个自定义 attribute 传递进来：~~

```js
<blog-post title="My journey with Vue"></blog-post>
<blog-post title="Blogging with Vue"></blog-post>
<blog-post title="Why Vue is so fun"></blog-post>
```

~~到这里，我们只看到了以字符串数组形式列出的 prop：~~

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

~~但是，通常你希望每个 prop 都有指定的值类型。这时，你可以以对象形式列出 prop，这些 property 的名称和值分别是 prop 各自的名称和类型：~~

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```

~~可以像这样给 prop 传入一个静态的值~~

```html
<blog-post title="My journey with Vue"></blog-post>
```

~~也可以像这样给prop传入一个动态的值~~

```html
<!-- 动态赋予一个变量的值 -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- 动态赋予一个复杂表达式的值 -->
<blog-post
  v-bind:title="post.title + ' by ' + post.author.name"
></blog-post>
```

~~**请注意：一个数组 一个对象**~~

```html
<!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:likes="42"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:likes="post.likes"></blog-post>

<!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>

<!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
<!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
<blog-post
  v-bind:author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- 用一个变量进行动态赋值。-->
<blog-post v-bind:author="post.author"></blog-post>
```

#### ~~Prop单向数据流（通过prop可以实现父组件向子组件传递数据的行为。）~~

> ~~<u>**注意:在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变变更这个对象或数组本身将会影响到父组件的状态。**</u>~~

#### ~~Prop验证~~ 

> ~~基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)~~

~~我们可以为组件的 prop 指定验证要求, Prop是一个什么样的类型，多个类型的用数组，对某个prop需要进行更多的控制，就用对象 例如required （是否必填）define（默认提供一个值）validator（一个验证器函数方法 ）~~

> ~~**注意那些 prop 会在一个组件实例创建之前进行验证，所以实例的 property (如 `data`、`computed` 等) 在 `default` 或 `validator` 函数中是不可用的。**~~

~~为了定制 prop 的验证方式，可以为 `props` 中的值提供一个带有验证需求的对象，而不是一个字符串数组。例如：~~

```
Vue.component('my-component', {
  props: {
    
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].includes(value)
      }
    }
  }
})
```

#### ~~Prop 中的 Attribute~~

##### ~~非 prop 的 attribute~~

~~一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 prop 定义的 attribute，这些 attribute 会被添加到这个组件的根元素上。~~

##### ~~替换&合并Attribute~~

~~想象一下 `<bootstrap-date-input>` 的模板是这样的：~~

```html
<input type="date" class="form-control">
```

~~为了给我们的日期选择器插件定制一个主题，我们可能需要像这样添加一个特别的类名：~~

```html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

~~在这种情况下，我们定义了两个不同的 `class` 的值：~~

- ~~`form-control`，这是在组件的模板内设置好的~~
- ~~`date-picker-theme-dark`，这是从组件的父级传入的~~

~~对于绝大多数 attribute 来说，从外部提供给组件的值会替换掉组件内部设置好的值。所以如果传入 `type="text"` 就会替换掉 `type="date"` 并把它破坏！庆幸的是，**`class` 和 `style` attribute** 会稍微智能一些，即两边的值会被合并起来，从而得到最终的值：`form-control date-picker-theme-dark`。~~

##### ~~inheritAttrs 参数和 $attrs方法~~ 

> ~~注意 `inheritAttrs: false` 选项**不会**影响 `style` 和 `class` 的绑定。~~

 ~~$attrs 会包含没有被props包含的attrobute，inheritAttrs会决定这个attribute会不会存在于这个组件的元素上面。（主组件上的是根元素，组件上的那个是模板）~~

##### ~~$emit方法 【vm.$emit( eventName, […args] )】~~

~~作用：触发父组件中绑定事件【eventName】，附加参数【[…args]】传递给父组件事件监听器通过`$event`访问~~

~~如果父组件是一个监听器是一个函数方法，会将附加参数作为第一个参数传入这个方法~~

#### ~~插槽~~

~~组件中间的内容称为插槽，在组件的模版中 用<slot>作为出口 ，会将插槽替换掉 <slot>的内容,~~

~~父组件中用于传递内容的占位符通常被称为“插槽内容”（Slot Content），而子组件模板中的 `<slot>` 标签则是插槽的“出口”，用于接收和展示从父组件传递进来的内容。**如果出口不存在 插槽内容也就不存在了**，如果`  <slot>Submit</slot>` 出口这里存在一个内容，在插槽内容不存在时默认显示这个，如果存在就会替换掉~~

```html
<navigation-link url="/profile">
  Logged in as {{ user.name }}
</navigation-link>
```

~~插槽（slot）的内容是父组件的一部分，因此它共享父组件的实例属性和方法，也就是说，插槽内容可以访问父组件的“作用域”。~~

~~`<navigation-link>` 是一个子组件，而插槽内容 `Logged in as {{ user.name }}` 是父组件的模板部分。这里的 `{{ user.name }}` 表示插槽内容正在访问父组件实例的 `user` 属性中的 `name` 属性。由于插槽内容是父组件的一部分，它自然可以访问父组件的属性。然而，插槽内容通常不能直接访问子组件 `<navigation-link>` 的属性或数据，比如这里的 `url` 属性。这是因为 `url` 是 `<navigation-link>` 组件自己的属性，它的作用域是局部的，仅限于 `<navigation-link>` 组件内部。如果需要在插槽内容中使用子组件的属性，通常需要通过子组件提供的接口，如 props、事件或插槽 prop 等方式来实现。~~



##### ~~具名插槽~~

~~当我们存在多个插槽的需求时，可以定义几个具名插槽，具名插槽顾名思义就是带名字的插槽。~~

~~默认插槽不带名字的时候，都是默认 `define`作为名字。另外 多个插槽 并且还带名字 要用`<template>`~~

~~我们可以在一个 `<template>` 元素上使用 `v-slot` 指令，并以 `v-slot` 的参数的形式提供其名称：~~

~~实例：~~

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

~~然后在组件中可以 也就是**出口上** 通过添加一个 `attribute`来实现插槽的对应，~~

```vue
<div class="container">
    <slot name="header"></slot>
    <slot></slot>
    <slot name="footer"></slot>
</div>
```



##### ~~插槽内访问property~~

~~在某些情况下，可能还需要再插槽内访问 子组件中的 property~~ 

~~在使用这个组件 在组件部分访问数据，请注意，在父组件中 并不能访问 子组件的数据，在父组件中渲染的内容 是替换到 组件的<slot>标签的，如果想要进行访问，**就要在子组件中 将数据作为 prop传递给 父组件。**~~

##### ~~插槽prop~~

~~例如：在template用v-bind绑定值~~

```vue
<span>
  <slot v-bind:user="user" name="test">
  </slot>
</span>
```

 ~~然后在父组件中使用的时候：~~

```html
<current-user>
  <template v-slot:default="slotProps"> //slotProps 随便取一个名字就好了 自定义的 
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

  ~~`default`这个是插槽的名字` slotProps` 随便取一个名字就好了 自定义的 用来接受子组件的`prop`~~

~~在上述情况下，当被提供的内容*只有*默认插槽时，组件的标签才可以被当作插槽的模板来使用。这样我们就可以把 `v-slot` 直接用在组件上：~~

```html
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

~~**插槽中未指明的内容对应默认插槽，不带参数的 `v-slot` 被假定对应默认插槽**~~

~~只要出现多个插槽，请始终为*所有的*插槽使用完整的基于 `<template>` 的语法~~



##### ~~解析插槽~~

~~插槽的内部工作原理是将你的插槽内容包裹在一个拥有单个参数的函数里：~~

```js
function (slotProps) {
  // 插槽内容
}
```

## 组件（note v2.0)

### 组件注册

1. **注册组件时的命名**： 可以使用短横线命名（my-component-name)或者首字母大写命名（MyComponentName)，推荐前面这种。

2. **注册组件的方式：**Vue.component全局注册和JavaScript 对象来定义组件

   1. `Vue.component('component-a', { /* ...这是一个选项对象 */ })` 全局注册

   2. `var ComponentA = { /* ...这是一个选项对象 */ }` 局部注册

   3. 两者的区别，前者在 Vue 根实例中任何地方都可以使用，子组件中也是可以的；后者不能在组件中使用。

      ```js
      全局注册
      Vue.component(ComponentName,OptionsObject)
      局部注册
      let componentA = OptionsObject;
      New Vue({
      	...
          components:{
              componentName:componentA,
          }
      })
      
      ```

      > **全局注册的行为必须在根 Vue 实例 (通过 `new Vue`) 创建之前发生**

### Prop

1. #### **prop名字**

   因为html中的attribute的大小写不敏感，在html写attribute的时候，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名。

   ```JS
   Vue.component('blog-post', {
     // 在 JavaScript 中是 camelCase 的
     props: ['postTitle'],
     template: '<h3>{{ postTitle }}</h3>'
   })
   ```

   ```html
   <!-- 在 HTML 中是 kebab-case 的 -->
   <blog-post post-title="hello!"></blog-post>
   ```

2. #### **prop类型**

   1. 可以通过**字符串数组**列出的 prop `props: ['title', 'likes', 'isPublished', 'commentIds', 'author']`。

   2. 如果希望每个 prop 都有指定的值类型。可以以对象形式列出 prop，这些 property 的名称和值分别是 prop 各自的名称和类型
      这样会在遇到错误的类型时从浏览器的 JavaScript 控制台提示用户。

      ```js
      props: {
        title:  String,
        likes: Number,
        isPublished: Boolean,
        commentIds: Array,
        author: Object,
        callback: Function,
        contactsPromise: Promise // or any other constructor
      }
      ```

3. #### **传入prop的值**

   1. 可以传入一个静态的值 也可以用v-bind传入一个表达式的值

      - 字符串

        ```html
          <!-- 传入一个静态的字符串 -->
          <blog-post title="My journey with Vue"></blog-post>
        
          <!-- 动态赋予一个变量的值 -->
          <blog-post v-bind:title="post.title"></blog-post>
        
          <!-- 动态赋予一个复杂表达式的值 -->
          <blog-post
            v-bind:title="post.title + ' by ' + post.author.name"
          ></blog-post>
        ```

      - boolean

        ```html
        <!-- 包含该 prop 没有值的情况在内，都意味着 `true`。-->
        <blog-post is-published></blog-post>
        
        <!-- 即便 `false` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
        <!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
        <blog-post v-bind:is-published="false"></blog-post>
        
        <!-- 用一个变量进行动态赋值。-->
        <blog-post v-bind:is-published="post.isPublished"></blog-post>
        ```

      - 数字

        ```html
        <!-- 即便 `42` 是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
        <!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
        <blog-post v-bind:likes="42"></blog-post>
        
        <!-- 用一个变量进行动态赋值。-->
        <blog-post v-bind:likes="post.likes"></blog-post>
        ```

      - 对象

        ```html
        <!-- 即便对象是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
        <!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
        <blog-post
          v-bind:author="{
            name: 'Veronica',
            company: 'Veridian Dynamics'
          }"
        ></blog-post>
        
        <!-- 用一个变量进行动态赋值。-->
        <blog-post v-bind:author="post.author"></blog-post>
        ```

      - 将一个对象的所有 property 都作为 prop 传入

        ```js
        post: {
          id: 1,
          title: 'My Journey with Vue'
        }
        ```

        这个模板：

        ```html
        <blog-post v-bind="post"></blog-post>
        ```

        等价于：

        ```html
        <blog-post
          v-bind:id="post.id"
          v-bind:title="post.title"
        ></blog-post>
        ```

      - 数组

        ```html
        <!-- 即便数组是静态的，我们仍然需要 `v-bind` 来告诉 Vue -->
        <!-- 这是一个 JavaScript 表达式而不是一个字符串。-->
        <blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>
        
        <!-- 用一个变量进行动态赋值。-->
        <blog-post v-bind:comment-ids="post.commentIds"></blog-post>
        ```

      > 注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变变更这个对象或数组本身**将会**影响到父组件的状态

4. **单向数据流**（父组件的更新子组件可以跟着更新，反过来不行）

   不应该在子组件内去尝试更改prop

   场景一：**prop 用来传递一个初始值；这个子组件接下来希望将其作为一个本地的 prop 数据来使用**

   场景二： **prop 以一种原始的值传入且需要进行转换处理**

   ...

   针对场景一 最好定义一个本地的 data property 并将这个 prop 用作其初始值；

   针对场景二 最好使用这个 prop 的值来定义一个computed；

5. **prop的数据验证**

   > 在prop定义的时候，就说了prop使用对象的形式可以指定值的类型，这一步是进行更强大的一部分功能

   为了定制 prop 的验证方式，你可以为 `props` 中的值提供一个带有验证需求的对象:

   ```js
   Vue.component('my-component', {
     props: {
       // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
       propA: Number,
       // 多个可能的类型
       propB: [String, Number],
       // 必填的字符串
       propC: {
         type: String,
         required: true
       },
       // 带有默认值的数字
       propD: {
         type: Number,
         default: 100
       },
       // 带有默认值的对象
       propE: {
         type: Object,
         // 对象或数组默认值必须从一个工厂函数获取
         default: function () {
           return { message: 'hello' }
         }
       },
       // 自定义验证函数
       propF: {
         validator: function (value) {
           // 这个值必须匹配下列字符串中的一个
           return ['success', 'warning', 'danger'].includes(value)
         }
       }
     }
   })
   ```

   针对这个实例得到如下总结：

   1. `null` 和 `undefined` 会通过任何类型验证。

   2. prop 会在一个组件实例创建**之前**进行验证，所以实例的 property (如 `data`、`computed` 等) 在 `default` 或 `validator` 函数中是不可用的。

   3. `type` 、 `default` 、`validator`和`required` 

      - **type** 用来检查类型，不仅可以是原生的构造函数检查【<u>String、Number、Boolean、Array、Object、Date、Function、Symbol</u>】还可以是自定义的构造函数类型检查

        ```js
        function Person (firstName, lastName) {
          this.firstName = firstName
          this.lastName = lastName
        }
        
        Vue.component('blog-post', {
          props: {
            author: Person
          }
        })
        ```

        这样就可以检查 author是不是Person构造函数实例化的。

        多个数据类型用数组包裹。

      - **default** ：用来给定一个默认值，对象和数组需要函数获得，如果父组件中attribute没给 就用默认值。

      -  **required**：是否是必须给定prop

      -  **validator**：值是一个函数， 对prop进行一些校验 返回值为真假。

6. **非 Prop 的 Attribute**

   - 一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 prop 定义的 attribute。这个**attribute会被添加到这个组件的根元素下**，并且prop 会被`$attrs`对象所管理。
   - 禁用这个自动添加，只要在子组件的选项中设置 `inheritAttrs: false`

   <u>对于这些非prop的attribute 他们会被添加到 `$attrs`对象上面，这个可以与`inheritattrs`配合使用。</u>

### 事件

> vm.$emit( eventName, […args] )
>
> - **参数**：
>
>   - `eventName` 
>   - `[...args]`
>
>   触发当前实例上的事件。附加参数都会传给监听器回调。
>
> - 可以用此在子组件和父组件之间传值

#### 在组件上自定义事件

利用`$emit` 

实例：

```html
<div id="app">
  <base-input v-on:event="sayHi"></base-input>
</div>
```

```js
Vue.component("base-input", {
  template: `
    <div>
      <button v-on:click="$emit('event')">test</button>
    </div>
  `,
});

new Vue({
  el: "#app",
  methods: {
    sayHi: function() {
      alert("hi");
    },
  },
});
```

#### 在组件上使用原生事件

​	<u>这一段我还不是很理解</u>

在组件上使用v-modle是时候，他会自动选择一个property和 event作为prop和event。

我门可以通过设置modle选项为v-modle确认prop和event。

### 插槽



## 过度动画

## 路由

