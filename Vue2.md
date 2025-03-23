# [Vue2.0](https://v2.cn.vuejs.org/)

**特点：**

+ 组件化，提高代码复用率，更易于维护
+ 声明式，无需操作 DOM，提高开发效率
+ 使用虚拟 DOM + 优秀的 Diff 算法，尽量复用 DOM 节点



## 安装

### `<script>`引入

+ [点击下载开发版本 - vue.js](https://v2.cn.vuejs.org/js/vue.js)
+ [点击下载生产版本 - vue.min.js](https://v2.cn.vuejs.org/js/vue.min.js)

```html
<!-- 制作原型或学习 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.16"></script>
<!-- 生产环境 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.16"></script>
<!-- 原生 ES Modules -->
<script type="module">
	import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.7.16/dist/vue.esm.browser.js'
</script>
```



### Npm

```shell
npm install vue@^2
```



## Hello Vue.js

要注意，容器和`vue`实例是一一对应关系

真实开发中只有一个`vue`示例并且配合大量组件使用

```html
<div id="root"> <!-- root 容器里面的代码被称为 vue模板 -->
    <h1>Hello, {{name}}</h1> <!-- 利用插值语法引入变量 或者表达式和 js 代码 -->
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root', // 指定 Vue 实例为哪个容器服务 可以是 css 选择器 也可以是 dom 对象
        data: {
            name: 'Vue'
        }
    })
</script>
```



### 拓展写法

+ `el`

```html
<div id="root">
    <h1>Hello, {{name}}</h1>
</div>

<script type="text/javascript">
    const v = new Vue({
        // 暂时不指定 el
        data: {
            name: 'Vue'
        }
    })
    
    setTimeout(() => {
        v.$mount('#root') // 延时挂载
    }, 1000)
</script>
```

+ `data`

```html
<div id="root">
    <h1>Hello, {{name}}</h1>
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root',
        data: function () { // 通过函数返回一个对象 一定不要写箭头函数
            return {
                name: 'Vue'
            }
        }
    })
</script>
```





## 模板语法

在`html`文件中可以通过模板语法，预置一系列的变量、表达式或者`js`代码，由`vue`解析为对应的内容



### 插值语法

用于解析标签体内容

```html
<div id="root">
    <h1>Hello, {{name}}</h1> <!-- 利用插值语法引入变量 或者表达式和 js 代码 -->
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root',
        data: {
            name: 'Vue' // 供 vue 模板使用的数据
        }
    })
</script>
```



### 指令语法

形如`v-#`，用于解析标签，包括标签属性、标签体、绑定事件等

示例：

```html
<div id="root">
    <a v-bind:href="url">启动！</a> <!-- 通过指令语法v-bind 获取 url -->
    <!-- v-bind 也可以像这样简写 -->
    <a :href="url">启动！</a>
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root',
        data: {
            url: 'https://ys.mihoyo.com/'
        }
    })
</script>
```



## 数据绑定

### 单向数据绑定

只有数据`data`对`input.value`进行绑定

`data`变化`input.value`的值也会变化，但是`input.value`的变化不会引起`data`的改变

```html
<div id="root">
    单向数据绑定: <input type="text" :value="val">
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root',
        data: {
            val: 'single bind'
        }
    })
</script>
```



### 双向数据绑定

通过`v-model`可以实现数据的双向绑定，但是`v-model`只能用在表单元素上（得有`value`）

```html
<div id="root">
    双向数据绑定: <input type="text" v-model:value="val">
    <!-- v-model:value 可以简写为 v-model -->
    双向数据绑定: <input type="text" v-model="val">
</div>

<script type="text/javascript">
    const v = new Vue({
        el: '#root',
        data: {
            val: 'single bind'
        }
    })
</script>
```

修饰符：

+ `.number`：如果输入内容为数字则自动转为数字型
+ `.lazy`：失去焦点后才获取数据
+ `.trim`：去除首位空白字符



## MVVM

+ M：模型（Model），对应`data`中的数据
+ V：视图（View），对应`Vue`模板
+ VM：视图模型（ViewModel），`Vue`实例对象

`data`中所有的属性，最终会传递给`vm`作为`vm`的属性

在模板中，可以直接获取`vm`的属性



## Vue 的数据代理

### Object.defineProperty()

通过以下方法，可以在对象`o`上添加属性`age`，值为`18`

但是该方法添加的属性默认不参与枚举（通过`Object.keys(o)`无法获取到`age`属性），如果需要该属性参与枚举，可以修改配置项的`enumerable`值为`true`

该方法添加的属性默认是不可写的，如果需要更改属性为可写，可以修改配置项的`writable`值为`true`

该方法添加的属性默认是不可删除的，如果需要更改属性为可删除，可以修改配置项的`configurable`值为`true`

```js
let number = 18

const o = {
    name: 'Leo'
}

Object.defineProperty(o, 'age', {
    value: number,
    enumerable: true, // 表示属性可以枚举
    writable: true, // 表示属性可写
    configurable: true // 表示属性可删除
})
```

通过上述方法直接指定`value`，如果后续`number`发生了改变，`o.age`不会相应地做出改变

为此可以这么写

```js
let number = 18

const o = {
    name: 'Leo'
}

Object.defineProperty(o, 'age', {
    get: function() { // 设定 getter 后不能配置 value 或者 writable 属性
        return number
    },
    set: function(val) { // 设定 setter 来接收 age 属性的改动值
        number = val
    }
})

// 或者简写为
Object.defineProperty(o, 'age', {
    get() { return number },
    set(val) { number = val }
})
```



### 数据代理的定义

通过一个对象代理对另一个对象属性的操作

```js
// 简单的数据代理
const o1 = { x: 1 }
const o2 = { y: 2 }

Object.defineProperty(o2, 'x', {
    get() { return o1.x },
    set(val) { o1.x = val }
})
```



### Vue 的数据代理

`Vue`将接收到的`data`保存在自身的属性`vm._data`上

然后，`vm`遍历`data`的所有属性，将这些属性都通过`Object.defineProperty()`方法添加到自身身上，并为这些新增属性添加上`getter`和`setter`，以此完成对`data`数据的代理

如果在前端开发者工具中，检查`vm._data`或者`data`，会发现里面的属性变成了`(...)`，出现了数据代理的特征，事实上`vm._data`中的属性并未进行数据代理，而是添加了数据劫持，目的是实现`vm._data`的数据发生改变的同时，让前端模板进行数据同步

```js
// 关于 data 处理方面的简单的底层原理复刻
const data = {
    name: 'rekaime',
    age: 18,
    school: {
        name: 'scu',
        addr: 'sichuan'
    }
}

function Observer(data) {
    Object.keys(data).forEach(k => {
        /* 判断 data[k] 的类型 */
        /* 注意 对于对象 Vue 会对所有的 key 都添加相应的 getter 和 setter */
        /* 而数组没有严格意义上的键值对应 因此不会对每个元素做好绑定 */
        /* 直接赋值修改数组元素是不会引发响应式的 需要借助 array 上由 Vue 重写的 7 个方法 或者 Vue.set 方法 */
        /* push pop unshift shift splice sort reverse */
        Object.defineProperty(this, k, {
            get() {
                return data[k]
            },
            set(val) {
                log(val)
                log(this.age)
                data[k] = val
            }
        })
    })
}

const obs = new Observer(data)
```



### 追加属性

如果希望向`vm._data`中的某个对象上添加属性，是无效操作，因为`vm._data`是经过数据劫持处理过的`data`

因此需要借助`Vue`提供的 API 进行处理：

+ `Vue.set(target, key, value)`：如`Vue.set(vm._data.school, 'leader', '莲蓉')`
+ `vm.$set = Vue.set`



## 事件

### 添加事件

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <button v-on:click="showMsg($event)">点我弹出好玩的东西</button> <!-- v-on:#="methodName" 可以绑定事件 -->
    <button v-on:click="showMsg">点我弹出好玩的东西</button> <!-- 也可以这样简写 -->
    <button @click="showMsg">点我弹出好玩的东西</button> <!-- 进一步简写 -->
    <button @click="showMsg()">点我弹出好玩的东西</button> <!-- 如果需要传参 可以通过 $event 传递事件对象 -->
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        methods: { // 事件的回调函数都写在这里
            showMsg(event) { // 接收事件对象
                // this 的指向是 vm 或者组件实例对象
                alert('原神，启动！')
            }
        }
    })
</script>
```

其中的`methods`对象中的函数也会被添加到`vm`中作为属性，但是这些函数不会进行数据代理

如果回调函数写在`data`中而不是`methods`中，也能够被模板访问到，但是这些函数会经理数据代理，这是没有必要的



### 事件修饰符

+ `.prevent`：阻止默认事件（对应`event.preventDefault()`）
+ `.stop`：阻止事件冒泡（对应`event.stopPropagation()`）
+ `.once`：事件只触发一次
+ `.capture`：使用时间的捕获模式
+ `.self`：只有`event.target`是当前操作的元素时才触发事件
+ `.passive`：事件的默认行为立即执行，无需等待事件回调执行完毕

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <a href="https://www.bilibili.com/" @click.prevent="showMsg">点我弹出好玩的东西</a> <!-- Vue 的方式阻止事件默认行为 -->
</div>

<div id="root">
    <h1>Hello, Vue!</h1>
    <div @click="showMsg">
        <!-- 修饰符可以连续写 表示先阻止默认事件 然后阻止冒泡 -->
        <a href="https://www.bilibili.com/" @click.prevent.stop="showMsg">点我弹出好玩的东西</a> 
    </div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        methods: {
            showMsg(e) {
                e.preventDefault() // js 的方式阻止事件默认行为
                alert('原神，启动！')
            }
        }
    })
</script>
```



### 键盘事件

绑定键盘事件时，可以通过别名来代替`e.keyCode`过滤按键，常用别名如下：

+ `.enter`：回车
+ `.delete`：删除和退格
+ `.esc`：退出
+ `.space`：空格
+ `.tab`：换行（需要配合`keydown`使用）
+ `.up`：上
+ `.down`：下
+ `.left`：左
+ `.right`：右

其他别名可以通过输出`e.key`查询，如果键名由两个字母组成，需要用横杠`-`隔开两个单词

`ctrl`、`alt`、`shift`、`meta(win)`：

