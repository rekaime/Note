# [Vue3.0](https://cn.vuejs.org/)

## 和 Vue2 的区别

- 使用上：
  - 选项式 -> 组合式，方便逻辑聚合
  - 没有`this`
  - 生命周期变化
  - v-if 优先级高于 v-for
  - 实例创建从`new Vue({ render: h => h(App) })`变为`createApp`
  - 全局注册（如 mixin、全局组件、use）改为了 app 实例调用，而不是 Vue 类调用
  - 新增传送门`<teleport>`
  - `<template>`可以不单独包在根`<div>`中
- 原理方面：
  - 采用 Proxy 实现响应式，解决数组无法通过下标修改、对象属性新增删除无法监听的问题，同时提高响应式效率
  - vue3 没有完全抛弃 defineProperty，通过 ref 定义的数据会返回一个 RefImpl 实例，这个实例的 value 属性是通过 defineProperty 来追加的
  - 组合式风格的 js 采用函数式编程，方便按需引入，可以配合 tree-shaking 实现打包优化
  - 增加静态节点标记，在 diff 算法过程中跳过不会变化的 vnode（`_createElementVNode('div', {}, '123', 位掩码)`）
- 进阶：
  - 推荐使用 hook 替代 mixin
  - v-model 监听组件的行为不再是`chang|input`，而是`update:modelValue`
  - 更易于结合 ts



## 创建 Vue3

### vue-cli 创建

**目前`vue-cli`处于维护模式，官方推荐基于`vite`创建项目**

```shell
vue create {{{project_name}}}
# 然后选 3.x
npm run serve
```



### vite