+ `keyup`：需要按下其他键组成组合才能触发，如果需要特定组合才能触发可以这么写`keyup.ctrl.x`表示只有 **ctrl + x** 才能触发，`keyup.ctrl.x.y`表示只有 **ctrl + x** 或者 **ctrl + y** 才能触发
+ `keydown`：正常触发

可以通过`keyup.keyCode|keydown.keyCode`绑定对应的键码，但是不推荐，未来可能废除键码，此外可以通过`Vue.config.keyCodes.{{{自定义键名}}} = keyCode`来自定义一个新的键名

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 只有在按下回车时 才会触发 showMsg -->
    <input placeholder="按下回车以输入" @keyup.enter="showMsg" >
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        methods: {
            showMsg(e) {
                console.log(e.target.value)
            }
        }
    })
</script>
```



## 计算属性

### 示例

输入姓和名，完成拼接

+ 插值语法实现

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    姓：<input v-model:value="x"> <br><br>
    名：<input v-model:value="y"> <br><br>
    <span>{{x}}-{{y}}</span>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            x: '张',
            y: '三'
        }
    })
</script>
```

+ `methods`实现

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    姓：<input v-model:value="x"> <br><br>
    名：<input v-model:value="y"> <br><br>
    <span>{{concatName()}}</span>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            x: '张',
            y: '三'
        },
        methods: {
            concatName() {
                return `${this.x}-${this.y}`
            }
        }
    })
</script>
```

被绑定的数据发生改变时，`Vue`会对模板进行重新解析，凡是用到以及可能用到（函数调用处）更新数据的地方都会重新加载



### 计算属性方法实现

`computed`中的属性在初次加载的时候会调用一次`getter`获取数据，并缓存

当所依赖的数据发生变化时，`getter`才会再次调用

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    姓：<input v-model:value="x"> <br><br>
    名：<input v-model:value="y"> <br><br>
    <span>{{fullName}}</span>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            x: '张',
            y: '三'
        },
        computed: {
            fullName: {
                get() {
                    return `${this.x}-${this.y}`
                }
                // 如果需要可以补充上 setter
            }
        }
    })
</script>
```

简写形式：如果一个计算属性确定只有`getter`，那么可以简写为一个函数

```js
const vm = new Vue({
    el: '#root',
    data: {
        x: '张',
        y: '三'
    },
    computed: {
        fullName() {
            return `${this.x}-${this.y}`
        }
    }
})
```



## 侦听

### 侦听器

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    今天应该学 Vue 吗？<br>
    {{learningPlan}}<br>
    <!-- 点击后切换 vm.shouldLearn -->
    <button @click="shouldLearn = !shouldLearn" >点击切换</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            shouldLearn: true
        },
        computed: {
            learningPlan() {
                return this.shouldLearn ? '当然要狠狠地学！' : '奖励一会先'
            }
        },
        watch: { // 通过 watch 可以对 vm 上的属性进行监听
            shouldLearn: { // 监听的是 vm.shouldLearn
                immediate: true, // 默认为 false 如果为 true 代表的是在值初始化时也调用 handler
                handler(newVal, oldVal) { // 传入两个参数 分别为新值和旧值
                    log(newVal, oldVal)
                }
            }
        }
    })
    
    // 或者可以把监视的配置写在外面
    vm.$watch('shouldLearn', {
        immediate: true,
        handler(newVal, oldVal) {
            log(newVal, oldVal)
        }
    })
</script>
```



### 深度监视

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    a 的值为：<span>{{numbers.a}}</span> <button @click="numbers.a++" >点击让 a+1</button> <br>
    b 的值为：<span>{{numbers.b}}</span> <button @click="numbers.b++" >点击让 b+1</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            numbers: {
                a: 0,
                b: 18
            }
        },
        watch: {
            ...
        }
    })
</script>
```

+ 情形1：监视`numbers`对象本身

```js
// 在这种情况下 只有 numbers 的引用发生了变化才会调用 handler
// 无论 numbers 的属性如何变化都不会触发 handler
watch: {
    numbers: {
        handler() {
            log('numbers 对象发生了变化')
        }
    }
}
```

+ 情形2：监视`numbers`的某一属性

```js
// 在这种情况下 只有 numbers.a 发生了改变才会调用 handler
// 无论 b 如何变化都不会出发 handler
// 即使 numbers 对象的引用发生了变化 只要还有 a 属性并且发生 a 属性的变化 就会触发 handler
watch: {
    'numbers.a': {
        handler() {
            log('numbers.a 发生了变化')
        }
    }
}
```

+ 情形3：监听`numbers`整体（深度监视）

```js
// 此时无论 numbers 的引用还是它的属性发生变化 都会调用 handler
watch: {
    numbers: {
        deep: true, // 默认为 false 值为 true 时开启深度监视
        handler() {
            log('numbers 对象整体有人发生了变化')
        }
    }
}
```



### 简写

```javascript
watch: {
    shouldLearn: { // 如果一项数据的监视只有 handler 可以简写
        handler(newVal, oldVal) {
            log(newVal, oldVal)
        }
    }
}

vm.$watch('shouldLearn', {
    immediate: true,
    handler(newVal, oldVal) {
        log(newVal, oldVal)
    }
})

// 简写后
watch: {
    shouldLearn(newVal, oldVal) {
        log(newVal, oldVal)
    }
}

vm.$watch('shouldLearn', function(newVal, oldVal) {
    log(newVal, oldVal)
})
```



## 可复用性 & 组合

### 过滤器

可用于插值语法和`v-bind`两处，用作数据简单格式化处理

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 利用管道符将 time 作为 timeFormatter 的第一个参数传入 -->
    <div>现在时间为：{{time | timeFormatter}}</div>
    <!-- 效果同上 -->
    <div>现在时间为：{{time | timeFormatter()}}</div>
    <!-- 过滤器接收的第一个参数仍是 time 第二个参数才是 'YYYY年MM月DD日 HH:mm:ss' -->
    <div>现在时间为：{{time | timeFormatterTypeOf('YYYY年MM月DD日 HH:mm:ss')}}</div>
    <!-- 可以通过管道符串联 -->
    <div>现在时间为：{{time | timeFormatter | cut}}</div>
</div>

<script src="https://cdn.bootcdn.net/ajax/libs/dayjs/1.11.13/dayjs.min.js"></script>
<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            time: Date.now()
        },
        filters: { // 定义一系列列局部过滤器
            timeFormatter(time) {
                return dayjs(time).format('YYYY-MM-DD HH:mm:ss')
            },
            timeFormatterTypeOf(time, format='YYYY-MM-DD HH:mm:ss') {
                return dayjs(time).format(format)
            },
            cut(str) {
                return str.slice(0, 4)
            }
        }
    })
</script>
```

```js
// 可以把 cut 定义为全局的过滤器 供其他 Vue 实例使用
Vue.filter('cut', function (str) {
    return str.slice(0, 4)
})
```





## 绑定 Class 与 Style

### 绑定 Class

```css
/* css */
.basic {
    width: 200px;
    height: 200px;
    border: 2px solid black;
}

.red { color: red; }
.cyan { color: cyan; }
.hotpink { color: hotpink; }

.s1 { background: cadetblue; }
.s2 { font-size: 1.5em; }
.s3 { border-radius: 16px; }
```



#### 字符串形式

功能：点击盒子后切换字体颜色

效果：`<div class="basic red">`

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 可以通过 Vue 自由指定类名 -->
    <div class="basic" :class="color" @click="changeColor" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            color: ''
        },
        methods: {
            changeColor() {
                const colors = ['red', 'cyan', 'hotpink']
                this.color = colors[(colors.indexOf(this.color) + 1) % colors.length]
            }
        }
    })
</script>
```



#### 数组形式

功能：为盒子同时添加`s1 s2 s3`三组样式

效果：`<div class="basic s1 s2 s3">`

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 这种写法不便于管理 -->
    <div class="basic" :class="['s1', 's2', 's3']" >这是一个盒子</div>
    <!-- 可以通过 Vue 自由控制类的数目和种类 -->
    <div class="basic" :class="classArr" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            classArr: ['s1', 's2', 's3']
        }
    })
</script>
```



#### 对象形式

盒子存在`s1 s2 s3`三组样式，现在需要控制这些样式是否活跃

效果：`<div class="basic s1 s3">`

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 这种写法不便于管理 -->
    <div class="basic" :class="{ s1: true, s2: false, s3: true }" >这是一个盒子</div>
    <!-- 可以通过 Vue 自由控制类是否活跃 -->
    <div class="basic" :class="classObj" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            classObj: {
                s1: true,
                s2: false,
                s3: true
            }
        }
    })
</script>
```



### 绑定 Style

#### 字符串形式

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 用模板字符串占位 -->
    <div class="basic" :style="`font-size: ${fSize}em`" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            fSize: 2
        }
    })
</script>
```



#### 对象形式

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 通过对象管理内联样式 -->
    <div class="basic" :style="{ fontSize: 2em }" >这是一个盒子</div>
    <div class="basic" :style="styleObj" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            styleObj: {
                fontSize: '2em'
            }
        }
    })
</script>
```



#### 数组形式

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 数组里可以填写多个对象 -->
    <div class="basic" :style="[styleObj, styleObj2]" >这是一个盒子</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            styleObj: {
                fontSize: '2em'
            },
            styleObj2: {
                ...
            }
        }
    })
</script>
```



## 条件渲染

### v-show

通过指定一个表达式，当表达式的值为`true`则渲染当前元素

实现原理为：当表达式值为`false`时，利用`display: none;`隐藏元素

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- v-show -->
    <div v-show="counter === 0">我是 0</div>
    <div v-show="counter === 1">我是 1</div>
    <div v-show="counter === 2">我是 2</div>
    <button @click="toggle" >切换</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            counter: 0
        },
        methods: {
            toggle() {
                this.counter = (this.counter + 1) % 3
            }
        }
    })
</script>
```



### v-if

如果表达式的值为`false`则不渲染该**标签**

使用后要考虑到 DOM 元素可能不可被获取，也就是结点不存在了

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- v-if 以下结构必须连续 中间不能穿插别的标签 -->
    <div v-if="sayHello === 0">你好呀</div>
    <div v-else-if="sayHello === 1">又见面啦</div>
    <div v-else>加内~</div>
    
    <!-- 可以和 v-if 配合使用 --> <!-- v-show 不能这么做 -->
    <template v-if="sayHello == true">
        <div>我们</div>
        <div>三个</div>
        <div>一块</div>
    </template>
    <button @click="toggle" >切换</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            sayHello: 0
        },
        methods: {
            toggle() {
                this.sayHello = (this.sayHello + 1) % 3
            }
        }
    })
</script>
```



## 其他内置指令

### v-text

向当前标签插入文本，以下四个`<div>`展示效果是相同的

`v-text`不会解析标签，也就是说将内容作为纯文本`innerText`插入

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <div>&lt;h2&gt;rekaime&lt;/h2&gt;</div>
    <div>{{name}}</div>
    <div v-text="name"></div>
    <div v-text="name">www</div>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            name: '<h2>rekaime</h2>'
        }
    })
</script>
```



### v-html

与`v-text`对比，`v-html`支持标签的解析



**安全问题：**动态渲染容易遭受 XSS 攻击



### v-cloak

在`Vue`接管容器时，移除所有标签中的`v-cloak`属性

作用为，配合`css`可以在`Vue.js`未从服务器响应时，隐藏部分标签

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <div v-cloak>{{name}}</div>
</div>
```

```css
[v-cloak] {
    display: none;
}
```



### v-once

标签初次渲染完毕后，视为静态内容

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <div v-once>n 初始为：{{n}}</div>
    <div>n 当前为：{{n}}</div>
    <button @click="n++">点我助力n变大</button>
</div>
```



### v-pre

跳过所在节点的编译过程，可以跳过没有使用指令语法、插值语法的节点，有助于加速编译



## 自定义指令

通过自定义指令可以结合 DOM 的操作，封装一组`Js`代码

当标签和指令完成绑定时，以及指令所在模板发生变化时，都会进行解析

### 函数式

是对象式的简写，相当于一次提供`bind()`和`update()`两个操作

 ```html
 <div id="root">
     <h1>Hello, Vue!</h1>
     <h2 v-font-up="n">Hello!</h2>
     <button @click="n+=.5" >点我放大字体</button>
 </div>
 
 <script type="text/javascript">
     const vm = new Vue({
         el: '#root',
         data: {
             n: 1
         },
         directives: { // 存放自定义指令配置项
             'font-up'(element, binding) { // 函数名也可以写成 fontUp(element, binding)
                 // element 指的是绑定的 DOM
                 // binding 包含一系列参数
                 // 其中 def 包含 bind 和 update 函数
                 // expression: 'n'
                 // name: 'font-up'
                 // rawName: 'v-font-up'
                 // value: 传递的参数n值
                 const {def, expression, modifiers, name, rawName, value} = binding
                 element.style.fontSize = value + 'em'
             }
         }
     })
 </script>
 ```



### 对象式

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <input type="text" v-fbind:value="n">
    <button @click="n++" >点我增加输入值</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            n: 9
        },
        directives: {
            fbind: {
                bind(element, binding) { // 标签和指令绑定时
                    element.value = binding.value
                },
                inserted(element, binding) { // 标签插入页面中后
                    element.focus()
                },
                update(element, binding) { // 模板重新解析时
                    element.value = binding.value
                    element.focus()
                }
            }
        }
    })
</script>
```



### 全局定义

```js
Vue.directive('fbind', {
    bind(element, binding) {
        element.value = binding.value
    },
    inserted(element, binding) {
        element.focus()
    },
    update(element, binding) {
        element.value = binding.value
        element.focus()
        // 因为做的事和前两个函数一样 所以也可以这么做
        // binding.def.bind(element, binding)
        // binding.def.inserted(element, binding)
    }
})
```





## 列表渲染

### 基本形式

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <h2>下面展示我的老婆们</h2>
    <ul>
        <!-- 通过对象遍历方法获取列表的每个对象 对它们用同样的模板进行渲染 -->
        <li v-for="s in students" :key="s.id" >这位是{{s.name}}，今年{{s.age}}岁了</li>
    </ul>
    
    <ul>
        <!-- for 可接受的参数其实有两个 -->
        <!-- 参数接收部分其实可以不用括号 但是写上去显得工整 -->
        <!-- for (value, key) of ... 也是可以用的 -->
        <li v-for="(obj, index) in students" :key="obj.id" >...</li>
    </ul>
    
    <ul>
        <!-- for of 的少见用法 迭代 9 次 -->
        <li v-for="(num, index) of 9" :key="obj.id" >...</li>
    </ul>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            students: [
                { id: '001', name: 'rekaime', age: 18, gender: 0 },
                { id: '002', name: 'Yifeng', age: 20, gender: 0 },
                { id: '003', name: 'YouiMiya', age: 16, gender: 1 },
                { id: '004', name: 'YugiAmane', age: 13, gender: 0 },
                { id: '005', name: 'YugiTsukasa', age: 13, gender: 0 }
            ]
        }
    })
</script>
```



### 特殊属性 - key

#### 没有 key 导致的问题

以下代码在插入新的数据后，文本节点`这位是{{s.name}}，今年{{s.age}}岁了`会依次向下移动，但是`<input>`标签会保持原位，这就导致人物姓名和输入框信息无法对应

```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <h2>下面展示我的老婆们</h2>
    <ul>
        <!-- key 属性不存在时 默认 key="index" -->
        <li v-for="(s, index) in students" >
            <!-- 这里添加了输入框 假设每个人物后方都需要填写自己对应的信息 -->
            这位是{{s.name}}，今年{{s.age}}岁了 <input type="text">
        </li>
    </ul>
    <!-- 点击按钮后 在原先的列表中首部插入一条新数据 -->
    <button @click="cl" >点我</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: '#root',
        data: {
            students: [
                { id: '001', name: 'rekaime', age: 18, gender: 0 },
                { id: '002', name: 'Yifeng', age: 20, gender: 0 },
                { id: '003', name: 'YouiMiya', age: 16, gender: 1 },
                { id: '004', name: 'YugiAmane', age: 13, gender: 0 },
                { id: '005', name: 'YugiTsukasa', age: 13, gender: 0 }
            ]
        },
        methods: {
            cl() {
                this.students.unshift({ id: '006', name: 're', age: 18, gender: 0 })
            }
        }
    })
</script>
```



#### Vue 中的 Diff 算法

在`Vue`内部存在虚拟 DOM，在生成页面时再将虚拟 DOM 转化为真实的 DOM

如果页面内容发生变化，`Vue`会比对新旧虚拟 DOM，将可以复用的节点保存下来，将新增或修改过的节点进行插入或更替

上例中以`index`为标识，插入新数据前：`<li key="0">这位是rekaime，今年18岁了</li>`，插入新数据后：`<li key="0">这位是re，今年18岁了</li>`

对于`key="0"`的标签，文本节点发生了变化（内容改变了），而文本节点后方的`<input>`本质上没有改变（用户所填写的内容改变的是真实 DOM 的`value`，对虚拟 DOM 没有影响），因此所有原先就存在的`<input>`在页面中的位置不会发生改变

由此，新生成的页面，`<input>`不会发生移位，而`<input>`前的文本节点需要做出更新，就出现了文本信息和`<input>`中的内容不对应的情况

同时还存在隐藏的问题，那就是每个文本节点并不是依次向后移动进行复用，而是每个文本节点都重新生成了一遍，造成了效率下降的问题



### 列表过滤与排序

```js
const vm = new Vue({
    el: '#root',
    data: {
        keyword: '', // 输入 name 中的关键词
        students: [
            { id: '001', name: 'rekaime', age: 18, gender: 0 },
            { id: '002', name: 'Yifeng', age: 20, gender: 0 },
            { id: '003', name: 'YouiMiya', age: 16, gender: 1 },
            { id: '004', name: 'YugiAmane', age: 13, gender: 0 },
            { id: '005', name: 'YugiTsukasa', age: 13, gender: 0 }
        ]
    },
    computed: {
        displayStudents() { // 通过计算属性代替侦听器来过滤列表
            const filStudents = this.students.filter(s => s.name.indexOf(this.keyword) !== -1)
            /* If sort needed */
            /* Executing */
            return filStudents
        }
    }
})
```



## 表单

### text

```html
账号：<input type="text" name="account" v-model="form.account">
密码：<input type="text" name="password" v-model="form.password">
```



### radio

```html
<!-- 必须设定 value -->
性别：
男<input type="radio" name="gender" value="0" v-model="form.gender">
女<input type="radio" name="gender" value="1" v-model="form.gender">
```



### checkbox

```html
<!-- 如果不设定 value 则 v-model 默认捕获的是 checked -->
<!-- 不写 name 也能完成业务 -->
爱好：
学习<input type="checkbox" name="hobbies[]" value="learning" v-model="form.hobbies">
打游戏<input type="checkbox" name="hobbies[]" value="play game" v-model="form.hobbies">
吃饭<input type="checkbox" name="hobbies[]" value="eating" v-model="form.hobbies">
```

```js
const vm = new Vue({
    el: '#root',
    data: {
        form: {
            hobbies: [] // 如果 hobbies 初值不是数组 则收集的是 checked
        }
    }
})
```



### select

```html
<select name="school" v-model="form.addr">
    <option value="">请选择你的学校地址</option>
    <option value="beijing">北京</option>
    <option value="shanghai">上海</option>
    <option value="shenzhen">深圳</option>
    <option value="sichuan">四川</option>
</select>
```



### textarea

```html
其他信息：<textarea name="other" v-model="form.other"></textarea>
```



## 生命周期

又名生命周期函数、生命周期钩子

会在`Vue`在某一时期自动调用这一系列函数