`vite`通过`Rust`重构，是新生前端构建工具，[点击进入官网](https://vitejs.cn)

`vite`具有诸多优势：

+ 轻量快速的热重载（`HMR`），能实现极速服务启动
+ 支持`TypeScript`、`JSX`、`CSS`等开箱即用
+ 按需编译，无需等待整个应用编译完成
+ `webpack`构建

```shell
npm create vue@latest

# 配置
# Vue.js - The Progressive JavaScript Framework
√ 请输入项目名称： ... myproject_vue3
√ 是否使用 TypeScript 语法？ ... 否 / [是]
√ 是否启用 JSX 支持？ ... [否] / 是
√ 是否引入 Vue Router 进行单页面应用开发？ ... [否] / 是
√ 是否引入 Pinia 用于状态管理？ ... [否] / 是
√ 是否引入 Vitest 用于单元测试？ ... [否] / 是
√ 是否要引入一款端到端（End to End）测试工具？ » 不需要
√ 是否引入 ESLint 用于代码质量检测？ ... [否] / 是
√ 是否引入 Vue DevTools 7 扩展用于调试? (试验阶段) ... [否] / 是
```

此后可以初始化依赖

```shell
cd myproject_vue3
npm install # or npm i
npm run dev
```



## 工程文件结构

+ `/src`：前端主要内容
+ `index.html`：入口
+ `/public`：脚手架目录
+ `/env.d.ts`：`Ts`配置可接受的文件类型
+ `vite.config.ts`：配置`vite`，用于安装插件、配置代理
+ `tsconfig.json`、`tsconfig.app.json`、`tsconfig.node.json`：`Ts`配置文件



## 挂载 App

`vite`识别`index.html`为入口，然后解析该文件下的`<script type="module" src="/src/main.ts">`，找到`/src/main.ts`文件，在该`ts`文件下以`createApp`的方式创建`vue`实例

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

同时需要准备好`App`组件：`App.vue`

```vue
<script lang="ts">
    import Student from './components/Student.vue'

    export default {
        name: 'App',
        components: {
            Student
        }
    }
</script>

<template>
    <Student></Student>
</template>
```

组件`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name - {{ name }}</li>
            <li>age - {{ age }}</li>
        </ul>
    </div>
</template>

<script lang="ts">
    export default {
        name: 'Student',
        data() {
            return {
                name: 'rekaime',
                age: 18
            }
        }
    }
</script>

<style scoped>
    .student {
        background: skyblue;
    }
</style>
```



## Vue3 生命周期

### 图示

![组件生命周期图示](https://cn.vuejs.org/assets/lifecycle_zh-CN.W0MNXI0C.png)

### V2 V3 的异同

`Vue2`生命周期：

+ `beforeCreate`、`created`
+ `beforeMount`、`mounted`
+ `beforeUpdate`、`updated`
+ `beforeDestroy`、`destroyed`

`Vue3`生命周期：（触发需要引入对应的`on#`钩子）

+ `setup`
+ `beforeMount`、`mounted`
+ `beforeUpdate`、`updated`
+ `beforeUnmount`、`unmounted`



## 从 OptionAPI 到 CompositionAPI

在`Vue2`中，组件的配置是基于选项的，而实现任意功能都需要将数据、业务堆砌在`data`、`methods`、`computed`等配置项当中

而`Vue3`提出了基于组合式的风格，利用函数的方式组织代码片段，有序组织相关功能的数据



### setup()

本质是一个函数：

+ `setup`返回的对象可以直接在模板中使用
+ `setup`访问的`this`是`undefined`
+ `setup`函数在`beforeCreate`前调用，领先于所有钩子执行
+ `setup`返回值可以是对象，也可以是一个函数

`Student.vue`

```vue
<script lang="ts">
    export default {
        name: 'Student',
        setup() {
            // 没有 this 无法获取 OptionAPI 配置的数据
            // 但是 OptionAPI 可以读取 CompositionAPI 配置的数据
            let name = 'rekaime'
            let age = 18

            return () => {
                name,
                age
            }
        }
    }
</script>
```

**语法糖**

```vue
<script lang="ts">
    <!-- 可以不写这坨 因为一般文件名和组件名是一样的 -->
    export default {
        name: 'Student'
    }
</script>

<script setup lang="ts">
    let name = 'rekaime'
    let age = 18
</script>
```



### ref()

用于创建、定义一个响应式的基本类型变量，返回值为`RefImpl`实例对象，包含一系列私有属性和最关键的`value`值

不要通过后续操作更换一个变量指向的`ref`对象，后面涉及到的`reactive`也一样

#### 创建基本类型

`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name - {{ name }}</li>
            <li>age - {{ age }}</li>
        </ul>
        <div>
            <button @click="IncreaseAge">年龄++</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'

    let name = ref('rekaime')
    let age = ref(18)

    function IncreaseAge() {
        age.value++
    }
</script>
```



#### 创建复杂类型

`ref`处理对象时，会将对象包装进`Proxy`然后赋值给`value`

`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name: {{ name }} - {{ age }}</li>
            <li>girlfriend: {{ girlfriend.name }} - {{ girlfriend.age }}</li>
            <li>hobbies: {{ hobbies.join(' - ') }}</li>
        </ul>
        <div>
            <button @click="IncreaseAge">年龄++</button>
            <button @click.once="DownloadGame">点我下载百分百鲜橙汁</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'

    const name = ref('rekaime')
    const age = ref(18)
    const girlfriend = ref({
        name: 'Yifeng',
        age: 18
    })
    const hobbies = ref([
        'Genshin', 'Honkai', 'ZZZ'
    ])

    function IncreaseAge() {
        age.value++
    }

    function DownloadGame() {
        hobbies.value.push('Orange juice')
    }
</script>
```





### reactive()

将一个`Js`**对象**转变为响应式对象，利用原生的`Proxy`实现

深层级的对象建议使用`reactive`

注意，`reactive`变量不可以通过重新赋值来进行替换，即便赋值的内容本身是`reactive`变量，但是可以通过`Object.assign(obj, src_1, src_2, ...)`来完成对应操作

`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name: {{ name }} - {{ age }}</li>
            <li>girlfriend: {{ girlfriend.name }} - {{ girlfriend.age }}</li>
            <li>hobbies: {{ hobbies.join(' - ') }}</li>
        </ul>
        <div>
            <button @click="IncreaseAge">年龄++</button>
            <button @click="IncreaseGirlfriendAge">npy年龄++</button>
            <button @click="ResetGirlfriend">npy重置</button>
            <button @click.once="DownloadGame">点我下载百分百鲜橙汁</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref, reactive } from 'vue'

    const name = ref('rekaime')
    const age = ref(18)
    const girlfriend = reactive({
        name: 'Yifeng',
        age: 18
    })
    const hobbies = reactive([
        'Genshin', 'Honkai', 'ZZZ'
    ])

    function IncreaseAge() {
        age.value++
    }

    function IncreaseGirlfriendAge() {
        girlfriend.age++
    }

    function DownloadGame() {
        hobbies.push('Orange juice')
    }

    function ResetGirlfriend() {
        Object.assign(girlfriend, {
            name: 'Yifeng',
            age: 18
        })
    }
</script>
```



### toRefs()

将一个`reactive`定义的对象转为一个`js`对象，其中的属性都是`ObjectRefImpl`类型的响应式变量

`ObjectRefImpl`会保存解构的对象`_object`以及每个属性对应的`_key`

`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name: {{ name }} - {{ age }}</li>
        </ul>
        <div>
            <button @click="IncreaseAge">年龄++</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { reactive, toRefs } from 'vue'

    const student = reactive({
        name: 'rekaime',
        age: 18
    })

    const { name, age } = toRefs(student)

    function IncreaseAge() {
        age.value++
    }
</script>
```



### toRef()

`toRefs`的单一版本

`Student.vue`

```vue
<template>
    <div class="student">
        <ul>
            <li>name: {{ name }} - {{ age }}</li>
        </ul>
        <div>
            <button @click="IncreaseAge">年龄++</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { reactive, toRef } from 'vue'

    const student = reactive({
        name: 'rekaime',
        age: 18
    })

    const name = toRef(student, 'name')
    const age = toRef(student, 'age')

    function IncreaseAge() {
        console.log(age)
        age.value++
    }
</script>
```



## 计算属性

返回一个`ComputedRefImpl`对象

如果只提供一个函数则视为`getter`，其中的`value`是只读属性

还可以提供一个对象，包含`getter`和`setter`，如此`value`就是可读写的

`Student.vue`

```vue
<template>
    <div class="student">
        姓: <input type="text" v-model="lName"><br>
        名: <input type="text" v-model="fName"><br>
        全名: <span>{{ fullName }}</span>
    </div>
</template>

<script setup lang="ts">
    import { ref, computed } from 'vue'

    let lName = ref('')
    let fName = ref('')

    let fullName = computed(() => {
        if (lName.value === '' || fName.value === '') return 'Waiting for input...'
        return `${lName.value} - ${fName.value}`
    })
</script>
```



## 数据侦听

### 概述

`Vue3`的`watch`只能监视以下四种数据：

+ `ref`对象
+ `reactive`对象
+ `getter`函数
+ 包含上述内容的数组



### 侦听 ref - 基本类型

`Student.vue`

```vue
<template>
    <div class="student">
        <h2>Sum: {{ sum }}</h2>
        <button @click="Summation">+</button>
    </div>
</template>

<script setup lang="ts">
    import { ref, watch } from 'vue'

    let sum = ref(0)
    
    function Summation() {
        sum.value++
    }

    // 如果希望结束侦听，可以接受`watch`函数的返回值，这个返回值也是一个函数，调用即可结束侦听
    // 如果进行深度侦听 只有属性发生变化时 newValue === oldValue
    const stopWatch = watch(sum, (newValue, oldValue) => {
        console.log(newValue, oldValue)
        if (newValue >= 10) stopWatch()
    })
</script>
```



### 侦听 ref - 复杂类型

`Student.vue`

```vue
<template>
    <div class="student">
        <h2>name: {{ student.name }}</h2>
        <h2>age: {{ student.age }}</h2>
        <button @click="changeName">~</button>
        <button @click="changeAge">+</button>
        <button @click="reset">reset</button>
    </div>
</template>

<script setup lang="ts">
    import { ref, watch } from 'vue'

    const student = ref({
        name: 'rekaime',
        age: 18
    })

    function changeName() {
        student.value.name += '~'
    }
    function changeAge() {
        student.value.age++
    }
    function reset() {
        student.value = {
            name: 'rekaime',
            age: 18
        }
    }

    // 不传递第三个参数 则不关注 student 属性的变化 只关注 student 引用的变化
    // 如果进行深度侦听 且只有属性发生变化时 newValue === oldValue
    watch(student, (newValue, oldValue) => {
        console.log(newValue === oldValue)
    }, {
        deep: true,
        immediate: true
    })
</script>
```



### 侦听 reactive

`Student.vue`

```vue
<template>
    <!-- 同 #侦听 ref - 复杂类型 -->
</template>

<script setup lang="ts">
    import { reactive, watch } from 'vue'

    const student = reactive({
        name: 'rekaime',
        age: 18
    })
    <!-- 同 #侦听 ref - 复杂类型 -->

    // 无论如何 都开启深度监视 也就是隐式创建深层侦听
    // 且 newValue === oldValue
    watch(student, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```



### 侦听指定属性

`Student.vue`

```vue
<template>
    <!-- 同 #侦听 ref - 复杂类型 -->
</template>

<script setup lang="ts">
    import { ref, watch } from 'vue'

    const student = ref({
        name: 'rekaime',
        age: 18
    })
    <!-- 同 #侦听 ref - 复杂类型 -->

    // 侦听单独一个属性 利用 getter
    // reactive 版本的也一样
    watch(() => student.value.age, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```

如果想玩点花的也可以这么写，如此你的每次侦听都会得到`{name: string, age: number}`对象

```vue
<script>
    watch(() => ({
        name: student.value.name,
        age: student.value.age
    }), (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```

注意，如果侦听的某一属性也是个对象，最好采用`getter`的方式

```vue
<script>
    const student = ref({
        name: 'rekaime',
        age: 18,
        friend: {
            name: 'Yui',
            age: 18
        }
    })
    
    // 如果直接侦听 student.value.friend
    // 则侦听的是 friend 内属性的变化 friend 引用变化后侦听失效
    // 使用 getter 则侦听的是 friend 引用的变化
    // 利用 deep 开启深层侦听
    watch(() => student.value.friend, (newValue, oldValue) => {
        console.log(newValue, oldValue)
    })
</script>
```



### watchEffect

`Student.vue`

```vue
<template>
    <div class="student">
        <h2>student: {{ student.name }} - {{ student.age }}</h2>
        <h2>friend: {{ student.friend.name }} - {{ student.friend.age }}</h2>
        <button @click="changeName">~</button>
        <button @click="changeAge">+</button>
        <button @click="changeFName">f~</button>
        <button @click="changeFAge">f+</button>
        <button @click="reset">reset</button>
    </div>
</template>

<script setup lang="ts">
    import { ref, watchEffect } from 'vue'

    const student = ref({
        name: 'rekaime',
        age: 16,
        friend: {
            name: 'Yui',
            age: 16
        }
    })

    function changeName() {
        student.value.name += '~'
    }

    function changeAge() {
        student.value.age++
    }

    function changeFName() {
        student.value.friend.name += '~'
    }

    function changeFAge() {
        student.value.friend.age++
    }

    function reset() {
        student.value = {
            name: 'rekaime',
            age: 16,
            friend: {
                name: 'Yui',
                age: 16
            }
        }
    }

    // 自动判断侦听哪些属性 (student.age student.friend.age)
    watchEffect(() => {
        if (student.value.age >= 18 || student.value.friend.age >= 18) {
            console.log('有人成年了')
        }
    })

    console.log()
</script>
```



## ref 属性

### 获取 html 标签

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 ref="me">student: {{ student.name }} - {{ student.age }}</h2>
        <h2 ref="friend">friend: {{ student.friend.name }} - {{ student.friend.age }}</h2>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'

    const student = ref({
        name: 'rekaime',
        age: 16,
        friend: {
            name: 'Yui',
            age: 16
        }
    })

    // 变量名需要和 ref 属性同名
    const me = ref()
    const friend = ref()

    console.log(me, friend)
</script>
```



### 获取组件标签

首先需要在子组件中选择导出的数据，否则默认将所有数据进行保护

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 ref="me">student: {{ student.name }} - {{ student.age }}</h2>
        <h2 ref="friend">friend: {{ student.friend.name }} - {{ student.friend.age }}</h2>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'

    const student = ref({
        name: 'rekaime',
        age: 16,
        friend: {
            name: 'Yui',
            age: 16
        }
    })

    const me = ref()
    const friend = ref()
    defineExpose({ me, friend }) // 导出的数据
</script>
```

`App.vue`

```vue
<template>
    <button @click="show">点击查看组件</button>
    <Student ref="student" />
</template>

<script setup lang="ts">
    import Student from './components/Student.vue'
    import { ref } from 'vue'

    const student = ref()
    
    function show() {
        console.log(student.value) // 获取子组件实例对象 其上挂载着子组件导出的数据
    }
</script>
```



## props

### 利用泛型定义类型

`/src/type/index.ts`

```ts
export interface IStudent {
    id: string
    name: string
    age?: number
}

export type StudentList = IStudent[]
```



### 数据传递

`App.vue`

```vue
<template>
    <!-- 利用 prop 传递数据 -->
    <Student :studentList="studentList"></Student>
</template>

<script setup lang="ts">
    import Student from './components/Student.vue'
    import { reactive } from 'vue'
    import { type StudentList } from '@/type' // 导入类型

    // 利用泛型规范数据
    const studentList = reactive<StudentList>([
        { id: '001', name: 'rekaime', age: 16 },
        { id: '002', name: 'Yifeng', age: 18 },
        { id: '003', name: 'YugiAmane', age: 13 },
        { id: '004', name: 'YugiTsukasa', age: 13 }
    ])
</script>
```



### 数据接收

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">student: {{ student.name }} - {{ student.age }}</h2>
    </div>
</template>

<script setup lang="ts">
    // 向 definePros 传递一个数组 数组中是各 props 变量名
    defineProps(['studentList'])
</script>
```

如果需要接收数据并保存起来，需要主动接收`defineProps()`的返回值

```vue
<script setup lang="ts">
    const props = defineProps(['studentList'])
</script>
```

如果需要限制类型

```vue
<script setup lang="ts">
    import { type StudentList } from '@/type'

    defineProps<{
        studentList: StudentList
    }>()
</script>
```

可选与默认值

```vue
<script setup lang="ts">
    import { withDefaults } from 'vue'
    import { type StudentList } from '@/type'

    type PropsType = {
        studentList?: StudentList
    }
    
    withDefaults(defineProps<PropsType>(), {
        studentList: () => [
            { id: '001', name: 'rekaime', age: 16 }
        ]
    })
</script>
```



## 自定义 Hooks

本质上是包管理：`/src/hooks/use<...>.ts`

和一般的包区别在于，你可以在`hooks`内使用生命周期钩子、`computed`等

`/src/hooks/useStudentList.ts`

```ts
import { reactive } from 'vue'
import { type StudentList } from '@/type'

export default function () {
    const studentList = reactive<StudentList>([
        { id: '001', name: 'rekaime', age: 16 },
        { id: '002', name: 'Yifeng', age: 18 },
        { id: '003', name: 'YugiAmane', age: 13 },
        { id: '004', name: 'YugiTsukasa', age: 13 }
    ])

    return { studentList }
}
```

`/src/hooks/useSum.ts`

```ts
import { ref } from 'vue'

export default function () {
    let sum = ref(0)

    function increaseSum() {
        sum.value++
    }

    return { sum, increaseSum }
}
```

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">student: {{ student.name }} - {{ student.age }}</h2>
        <hr>
        <h2>Sum: {{ sum }}</h2>
        <button @click="increaseSum">+</button>
    </div>
</template>

<script setup lang="ts">
    // 引入 hooks
    import useStudentList from '@/hooks/useStudentList'
    import useSum from '@/hooks/useSum'
    // 解构获取对应的数据
    const { studentList } = useStudentList()
    const { sum, increaseSum } = useSum()
</script>
```



## 路由

### 安装

```shell
npm i vue-router
```



### 路由组件

`/src/pages/Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">student: {{ student.name }} - {{ student.age }}</h2>
    </div>
</template>

<script setup lang="ts">
    import { reactive } from 'vue';
    import { type StudentList } from '@/type'

    const studentList = reactive<StudentList>([
        { id: '001', name: 'rekaime', age: 16 },
        { id: '002', name: 'Yifeng', age: 18 },
        { id: '003', name: 'YugiAmane', age: 13 },
        { id: '004', name: 'YugiTsukasa', age: 13 }
    ])
</script>

<style scoped lang="less">
    .student {
        background: skyblue;
    }

    button {
        margin: 0 4px;
    }
</style>
```

`/src/pages/Hobby.vue`

```vue
<template>
    <div class="hobby">
        <ul>
            <li v-for="hobby in hobbies">{{ hobby }}</li>
        </ul>
    </div>
</template>

<script setup lang="ts">
    import { reactive } from 'vue';

    const hobbies = reactive<string[]>(['Genshin', 'Honkai', 'ZZZ'])
</script>

<style scoped lang="less">
    .hobby {
        background: skyblue;
    }
</style>
```



### 路由配置

#### 路由器

`/src/router/routes.ts`

```ts
export default [
    {
        path: '/student',
        component: () => import('@/views/Student.vue')
    },
    {
        path: '/hobby',
        component: () => import('@/views/Hobby.vue')
    }
]
```



`/src/router/index.ts`

```ts
import { createRouter, createWebHistory } from "vue-router"
import routes from './routes'

const router = createRouter({
    history: createWebHistory(), // 想用 Hash 模式就改成 createWebHashHistory()
    routes
})

export default router
```



#### 注册

`main.ts`

```ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App)
    .use(router)
    .mount('#app')
```



### 布局

`App.vue`

```vue
<template>
    <nav>
        <router-link to="/student" active-class="active">学生信息</router-link>
        <router-link to="/hobby" active-class="active">兴趣爱好</router-link>
    </nav>
    <main>
        <router-view></router-view>
    </main>
</template>

<script setup lang="ts">
    import { RouterLink, RouterView } from 'vue-router'
</script>

<style scoped lang="less">
    nav {
        display: flex;
        justify-content: space-evenly;

        a {
            height: 4em;
            line-height: 4em;
            text-decoration: none;
            color: black;
            padding: 0 1em;
            border: none;
            border-radius: .5em;
            background: linear-gradient(to right, cyan 0, skyblue);
            font-weight: bold;

            &.active {
                color: orangered;
            }
        }
    }
</style>
```



### 命名路由

`/src/router/routes.ts`

```ts
export default [
    {
        name: 'student',
        path: '/student',
        component: () => import('@/views/Student.vue')
    },
    {
        name: 'hobby',
        path: '/hobby',
        component: () => import('@/views/Hobby.vue')
    }
]
```



`App.vue`

```vue
<template>
    <nav>
        <router-link :to="{
            name: 'student'
        }" active-class="active">学生信息</router-link>
        <router-link :to="{
            path: '/hobby'
        }" active-class="active">兴趣爱好</router-link>
    </nav>
    <main>
        <router-view></router-view>
    </main>
</template>
```



### 嵌套路由

嵌套路由**不需要**在最前方加`/`

`/src/router/routes.ts`

```ts
export default [
    {
        path: '/student',
        component: () => import('@/views/Student.vue'),
        children: [
            {
                path: 'wiggle-student',
                component: () => import('@/views/WiggleStudent.vue')
            }
        ]
    },
    {
        path: '/hobby',
        component: () => import('@/views/Hobby.vue')
    }
]
```

`WiggleStudent.vue`

```vue
<template>
    <div class="wiggle-student">
        <h2>name，早上好喵</h2>
    </div>
</template>
```

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <!-- 写完整的路径 -->
            <router-link to="/student/wiggle-student">student: {{ student.name }} - {{ student.age }}</router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```



### 路由传参

#### useRoute()

`vue-router`提供的一个钩子，调用后获取`route`实例，其中包含大量的属性

接收参数方可以通过`route`获取发送方传递的`query`、`params`

```vue
<script setup lang="ts">
    import { useRoute } from 'vue-router'
    const route = useRoute()
</script>
```



#### query

字符串传参

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <router-link :to="`/student/wiggle-student?name=${student.name}`">student: {{ student.name }} - {{ student.age }}</router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```

对象传参

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <router-link :to="{
                path: '/student/wiggle-student',
                query: {
                    name: student.name
                }
            }">
                student: {{ student.name }} - {{ student.age }}
            </router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```



#### params

`/src/router/routes.ts`

```ts
export default [
    {
        name: 'student',
        path: '/student',
        component: () => import('@/views/Student.vue'),
        children: [
            {
                path: 'wiggle-student/:name/:id?', // 设置参数位置 ?表示可选
                component: () => import('@/views/WiggleStudent.vue')
            }
        ]
    },
    {
        name: 'hobby',
        path: '/hobby',
        component: () => import('@/views/Hobby.vue')
    }
]
```

字符串传参

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <router-link :to="`/student/wiggle-student/${student.name}`">student: {{ student.name }} - {{ student.age }}</router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```

对象传参

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <!-- 必须使用命名路由 -->
            <router-link :to="{
                name: 'wiggle-student',
                params: {
                    name: student.name
                }
            }">
                student: {{ student.name }} - {{ student.age }}
            </router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```



### 路由 Props

#### 其一

在`params`参数基础上，将`params`自动解析成`props`

`/src/router/routes.ts`

```ts
{
    name: 'wiggle-student',
    path: 'wiggle-student/:name/:id?',
    component: () => import('@/views/WiggleStudent.vue'),
    props: true // 写法一
}
```

`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <router-link :to="{
                name: 'wiggle-student',
                params: {
                    name: student.name,
                    id: student.id
                }
            }">
                student: {{ student.name }} - {{ student.age }}
            </router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```

`WiggleStudent.vue`

```vue
<template>
    <div class="wiggle-student">
        <h2>
            <span v-if="id">{{ id }} - </span>{{ name }}，早上好喵
        </h2>
    </div>