![生命周期图示](https://v2.cn.vuejs.org/images/lifecycle.png)

生命周期：

+ 挂载流程
  + `beforeCreate`：刚进行生命周期和事件的初始化，但是数据代理未开始进行，此时无法通过`vm`访问到`data`的数据、`methods`的方法
  + `created`：数据监测和数据代理进行完毕，可以访问到`data`的数据、`methods`的方法
  + `beforeMount`：模板解析完毕，生成对应的虚拟 DOM，页面中显示的是未解析未编译的 DOM 结构，所有对 DOM 的操作都不会产生效果（因为挂载完毕后会借助虚拟 DOM 生成新的真实 DOM）
  + `mounted`：页面中开始展示经过`Vue`编译的 DOM，对 DOM 的操作均有效，此时初始化过程结束，可以进行定时器、网络请求、订阅消息、绑定自定义事件等操作
+ 更新流程
  + `beforeUpdate`：此时数据是新的数据，但是还没回显到页面中
  + `updated`：根据新数据生成新的虚拟 DOM，并与旧的虚拟 DOM 进行比较，最终完成页面更新，此时数据和页面是同步的
+ 销毁流程：完全销毁一个实例，清理它与其它实例的连接，解绑所有的指令和组件自定义事件监听器
  + `beforeDestroy`：此时`vm`中的数据都处于可用状态，但是对数据的更新无法响应到页面，在此阶段可以关闭定时器、取消订阅消息、解绑自定义事件等
  + `destroyed`：实例销毁完毕



## 组件

### 定义

实现应用中局部功能代码和资源的集合



### 非单文件组件

一个文件中包含一到多个组件

```html
<!-- 原本的 Vue 实例 -->
<div id="root">
    <h1>Hello, Vue!</h1>
    <h2>学校 - {{schoolName}}</h2>
    <h2>地址 - {{schoolAddr}}</h2>
    <hr>
    <h2>姓名 - {{stuName}}</h2>
    <h2>姓名 - {{stuAge}}</h2>
</div>

<script type="text/javascript">
    new Vue({
        el: '#root',
        data: {
            schoolName: 'SCU',
            schoolAddr: 'SiChuan',
            stuName: 'rekaime',
            stuAge: 18
        }
    })
</script>
```



```html
<div id="root">
    <h1>Hello, Vue!</h1>
    <!-- 组件标签 -->
    <school></school>
    <hr>
    <!-- 组件标签 -->
    <student></student>
</div>

<script type="text/javascript">
    const School = Vue.extend({ // 还可以指定 name 配置项 设置组件在开发者工具中的名称
        template: `
            <div>
                <h2>学校 - {{schoolName}}</h2>
                <h2>地址 - {{schoolAddr}}</h2>
            </div>
        `,
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan'
            }
        }
    })
    // 简写
    const Student = {
        template: `
            <div>
                <h2>姓名 - {{stuName}}</h2>
                <h2>姓名 - {{stuAge}}</h2>
            </div>
        `,
        data() {
            return {
                stuName: 'rekaime',
                stuAge: 18
            }
        }
    }

    new Vue({
        el: '#root',
        components: { // 注册组件 - 局部
            School: School, // 为了规范 组建的键值对采用同一个名称
            Student // 因为这样可以进行简写
        }
    })
    
    // 可以全局注册组件
    Vue.component('School', School)
</script>
```



### 组件嵌套

```js
// 利用 App 管理所有的组件
const App = Vue.extend({
    template: `
        <school></school>
        <hr>
        <student></student>
    `,
    components: {
        School,
        Student
    }
})
```



### 组件细节

+ 通过`Vue.extend()`生成的实际上是一个`VueComponent`构造函数
+ 在模板中写入`<school></school>`或者`<school/>`后，`Vue`在解析时自动创建`school`组件的实例对象（`new VueComponent(options)`）
+ 每次调用`Vue.extend()`后返回的都是一个全新的`VueComponent`
+ 关于`data`、`methods`、`watch`、`computed`中的`this`
  + 在组件中指向的是`VueComponent`
  + 在`new Vue`中指向的是`Vue`
+ `Vue`管理的组件在`vm.children: Array`当中
+ `VueComponent.prototype.__proto__ === Vue.prototype`，这样的设定使得`vc`实例能够顺着原型链访问到`Vue`原型上的方法（`vc.__proto__.__proto__ ==> Vue.prototype`）



### 单文件组件

一个文件中只包含一个组件



#### 基本结构

文件一般由`.vue`结尾，基本结构如下：

```vue
<template>
    <div>
        <!-- 组件的结构 -->
    </div>
</template>

<style>
    /* 组件的样式 */
</style>

<script>
    // 组件交互相关
</script>
```



#### 定义

`School.vue`

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
    </div>
</template>

<script>
    const School = Vue.extend({
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan'
            }
        }
    })

    export default School
</script>

<style>
    .school-style {
        background: cyan;
    }
</style>
```

`Js`部分进一步简化为：

```js
export default {
    name: 'School', // 补上 name 并尽可能和文件名保持一致
    data() {
        return {
            schoolName: 'SCU',
            schoolAddr: 'SiChuan'
        }
    }
}
```



## [脚手架](https://cli.vuejs.org/zh/#%E8%B5%B7%E6%AD%A5)

### 概念

`Vue`官方提供的一个标准化开发工具



### 安装

```shell
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```



### 创建项目

```shell
cd {{{workspace}}}

vue create {{{project-name}}}
# OR
vue ui
```



### 启动脚手架

```shel
cd {{{project-name}}}
npm run serve
```



### 结构

`main.js`作为入口文件，引入`App.vue`并将其导入模板中

`App.vue`作为所有组件的管理者，在此处引入并注册`/components`下的组件

`/public/index.html`是页面模板



### 关于`render`

初始的`main.js`当中，`vm`配置项如下：

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    render: h => h(App),
}).$mount('#app')
```

如果采用原来的写法会报错：

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
    el: '#app',
    template: '<App></App>',
    components: { App }
})
```

因为在`vue/package.json`中，配置了默认导入的包为`vue.runtime.esm.js`，不包括模板解析器

将导入的包改为`vue/dist/vue`即可采用完整版`vue`

在残缺版当中，`render`的具体写法如下：

```js
new Vue({
    render(createElement) {
        // createElement 为函数 接受参数可以创建标签
        return createElement('div', 'Hello Vue!')
    }
}).$mount('#app')

// 简化后
new Vue({
    render: f => f('App'), //传入标签
    components: { App }
}).$mount('#app')

// 进一步
new Vue({
    render: f => f(App) // 传入组件
}).$mount('#app')
```

`Vue`当中包含核心功能和模板解析器，在开发完毕后，代码会被转为`html+css+js`，此时模板解析器就没用了，如果被打包进入项目会占相当一部分体积

因此`Vue`提供了一系列`.runtime`文件，它们都不包含模板解析器

而`.esm`表示的是`ES Module`



### 关于默认配置

`Vue`通过`webpack`设置好了项目的相关配置项并进行隐藏，如果希望查看配置项内容可以通过以下代码输出`webpack.config.json`

```shell
vue inspect > output.js
```

其中可选的配置项可以通过`vue.config.js`进行修改，细节可以[查看此处](https://cli.vuejs.org/zh/config/)



## ref

在`html`中`id`可以唯一标识一个元素

`Vue`为了方便，允许使用`ref`代替`id`，然后在`vc`中通过`this.$refs.{{{id}}}`来获取对应的 DOM 元素

如果给组件标签添加`ref`属性，则获取到的是对应组件的`vc`实例对象



## props

`props`属性可以指定模板中可传入的参数，在模板被使用时可以传递一系列参数渲染模板

`props`在`Vue`当中尽可能保证是只读的，如果对它的值进行修改，会引起`Vue`的警告

```vue
<template>
    <div class="student-style">
        <h2>姓名 - {{stuName}}</h2>
        <h2>年龄 - {{stuAge}}</h2>
    </div>
</template>

<script>
    export default {
        name: 'Student',
        data() {
            return {
                stuName: 'rekaime'
            }
        },
        // 如果不希望限制 stuAge 的值 可以将其写在 props 中作为参数
        // 这是简单写法 比较常用
        props: ["stuAge"],
        
        // 这种写法下可以指定变量的类型
        props: {
            stuAge: Number
        },
        
        // 更为详细
        props: {
            stuAge: {
                type: Number,
                required: false, // 默认非必须
                default: 18 // 缺省值
            }
        }
    }
</script>
```

在使用模板时

```vue 
<template>
    <div id="app">
        <!-- 传递静态 prop -->
        <Student stu-age="18"></Student>
        <!-- 利用 v-bind 将指定传入的参数解析为 js Number -->
        <!-- 可用于动态赋值 -->
        <Student :stu-age="18"></Student>
        <!-- 可以将一个对象的所有 property 作为 prop 传入 -->
        <!-- 相当于 v-bind:stu-age="p.stuAge" -->
        <Student v-bind="p"></Student>
    </div>
</template>

<script>
    import Student from './components/Student.vue'

    export default {
        name: 'App',
        components: {
            School,
            Student
        },
        data() { return {
            p: {
                stuAge: 18
            }
        }}
    }
</script>
```



## mixin

如果有多个组件运用到同类配置，可以通过混合来将方法统一封装，然后导入各个组件中

混合内的属性不会替代原组件的配置

`mixin.js`

```js
export const m = {
    methods: {
        sayHi() {
            alert('Hello Vue!')
        }
    }
}
```

组件中

```vue
<script>
    import Student from './components/Student.vue'
    import { m } from '../mixin.js'
    export default {
        name: 'App',
        components: {
            School,
            Student
        },
        mixins: [m] // 引入混合
    }
</script>
```

可以在全局处进行混合

`main.js`

```js
import Vue from 'vue'
import App from './App.vue'
import { m } from './mixin.js'

Vue.config.mixin(m) // 此时 vm 以及所有的 vc 都会获取该混合

new Vue({
    // ...
})
```



## 插件

本质上是一个对象，默认包含一个`install()`方法，包含参数`Vue`构造方法，可以用于注册全局指令、全局过滤器、全局混合、全局方法等

并且在`.use(plugins, ...rest)`处会将剩余参数传递给`install()`

`plugins.js`

```js
export default {
    install(Vue) {
        
    }
}
```

`main.js`

```js
import Vue from 'vue'
import App from './App.vue'
import { plugins } from './plugins.js'

Vue.user(plugins) // 应用插件

new Vue({
    // ...
})
```



## scoped 样式

在组件中书写的样式，在引入时都会合并为一个`<style>`，这就会导致类名冲突

在`<style scoped>`中添加一个 scoped 可以保证所书写的样式只服务于本组件模板

一般在子组件中使用`scoped`而`App.vue`中则不会使用，如果在`App.vue`中使用则会导致覆盖旧样式



## 组件自定义事件

### 绑定

自定义事件在组件以及父组件被销毁前，能承担一定的作用

一般用于子组件给父组件传递数据

在组件上绑定的事件一律视为自定义事件，如果需要的是原生事件可以添加修饰符进行提示`@click.native`

`App.vue`

```vue
<template>
    <div id="app">
        <!-- v-on 绑定自定义事件以及触发事件后的回调函数 -->
        <!-- v-on 同样可以简写为 @ -->
        <School v-on:show-name="getSchoolName"></School>
        <hr>
        <!-- 第二种绑定方法是获取组件实例对象后 在 js 中绑定 -->
        <Student ref="student"></Student>
    </div>
</template>

<script>
    import School from './components/School.vue'
    import Student from './components/Student.vue'

    export default {
        name: 'App',
        components: {
            School, 
            Student
        },
        methods: {
            getSchoolName(schoolName) {
                console.log(schoolName)
            }
        },
        mounted() {
            // 通过 $on 绑定自定义事件
            this.$refs.student.$on('getStudentName', function (stuName) {
                console.log(stuName)
            })
        }
    }
</script>
```

`School.vue`

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
        <button @click="transmitName">点我</button>
    </div>
</template>

<script>
    export default {
        name: 'School',
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan'
            }
        },
        methods: {
            transmitName() {
                // 通过 $emit 触发 show-name 事件
                // 其中第二个参数往后可以传递参数
                this.$emit('show-name', this.schoolName)
            }   
        }
    }
</script>

<style scoped>
    .school-style {
        background: cyan;
    }
</style>
```



### 解绑

```js
// 解绑一个事件
vc.$off('show-name')

// 解绑多个事件
vc.$off(['show-name', ...])

// 解绑全部事件
vc.$off()
```



## 全局事件总线

实现任意组件之间的通信

原理基于`VueComponent.prototype.__proto__ === Vue.prototype`这一内置关系

`main.js`

```js
new Vue({
    el: '#app',
    render: f => f(App),
    beforeCreate() {
        // 将 vm 挂载成为全局事件总线
        // 这样所有的 vm vc 都能访问到 $bus
        Vue.prototype.$bus = this
        // 如果你艺高人胆大 可以这么做
        // 也就是创建一个傀儡 vc 用于数据传递
        Vue.prototype.$bus.# = new (Vue.extend({}))()
    }
})
```

`School.vue`

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
        <h2>学生的姓名为 - {{stuName}}</h2>
    </div>
</template>

<script>
    export default {
        name: 'School',
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan',
                stuName: ''
            }
        },
        mounted() {
            // 绑定事件 用于接收学生姓名
            this.$bus.$on('transmitStuName', (name) => {
                this.stuName = name
            })
        },
        beforeDestroy() {
            // 一定要在组件销毁的同时 注销 $bus 上所有注册的事件
            this.$bus.$off('transmitStuName')
        }
    }
</script>

<style scoped>
    .school-style {
        background: cyan;
    }
</style>
```

`Student.vue`

```vue
<template>
    <div class="student-style">
        <h2>姓名 - {{stuName}}</h2>
        <h2>年龄 - {{stuAge}}</h2>
        <button @click="transmitStuName">传递学生姓名</button>
    </div>
</template>

<script>
    export default {
        name: 'Student',
        data() {
            return {
                stuName: 'rekaime',
                stuAge: 18
            }
        },
        methods: {
            // 触发事件 用于发送学生姓名
            transmitStuName() {
                this.$bus.$emit('transmitStuName', this.stuName)
            }
        }
    }
</script>

<style scoped>
    .student-style {
        background: orange;
    }
</style>
```



## 消息订阅与发布

借助第三方库`pubsub-js`实现消息订阅与发布

```shell
npm i pubsub-js
```

`School.vue`

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
        <h2>学生的姓名为 - {{stuName}}</h2>
    </div>
</template>

<script>
    import pubsub from 'pubsub-js'

    export default {
        name: 'School',
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan',
                stuName: ''
            }
        },
        mounted() {
            // 订阅消息然后记录消息号
            // msgName一般不用 所以可以用下划线 _ 占位
            this.psidTransmitStuName = pubsub.subscribe('transmitStuName', (msgName, stuName) => {
                this.stuName = stuName
            })
        },
        beforeDestroy() {
            // 取消订阅
            pubsub.unsubscribe(this.psidTransmitStuName)
        }
    }
</script>
```

`Student.vue`

```vue
<template>
    <div class="student-style">
        <h2>姓名 - {{stuName}}</h2>
        <h2>年龄 - {{stuAge}}</h2>
        <button @click="transmitStuName">传递学生姓名</button>
    </div>
</template>

<script>
    import pubsub from 'pubsub-js'

    export default {
        name: 'Student',
        data() {
            return {
                stuName: 'rekaime',
                stuAge: 18
            }
        },
        methods: {
            transmitStuName() {
                // 发布消息
                pubsub.publish('transmitStuName', this.stuName)
            }
        }
    }
</script>
```



## `$nextTick`

指定`Vue`在下一次更新完模板后执行的回调函数



## 动画与过渡

### 动画

控制元素显示与隐藏之前执行一段动画

`<transistion>`标签可以控制一个单独的标签的动画，不会被渲染到页面中

```vue
<template>
    <div class="Ani">
        <button @click="isShow = !isShow">进入/离开</button>
        <!-- 通过 transition 标签配合 isShow 实现动画 -->
        <transition>
            <h1 v-show="isShow">Hello Vue!</h1>
        </transition>
    </div>
</template>

<script>
    export default {
        name: 'Ani',
        data() {
            return {
                isShow: true
            }
        }
    }
</script>

<style>
    h1 {
        background: cyan;
    }

    /* 元素显示时执行的动画 */
    .v-enter-active {
        animation: toggle 1s;
    }
    /* 元素隐藏时执行的动画 */
    .v-leave-active {
        animation: toggle 1s reverse;
    }

    @keyframes toggle {
        from {
            transform: translateX(-100%)
        }
        to {
            transform: translateX(0px)
        }
    }
</style>
```

`<transistion>`是可以起名的，为它添加`name`属性

```vue
<template>
    <div class="Ani">
        <button @click="isShow = !isShow">进入/离开</button>
        <!-- 名称为 ani-test -->
        <!-- :appear="true" 表示进场时也播放动画 可以简写为 appear -->
        <transition name="ani-test" :appear="true">
            <h1 v-show="isShow">Hello Vue!</h1>
        </transition>
    </div>
</template>

<script> /* 同上 */ </script>

<style>
    h1 {
        background: cyan;
    }
    /* 要同步更新名称 */
    .ani-test-enter-active {
        animation: toggle 1s;
    }
    /* 要同步更新名称 */
    .ani-test-leave-active {
        animation: toggle 1s reverse;
    }

    @keyframes toggle { /* 同上 */ }
</style>
```



### 过渡

控制元素显示与隐藏之前的过渡效果

道理和`Animation`类似

```vue
<template>
    <div class="Ani">
        <button @click="isShow = !isShow">进入/离开</button>
        <transition name="ani-test" :appear="true">
            <h1 v-show="isShow">Hello Vue!</h1>
        </transition>
    </div>
</template>

<script>
    export default {
        name: 'Ani',
        data() {
            return {
                isShow: true
            }
        }
    }
</script>

<style>
    h1 {
        background: cyan;
        transition: 1s; /* 执行过渡 */
    }
    /* 出现时 过渡起点 */
    .ani-test-enter {
        transform: translateX(-100%)
    }
    /* 出现时 过渡终点 */
    .ani-test-enter-to {
        transform: translateX(0)
    }
    /* 离开时 过渡起点 */
    .ani-test-leave {
        transform: translateX(0)
    }
    /* 离开时 过渡终点 */
    .ani-test-leave-to {
        transform: translateX(100%)
    }
</style>
```

因为出现时的起点和离开时的终点是一样的，对于另外两者同理

以下写法可以精简样式

```vue
<style>
    .ani-test-enter, .ani-test-leave-to {
        transform: translateX(-100%)
    }
    
    .ani-test-enter-to, .ani-test-leave {
        transform: translateX(0)
    }
</style>
```

为了不破坏`h1`样式，我们可以把`transition: 1s;`配置在这里

```vue
<style>
    /* 元素出现和离开时 */
    .ani-test-enter-active, .ani-test-leave-active {
        transition: 1s;
    }
    
    .ani-test-enter, .ani-test-leave-to {
        transform: translateX(-100%)
    }
    
    .ani-test-enter-to, .ani-test-leave {
        transform: translateX(0)
    }
</style>
```



### 多元素过渡

通过`<transision-group>`可以控制多个元素的过渡效果

其中每个子标签都需要填写`key`值



### 集成第三方动画库