</template>

<script setup lang="ts">
    defineProps(['name', 'id'])
</script>
```



#### 其二

给`props`传递一个函数，第一个参数是`route`实例

`/src/router/routes.ts`

```ts
{
    name: 'wiggle-student',
    path: 'wiggle-student/:name/:id?',
    component: () => import('@/views/WiggleStudent.vue'),
    props(route: any) { // 写法二
        return route.query
    }
}
```



`Student.vue`

```vue
<template>
    <div class="student">
        <h2 v-for="student in studentList" :key="student.id">
            <router-link :to="{
                name: 'wiggle-student',
                query: {
                    name: student.name,
                    id: student.id
                }
            }">
                student: {{ student.name }} - {{ student.age }}
            </router-link>
        </h2>
    </div>
    <hr>
    <div class="action">
        <router-view></router-view>
    </div>
</template>
```



`WiggleStudent.vue`

```vue
<template>
    <div class="wiggle-student">
        <h2>
            <span v-if="id">{{ id }} - </span>{{ name }}，早上好喵
        </h2>
    </div>
</template>

<script setup lang="ts">
    defineProps(['name', 'id'])
</script>
```



#### 其三

给`props`传递一个对象，写死了的

`/src/router/routes.ts`

```ts
{
    name: 'wiggle-student',
    path: 'wiggle-student/:name/:id?',
    component: () => import('@/views/WiggleStudent.vue'),
    props: { // 写法三
        name: 'rekaime',
        id: '001'
    }
}
```



### replace 属性

默认为`push`模式，可以自己手动切换到`replace`模式

```vue
<template>
    <router-link replace></router-link>
</template>
```



### 编程式路由导航

只利用`<router-link>`进行跳转，那么最后渲染到页面上的都是`<a>`

通过编程式路由导航可以实现路由切换

引入`router`

```vue
<script setup lang="ts">
    import { RouterLink, RouterView, useRouter } from 'vue-router'
    const router = useRouter()
    
    // 以下两种方式的参数和 to 参数一样
    // push 方式
    router.push('/')
    // replace 方式
    router.replace('/')
</script>
```



### 重定向

`/src/router/routes.ts`

```ts
export default [
    {
        path: '/student',
        component: () => import('@/views/Student.vue')
    },
    {
        path: '/hobby',
        component: () => import('@/views/Hobby.vue')
    },
    {
        path: '/',
        redirect: '/student' // 重定向
    }
]
```



## Pinia

### 介绍

[Pinia](https://pinia.vuejs.org/)是符合直觉的`Vue`集中式状态管理库，规避了`vuex`的臃肿等缺点

可以将需要共享的数据保存在`pinia`当中



### 组件准备

`Counter.vue`：对集中数据中的`number`进行改变

```vue
<template>
    <div class="counter">
        <h2>Counter</h2>

        <div class="input-area">
            <input type="number" v-model="counter">
        </div>

        <div class="button-area">
            <button @click="add">+</button>
            <button @click="sub">-</button>
            <button @click="mul">*</button>
            <button @click="rev">reverse</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    
    let counter = ref(0)
    let number = 0

    function add() {
        number += counter.value
    }
    function sub() {
        number -= counter.value
    }
    function mul() {
        number *= counter.value
    }
    function rev() {
        number = -number
    }