[Animate.css](https://animate.style/)

安装

```shell
npm i animate.css --save
```

引入
```vue
<script>
    import 'animate.css'
</script>
```

使用

```vue
<template>
    <div class="Ani">
        <button @click="isShow = !isShow">进入/离开</button>
        <transition
            appear
            name="#"
            enter-active-class="animate__animated animate__swing"
            leave-active-class="animate__animated animate__backOutUp"
        >
            <h1 v-show="isShow">Hello Vue!</h1>
        </transition>
    </div>
</template>
```



## 异步请求

### 请求方式

推荐`axios`

```shell
npm i axios
```

```js
import axios from 'axios'

export default {
    name: 'App',
    methods: {
        getStudentInfo() {
            axios.get('http://localhost:8080/students').then(
                res => {
                    /* do something */
                },
                err => {
                    /* do something */
                }
            )
        }
    }
}
```

但是前端直接进行`ajax`请求可能会出现跨域问题

可以借助代理服务器帮忙获取数据，常用方法有：`nginx`以及`vue-cli`



### 配置代理

在`vue.config.js`中的`devServer.proxy`可以开启代理服务器

以下配置可以告诉开发服务器将未知请求代理到`http://localhost:4000`

当然，前端的请求要向代理服务器发送，也就是前端本身所处端口

```js
module.exports = {
    devServer: {
        proxy: 'http://localhost:4000'
    }
}
```

需要注意的是，代理服务器没有的资源才会将请求转发给后端

`Vue`的根路径为`public`，如果该目录下的资源收到请求，则会直接响应

上一种写法只支持一个路径，以下是多路径写法

```js
module.exports = {
    devServer: {
        proxy: {
            '/api': {
                // 如果请求url为 http://localhost:8080/api/...
                // 也就是前缀为 /api
                // 就会走这条分支
                target: 'http://localhost:4000',
                // 但是这么做给后端发的请求路径也就发生了变化
                // 因此要通过正则去除前缀 重写路径
                pathRewrite: { '^/api': '' },
                ws: true, // 用于支持 websocket 默认 true
                changeOrigin: true // 骗后端说前端是同源的 也就是控制 host 默认 true
            },
            '/foo': {
                target: '<other_url>'
            }
        }
    }
}
```



### 其他方式

+ xhr
+ jQuery
+ fetch
+ vue-resource

`vue-resource`仅作为了解，在`Vue1.0`作为插件使用较为广泛，是对`xhr`的封装

```shell
npm i vue-resource
```

```js
import vueResource from 'vue-resource'
// vm 和 vc 上新增方法 $http()
Vue.use(vueResource)
```

使用方法和`axios`一样，都是`Promise`风格

但是这个库现在不在`Vue`团队下维护，维护频率不高，因此还是推荐`axios`

```js
this.$http.get(url).then().error()
```



## slot 插槽

### 关于 v-slot

`[slot]`声明具名插槽的方式已被弃用

推荐使用`v-slot`



### 默认插槽

当我们向组件标签当中写入其他内容时，会将它们作为插槽传入组件内

`App.vue`

```vue
<template>
    <div id="app">
        <!-- 组件标签内没有写入内容时 使用插槽默认值 -->
        <SlotTs></SlotTs>
        <!-- 组件标签内写入内容时 将插槽内容进行替换 -->
        <SlotTs>Hello Vue!</SlotTs>
        <!-- 插槽可以接收任意内容 -->
        <SlotTs>
            <h2>Hello</h2>
            <h3>Vue!</h3>
        </SlotTs>
    </div>
</template>

<script>
    import SlotTs from './components/SlotTs'

    export default {
        name: 'App',
        components: {
            SlotTs
        }
    }
</script>
```

`SlotTs.vue`

```vue
<template>
    <div class="slot-ts">
        <!-- 插槽标签 占位表示插槽内容放入此处 -->
        <!-- 里面可以写一些默认内容 -->
        <slot>
            <button @click="getEmp">点我</button>
            <ul>
                <li v-for="emp in emps" :key="emp.id">
                    {{emp.id}} - {{emp.name}} - {{emp.age}} - {{emp.gender === 0 ? '男' : '女'}}
                </li>
            </ul>
        </slot>
    </div>
</template>

<script>
    import axios from 'axios'
    export default {
        name: "SlotTs",
        data() {
            return {
                emps: []
            }
        },
        methods: {
            getEmp() {
                axios.get('http://localhost:5000/students').then(res => {
                    this.emps = res.data
                }, err => {
                    console.log(err.massage)
                })
            }
        }
    }
</script>
```



### 具名插槽

如果一个组件内有多个插槽，就需要给每个插槽都起名字

`SlotTs.vue`

```vue
<template>
    <div class="slot-ts">
        <!-- 通过 name 属性为插槽起名 -->
        <slot name="slot-1">
            <button @click="getEmp">点我</button>
            <ul>
                <li v-for="emp in emps" :key="emp.id">
                    {{emp.id}} - {{emp.name}} - {{emp.age}} - {{emp.gender === 0 ? '男' : '女'}}
                </li>
            </ul>
        </slot>
        <!-- 通过 name 属性为插槽起名 -->
        <slot name="slot-2">
            <h2>Is that Vue?</h2>
        </slot>
    </div>
</template>
```

`App.vue`

```vue
<template>
    <div id="app">
        <SlotTs></SlotTs>
        <SlotTs>
            <!-- slot 属性可以指定元素进入哪个插槽 -->
            <h2 slot="slot-1">Hello</h2>
            <h3 slot="slot-2">Vue!</h3>
        </SlotTs>
        <SlotTs>
            <!-- 如果希望为同一级多个标签添加同一插槽标识 可以用<template> -->
            <template slot="slot-1">

            </template>
            <!-- 推荐 Vue2.6 的新写法 仅适用于 template -->
            <template v-slot:slot-2>
                
            </template>
        </SlotTs>
    </div>
</template>
```



### 作用域插槽

如果使用其他插槽，那么插槽维护的数据需要写在父组件中

通过作用域插槽可以将组件内维护的数据传递到插槽使用者上

`SlotTs.vue`

```vue
<template>
    <div class="slot-ts">
        <!-- <slot>上的属性向插槽使用者传递一系列维护的数据 -->
        <!-- 一般通过 v-bind 传值 你要是闲的蛋疼或者真有必要可以传静态数据 -->
        <slot :emps="emps" :getEmp="getEmp"></slot>
    </div>
</template>

<script>
    import axios from 'axios'
    export default {
        name: 'SlotTs',
        data() {
            return {
                emps: []
            }
        },
        methods: {
            getEmp() {
                axios.get('http://localhost:5000/students').then(res => {
                    this.emps = res.data
                }, err => {
                    console.log(err.massage)
                })
            }
        }
    }
</script>
```

`App.vue`

```vue
<template>
    <div id="app">
        <SlotTs>
            <!-- 必须要通过<template>接受数据 -->
            <template scope="data">
                <button @click="data.getEmp">点我</button>
                <ul>
                    <li v-for="emp in data.emps" :key="emp.id">
                        {{emp.id}} - {{emp.name}} - {{emp.age}} - {{emp.gender === 0 ? '男' : '女'}}
                    </li>
                </ul>
            </template>
        </SlotTs>

        <SlotTs>
            <!-- 可以以解构的形式接收数据 -->
            <template scope="{ getEmp, emps }">
                <!-- ... -->
            </template>
        </SlotTs>

        <SlotTs>
            <!-- 还有一种写法 但我觉得很多余 2023.4 的更新中似乎已经废弃 -->
            <template slot-scope="{ getEmp, emps }">
                <!-- ... -->
            </template>
        </SlotTs>

        <SlotTs>
            <!-- 所以建议你写这种 -->
            <template v-slot="{ getEmp, emps }">
                <!-- ... -->
            </template>
        </SlotTs>
    </div>
</template>
```



## Vuex

### 概念

在`Vue`中实现集中式状态（数据）管理的一个插件

对`Vue`中的多个组件共享状态进行集中式的读写管理，是一种适用于任意组件间通信的方式

[Github 地址](https://github.com/vuejs/vuex)

**适用场景：**

+ 多个组件依赖于同一状态
+ 不同组件的行为会变更同一状态



### 图示



![vuex.png](https://vuex.vuejs.org/vuex.png)



### 安装

注意`Vue2`最高使用`vuex@3`

```shell
npm i vuex@3
```



### 加法案例 - Vue

`App.vue`

```vue
<template>
    <div id="app">
        <Vx></Vx>
    </div>
</template>

<script>
    import Vx from './components/Vx'
    export default {
        name: 'App',
        components: { Vx }
    }
</script>
```

`Vx.vue`

```vue
<template>
    <div>
        <h2>当前总和为: {{sum}}</h2>
        <select v-model="n">
            <option :value="1">1</option>
            <option :value="2">2</option>
            <option :value="3">3</option>
        </select>
        <button @click="add">+</button>
        <button @click="sub">-</button>
        <button @click="addWhileOdd">总和为奇数则+</button>
        <button @click="waitingAdd">延时+</button>
    </div>
</template>

<script>
    export default {
        name: 'Vx',
        data() {
            return {
                sum: 0,
                n: 1
            }
        },
        methods: {
            add() {
                this.sum += this.n
            },
            sub() {
                this.sum -= this.n
            },
            addWhileOdd() {
                if (this.sum & 1) this.add()
            },
            waitingAdd() {
                setTimeout(() => {
                    this.add()
                }, 100)
            }
        }
    }
</script>
```



### 环境搭建

首先在`App.vue`的同级目录下创建`/store/index.js`

`/store/index.js`的默认形式如下

```js
import Vue from 'vue'
import vuex from 'vuex'

Vue.use(vuex) // 要在 vuex.Store() 调用前注册插件

const actions = {}

const mutations = {}

const state = {}

export default new vuex.Store({ actions, mutations, state })
```

`main.js`

```js
import store from './store/index.js'

new Vue({
    render: f => f(App),
    store
}).$mount('#app')
```



### 加法案例 - vuex

`/store/index.js`

```js
const actions = {
    // actions 一般进行业务逻辑的处理 mutations 中直接处理数据即可
    add(context, value) {
        // context 是 miniStore 包含部分 Store 上的属性
        // 包括 commit: f, dispatch: f, getters: {}, rootGetters: {}, rootState: {__observer__}, state: {__observer__}
        // 通过 commit 将数据提交给 mutation 进行真正的数据处理
        context.commit('ADD', value)
    }
}

const mutations = {
    ADD(state, value) {
        // 处理数据
        state.sum += value
    },
    SUB(state, value) {
        state.sum -= value
    }
}

const state = {
    // 存储一系列的数据
    sum: 0
}
```

`Vx.vue`

```vue
<template>
    <div>
        <!-- 监视的数据被管理在 Store 当中 -->
        <h2>当前总和为: {{$store.state.sum}}</h2>
        <select v-model="n">
            <option :value="1">1</option>
            <option :value="2">2</option>
            <option :value="3">3</option>
        </select>
        <button @click="add">+</button>
        <button @click="sub">-</button>
        <button @click="addWhileOdd">总和为奇数则+</button>
        <button @click="waitingAdd">延时+</button>
    </div>
</template>

<script>
    export default {
        name: 'Vx',
        data() {
            return {
                n: 1
            }
        },
        methods: {
            add() {
                // 将数据提交给 action
                this.$store.dispatch('add', this.n)
            },
            sub() {
                // 像加法示例中没有进行任何处理直接 commit
                // 那么可以在这一步就直接 commit
                this.$store.commit('SUB')
                // 其实如果你艺高人胆大 可以在这里直接操作 this.$store.state.sum
            },
            addWhileOdd() {
                /* ... */
            },
            waitingAdd() {
                /* ... */
            }
        }
    }
</script>
```



### 关于`getters`

`vuex`版本的`computed`

`/store/index.js`

```js
const actions = { /* ... */ }

const mutations = { /* ... */ }

const state = {
    sum: 0
}

const getters = {
    // 管理数据
    dozenSum(state) {
        return state.sum * 12
    }
}
// 记得把 getters 塞到配置项里
export default new vuex.Store({ actions, mutations, state, getters })
```

`Vx.vue`

```vue
<template>
    <div>
        <h2>当前总和为: {{$store.state.sum}}</h2>
        <!-- 引用 getters 的数据 -->
        <h2>来一打总和: {{$store.getters.dozenSum}}</h2>
    </div>
</template>
```



### 关于`mapState`和`mapGetters`

基于`vuex`实现的加法案例，其中`Vx.vue`可以进行优化

将模板中从`vuex`获取数据的部分包装进计算属性，以减轻模板负担

但是这样引出的问题是，`computed`中会出现大量重复的代码

```vue
<template>
    <div>
        <h2>当前总和为: {{sum}}</h2>
        <h2>来一打总和: {{dozenSum}}</h2>
        <!-- ... -->
    </div>
</template>

<script>
    export default {
        name: 'Vx',
        data() {
            return {
                n: 1
            }
        },
        computed: {
            sum() {
                // 将 sum 管理在计算属性中 减轻模板的代码量
                return this.$store.state.sum
            },
            // 如果还有其他类似的数据管理在 vuex 中
            // 这后面还要写很多重复的内容
            
            // 类似地还可以写 getters 的获取
            dozonSum() {
                return this.$store.getters.dozenSum
            }
        },
        methods: {
            /* ... */
        }
    }
</script>
```

通过`mapState`和`mapGetters`可以批量生成计算属性中的函数集

```vue
<script>
    import { mapState, mapGetters } from 'vuex'

    export default {
        name: 'Vx',
        computed: {
            // 构造方法 mapState 接收参数为对象
            // 其中 key 为生成的函数名为 sum
            // value 为从 state 中获取值的变量名
            // 也就是生成 sum() { return this.$store.state.sum }
            // 返回值是对象 包含生成完毕的函数 要进行解构
            ...mapState({
                sum: 'sum'
            }),
            // 同理
            ...mapGetters({
                dozenSum: 'dozenSum'
            })
        }
    }
</script>
```

更精简的写法

```vue
<script>
    export default {
        name: 'Vx',
        computed: {
            // 因为 key 和 value 是一样的
            // 所以可以通过数组传参
            ...mapState(['sum']),
            // 同理
            ...mapGetters(['dozenSum'])
        }
    }
</script>
```



关于`mapActions`和`mapMutations`

如果`vc`中配置的`methods`，有一部分方法直接调用`.dispatch()`或是`.commit()`

那么这部分方法可以利用`mapActions`和`mapMutations`直接生成

示例：

```js
...mapActions({
    add: 'add'
})

// OR

...mapActions(['add'])

// 生成如下函数
// 注意 需要在模板中进行传参
{
    function add(value) {
        this.$store.dispatch('add', value)
    }
}
```



### 模块化

为了防止多个组件在`vuex`使用上的冲突

下面给出示例，如果你希望做得更好，可以在这基础上把配置项拆分为多个文件

`/store/index.js`

```js
import Vue from 'vue'
import vuex from 'vuex'

Vue.use(vuex)

// 把属于某一部分的配置写在一个对象中
const countOpts = {
    namespaced: true, // 一定要开启命名空间 否则无法通过配置项键值匹配到配置项内容
    actions: {
        add(context, value) {
            context.commit('ADD', value)
        }
    },
    mutations: {
        ADD(state, value) {
            state.sum += value
        },
        SUB(state, value) {
            state.sum -= value
        }
    },
    state: {
        sum: 0
    },
    getters: {
        dozenSum(state) {
            return state.sum * 12
        }
    }
}

const otherOpts = {
    namespaced: true,
    actions: {},
    mutations: {},
    state: {},
    getters: {}
}

export default new vuex.Store({
    modules: {
        // 多组配置项通过 modules 导入
        // state 内会以各个配置项为独立空间维护不同数据
        // 但是不同配置项的 getters 会集中到 getters: {} 当中 除非配置了命名空间
        countAbout: countOpts,
        otherAbout: otherOpts
    }
})
```

`Vx.vue`

```vue
<template>
    <div>
        <h2>当前总和为: {{sum}}</h2>
        <h2>来一打总和: {{dozenSum}}</h2>
        <select v-model="n">
            <option :value="1">1</option>
            <option :value="2">2</option>
            <option :value="3">3</option>
        </select>
        <button @click="add">+</button>
        <button @click="sub">-</button>
        <button @click="addWhileOdd">总和为奇数则+</button>
        <button @click="waitingAdd">延时+</button>
    </div>
</template>

<script>
    import { mapState, mapGetters } from 'vuex'

    export default {
        name: 'Vx',
        data() {
            return {
                n: 1
            }
        },
        computed: {
            // 如果有多个空间的 State 或 getters 可以通过多个映射函数进行管理
            
            // 一般获取方法为 $store.state.countAbount.sum
            // 不加第一个参数就相当于未指定命名空间内查找
            ...mapState('countAbout', ['sum']),
            // 一般获取方法为 $store.getters['countAbout/dozenSum']
            // 配置项不指定命名空间 则此处无需指定完整的变量名 直接写 dozenSum 即可
            // 但如果配置项指定命名空间 此处也必须提供命名空间
            // 否则就要使用对象配置 {'dozenSum': 'countAbout/dozenSum'}
            ...mapGetters('countAbout', ['dozenSum'])
        },
        methods: {
            // 同理 不主动提供命名空间就得这么写
            add() {
                this.$store.dispatch('countAbout/add', this.n)
            },
            sub() {
                this.$store.commit('countAbout/SUB', this.n)
            },
            // 如果不想要上面两种写法就像下面一样
            // 记得在模板传参
            ...mapActions('countAbout', ['add']),
            ...mapMutations('countAbout', {
                sub: 'SUB'
            }),
            addWhileOdd() {
                /* ... */
            },
            waitingAdd() {
                /* ... */
            }
        }
    }
</script>
```



这里单独列一个有无配置命名空间的对比：

+ 无配置

```js
const $store = {
    ...,
    
}
```



## 路由

### 概念

实现 SPA（Single Page web Application）应用

需要借助`Vue`的一个插件库`vue-router`

注意`Vue2`最高使用`vue-router@3`

```shell
npm i vue-router@3
```



### 创建路由器

在`App.vue`的同级目录下创建`/router/index.js`

`/router/index.js`

```js
import VueRouter from 'vue-router'

// 导入组件
import Com1 from '...'

// 创建路由器
export default new VueRouter({
    routes: [
        {
            path: '/path-1',
            component: Com1
        },
        ...
    ]
})
```

`main.js`

```js
import VueRouter from 'vue-router'
import router from './router'

Vue.use(VueRouter)

new Vue({
    render: f => f(App),
    router
}).mount('#app')
```



### 简单的路由实现

#### 子组件

`School.vue`

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
    </div>
</template>

<script>
    export default {
        name: 'School',
        data() {
            return {
                schoolName: 'SCU',
                schoolAddr: 'SiChuan'
            }
        }
    }
</script>

<style scoped>
    .school-style {
        background: cyan;
    }
</style>
```

`Student.vue`

```vue
<template>
    <div class="student-style">
        <h2>姓名 - {{stuName}}</h2>
        <h2>年龄 - {{stuAge}}</h2>
    </div>
</template>

<script>
    export default {
        name: 'Student',
        data() {
            return {
                stuName: 'rekaime',
                stuAge: 18
            }
        }
    }
</script>

<style scoped>
    .student-style {
        background: orange;
    }
</style>
```



#### 路由配置

`/router/index.js`

```js
import VueRouter from 'vue-router'

import School from '../pages/School.vue'
import Student from '../pages/Student.vue'

export default new VueRouter({
    routes: [
        {
            path: '/school',
            component: School
        },
        {
            path: '/student',
            component: Student
        }
    ]
})
```



#### APP

```vue
<template>
    <div id="app">
        <div class="button-container">
            <!-- router-link 在此处代替<a>来实现路由管理 最终也默认转化为<a> -->
            <!-- to 属性参照路由配置的 path 来写 不能用相对路径的 ./ 标识 也不能少了开头的 / -->
            <!-- active-class 表示活跃路由对应<a>的样式 -->
            <router-link class="btn-like" active-class="btn-active" to="/school">School</router-link>
            <router-link class="btn-like" active-class="btn-active" to="/student">Student</router-link>
        </div>
        <hr>
        <div class="view-container">
            <!-- 表示路由视图显示的位置 -->
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'App'
    }
</script>

<style>
    .btn-like {
        text-decoration: none;
        margin: 1em;
    }

    .btn-active {
        font-weight: bold;
    }
</style>
```



#### 文件管理

为了区分路由组件和一般组件（路由组件不会写在`App.vue`当中），会将路由组件放在`/components`同级的`/pages`当中

在页面中，不处于活跃状态的路由组件默认会被销毁，而不是被隐藏，你也可以大发慈悲选择不销毁



#### 细节

路由组件上会出现`$route`和`$router`

所有的`$router`都是同一个对象，包含大量路由器相关方法

`$route`各不相同，包含的是各自的路由配置项

但是`vm`和`vc`身上的`$router`和`$route`指向的是当前活跃的路由组件的`$router`和`$route`



### 嵌套路由

也叫多级路由，在路由组件中也存在路由功能

#### 新增组件

在`School.vue`下希望展示两个新组件`SchoolInfo.vue`、`StudentList.vue`

`SchoolInfo.vue`

```vue
<template>
    <div class="school-info-style">
        <div>校长 - {{master}}</div>
        <div>类型 - {{type}}</div>
    </div>
</template>

<script>
    export default {
        name: 'SchoolInfo',
        data() {
            return {
                master: 'LiYanrong',
                type: '985'
            }
        }
    }
</script>
```

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">{{student.name}} - {{student.age}} - {{student.gender}}</li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: 'StudentList',
        data() {
            return {
                studentList: [
                    { id: '001', name: 'rekaime', age: 18, gender: '男' },
                    { id: '002', name: 'yifeng', age: 20, gender: '男' },
                    { id: '003', name: 'YoiMiYa', age: 16, gender: '女' }
                ]
            }
        }
    }
</script>
```



#### 路由配置

```js
export default new VueRouter({
    routes: [
        {
            path: '/school', // 一级路由
            component: School,
            children: [ // 二级路由
                {
                    path: 'school-info', // 二级路由无需以 / 开头
                    component: SchoolInfo
                },
                {
                    path: 'student-list',
                    component: StudentList
                }
            ]
        },
        {
            path: '/student',
            component: Student
        }
    ]
})
```



#### School

```vue
<template>
    <div class="school-style">
        <h2>学校 - {{schoolName}}</h2>
        <h2>地址 - {{schoolAddr}}</h2>
        <div class="button-container">
            <router-link class="btn-like" active-class="btn-active" to="/school/school-info">School Information</router-link>
            <router-link class="btn-like" active-class="btn-active" to="/school/student-list">Student List</router-link>
        </div>
        <div class="view-container">
            <router-view></router-view>
        </div>
    </div>
</template>
```



### 路由 query 传参

假设我们希望在`StudentList`组件下，实现点击学生信息能够展示学生学号和姓名

#### 参数接收

`StudentDetail.vue`

```vue
<template>
    <div class="student-detail-style">
        <!-- 从 vc.$route.query 接收参数 studentName -->
        <h3>学生学号为: {{$route.query.studentId}}</h3>
        <h3>学生姓名为: {{$route.query.studentName}}</h3>
    </div>
</template>

<script>
    export default {
        name: 'StudentDetail'
    }
</script>
```



`/router/index.js`

```js
export default new VueRouter({
    routes: [
        {
            path: '/school',
            component: School,
            children: [
                {
                    path: 'school-info',
                    component: SchoolInfo
                },
                {
                    path: 'student-list',
                    component: StudentList,
                    children: [
                        {
                            path: 'student-detail', // 三级路由
                            component: StudentDetail
                        }
                    ]
                }
            ]
        },
        {
            path: '/student',
            component: Student
        }
    ]
})
```



#### 字符串传参

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">
                <!-- 自行拼接查询参数 -->
                <router-link :to="`/school/student-list/student-detail?studentId=${student.id}&studentName=${student.name}`">{{student.name}} - {{student.age}} - {{student.gender}}</router-link>
            </li>
        </ul>
        <hr>
        <router-view></router-view>
    </div>
</template>
```



#### 对象传参

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">
                <!-- 通过对象配置查询参数 -->
                <router-link :to="{
                    path: '/school/student-list/student-detail',
                    query: {
                        studentId: student.id,
                        studentName: student.name
                    }
                }">
                    {{student.name}} - {{student.age}} - {{student.gender}}
                </router-link>
            </li>
        </ul>
        <hr>
        <router-view></router-view>
    </div>
</template>
```



### 路由 params 传参

#### 参数接收

`StudentDetail.vue`

```vue
<template>
    <div class="student-detail-style">
        <!-- 这次是通过 params 接受参数 -->
        <h3>学生学号为: {{$route.params.studentId}}</h3>
        <h3>学生姓名为: {{$route.params.studentName}}</h3>
    </div>
</template>
```



`/router/index.js`

```js
export default new VueRouter({
    routes: [
        {
            path: '/school',
            component: School,
            children: [
                {
                    name: 'school-info',
                    path: 'school-info',
                    component: SchoolInfo
                },
                {
                    name: 'student-list',
                    path: 'student-list',
                    component: StudentList,
                    children: [
                        {
                            name: 'student-detail',
                            path: 'student-detail/:id/:name', // 预留 params
                            component: StudentDetail
                        }
                    ]
                }
            ]
        }
    ]
})
```



#### 字符串传参

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">
                <router-link :to="`/school/student-list/student-detail/${student.id}/${student.name}`">
                    {{student.name}} - {{student.age}} - {{student.gender}}
                </router-link>
            </li>
        </ul>
        <hr>
        <router-view></router-view>
    </div>
</template>
```



#### 对象传参

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">
                <!-- 这里必须用 name 不能用 path -->
                <router-link :to="{
                    name: 'student-detail',
                    params: {
                        studentId: student.id,
                        studentName: student.name
                    }
                }">
                    {{student.name}} - {{student.age}} - {{student.gender}}
                </router-link>
            </li>
        </ul>
        <hr>
        <router-view></router-view>
    </div>
</template>
```



### 命名路由

为路由命名，可以简化路由路径书写

`/router/index.js`

```js
export default new VueRouter({
    routes: [
        {
            path: '/school',
            component: School,
            children: [
                {
                    name: 'school-info', // 命名
                    path: 'school-info',
                    component: SchoolInfo
                },
                {
                    name: 'student-list', // 命名
                    path: 'student-list',
                    component: StudentList,
                    children: [
                        {
                            name: 'student-detail', // 命名
                            path: 'student-detail',
                            component: StudentDetail
                        }
                    ]
                }
            ]
        }
    ]
})
```

`StudentList.vue`

```vue
<template>
    <div class="student-list-style">
        <ul>
            <li v-for="student of studentList" :key="student.id">
                <!-- 通过 name 而不是 path 指定路由 -->
                <router-link :to="{
                    name: 'student-detail',
                    query: {
                        studentName: student.name
                    }
                }">
                    {{student.name}} - {{student.age}} - {{student.gender}}
                </router-link>
            </li>
        </ul>
        <hr>
        <router-view></router-view>
    </div>
</template>
```



### 路由的 props

可以在`/router/index.js`中为路由配置`props`以向对应的路由组件传递参数

设置好`props`后，在`StudentDetail.vue`中就可以直接访问这些数据

```vue
<template>
    <div class="student-detail-style">
        <h3>学生学号为: {{studentId}}</h3>
        <h3>学生姓名为: {{studentName}}</h3>
    </div>
</template>

<script>
    export default {
        name: 'StudentDetail',
        props: ['studentId', 'studentName'] // 一定要进行接收
    }
</script>
```

下面只关注`StudentDetail`的配置



#### 对象形式

传入的数据是死的

```js
{
    name: 'student-list',
    path: 'student-list',
    component: StudentList,
    children: [
        {
            name: 'student-detail',
            path: 'student-detail',
            component: StudentDetail,
            props: {
                studentId: '004',
                studentName: 'Amane'
            }
        }
    ]
}
```



#### 布尔值形式

如果值为`true`则表示将`params`的所有键值对作为`prop`添加到`vc`上

注意，无法处理`query`

```js
{
    name: 'student-list',
    path: 'student-list',
    component: StudentList,
    children: [
        {
            name: 'student-detail',
            path: 'student-detail/:studentId/:studentName', // 需要先预置 params
            component: StudentDetail,
            props: true
        }
    ]
}
```



#### 函数形式

函数将接受参数`$route`，因此我们可以通过参数来获取`query`和`params`等属性

```js
{
    name: 'student-list',
    path: 'student-list',
    component: StudentList,
    children: [
        {
            name: 'student-detail',
            path: 'student-detail',
            component: StudentDetail,
            props({ query }) {
                return { studentId: query.studentId, studentName: query.studentName }
            }
        }
    ]
}
```



### replace 模式

`replace`模式就是在进入新路由时，将浏览记录栈顶的元素给替换掉，而不是进行压栈

为`<router-link>`标签添加`replace`属性即可为该路由开启`replace`模式



### 编程式路由导航

人为控制路由

#### push&replace

`$router`中的 API `$router.push()`和`$router.replace()`可以接收和`<router-link>`的`to`属性一样的值，以跳转到对应的路由



#### back&forward&go

同样是`$router`上的 API，分别代表后退、前进、移动 n 步（参数为正数则代表前进、负数则代表后退）



### 缓存路由组件

如果一个路由中存在一系列操作，操作未完成时切至别的路由组件，再切回原路由组件时，会导致操作丢失

将`<router-view>`包裹在`<keep-alive>`标签内可以缓存路由组件（所有内容）

```vue
<keep-alive>
    <router-view></router-view>
</keep-alive>
```

你也可以通过`include`属性可选地选择缓存的内容

```vue
<keep-alive include="{{{需要缓存的组件名}}}">
    <router-view></router-view>
</keep-alive>
<!-- 缓存多个组件则写成数组 -->
<keep-alive :include="['Cpn1', ...]">
    <router-view></router-view>
</keep-alive>
```



### 相关生命周期钩子

以下两个生命周期钩子必须在开启缓存时才会启用

此时路由组件不处于活跃态时不会被销毁 ，而是失活，当重新被移入页面中时激活

#### 路由激活

`.activated()`在路由组件处于活跃时调用

#### 路由失活

`.deactivated()`在路由组件被其他路由组件顶替掉时调用



## 路由守卫

### 概念

当路由发生跳转操作，路由守卫可以运行一系列逻辑，维护路由跳转的过程



### 全局前置

需要接收`/router/index.js`的`router`配置，然后进行配置

前置路由守卫在路由切换前调用

`$route.meta`是一个储存元数据的数据，我们可以在其中设置某些属性（如`isAuth: true`）帮助我们判断路由是否需要受路由守卫管控

```js
router.beforeEach((to, from, next) => {
    // to&from 是起点和终点对应的 $route
    // next 是一个函数 调用则允许进入下一路由(to)
})
```



### 全局后置

也就是路由切换后才调用

```js
router.afterEach((to, from) => {
    // 和上面那个兄弟一样
    // 你可以用这玩意做一些小的维护
    // 比如切换标题 document.title = ...
})
```



### 独享路由守卫

为单独的路由设定前置，注意没有独享后置守卫

通过路由配置项的`beforEnter`设定

和全局守卫一样，配置内容为函数

```js
export default new VueRouter({
    routes: [
        {
            path: '/student',
            component: Student,
            beforeEnter: (to, from, next) => { }
        }
    ]
})
```



### 组件内路由守卫

通过组件配置项的`beforeRouteEnter`和`beforeRouteLeave`属性配置，内容为函数

```vue
<script>
    export default {
        name: 'StudentDetail',
        props: ['studentId', 'studentName'],
        beforeRouteEnter(to, from, next) {
            // 路由组件通过路由规则进入该组件时
        },
        beforeRouteLeave(to, from, next) {
            // 路由组件通过路由规则离开该组件时
        }
    }
</script>
```



### history & hash

我们目前为止编辑的路由，会在`Url`中产生一个`#`

`#`以及其后的部分都被称作路径的`hash`值，`hash`值不会随`http`请求被发送给服务器

而`history`模式下，路径以标准的`/`标识层级

更改`/router/index.js`配置项`mode`属性，可以更改路由模式

```js
export default new VueRouter({
    mode: 'history'
})
```



## 项目打包

将`/src`和`/public`的内容进行整合，生成`html`、`css`、`js`代码

输出文件夹为`/dist`

```shel
npm run build
```

输出的资源需要在服务器挂载才能使用

如果服务器不进行处理详细的路由处理，那么在`history`模式下可能会出错，而`hash`模式不会

`NodeJs`可以通过库`connect-history-api-fallback`解决`history`模式的路由匹配问题

```js
const history = require('connect-history-api-fallback')
app.use(history())
```