</script>

<style scoped lang="less">
    .counter {
        background: skyblue;
        padding: 8px;

        input {
            outline: none;
        }

        button {
            margin: 0 4px;
        }
    }
</style>
```

`FindExp.vue`：检查集中数据中的`number`的指数范围

```vue
<template>
    <div class="find-exp">
        <h2>Find exponent</h2>
        <h3 v-if="number > 0">
            2<span class="exp">{{ l }}</span> ≤ {{ number }} < 2<span class="exp">{{ l+1 }}</span>
        </h3>
    </div>
</template>

<script setup lang="ts">
    import { ref, computed } from 'vue'

    let number = ref(0)
    let l = computed(() => {
        let exp = 0
        let cur = 1
        while (cur < number.value) {
            cur = cur << 1
            exp++
        }
        if (cur !== number.value) exp--
        return exp
    })
</script>

<style scoped lang="less">
    .find-exp {
        background: orange;
        padding: 8px;

        .exp {
            font-size: .5em;
            vertical-align: top;
        }
    }
</style>
```

`App.vue`

```vue
<template>
    <h2>Number: </h2>
    <hr>
    <Counter></Counter>
    <hr>
    <FindExp></FindExp>
</template>

<script setup lang="ts">
    import Counter from './components/Counter.vue'
    import FindExp from './components/FindExp.vue'
</script>
```



### 环境搭建

```shell
npm i pinia
```

`main.ts`

```ts
import { createApp } from 'vue'
import App from './App.vue'
import { createPinia } from 'pinia'

const pinia = createPinia()

createApp(App)
    .use(pinia)
    .mount('#app')
```



### 数据操作

#### 存储

`/src/store/Counter.ts`

```ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('Counter', {
    // state: 数据存储的空间
    state() {
        return {
            number: 6
        }
    }
})
```



#### 读取

`App.vue`

```vue
<script setup lang="ts">
    // import { toRefs } from 'vue';
    import Counter from './components/Counter.vue'
    import FindExp from './components/FindExp.vue'
    import { storeToRefs } from 'pinia'
    // 引入 store
    import { useCounterStore } from '@/store/Counter'
    // 获取实例
    const counterStore = useCounterStore()
    // 所有存储在 state 中的值都能在 counterStore 上直接获取
    // 如果采用直接赋值而不是解构对象 那么拿到的是 Ref.value
    // 采用解构的方式可以获取 RefImpl
    // const { number } = toRefs(counterStore)
    
    // 但是解构的时候最好用 storeToRefs
    // 这样可以只取出数据(state) 不解构方法
    const { number } = storeToRefs(counterStore)
</script>
```

`Counter.vue`

```vue
<script setup lang="ts">
    import { toRefs } from 'vue'
    import { useCounterStore } from '@/store/Counter'
    import { storeToRefs } from 'pinia'

    const counterStore = useCounterStore()
    
    let counter = ref(0)
    const { number } = storeToRefs(counterStore)

    function add() {
        number.value += counter.value
    }
    function sub() {
        number.value -= counter.value
    }
    function mul() {
        number.value *= counter.value
    }
    function rev() {
        number.value = -number.value
    }
</script>
```

`FindExp.vue`

```vue
<script setup lang="ts">
    import { computed } from 'vue'
    import { useCounterStore } from '@/store/Counter'
    import { storeToRefs } from 'pinia'

    const counterStore = useCounterStore()
    
    const { number } = storeToRefs(counterStore)

    let l = computed(() => {
        let exp = 0
        let cur = 1
        while (cur < number.value) {
            cur = cur << 1
            exp++
        }
        if (cur !== number.value) exp--
        return exp
    })
</script>
```



#### 修改

```vue
<script setup lang="ts">
    import { toRefs } from 'vue'
    import { useCounterStore } from '@/store/Counter'

    const counterStore = useCounterStore()
    
    // 直接修改
    couterStore.number += 1
    // $patch()
    counterStore.$patch({
        number: counterStore.number + 1
    })
</script>
```

第三种方式是利用`action`

`Counter.ts`

```ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('Counter', {
    // 供组件使用的方法
    actions: {
        add() {
            this.number++
        }
    },
    // 数据存储的空间
    state() {
        return {
            number: 6
        }
    }
})
```

组件内

```vue
<script setup lang="ts">
    import { toRefs } from 'vue'
    import { useCounterStore } from '@/store/Counter'

    const counterStore = useCounterStore()
    
    counterStore.add()
</script>
```



### getters

相当于`Pinia`中的一类计算属性

`Counter.ts`

```ts
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('Counter', {
    state() {
        return {
            number: 6
        }
    },
    getters: {
        dozenNumber(state) {
            // state === this
            return state.number * 12
        }
    }
})
```

`Components.vue`

```vue
<template>
    <h2>Number: {{ counterStore.dozenNumber }}</h2>
</template>
```



### $subscribe

可以通过`store.$subscribe(mutate, state)`监测`store`中`state`的变化

其中参数`state`顾名思义

`mutate`

```ts
type TMutate = {
    events: Array<{}> || {} // 记录数据的前后变化 如果通过$patch修改数据则为数组
    payload: {}
    storeId: string // 所监视的store的名称
    type: 'direct' || 'patch object'
}

// 如果连续direct修改一个数据 key newValue oldValue 都显示的是后修改数据的值
// action 方式修改也可以属于 direct
// 但是 target 会记录所有更新数据的当前值
type TEvent = {
    effect: Array<ReactiveEffect> || ReactiveEffect
    key: string // 修改的变量的名称
    newValue
    oldValue
    target: {}
    // ...
}
```

`Components.vue`

```vue
<script setup lang="ts">
    import { useCounterStore } from '@/store/Counter'

    const counterStore = useCounterStore()

    counterStore.$subscribe((mutate, state) => {
        console.log(mutate, state)
    })
</script>
```



### 组合式风格

`Counter.ts`

```ts
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useCounterStore = defineStore('Counter', () => {
    // 数据相当于 state
    const number = ref(6)

    // 函数相当于 actions
    function add(v: number) {
        number.value += v
    }
    
    // 计算属性相当于 getter 我懒得写了自己脑补

    return {
        number,
        add
    }
})
```



## 组件通信

### 组件结构

`App.vue`

```vue
<template>
    <h2>Number: {{ number }}</h2>
    <hr>
    <Counter></Counter>
    <hr>
    <FindExp></FindExp>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    import Counter from './components/Counter.vue'
    import FindExp from './components/FindExp.vue'
    
    const number = ref(6)
</script>
```

`Counter.vue`

```vue
<template>
    <div class="counter">
        <h2>Counter</h2>

        <div class="input-area">
            <input type="number" v-model="counter">
        </div>

        <div class="button-area">
            <button @click="add">+</button>
            <button @click="sub">-</button>
            <button @click="mul">*</button>
            <button @click="rev">reverse</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    
    let counter = ref(0)
    // const number

    function add() {
        number.value += counter.value
    }
    function sub() {
        number.value -= counter.value
    }
    function mul() {
        number.value *= counter.value
    }
    function rev() {
        number.value = -number.value
    }
</script>
```

`FindExp.vue`

```vue
<template>
    <div class="find-exp">
        <h2>Find exponent</h2>
        <h3 v-if="number > 0">
            2<span class="exp">{{ l }}</span> ≤ {{ number }} < 2<span class="exp">{{ l+1 }}</span>
        </h3>
    </div>
</template>

<script setup lang="ts">
    import { computed } from 'vue'
    
    // const number

    let l = computed(() => {
        let exp = 0
        let cur = 1
        while (cur < number.value) {
            cur = cur << 1
            exp++
        }
        if (cur !== number.value) exp--
        return exp
    })
</script>
```



### props

`App.vue`

```vue
<template>
    <h2>Number: {{ number }}</h2>
    <hr>
    <!-- 向子组件传递数据 number, 同时传递修改 number 的接口 -->
    <Counter :number="number" :MutateNumber="MutateNumber"></Counter>
    <hr>
    <FindExp :number="number"></FindExp>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    import Counter from './components/Counter.vue'
    import FindExp from './components/FindExp.vue'
    
    const number = ref(6)

    const MutateNumber = {
        add(v: number) {
            number.value += v
        },
        sub(v: number) {
            number.value -= v
        },
        mul(v: number) {
            number.value *= v
        },
        rev() {
            number.value = -number
        }
    }
</script>
```

`Counter.vue`

```vue
<template>
    <div class="counter">
        <h2>Counter</h2>

        <div class="input-area">
            <input type="number" v-model="counter">
        </div>

        <div class="button-area">
            <button @click="MutateNumber.add(counter)">+</button>
            <button @click="MutateNumber.sub(counter)">-</button>
            <button @click="MutateNumber.mul(counter)">*</button>
            <button @click="MutateNumber.rev">reverse</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    
    let counter = ref(0)
    defineProps(['number', 'MutateNumber'])
</script>
```



### 自定义事件

`App.vue`

```vue
<template>
    <h2>Number: {{ number }}</h2>
    <hr>
    <!-- 注册传递自定义事件 -->
    <Counter :number="number" @apply-for-add="add"></Counter>
    <hr>
    <FindExp :number="number"></FindExp>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    import Counter from './components/Counter.vue'
    import FindExp from './components/FindExp.vue'
    
    const number = ref(6)
    // 自定义事件的定义
    function add(v: number) {
        number.value += v
    }
</script>
```

`Counter.vue`

```vue
<template>
    <div class="counter">
        <h2>Counter</h2>

        <div class="input-area">
            <input type="number" v-model="counter">
        </div>

        <div class="button-area">
            <!-- emit() 方法触发自定义事件 -->
            <button @click="emit('apply-for-add', counter)">+</button>
        </div>
    </div>
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    
    let counter = ref(0)
    // 获取 emit 实例
    const emit = defineEmits(['apply-for-add'])
</script>
```



### mitt

#### 安装和配置

```shell
npm i mitt
```

`/src/utils/emitter.ts`

```ts
import mitt from 'mitt'

const emitter = mitt()

export default emitter
```



#### 方法

+ `emitter.on()`：绑定事件
+ `emitter.emit()`：触发事件
+ `emitter.off()`：解绑事件
+ `emitter.all()`触发所有事件

记得利用`onUnmounted()`解绑事件



### v-model

首先需要明白的一点，以下两行代码是等价的

```vue
<template>
    <input v-model="number">
    <input
           :value="number"
           @input="number = (<HTMLInputElement>$event.target).value"
    >
</template>
```

对于组件而言

```vue
<template>
    <MyInput v-model="number" />
    <MyInput
           :modelValue="number"
           @update:modelValue="number = <arg>"
    />
</template>
```



`App.vue`

```vue
<template>
    <h2>Number: {{ number }}</h2>
    <hr>
    <MyInput v-model="number" />
</template>

<script setup lang="ts">
    import { ref } from 'vue'
    import MyInput from './components/MyInput.vue'
    const number = ref
</script>
```

`MyInput.vue`

```vue
<template>
    <input
           type="text"
           :value="modelValue"
           @input="emit('update:modelValue', Number((<HTMLInputElement>$event.target).value))"
    >
</template>

<script setup lang="ts">
    defineProps(['modelValue'])
    const emit = defineEmits(['update:modelValue'])
</script>
```



其他细节：

```vue
<template>
    <MyInput v-model:another="number"></MyInput>
    <MyInput
           :another="number"
           @update:another="number = <arg>"
    ></MyInput>
</template>
```



### $attrs

适用于当前组件的父组件向当前组件的子组件通信，也就是祖→孙

父组件向子组件传递的数据，不在子组件中通过`defineProps()`接收的部分，会保存在`$attrs`属性中

父组件还可以传递方法，子组件可以借此更新数据

`App.vue`

```vue
<template>
    <Child v-bind="{
        name: 'rekaime',
        age: 18
    }"></Child>
</template>
```

`Child.vue`

```vue
<template>
    <Grandchild v-bind="$attrs"></Grandchild>
</template>
```



### \$refs & \$parant

`$refs`和`$event`一样可以用于标签方法参数占位

`$refs`会捕获所有带有`ref`属性的子组件

```vue
<template>
    <button @click="showMsg($refs)"> </button>
    <Cm1 ref="cm1"></Cm1>
    <Cm2 ref="cm2"></Cm2>
</template>
```

`$parent`就是在子组件获取父组件实例对象



### provide & inject

可以跨越当前组件实现父组件到子组件的通信

`App.vue`

```vue
<template>
    <Child></Child>
</template>

<script>
    import Child from './Child.vue'
    import { ref, provide } from 'vue'
    
    const name = ref('rekaime')
    provide('name', name) // 提供数据给后台
</script>
```

`Grandchild.vue`

```vue
<template>
    <div class="Grandchild">
        
    </div>
</template>

<script>
    import { ref, inject } from 'vue'
    
    const name = inject('name', 'Amane') // 获取后台的数据 如果没获取到对应数据则采用默认值
</script>
```



## slot

### 默认插槽

略



### 具名插槽

`Category.vue`

```vue
<template>
    <div class="category">
        <slot name="title">默认内容</slot>
        <slot name="body">默认内容</slot>
    </div>
</template>
```

`App.vue`

```vue
<template>
    <!-- v-slot 标记于组件标签上 -->
    <Category v-slot:body>
        <h2>Hello</h2>
    </Category>
</template>

<script setup lang="ts">
    import Category from './components/Category.vue'
</script>
```

`App.vue`

```vue
<template>
    <Category>
        <!-- v-slot 标记于template标签上 -->
        <template v-slot:body>
            <h2>I'm rekaime</h2>
        </template>
        <template v-slot:title>
            <h2>Hello</h2>
        </template>
    </Category>

    <Category>
        <!-- # === v-slot: -->
        <template #body>
            <h2>I'm rekaime</h2>
        </template>
        <template #title>
            <h2>Hello</h2>
        </template>
    </Category>
</template>
```



### 作用域插槽

在子组件维护数据，然后父组件修改插槽内的结构

可以结合具名插槽使用：`v-slot:slot-name="{ dataList }"`或者`#slot-name="{ dataList }"`

`App.vue`

```vue
<template>
    <Game>
        <template v-slot="{ games }">
            <ul>
                <li v-for="game in games" :key="game.id">
                    {{ game.name }}
                </li>
            </ul>
        </template>
    </Game>
</template>

<script setup lang="ts">
    import Game from './components/Game.vue'
</script>
```

`Game.vue`

```vue
<template>
    <div class="game">
        <h2>Games</h2>
        <slot :games="games">默认内容</slot>
    </div>
</template>

<script setup lang="ts">
    import { reactive } from 'vue'

    const games = reactive([
        { id: '001', name: 'Genshin' },
        { id: '002', name: 'Honkai' },
        { id: '003', name: 'ZZZ' }
    ])
</script>
```



## 常用 API

### shallowRef & shallowReactive

浅层响应式，只监视第一层数据的变化，适用于数据量大的对象

`shallowRef()`定义的数据只能监视`var.value`这一层数据的变化

`shallowReactive()`定义的数据只能监视第一层属性的变化

```vue
<template>
    <div class="test">
        <h2>Number: {{ number }}</h2>
        <h3>{{ student.name }} - {{ student.age }}</h3>
        <button @click="increaseNumber">number++</button>
        <button @click="increaseAge">age++</button>
        <button @click="cover">cover student</button>
    </div>
</template>

<script setup lang="ts">
    import { shallowRef } from 'vue'

    const number = shallowRef(0)
    const student = shallowRef({
        name: 'rekaime',
        age: 18
    })
    // 响应式
    function increaseNumber() {
        number.value++
    }
    // 非响应式
    function increaseAge() {
        student.value.age++
    }
    // 响应式
    function cover() {
        student.value = {
            name: 'rekaime',
            age: 20
        }
    }
</script>
```



### readonly & shallowReadonly

`readonly()`可以通过一个对象创建一个只读对象

一般传递`ref`或`reactive`

生成的新变量和传入的对象是关联的

而`shallowReadonly()`的只读监视范围仅限于数据的第一层

**readonly**

```vue
<template>
    <div class="test">
        <h2>Number: {{ number }}</h2>
        <h2>n: {{ n }}</h2>
        <button @click="increaseNumber">number++</button>
    </div>
</template>

<script setup lang="ts">
    import { ref, readonly } from 'vue'

    const number = ref(0)
    const n = readonly(number) // 构造一个只读版本的 number

    function increaseNumber() {
        number.value++
    }
</script>
```

**shallowReadonly**

```vue
<script setup lang="ts">
    import { reactive, shallowReadonly } from 'vue'

    const student = reactive({
        name: 'rekaime',
        score: {
            ch: 100,
            en: 100,
            jp: 100
        }
    })

    const s = shallowReadonly(student)
    s.name = ''     // 不行
    s.score.ch = 99 // 彳亍
</script>
```



### toRaw & markRaw

获取原始数据

经过`ref`或`reactive`包装的数据，会成为`Proxy`对象，使用`toRaw()`可以获取`target`

在数据经过中间件处理时，不希望对处理过程进行追踪，可以传入`toRaw(obj)`

```vue
<template>
    <div class="test">
        <h2>{{ student.name }}</h2>
        <ul>
            <li v-for="(key, index) in student.score" :key="index">{{ key }}</li>
        </ul>
        <h2>{{ s.name }}</h2>
        <ul>
            <li v-for="(key, index) in s.score" :key="index">{{ key }}</li>
        </ul>
    </div>
    <!-- 点击这个按钮 两组数据都会发生变化 -->
    <button @click="student.score.ch--">student ch--</button>
    <!-- 点击这个按钮 数据都不发生变化 但是再点击上面的按钮 数据会更新为实际数据 -->
    <button @click="s.score.ch--">s ch--</button>
</template>

<script setup lang="ts">
    import { reactive, toRaw } from 'vue'

    const student = reactive({
        name: 'rekaime',
        score: {
            ch: 100,
            en: 100,
            jp: 100
        }
    })
    const s = toRaw(student)
</script>
```

`markRaw()`可以标记一个数据，使其永远不会成为响应式数据

```vue
<script setup lang="ts">
    import { reactive, markRaw } from 'vue'

    const student = markRaw({
        name: 'rekaime',
        score: {
            ch: 100,
            en: 100,
            jp: 100
        }
    })
    const s = reactive(student) // 此时 s 仍是原始数据
</script>
```



### customRef

自定义`ref`，比如可以利用这个实现数据防抖

`customRef`一般会封装为`hooks`

```ts
import { customRef } from 'vue'

export default function (initMsg: string, delay: number) {
    let oriMsg = initMsg || 'rekaime' // 目标数据
    let timer = -1
    delay = delay || 500

    const msg = customRef((track, trigger) => {
        return {
            get() {
                track() // 提示 Vue 这里有个重要数据需要跟踪
                return oriMsg
            },
            set(value) {
                clearTimeout(timer)
                timer = setTimeout(() => {
                    oriMsg = value
                    trigger() // 提示 Vue 数据发生了变化需要更新
                }, delay)
            }
        }
    })

    return {
        msg
    }
}
```



### teleport

当`<teleport>`内的元素展示时，以`to`属性内选择器所指向的元素作为父元素，也就是你可以指定一个 DOM，在其中插入`<teleport>`的内容

```vue
<template>
    <teleport to="body">
        <!-- any -->
    </teleport>
</template>
```



### suspense

如果子组件存在异步任务，在本组件中可能会丢失子组件

因此需要通过`<Suspense>`标签包裹子组件

```vue
<template>
    <Suspense>
        <!-- 通过插槽实现 -->
        <!-- 子组件成功响应前展示 fallback -->
        <!-- 子组件成功响应后展示 default -->
        <template #default>
            <Child></Child>
        </template>
        <template #fallback>
            <span>加载中...</span>
        </template>
    </Suspense>
</template>

<script setup lang="ts">
    import { Suspense } from 'vue'
    import Child from './components/Child.vue'
</script>
```



### 全局 API

#### app.component

注册全局组件

`main.ts`

```ts
import { createApp } from 'vue'
import App from './App.vue'
import Hello from './components/Hello.vue'

const app = createApp(App)
// 注册全局组件 可以在任意组件中使用该组件
app.component('Hello', Hello)

app.mount('#app')
```



#### app.config

全局配置项，相当于`Vue2`的`Vue.prototype.var = 9`

`main.ts`

```ts
import { createApp } from 'vue'
import App from './App.vue'
import Hello from './components/Hello.vue'

const app = createApp(App)
app.config.globalProperties.var = 9
declare module 'vue' {
    interface ComponentCustomProperties {
        x: number
    }
}

app.mount('#app')
```



#### app.directive

注册全局指令`v-#`

`main.ts`

```ts
import { createApp } from 'vue'
import App from './App.vue'
import Hello from './components/Hello.vue'

const app = createApp(App)
// 你可以通过 v-hello 修饰标签
app.directive('hello', (element, {value}) => {
    element.innerHTML = 'hello!'
})

app.mount('#app')
```



#### app.unmount

就是卸载`app`，和`app.mount()`相反的



#### app.use

使用插件



## 非兼容性改变

[自己看](https://v3-migration.vuejs.org/zh/breaking-changes/)



# 关于打包

## 修改输出目录名

`vite.config.ts`

```ts
export default defineConfig({
    build: {
        target: 'esnext', // 使用 await 等新语法 但是要注意浏览器兼容性是否允许这类新语法
        rollupOptions: {
            output: {
                dir: 'static'
            }
        }
    },
})
```



# 插件推荐

## volar(Vue-official)

`Vscode`内的插件，勾选配置项`Auto Insert: Dot Value`即可在`ref`变量后自动添加`.value`



## \<script name="#"\>

```shell
npm i vite-plugin-vue-setup-extend -D
```

`vite.config.ts`

```ts
import VueSetupExtend from 'vite-plugin-vue-setup-extend'

export default defineConfig({
    plugins: [
        VueSetupExtend()
    ]
})
```

`Component.vue`

```vue
<script setup lang="ts" name="App"></script>
```



## 按需引入

```shell
npm install -D unplugin-vue-components unplugin-auto-import
```

`vite.config.js`

```js
import Components from 'unplugin-vue-components/vite'

export default defineConfig({
    // ...
    plugins: [
        // ...
        Components(),
    ],
})
```

**按需引入组件库**

`vite.config.js`

```js
import Components from 'unplugin-vue-components/vite'

// 引入解析器
import {
    AntDesignVueResolver,
    ElementPlusResolver,
    VantResolver,
    NaiveUiResolver
} from 'unplugin-vue-components/vite'

export default defineConfig({
    // ...
    plugins: [
        // ...
        Components({
            resolvers: [
                AntDesignVueResolver(),
                ElementPlusResolver(),
                VantResolver(),
                NaiveUiResolver()
            ],
        }),
    ],
})
```

**按需引入 Vue API**

`vite.config.js`

```js
import AutoImport from 'unplugin-auto-import/vite'

export default defineConfig({
    plugins: [
        AutoImport({
            imports: ['vue', 'vue-router', 'pinia'],
            dts: true
        }),
    ]
})
```

`tsconfig.app.json`

```json
{
    "include": ["./auto-imports.d.ts"],
}
```

