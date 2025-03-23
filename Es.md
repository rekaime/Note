# [ES6](https://es6.ruanyifeng.com)

## let & const

`let`

+ 变量不能重复声明
+ 具有块级作用域
+ 不存在变量提升
+ 不影响作用域链

`const`

+ 需要初始化
+ 不能修改
+ 具有块级作用域



## 解构

`Array`

```js
const games = ['Genshin', ['Honkai', 'Honkai-StarRail'], 'ZZZ']
const [G, H, Z] = games

// 缺省值
const [G1, H1, Z1, HH = 'Honkai-StarRail'] = games

// 占位和剩余
const [ , H2, Z2] = games
const [ G3, ...otherGames ] = games // otherGames 为数组且只能作为最后一个变量接收

// 迭代
const [ G4, [ H41, H42 ], Z4 ] = games
```

`Object`

```js
const student = {
    name: 'rekaime',
    age: 18,
    school: {
        name: 'SCU',
        addr: 'SiChuan'
    }
}

const { name, age, school } = student

// 重命名以及获取部分属性
// studentBaseInfo = {name: 'rekaime', age: 18}
const { school: schoolInfo, ...studentBaseInfo } = student

// 迭代
const {
    name,
    age,
    school: {
        name: schoolName,
        addr: schoolAddr
    }
} = student
```

只要对象具有`Iterator`接口就可以采用数组形式解构赋值

```js
function* fibs() {
    let a = 0;
    let b = 1;
    while (true) {
        yield a;
        [a, b] = [b, a + b];
    }
}

let [first, second, third, fourth, fifth, sixth] = fibs()
sixth // 5
```



## 模板字符串

```js
function sayHi(name) {
    console.log(`Hello! I'm ${name}!`)
}
```



## 对象简写

```js
let name = 'rekaime'
let age = 18

const student = { name, age }
```



## 箭头函数

+ `this`是静态的，始终指向函数声明时所在作用域下的`this`
+ 无法作为构造方法
+ 无法使用`arguments`变量



## 参数默认值

```js
function sayHi(name = 'rekaime') {
    console.log(`Hello! I'm ${name}!`)
}
```



## rest

用来代替`arguments`，必须最后接收

```js
function add(...args) {
    return args.reduce((pre, cur) => pre + cur, 0)
}
```



## 扩展运算符

可以用在`string`、`array`、`map`、`set上`

```js
const games = ['Genshin', ['Honkai', 'Honkai-StarRail'], 'ZZZ']

// ...games => 'Genshin', ['Honkai', 'Honkai-StarRail'], 'ZZZ'
```



## Symbol

### Symbol

引入的第七种数据类型，表示独一无二的值，在生成后会对应一个动态的值

+ 值是唯一的，解决命名冲突问题
+ 不能与其他数据进行运算
+ 定义的对象属性不能使用`for in`进行遍历，可以使用`Reflect.ownKeys()`获取`key`值

```js
// 创建
let s = Symbol() // log(s) => Symbol()
s = Symbol('Description')
s.description // Description
s.toString() // Symbol('Description')

let s1 = Symbol('Description') // log(s1) => Symbol('Description')
let s2 = Symbol('Description') // log(s2) => Symbol('Description')
s1 === s2 // false

// .for() 会在全局注册一个 Symbol 对象
s1 = Symbol.for('Id') // log(s1) => Symbol('Id')
s2 = Symbol.for('Id') // log(s2) => Symbol('Id')
s1 === s2 // true
Symbol.keyFor(s) // undefined
Symbol.keyFor(s1) // 'Id'
```

可以用于定义常量，消除魔术字符串

```js
const obj = { /* ? */ }

UP: Symbol()
DOWN: Symbol()
const methods = {
    [UP]: "up",
    [DOWN]: "down",
    up: UP,
    down: DOWN
}
```

作为`key`时不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回，因此不会出现在`for...in`、`for...of`循环中，但是可以被`Object.getOwnPropertySymbols()`读取对象的所有`Symbol`属性名

`Reflect.ownKeys()`能够返回所有类型的`key`

```js
const foo = Symbol('foo')

const obj = {
    [foo]: foo,
    number: 2
}

Object.getOwnPropertyNames(obj)   // ['number']
Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
Reflect.ownKeys(obj)              // ['number', Symbol(foo)]
```



### 内置 Symbol 值

#### hasInstance

对象的`Symbol.hasInstance`属性，指向一个内部方法

当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法

```js
class MyArray {
    [Symbol.hasInstance](foo) {
        return foo instanceof Array
    }
}

[1, 2, 3] instanceof new MyArray() // true
```



#### isConcatSpreadable

对象的`Symbol.isConcatSpreadable`属性等于一个布尔值

表示该对象用于`Array.prototype.concat()`时，是否可以展开

```js
// 对于数组来说 默认可展开
let arr1 = ['c', 'd']
arr1[Symbol.isConcatSpreadable] // undefined
['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']

let arr2 = ['c', 'd']
arr2[Symbol.isConcatSpreadable] = false
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']

// 对于对象 默认不可展开
let obj = {length: 2, 0: 'c', 1: 'd'}
['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']

obj[Symbol.isConcatSpreadable] = true
['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']

// 可以将其定义在类中
class A1 extends Array {
  constructor(args) {
    super(args)
    this[Symbol.isConcatSpreadable] = true
  }
}
class A2 extends Array {
  constructor(args) {
    super(args)
  }
  get [Symbol.isConcatSpreadable] () {
    return false
  }
}
let a1 = new A1()
a1[0] = 3
a1[1] = 4
let a2 = new A2()
a2[0] = 5
a2[1] = 6
[1, 2].concat(a1).concat(a2) // [1, 2, 3, 4, [5, 6]]
```



#### species

对象的`Symbol.species`属性，指向一个构造函数

创建衍生对象时，会使用该属性

```js
class MyArray extends Array { }

const a = new MyArray(1, 2, 3)
const b = a.map(x => x)
const c = a.filter(x => x > 1)

b instanceof MyArray // true
c instanceof MyArray // true
// ===============================
class MyArray extends Array {
    // static get [Symbol.species]() { return this }
    static get [Symbol.species]() { return Array }
}

const a = new MyArray()
const b = a.map(x => x)

b instanceof MyArray // false
b instanceof Array // true
```



#### match

对象的`Symbol.match`属性，指向一个函数

当执行`str.match(myObject)`时，如果该属性存在，会调用它，返回该方法的返回值

```js
String.prototype.match(regexp)
// 等同于
regexp[Symbol.match](this)

class MyMatcher {
  [Symbol.match](string) {
    return 'hello world'.indexOf(string);
  }
}

const str = 'e'
const regexp = new MyMatcher()
str.match(regexp) // 1
// 等同于
regexp[Symbol.match](str) // 1
```



#### replace

对象的`Symbol.replace`属性，指向一个方法

当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值

```js
String.prototype.replace(searchValue, replaceValue)
// 等同于
searchValue[Symbol.replace](this, replaceValue)


const obj = {
    [Symbol.replace]: (searchValue, replaceValue) => console.log(searchValue, replaceValue)
}
'Hello'.replace(obj, 'World') // ["Hello", "World"]
```



#### search

对象的`Symbol.search`属性，指向一个方法

当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值

```js
String.prototype.search(regexp)
// 等同于
regexp[Symbol.search](this)


class MySearcher {
    constructor(value) {
        this.value = value
    }
    [Symbol.search](string) {
        return string.indexOf(this.value)
    }
}
'foobar'.search(new MySearch('foo')) // 0
```



#### split

对象的`Symbol.split`属性，指向一个方法

当该对象被`String.prototype.split`方法调用时，会返回该方法的返回值

```js
String.prototype.split(separator, limit)
// 等同于
separator[Symbol.split](this, limit)

class MySplitter {
    constructor(value) {
        this.value = value
    }
    [Symbol.split](string) {
        let index = string.indexOf(this.value)
        if (index === -1) return string
        return [
            string.substr(0, index),
            string.substr(index + this.value.length)
        ]
    }
}

'foobar'.split(new MySplitter('foo')) // ['', 'bar']
'foobar'.split(new MySplitter('bar')) // ['foo', '']
'foobar'.split(new MySplitter('baz')) // 'foobar'
```



#### iterator

对象的`Symbol.iterator`属性，指向该对象的默认遍历器方法

```js
const myIterable = {
    [Symbol.iterator]: function* () {
        yield 1
        yield 2
        yield 3
    }
}

[...myIterable] // [1, 2, 3]

// for...of 循环调用遍历器
class Collection {
    *[Symbol.iterator]() {
        let i = 0;
        while (this[i] !== undefined) yield this[i++]
    }
}

let myCollection = new Collection()
myCollection[0] = 1
myCollection[1] = 2

for (let value of myCollection) console.log(value)
// 1
// 2
```



#### toPrimitive

对象的`Symbol.toPrimitive`属性，指向一个方法

该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。

`Symbol.toPrimitive`被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式。

- Number：该场合需要转成数值
- String：该场合需要转成字符串
- Default：该场合可以转成数值，也可以转成字符串

```js
let obj = {
    [Symbol.toPrimitive](hint) {
        switch (hint) {
            case 'number': return 123
            case 'string': return 'str'
            case 'default': return 'default'
            default: throw new Error()
        }
    }
};

2 * obj // 246
3 + obj // '3default'
obj == 'default' // true
String(obj) // 'str'
```



#### toStringTag

对象的`Symbol.toStringTag`属性，用来设定一个字符串（设为其他类型的值无效，但不报错）

在目标对象上面调用`Object.prototype.toString()`方法时，如果`Symbol.toStringTag`属性存在，该属性设定的字符串会出现在`toString()`方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个大写字符串

ES6 新增内置对象的`Symbol.toStringTag`属性值如下：

- `JSON[Symbol.toStringTag]`：'JSON'
- `Math[Symbol.toStringTag]`：'Math'
- Module 对象`M[Symbol.toStringTag]`：'Module'
- `ArrayBuffer.prototype[Symbol.toStringTag]`：'ArrayBuffer'
- `DataView.prototype[Symbol.toStringTag]`：'DataView'
- `Map.prototype[Symbol.toStringTag]`：'Map'
- `Promise.prototype[Symbol.toStringTag]`：'Promise'
- `Set.prototype[Symbol.toStringTag]`：'Set'
- `%TypedArray%.prototype[Symbol.toStringTag]`：'Uint8Array'等
- `WeakMap.prototype[Symbol.toStringTag]`：'WeakMap'
- `WeakSet.prototype[Symbol.toStringTag]`：'WeakSet'
- `%MapIteratorPrototype%[Symbol.toStringTag]`：'Map Iterator'
- `%SetIteratorPrototype%[Symbol.toStringTag]`：'Set Iterator'
- `%StringIteratorPrototype%[Symbol.toStringTag]`：'String Iterator'
- `Symbol.prototype[Symbol.toStringTag]`：'Symbol'
- `Generator.prototype[Symbol.toStringTag]`：'Generator'
- `GeneratorFunction.prototype[Symbol.toStringTag]`：'GeneratorFunction'

```js
({[Symbol.toStringTag]: 'Foo'}.toString()) // "[object Foo]"

class Collection {
    get [Symbol.toStringTag]() {
        return 'Im Collection'
    }
}
let c = new Collection()
Object.prototype.toString.call(c) // "[object Im Collection]"
```



#### unscopables

对象的`Symbol.unscopables`属性，指向一个对象

该对象指定了使用`with`关键字时，哪些属性会被`with`环境排除

```js
// Array 中有 7 项会被with命令排除
Array.prototype[Symbol.unscopables]
// {
//   copyWithin: true,
//   entries: true,
//   fill: true,
//   find: true,
//   findIndex: true,
//   includes: true,
//   keys: true
// }

Object.keys(Array.prototype[Symbol.unscopables])
// ['copyWithin', 'entries', 'fill', 'find', 'findIndex', 'includes', 'keys']
```

```js
// 没有 unscopables 时
class MyClass {
    foo() { return 1 }
}

var foo = function () { return 2 };

with (MyClass.prototype) {
    foo() // 1
}

// 有 unscopables 时
class MyClass {
    foo() { return 1 }
    get [Symbol.unscopables]() {
        return { foo: true }
    }
}

var foo = function () { return 2 }

with (MyClass.prototype) {
    foo() // 2
}
```



## Set & Map

### Set

#### 定义

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值

```js
const s = new Set()
```

```js
// 可以接受一个数组或者具有 iterable 接口的其他数据结构作为参数
const s = new Set([1, 4, 5, 2, 3])
```

可用于字符串或数组的去重

向 Set 加入值的时候，不会发生类型转换，所以`5`和`"5"`是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是向 Set 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身

`Array.from()`方法可以将 Set 结构转为数组

```js
const items = new Set([1, 2, 3, 4, 5])
const array = Array.from(items);
```



#### Set.prototype

Attributes：

- `Set.prototype.constructor`：构造函数，默认就是`Set`函数
- `Set.prototype.size`：返回`Set`实例的成员总数

Methods：

- `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身
- `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
- `Set.prototype.clear()`：清除所有成员，没有返回值
- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员

注：对`Set`而言`key === value`

集合运算：

- `Set.prototype.intersection(other)`：返回交集
- `Set.prototype.union(other)`：返回并集
- `Set.prototype.difference(other)`：返回差集
- `Set.prototype.symmetricDifference(other)`：返回对称差集
- `Set.prototype.isSubsetOf(other)`：判断是否为子集
- `Set.prototype.isSupersetOf(other)`：判断是否为超集
- `Set.prototype.isDisjointFrom(other)`：判断是否不相交



#### WeekSet

与 Set 有两个区别：

+ WeakSet 的成员只能是对象和 Symbol 值，而不能是其他类型的值
+ WeakSet 的对象都是弱引用，WeakSet 不可遍历

```js
let ws
ws = new WeakSet()
ws = new WeakSet([[1, 4, 5], [2, 3]])
```



成员（不包含`size`因此不支持遍历）：

- **WeakSet.prototype.add(value)**：向 WeakSet 实例添加一个新成员，返回 WeakSet 结构本身
- **WeakSet.prototype.delete(value)**：清除 WeakSet 实例的指定成员，清除成功返回`true`，如果在 WeakSet 中找不到该成员或该成员不是对象，返回`false`
- **WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在 WeakSet 实例之中



### Map

#### 定义

`Map`解决了 JS 对象只能用字符串作为`key`的问题

如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如`0`和`-0`就是一个键，布尔值`true`和字符串`true`则是两个不同的键。另外，`undefined`和`null`也是两个不同的键。虽然`NaN`不严格相等于自身，但 Map 将其视为同一个键

```js
const m = new Map()
```

```js
// 可以接受一个数组或具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构作为参数
const set = new Set([
    ['name', 'rekaime'],
    ['age', 18]
])
const map = new Map(set)
const m2  = new Map(map)
```



#### Map.prototype

Attributes：

+ `Map.prototype.size`： Map 结构的成员总数

Methods：

+ `Map.prototype.set(key, value)`：设置键名`key`对应的键值为`value`，然后返回整个 Map 结构，如果`key`已经有值，则键值会被更新，否则就新生成该键
+ `Map.prototype.get(key)`：读取`key`对应的键值，如果找不到`key`，返回`undefined`
+ `Map.prototype.has(key)`：返回一个布尔值，表示某个键是否在当前 Map 对象之中
+ `Map.prototype.delete(key)`：删除某个键，返回`true`；如果删除失败，返回`false`
+ `Map.prototype.clear()`：清除所有成员，没有返回值
+ `Map.prototype.keys()`：返回键名的遍历器
+ `Map.prototype.values()`：返回键值的遍历器
+ `Map.prototype.entries()`：返回所有成员的遍历器（`map[Symbol.iterator] === map.entries`）
+ `Map.prototype.forEach()`：遍历 Map 的所有成员

注：遍历顺序为插入顺序



扩展

```js
const map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
])

[...map.keys()] // [1, 2, 3]

[...map.values()] // ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```



#### WeakMap

`WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合，区别在于：

+ `WeakMap`只接受对象（`null`除外）和 Symbol 值作为键名，不接受其他类型的值作为键名
+ `WeakMap`的键名所指向的对象，不计入垃圾回收机制

`WeakMap`的专用场合就是，它的键所对应的对象，可能会在将来消失，`WeakMap`结构有助于防止内存泄漏

```js
// WeakMap 弱引用的只是键名，而不是键值
const wm = new WeakMap()
let key = {}
let obj = {foo: 1}

wm.set(key, obj)
obj = null
wm.get(key) // Object {foo: 1}
```

WeakMap 与 Map 在 API 上的区别主要是两个，**一是没有遍历操作**（即没有`keys()`、`values()`和`entries()`方法），也没有`size`属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。**二是无法清空**，即不支持`clear`方法。因此，`WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`

典型用法就是以`DOM`节点作为键值存储信息，一旦`DOM`节点被删除，对应的值就会一起消失



### WeakRef

用于直接创建对象的弱引用

WeakRef 实例对象有一个`deref()`方法，如果原始对象存在，该方法返回原始对象；如果原始对象已经被垃圾回收机制清除，该方法返回`undefined`

```js
let target = {}
// 创建 target 的弱引用，垃圾回收机制不会计入这个弱引用
let wr = new WeakRef(target)

let obj = wr.deref()
if (obj) { // target 未被垃圾回收机制清除
    // ...
}
```

弱引用对象的一大用处，就是作为缓存，未被清除时可以从缓存取值，一旦清除缓存就自动失效

```js
function makeWeakCached(getByKey) {
    const cache = new Map()
    return key => {
        const ref = cache.get(key)
        if (ref) {
            const cached = ref.deref()
            if (cached !== undefined) return cached
        }

        const fresh = getByKey(key)
        cache.set(key, new WeakRef(fresh))
        return fresh
    }
}

const getImageCached = makeWeakCached(getImage)
```



### FinalizationRegistry

[ES2021](https://github.com/tc39/proposal-weakrefs#finalizers) 引入了清理器注册表功能 FinalizationRegistry，用来指定目标对象被垃圾回收机制清除以后，所要执行的回调函数

```js
const registry = new FinalizationRegistry(heldValue => {
    // ....
})

// 注册表实例的register()方法 用来注册所要观察的目标对象
// argument 作为 heldValue 传入回调函数 可以是任意类型的值 包括 undefined
// 如果未来需要注销则需要传入第三个参数作为标记 否则无法取消回调函数
registry.register(obj, "argument", obj)

// 注销 传入标记
registry.unregister(obj)
```

对缓存函数的优化

```js
function makeWeakCached(getByKey) {
    const cache = new Map()
    const cleanup = new FinalizationRegistry(key => {
        const ref = cache.get(key)
        if (ref && !ref.deref()) cache.delete(key)
    })
    
    return key => {
        const ref = cache.get(key)
        if (ref) {
            const cached = ref.deref()
            if (cached !== undefined) return cached
        }

        const fresh = getByKey(key)
        cache.set(key, new WeakRef(fresh))
        cleanup.register(fresh, key)
        return fresh
    }
}

const getImageCached = makeWeakCached(getImage)
```



## Iterator

### 概述

遍历器（Iterator）是一种接口，为各种不同的数据结构提供统一的访问机制：

+ 为各种数据结构，提供一个统一的、简便的访问接口
+ 使得数据结构的成员能够按某种次序排列
+ 为 ES6 新增命令`for...of`服务

本质上是一个指针对象，第一次调用`.next()`可以获取第一个成员，每次调用`.next()`可以不断获取后续成员直到数据结构的结束位置

每次调用`.next()`都会获取包含`value`和`done`两个属性的对象，代表当前值和是否遍历结束

```js
let it = makeIterator(['a', 'b'])

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
    let nextIndex = 0
    return {
        next() {
        return nextIndex < array.length ?
            { value: array[nextIndex++], done: false } :
            { value: undefined, done: true }
        }
    }
}
```

对于遍历器对象来说，`done: false`和`value: undefined`属性都是可以省略的，因此上面的`makeIterator`函数可以简写成下面的形式

```js
function makeIterator(array) {
    let nextIndex = 0
    return {
        next() {
        return nextIndex < array.length ?
            { value: array[nextIndex++] } :
            { done: true }
        }
    }
}
```

如果使用 TypeScript 的写法，遍历器接口（Iterable）、指针对象（Iterator）和`next`方法返回值的规格可以描述如下

```js
interface Iterable {
    [Symbol.iterator]() : Iterator,
}

interface Iterator {
    next(value?: any): IterationResult,
}

interface IterationResult {
    value: any,
    done: boolean,
}
```



### 默认 Iterator 接口

一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是**“可遍历的”**

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性

该属性本身是一个无参函数，执行结果返回遍历器

```js
const obj = {
    [Symbol.iterator]() {
        let index = 0
        return {
            next() {
                return { value: index, done: index++ < 10 ? false : true }
            }
        }
    }
}

for (let idx of obj) log(idx)
// 0
// 1
// ...
// 9
```

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象



### Iterator 的自动获取

有一些场合会默认调用 Iterator 接口（即`Symbol.iterator`方法）：

+ 解构赋值
+ 扩展运算符
+ `yield*`
+ 任何接受数组作为参数的场合，都调用了遍历器接口
  + `for...of`
  + `Array.from()`
  + `Map()`、`Set()`、`WeakMap()`、`WeakSet()`
  + `Promise.all()`、`Promise.race()`



### 其他方法

Iterator 可以具有`return()`方法和`throw()`方法

#### return( )

如果`for...of`循环提前退出（通常是因为出错，或者有`break`语句），就会调用`return()`方法

如果一个对象在完成遍历前，需要清理或释放资源，可以部署`return()`方法

注意，`return()`方法必须返回一个对象，这是 Generator 语法决定的

```js
function readLinesSync(file) {
    return {
        [Symbol.iterator]() {
            return {
                next() {
                    return { done: false }
                },
                return() {
                    file.close()
                    return { done: true }
                }
            }
        },
    }
}
```



#### throw( )

主要配合 Generator 函数使用，一般的遍历器对象用不到这个方法



## Generator

### 概述

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同

语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数，返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态

形式上，Generator 函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`表达式，定义不同的内部状态

Generator 函数调用后，该函数并不执行，返回的也不是函数运行结果，而是一个遍历器对象，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行

```js
function* helloWorldGenerator() {
    yield 'hello'
    yield 'world'
    return 'ending'
}

hw.next() // { value: 'hello', done: false }
hw.next() // { value: 'world', done: false }
hw.next() // { value: 'ending', done: true }
hw.next() // { value: undefined, done: true }
```



### yield

Generator 函数返回的 Iterator 的`.next()`运行逻辑如下：

1. 遇到`yield`表达式，暂停执行，并将紧跟在`yield`后面的表达式的值作为返回的对象的`value`
2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式
3. 如果遇到`return`，就将`return`后的表达式作为`value`返回，同时返回结果的`done`为`true`
4. 此后 Generator 状态是静止的，后续再调用`next`方法，返回结果都将是`{ value: undefined, done: true }`

`yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行

Generator 不使用`yield`时可以作为暂缓执行函数使用

`yield`表达式如果用在另一个表达式之中，必须放在圆括号里面（注：`yield`本身没有返回值，或者说返回值为一个`undefined`）

```js
function* demo() {
    console.log('Hello' + yield) // SyntaxError
    console.log('Hello' + yield 123) // SyntaxError

    console.log('Hello' + (yield)) // OK
    console.log('Hello' + (yield 123)) // OK
}

// yield表达式用作函数参数或放在赋值表达式的右边，可以不加括号
function* demo() {
    foo(yield 'a', yield 'b') // OK
    let input = yield // OK
}
```



### next( ) 的参数

`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值

由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的

```js
function* f() {
    for(var i = 0; true; i++) {
        const reset = yield i
        if (reset) i = -1
    }
}

const g = f()
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```



### Generator.prototype

#### throw( )

Generator 函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获

以下代码中，第一个错误被 Generator 函数体内的catch语句捕获

第二次抛出错误，由于 Generator 函数内部的catch语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就被抛出了 Generator 函数体，被函数体外的catch语句捕获

```js
function* g() {
    try {
        yield 'Start'
    } catch (e) {
        console.log('内部捕获', e)
    }
}

const it = g()
it.next()

try {
    it.throw('a')
    it.throw('b')
} catch (e) {
    console.log('外部捕获', e)
}
// 内部捕获 a
// 外部捕获 b
```

`throw`方法可以接受一个参数，该参数会被`catch`语句接收，建议抛出`Error`对象的实例

如果 Generator 函数内部没有部署`try...catch`代码块，那么`throw`方法抛出的错误，将被外部`try...catch`代码块捕获

如果 Generator 函数内部和外部，都没有部署`try...catch`代码块，那么程序将报错，直接中断执行

`throw`方法抛出的错误要被内部捕获，前提是必须至少执行过一次`next`方法，因为要让 Generator 函数启动，并且此时需要`yield`控制函数运行到`try...catch`片段

`throw`方法被内部捕获以后，会附带执行到下一条`yield`表达式，这种情况下等同于执行一次`next`方法

```js
function* g() {
    try {
        yield 1
        yield 2
    } catch (e) {
        console.log('内部捕获', e)
        yield 3
        yield 4
    }
    yield 5
}

const it = g()
it.next()  // {value: 1, done: false}
it.throw() // {value: 3, done: false}
it.next()  // {value: 4, done: false}
it.next()  // {value: 5, done: false}
```

一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了。如果此后还调用`next`方法，将返回一个`value`属性等于`undefined`、`done`属性等于`true`的对象，即 JavaScript 引擎认为这个 Generator 已经运行结束了



#### return( )

Generator 函数返回的遍历器对象还有一个`return()`方法，可以返回给定的值，并且结束遍历 Generator

```js
function* gen() {
  yield 1
  yield 2
  yield 3
}

const g = gen()

g.next()        // { value: 1, done: false }
// 如果调用时不提供参数 则返回值的 value 为 undefined
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```

如果 Generator 函数内部有`try...finally`代码块，且正在执行`try`代码块，那么`return()`方法会导致立刻进入`finally`代码块，执行完以后，整个函数才会结束

```js
function* numbers() {
    try {
        yield 1
    } catch (e) {
        yield 2
    } finally {
        yield 3
        yield 4
    }
    yield 5
}
const g = numbers()
g.next()    // { value: 1, done: false }
g.throw()   // {value: 2, done: false}
g.return(7) // { value: 3, done: false }
g.next()    // { value: 4, done: false }
g.next()    // { value: 7, done: true }
```



### next( ) & throw( ) & return( )

`next()`、`throw()`、`return()`这三个方法本质上是同一件事，可以放在一起理解。它们的作用都是让 Generator 函数恢复执行，并且使用不同的语句替换`yield`表达式

`next()`是将`yield`表达式替换成一个值

`throw()`是将`yield`表达式替换成一个`throw`语句

`return()`是将`yield`表达式替换成一个`return`语句



### yield*

如果在 Generator 函数内部，调用另一个 Generator 函数，就需要手动遍历来记录状态

`yield*`可以将后面的 Iterator 或具有 Iterator 接口的对象展开，成为自身函数的各个状态

如果被代理的 Generator 函数有`return`语句，那么就可以向代理它的 Generator 函数返回数据

```js
function* foo() {
    yield 2
    yield 3
    return "foo"
}

function* bar() {
    yield 1
    const v = yield* foo()
    console.log("v: " + v)
    yield 4
}

const it = bar()
it.next() // {value: 1, done: false}
it.next() // {value: 2, done: false}
it.next() // {value: 3, done: false}
it.next()
// "v: foo"
// {value: 4, done: false}
it.next() // {value: undefined, done: true}
```



### 作为对象属性

```js
// 简写形式
const obj = {
    * gen() { }
}
```



### About this

Generator 函数总是返回一个 Iterator，ES6 规定这个 Iterator 是 Generator 函数的实例，也继承了 Generator 函数的`prototype`对象上的方法

```js
function* g() {}
g.prototype.hello = function () {
    return 'hi!'
}

const obj = g()
obj instanceof g // true
obj.hello() // 'hi!
```

现版本，Generator 内`this`默认为`undefined`

为实现在 Generator 中获取到`this`，可以通过`call()`方法为 Generator 绑定`this`

```js
function* gen() {
    this.a = 1
    yield this.b = 2
    yield this.c = 3
}

function F() {
    return gen.call(gen.prototype)
}

const f = new F()

f.next()  // Object {value: 2, done: false}
f.next()  // Object {value: 3, done: false}
f.next()  // Object {value: undefined, done: true}

f.a // 1
f.b // 2
f.c // 3
```



### 含义

#### 状态机

Generator 是实现状态机的最佳结构

在 ES5 中需要这样定义：

```js
var ticking = true
var clock = function() {
    if (ticking) console.log('Tick!')
    else console.log('Tock!')
    ticking = !ticking
}
```

通过 Generator 实现，代码更简洁，更安全（状态不会被非法篡改）、更符合函数式编程的思想，在写法上也更优雅：

```js
const clock = function* () {
    while (true) {
        console.log('Tick!')
        yield
        console.log('Tock!')
        yield
    }
}
```



#### 协程

协程（coroutine）是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”。协程既可以用单线程实现，也可以用多线程实现。前者是一种特殊的子例程，后者是一种特殊的线程。

**（1）协程与子例程的差异**

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

从实现上看，在内存中，子例程只使用一个栈（stack），而协程是同时存在多个栈，但只有一个栈是在运行状态，也就是说，协程是以多占用内存为代价，实现多任务的并行。

**（2）协程与普通线程的差异**

不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

由于 JavaScript 是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。

Generator 函数是 ES6 对协程的实现，但属于不完全实现。Generator 函数被称为“半协程”（semi-coroutine），意思是只有 Generator 函数的调用者，才能将程序的执行权还给 Generator 函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用`yield`表达式交换控制权。



#### 上下文

JavaScript 代码运行时，会产生一个全局的上下文环境（context，又称运行环境），包含了当前所有的变量和对象。然后，执行函数（或块级代码）的时候，又会在当前上下文环境的上层，产生一个函数运行的上下文，变成当前（active）的上下文，由此形成一个上下文环境的堆栈（context stack）。

这个堆栈是“后进先出”的数据结构，最后产生的上下文环境首先执行完成，退出堆栈，然后再执行完成它下层的上下文，直至所有代码执行完成，堆栈清空。

Generator 函数不是这样，它执行产生的上下文环境，一旦遇到`yield`命令，就会暂时退出堆栈，但是并不消失，里面的所有变量和对象会冻结在当前状态。等到对它执行`next`命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。



#### 异步

```js
function* handle() {
    const result = yield request("http://some.url")
    log(JSON.parse(result))
}

function request(url) {
    makeAjaxCall(url, function(response){
        it.next(response)
    })
}

const it = handle()
it.next()
```



#### 控制流管理

如果有一个多步操作非常耗时

采用回调函数：

```js
step1(function (value1) {
    step2(value1, function(value2) {
        step3(value2, function(value3) {
            step4(value3, function(value4) {
                // Do something with value4
            })
        })
    })
})
```

采用 Promise：

```js
Promise.resolve(step1)
    .then(step2)
    .then(step3)
    .then(step4)
    .then(function (value4) {
        // Do something with value4
    }, function (error) {
        // Handle any error from step1 through step4
    })
    .done()
```

Generator 函数可以进一步改善代码运行流程：

```js
// 只适合同步 Task 的管理
function* longRunningTask(value1) {
    try {
        const value2 = yield step1(value1)
        const value3 = yield step2(value2)
        const value4 = yield step3(value3)
        const value5 = yield step4(value4)
        // Do something with value4
    } catch (e) {
        // Handle any error from step1 through step4
    }
}
```



### Thunkify

#### Thunk 函数的意义

编译器函数实现分为传值调用和传名调用，传值调用是在参数传入时就进行计算求值，而传名调用往往是将参数包装到临时函数中传入函数体，在参数使用时调用临时函数获得参数本身

```js
function f(m) {
    return m * 2
}

f(x + 5)

// 等同于
function thunk () {
    return x + 5
}

function f(thunk) {
    return thunk() * 2
}
```

在 JS 中函数是传值调用，而 Thunk 函数具有不同的意义，它替换的是多参数函数的参数列表

只要函数参数中存在回调函数，就可以把对应的函数拆解为 Thunk 函数

```js
// ES5版本
var Thunk = function(fn){
    return function (){
        var args = Array.prototype.slice.call(arguments)
        return function (callback){
            args.push(callback)
            return fn.apply(this, args)
        }
    }
}
// ES6版本
const Thunk = function (fn) {
    return function (...args) {
        return function (callback) {
            return fn.call(this, ...args, callback)
        }
    }
}
```

具体使用时，例如`fs.readFile`：

```js
const readFileThunk = Thunk(fs.readFile)
readFileThunk(filename)(callback)
```



#### Thunkify 模块

生产环境下可以借助 Thunkify 模块

```shell
npm install thunkify
```

```js
const thunkify = require('thunkify')
const fs = require('fs')

const read = thunkify(fs.readFile)
read('package.json')(function(err, str){
    // ...
})
```

Thunkify 的源码主要多了一个检查机制，变量`called`确保回调函数只运行一次

```js
function thunkify(fn) {
    return function () {
        var args = new Array(arguments.length);
        var ctx = this;

        for (var i = 0; i < args.length; ++i) {
            args[i] = arguments[i];
        }

        return function (done) {
            var called;

            args.push(function () {
                if (called) return;
                called = true;
                done.apply(null, arguments);
            });

            try {
                fn.apply(ctx, args);
            } catch (err) {
                done(err);
            }
        }
    }
};
```

变量`called`的设计与下文的 Generator 函数相关



#### Generator 函数的流程管理

首先 Generator 可以自动执行直到结束

```js
function* gen() {
    // ...
}

const g = gen()
const res = g.next()

while(!res.done){
    console.log(res.value)
    res = g.next()
}
```

但是缺点在于不适用于异步操作，因为必须保证前一步产生执行结果才能继续推进

借助 Thunk 我们可以将`yield`表达式包装起来，需要处理时再传入回调函数，就做到了在`yield`时移除 Generator 的执行权，而调用 Thunk 时传入回调并将执行权归还给 Generator

```js
const fs = require('fs')
const thunkify = require('thunkify')
const readFileThunk = thunkify(fs.readFile)

// 定义流函数
function* gen (){
    const r1 = yield readFileThunk('/etc/fstab')
    console.log(r1.toString())
    const r2 = yield readFileThunk('/etc/shells')
    console.log(r2.toString())
}

// 执行
const g = gen()
// 获取第一次的执行结果控制权
const r1 = g.next()
// 传入回调 执行权归还给 gen
r1.value(function (err, data) {
    if (err) throw err
    // 获取第二次的执行结果控制权
    const r2 = g.next(data)
    // 传入回调 执行权归还给 gen
    r2.value(function (err, data) {
        if (err) throw err
        g.next(data)
    })
})
```

执行过程其实是反复地在`next`方法调用后，向`value`传入回调函数

因此可以递归实现自动流管理：

```js
function* gen() {
    // ...
}

function run(gn) {
    const gen = gn()
    
    function next(err, data) {
        const result = gen.next(data)
        if (result.done) return
        result.value(next)
    }
    
    next()
}

run(gen)
```



#### co 模块

使用方式：

```js
const co = require('co')

// co 函数返回一个 Promise 对象，因此可以用 then 方法添加回调函数
co(function* () {
    const f1 = yield readFile('/etc/fstab')
    const f2 = yield readFile('/etc/shells')
    console.log(f1.toString())
    console.log(f2.toString())
}).then(function () {
    // ...
})
```



首先理解一下如何用 Promise 实现自动流管理：

```js
const fs = require('fs')

function readFile(fileName) {
    return new Promise(function (resolve, reject) {
        fs.readFile(fileName, function (error, data) {
            if (error) return reject(error)
            resolve(data)
        })
    })
}

function* gen() {
    const f1 = yield readFile('/etc/fstab')
    const f2 = yield readFile('/etc/shells')
    console.log(f1.toString())
    console.log(f2.toString())
}

function run(gen){
    const g = gen()
    function next(data){
        const result = g.next(data)
        if (result.done) return result.value
        result.value.then(function(data){
            next(data)
        })
    }
    next()
}
run(gen)
```

co 模块的实现方式就是 Promise 原理下的自动执行器的扩展

```js
function co(gen) {
    var ctx = this;

    return new Promise(function (resolve, reject) {
        // 检查函数是否为 Generator
        if (typeof gen === 'function') gen = gen.call(ctx);
        if (!gen || typeof gen.next !== 'function') return resolve(gen);

        onFulfilled();
        // 将 Generator 的 next 方法包装为 onFulfilled
        // 目的在于便于错误抛出
        function onFulfilled(res) {
            var ret;
            try {
                ret = gen.next(res);
            } catch (e) {
                return reject(e);
            }
            next(ret);
        }
        function next(ret) {
            if (ret.done) return resolve(ret.value);
            var value = toPromise.call(ctx, ret.value);
            if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
            return onRejected(
                new TypeError(
                    'You may only yield a function, promise, generator, array, or object, '
                    + 'but the following object was passed: "'
                    + String(ret.value)
                    + '"'
                )
            );
        }
    });
}
```





## Promise

### 概述

特点：

+ 对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变
+ 一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的



### 定义

```js
const promise = new Promise((resolve, reject) => {
    // ...
    if (success) return resolve(value)
    else return reject(error)
})

promise.then(value => {
    // success
}, error => {
    // error
})
// 更建议这么做捕获
promise.then(value => {
    // success
}).catch(error => {
    // error
})
```



### Promise.prototype

#### then

`then`方法的第一个参数是`resolved`状态的回调函数，第二个参数是`rejected`状态的回调函数，它们都是可选的

`then`方法返回的是一个新的`Promise`实例，不是原来那个`Promise`实例

```js
promise.then(data => {
    // ...
    return message
}).then(message => {
    // ...
})
```



####  catch

`Promise.prototype.catch()`方法是`.then(null, rejection)`或`.then(undefined, rejection)`的别名，用于指定发生错误时的回调函数

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止，也就是说，错误总是会被下一个`catch`语句捕获

跟传统的`try/catch`代码块不同的是，如果没有使用`catch()`方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码



#### finally

`finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作

`finally`方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是`fulfilled`还是`rejected`，这表明，`finally`方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果

`finally`本质上是`then`方法的特例：

```js
// finally 的实现
// 可以发现它总是返回原来的值
Promise.prototype.finally = function (callback) {
    let P = this.constructor
    return this.then(
        value  => P.resolve(
            callback()
        ).then(
            () => value
        ),
        reason => P.resolve(
            callback()
        ).then(
            () => { throw reason }
        )
    )
}
```



### Promise.all( )

将多个`Promise`实例，包装成一个新的`Promise`实例

所有`Promise`实例都变成`fulfilled`，状态才变成`fulfilled`

有一个`Promise`实例`rejected`状态就变为`rejected`

```js
Promise.all([
    Promise.resolve(1),
    Promise.resolve(4),
    Promise.resolve(5),
    Promise.resolve(2),
    Promise.resolve(3)
])
// dataList 为所有实例返回值的数组
.then(dataList => {
    log(dataList) // [1, 4, 5, 2, 3]
})
// reason 为第一个 rejected 实例的返回值
// 注 如果列表中的实例自带 catch 则不会在发生错误时触发 Promise.all() 的 catch
.catch(reason => {
    log(reason)
})
```



### Promise.race( )

将多个`Promise`实例，包装成一个新的`Promise`实例

只要有一个实例改变状态，就跟着改变状态，且返回值和该实例保持一致



### Promise.allSettled( )

将多个`Promise`实例，包装成一个新的`Promise`实例

等待所有的实例异步操作结束，才将状态转为`filfilled`（只会变成`filfulled`）

返回值为一个数组，每个成员对应先前传入的`Promise`实例

```js
const allSettledPromise = Promise.allSettled([
    Promise.resolve(42), Promise.reject(-1)
])
allSettledPromise.then(results => log(results))

// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```



### Promise.any( )

将多个`Promise`实例，包装成一个新的`Promise`实例

只要有一个实例变为`fulfulled`则状态转变为`fulfilled`，返回值为该实例的返回值

如果所有实例变为`rejected`则状态转变为`rejected`

```js
const p = Promise.any([
    Promise.reject(-1), Promise.reject(-1)
])
p.then(results => log(results)).catch(error => log(error))

// AggregateError: All promises were rejected
```



### Promise.resolve( )

将对象转为`Promise`对象

参数分为四种情况：

+ `Promise`实例：直接返回该实例
+ 具有`.then(resolve, reject)`方法的对象：返回`new Promise(obj.then)`
+ 不具有`.then` API 或不是对象：返回一个`fulfilled`状态的`Promise`对象，返回值为参数本身
+ 无参数：返回一个`fulfilled`状态的`Promise`对象

需要注意的是，立即`resolve()`的 Promise 对象，是在本轮“事件循环”（event loop）的结束时执行，而不是在下一轮“事件循环”的开始时

```js
setTimeout(() => { log('three') }, 0)
Promise.resolve().then(() => { log('two') }
log('one')
// one
// two
// three
```



### Promise.reject( )

`Promise.reject(reason)`方法返回一个新的 Promise 实例，该实例的状态为`rejected`

参数是一个字符串，后面`catch()`方法的参数`e`就是这个字符串



## Proxy

### 概述

+ target：操作的目标对象
+ propKey：键名
+ receiver：一般指`Proxy`对象



### Proxy

#### get

`get(target, property, receiver)`方法用于拦截某个属性的读取操作

返回结果为读取内容

如果一个属性不可配置（configurable）且不可写（writable），则 Proxy 不能修改该属性，否则通过 Proxy 对象访问该属性会报错

```js
const target = Object.defineProperties({}, {
    foo: {
        value: 123,
        writable: false,
        configurable: false
    }
})
const proxy = new Proxy(target, {
  get(target, propKey) { return 'abc' }
})

proxy.foo // TypeError: Invariant check failed
```



``` js
// 生成 dom 节点通用函数
const dom = new Proxy({}, {
    get(target, property, receiver) {
        return function(attrs = {}, ...children) {
            const el = document.createElement(property)
            for (let prop of Object.keys(attrs)) el.setAttribute(prop, attrs[prop])
            for (let child of children) {
                if (typeof child === 'string') child = document.createTextNode(child)
                el.appendChild(child)
            }
            return el
        }
    }
})

const el = dom.div({},
    'Hello, my name is ',
    dom.a({href: '//example.com'}, 'Mark'),
    '. I like:',
    dom.ul({},
        dom.li({}, 'The web'),
        dom.li({}, 'Food'),
        dom.li({}, '…actually that\'s it')
    )
)

document.body.appendChild(el)
```



#### set

`set(target, propKey, value, receiver)`方法用来拦截某个属性的赋值操作

`set`代理应当返回一个布尔值。严格模式下，`set`代理如果没有返回`true`，就会报错

注意，如果目标对象自身的某个属性不可写，那么`set`方法将不起作用

```js
// 实现内部属性避免被读写
const handler = {
    get(target, key) {
        invariant(key, 'get')
        return target[key]
    },
    set(target, key, value) {
        invariant(key, 'set')
        target[key] = value
        return true
    }
}
function invariant(key, action) {
    if (key[0] === '_') {
        throw new Error(`Invalid attempt to ${action} private "${key}" property`)
    }
}
const proxy = new Proxy({}, handler)
proxy._prop
// Error: Invalid attempt to get private "_prop" property
proxy._prop = 'c'
// Error: Invalid attempt to set private "_prop" property
```



#### apply

`apply(target, object, args)`方法拦截函数的调用、`call`和`apply`操作

返回值为函数调用结果

`object`参数指向目标对象的上下文对象`this`、`args`为目标对象的参数数组



#### has

`has(target, propKey)`方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时，这个方法会生效，典型的操作就是`in`运算符，需要注意，`has()`不对`for...in`生效

返回值为布尔值，如果原对象不可配置或者禁止扩展，这时`has()`拦截会报错

可以利用这点隐藏某些属性，值得注意的是，`has()`方法拦截的是`HasProperty`操作，而不是`HasOwnProperty`操作，即`has()`方法不判断一个属性是对象自身的属性，还是继承的属性

```js
var handler = {
    has(target, key) {
        if (key[0] === '_') return false
        return key in target
    }
}
var target = { _prop: 'foo', prop: 'foo' }
var proxy = new Proxy(target, handler)
'_prop' in proxy // false
```



#### construct

`construct(target, args, newTarget)`方法用于拦截`new`命令

返回值为对象实例

注意，`construct()`方法中的`this`指向的是`handler`，而不是实例对象

```js
const proxy = new Proxy(function () {}, {
    construct(target, args, newTarget) {
        log(args) // "[1, 2, 3]"
        log(newTarget === proxy) // true
        return []
    }
})

const o = new proxy(1, 2, 3)
```



#### deleteProperty

`deleteProperty(target, propKey)`方法用于拦截`delete`操作，如果这个方法抛出错误或者返回`false`，当前属性就无法被`delete`命令删除

返回值为布尔值

目标对象自身的不可配置（configurable）的属性，不能被`deleteProperty`方法删除，否则报错

```js
function invariant(key, action) {
    if (key[0] === '_') {
        throw new Error(`Invalid attempt to ${action} private "${key}" property`)
    }
}

var target = { _prop: 'foo' }
var proxy = new Proxy(target, {
    deleteProperty(target, propKey) {
        invariant(key, 'delete')
        delete target[key]
        return true
    }
})
delete proxy._prop
// Error: Invalid attempt to delete private "_prop" property
```



#### defineProperty

`defineProperty(target, propKey, propDesc)`方法拦截了`Object.defineProperty()`操作

如果目标对象不可扩展（non-extensible），则`defineProperty()`不能增加目标对象上不存在的属性，否则会报错

如果目标对象的某个属性不可写（writable）或不可配置（configurable），则`defineProperty()`方法不得改变这两个设置

```js
var handler = {
    defineProperty(target, key, descriptor) {
        // descriptor: { value: 'bar', writable: true, enumerable: true, configurable: true }
        Object.defineProperty(target, key, descriptor)
        return true
    }
}

var proxy = new Proxy({}, handler)
proxy.foo = 'bar' // 如果返回值为 false 则不会生效
```



#### getOwnPropertyDescriptor

`getOwnPropertyDescriptor(target, propKey)`方法拦截`Object.getOwnPropertyDescriptor()`

返回一个属性描述对象或者`undefined`

```js
var handler = {
    getOwnPropertyDescriptor(target, key) {
        if (key[0] === '_') return
        return Object.getOwnPropertyDescriptor(target, key)
    }
}
var target = { _foo: 'bar', baz: 'tar' }
var proxy = new Proxy(target, handler)
Object.getOwnPropertyDescriptor(proxy, 'wat')
// undefined
Object.getOwnPropertyDescriptor(proxy, '_foo')
// undefined
Object.getOwnPropertyDescriptor(proxy, 'baz')
// { value: 'tar', writable: true, enumerable: true, configurable: true }
```



#### getPrototypeOf

`getPrototypeOf(target)`方法主要用来拦截获取对象原型：

- `Object.prototype.__proto__`
- `Object.prototype.isPrototypeOf()`
- `Object.getPrototypeOf()`
- `Reflect.getPrototypeOf()`
- `instanceof`

返回值为对象或`null`，如果目标对象不可扩展（non-extensible）， `getPrototypeOf()`方法必须返回目标对象的原型对象

```js
var proto = {}
var p = new Proxy({}, {
    getPrototypeOf(target) {
        return proto
    }
})
Object.getPrototypeOf(p) === proto // true
```



#### isExtensible

`isExtensible(target)`方法拦截`Object.isExtensible()`操作

该方法必须返回一个布尔值，否则会被自动转为布尔值

这个方法有一个强限制，它的返回值必须与目标对象的`isExtensible`属性保持一致，否则就会抛出错误

```js
var p = new Proxy({}, {
    isExtensible: function (target) {
        console.log("called")
        return true
    }
})

Object.isExtensible(p)
// "called"
// true
```



#### ownKeys

`ownKeys(target)`方法用来拦截对象自身属性的读取操作：

- `Object.getOwnPropertyNames()`
- `Object.getOwnPropertySymbols()`
- `Object.keys()`
- `for...in`循环

返回值为数组对象，成员只能为字符串或者`Symbol`值，如果目标对象自身包含不可配置的属性，则该属性必须被`ownKeys()`方法返回，否则报错

如果通过`Object.keys()`方法获取`key`，有三类属性会被`ownKeys()`方法自动过滤，不会返回，`for...in`循环实际上也是通过`Object.keys()`获取`key`

- 目标对象上不存在的属性
- 属性名为 Symbol 值
- 不可遍历（`enumerable`）的属性

```js
const obj = {
    hello: 'world',
    _a: 1,
    _b: 2,
    [Symbol.for('s')]: 'symbol'
}
Object.defineProperty(obj, 'key', {
    enumerable: false,
    configurable: true,
    writable: true,
    value: 'static'
})
const p = new Proxy(obj, {
    ownKeys(target) {
        return ['hello', '_a', '_b', 'c', Symbol.for('s')]
    }
})

Object.getOwnPropertyNames(p)
// ['hello', '_a', '_b', 'c']
Object.getOwnPropertySymbols(p)
// [Symbol(s)]
Object.keys(p)
// ['hello', '_a', '_b']
for (let k in p) console.log(k)
// hello
// _a
// _b
```

如果目标对象是不可扩展的（non-extensible），这时`ownKeys()`方法返回的数组之中，必须包含原对象的所有属性，且不能包含多余的属性，否则报错

```js
var obj = {
    a: 1
}

Object.preventExtensions(obj)

var p = new Proxy(obj, {
    ownKeys: function (target) {
        return ['a', 'b']
    }
})

Object.getOwnPropertyNames(p)
// Uncaught TypeError: 'ownKeys' on proxy: trap returned extra keys but proxy target is non-extensible
```



#### preventExtensions

`preventExtensions(target)`方法拦截`Object.preventExtensions()`

该方法必须返回一个布尔值，否则会被自动转为布尔值

这个方法有一个限制，只有目标对象不可扩展时（即`Object.isExtensible(proxy)`为`false`），`proxy.preventExtensions`才能返回`true`，否则会报错

```js
const proxy = new Proxy({}, {
    preventExtensions: function (target) {
        console.log('called')
        Object.preventExtensions(target)
        return true
    }
})
Object.preventExtensions(proxy)
```



#### setPrototypeOf

`setPrototypeOf()`方法主要用来拦截`Object.setPrototypeOf()`方法

该方法只能返回布尔值，否则会被自动转为布尔值

如果目标对象不可扩展（non-extensible），`setPrototypeOf()`方法不得改变目标对象的原型

```js
var handler = {
    setPrototypeOf(target, proto) {
        throw new Error('Changing the prototype is forbidden')
    }
}
var proto = {}
var target = function () { }
var proxy = new Proxy(target, handler)
Object.setPrototypeOf(proxy, proto)
// Error: Changing the prototype is forbidden
```



### Proxy.revocable

`Proxy.revocable()`方法返回一个可取消的 Proxy 实例

`Proxy.revocable()`的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问

```js
const { proxy, revoke } = Proxy.revocable({}, {})

proxy.foo = 123
proxy.foo // 123

revoke()
proxy.foo // TypeError: Revoked
```



### about `this`

Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理

解决方案是为`this`绑定原始对象

另外，Proxy 拦截函数内部的`this`，指向的是`handler`对象

```js
const target = new Date('2024-01-01')
const handler = {
    get(target, prop) {
        if (prop === 'getDate') return target.getDate.bind(target)
        return Reflect.get(target, prop)
    }
}
const proxy = new Proxy(target, handler)

proxy.getDate() // 1
```



## Reflect

### 概述

`Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API

`Reflect`对象的设计目的：

+ 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。也就是说，从`Reflect`对象上可以拿到语言内部的方法
+ 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`
+ 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为
+ `Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为



### Reflect

#### get

`Reflect.get(target, name, receiver)`方法查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`

如果`name`属性部署了读取函数（getter），则读取函数的`this`绑定`receiver`

```js
const myObject = {
    foo: 1,
    bar: 2,
    get baz() {
        return this.foo + this.bar
    },
}

const myReceiver = {
    foo: 4,
    bar: 4,
}

Reflect.get(myObject, 'baz', myReceiver) // 8
```



#### set

`Reflect.set(target, name, value, receiver)`方法设置`target`对象的`name`属性等于`value`

如果提供了`receiver`而目标`propKey`不存在`setter`，则`receiver`会取代`target`成为赋值对象

如果`name`属性设置了赋值函数，则赋值函数的`this`绑定`receiver`

```js
const myObject = {
    foo: 4,
    set bar(value) {
        return this.foo = value
    },
}

const myReceiverObject = {
    foo: 0,
}

Reflect.set(myObject, 'bar', 1, myReceiverObject)
myObject.foo // 4
myReceiverObject.foo // 1
```

如果 `Proxy`对象和 `Reflect`对象联合使用，在`Proxy`对象中拦截赋值操作，并调用`Reflect.set`完成赋值的默认行为，此时若传入了`receiver`，那么`Reflect.set`会触发`Proxy.defineProperty`拦截

因为`Proxy.set`的`receiver`参数总是指向当前的 `Proxy`实例（即上例的`proxy`），而`Reflect.set`一旦传入`receiver`，就会将属性赋值到`receiver`上面（即`proxy`），导致触发`defineProperty`拦截

```js
const obj = {
    a: 'a'
}
const proxy = new Proxy(obj, {
    set(target, key, value, receiver) {
        console.log(`set ${key} = ${value}`)
        Reflect.set(target, key, value, receiver)
        return true
    },
    defineProperty(target, key, attribute) {
        console.log(target)
        console.log(`defineProperty ${key} = ${attribute.value}`)
        Reflect.defineProperty(target, key, attribute)
    }
})

proxy.a = 'A'
// set a = A
// {a: 'a'}
// defineProperty a = A
```



#### has

`Reflect.has(target, name)`方法对应`name in obj`里面的`in`运算符

返回布尔值



#### deleteProperty

`Reflect.deleteProperty(target, name)`方法等同于`delete obj[name]`，用于删除对象的属性

返回一个布尔值：如果删除成功，或者被删除的属性不存在，返回`true`；删除失败，被删除的属性依然存在，返回`false`

如果`Reflect.deleteProperty()`方法的第一个参数不是对象，会报错



#### construct

`Reflect.construct(target, args)`方法等同于`new target(...args)`，这提供了一种不使用`new`，来调用构造函数的方法

返回对象实例，如果`target`不是函数会报错

```js
function foo() {
    return new Array()
}

Reflect.construct(foo, []) // []
Reflect.construct(Array, [1, 2, 3]) // [1, 2, 3]
```



#### getPrototypeOf

`Reflect.getPrototypeOf(target)`方法用于读取对象的`__proto__`属性，对应`Object.getPrototypeOf(obj)`

`Reflect.getPrototypeOf`和`Object.getPrototypeOf`的一个区别是，如果参数不是对象，`Object.getPrototypeOf`会将这个参数转为对象，然后再运行，而`Reflect.getPrototypeOf`会报错

```js
Object.getPrototypeOf(1) // Number {[[PrimitiveValue]]: 0}
Reflect.getPrototypeOf(1) // 报错
```



#### setPrototypeOf

`Reflect.setPrototypeOf(target, prototype)`方法用于设置目标对象的原型（prototype），对应`Object.setPrototypeOf(obj, newProto)`方法

返回一个布尔值，表示是否设置成功

如果无法设置目标对象的原型（比如，目标对象禁止扩展），`Reflect.setPrototypeOf`方法返回`false`

```js
Reflect.setPrototypeOf({}, null) // true
Reflect.setPrototypeOf(Object.freeze({}), null) // false
```

如果第一个参数不是对象，`Object.setPrototypeOf`会返回第一个参数本身，而`Reflect.setPrototypeOf`会报错

如果第一个参数是`undefined`或`null`，`Object.setPrototypeOf`和`Reflect.setPrototypeOf`都会报错

```js
Object.setPrototypeOf(1, {}) // 1
Reflect.setPrototypeOf(1, {})
// TypeError: Reflect.setPrototypeOf called on non-object

Object.setPrototypeOf(null, {})
// TypeError: Object.setPrototypeOf called on null or undefined
Reflect.setPrototypeOf(null, {})
// TypeError: Reflect.setPrototypeOf called on non-object
```



#### apply

`Reflect.apply(target, thisArg, args)`方法等同于`Function.prototype.apply.call(func, thisArg, args)`，用于绑定`this`对象后执行给定函数

一般来说，如果要绑定一个函数的`this`对象，可以这样写`fn.apply(obj, args)`，但是如果函数定义了自己的`apply`方法，就只能写成`Function.prototype.apply.call(fn, obj, args)`，采用`Reflect`对象可以简化这种操作

返回值为执行结果



#### defineProperty

`Reflect.defineProperty(target, name, desc)`方法基本等同于`Object.defineProperty`，用来为对象定义属性。未来，后者会被逐渐废除，请从现在开始就使用`Reflect.defineProperty`代替它

如果`Reflect.defineProperty`的第一个参数不是对象，就会抛出错误，比如`Reflect.defineProperty(1, 'foo')`

返回值为布尔值，表示是否成功

可以配合`Proxy`食用效果更加

```js
const p = new Proxy({}, {
    defineProperty(target, prop, descriptor) {
        console.log(descriptor)
        return Reflect.defineProperty(target, prop, descriptor)
    }
})

p.foo = 'bar'
// {value: "bar", writable: true, enumerable: true, configurable: true}
```



#### getOwnPropertyDescriptor

`Reflect.getOwnPropertyDescriptor(target, name)`基本等同于`Object.getOwnPropertyDescriptor`，用于得到指定属性的描述对象，将来会替代掉后者

`Reflect.getOwnPropertyDescriptor`和`Object.getOwnPropertyDescriptor`的一个区别是，如果第一个参数不是对象，`Object.getOwnPropertyDescriptor(1, 'foo')`不报错，返回`undefined`，而`Reflect.getOwnPropertyDescriptor(1, 'foo')`会抛出错误，表示参数非法



#### isExtensible

`Reflect.isExtensible(target)`方法对应`Object.isExtensible`

返回一个布尔值，表示当前对象是否可扩展

如果参数不是对象，`Object.isExtensible`会返回`false`，因为非对象本来就是不可扩展的，而`Reflect.isExtensible`会报错



#### preventExtensions

`Reflect.preventExtensions(target)`对应`Object.preventExtensions`方法，用于让一个对象变为不可扩展

返回一个布尔值，表示是否操作成功

如果参数不是对象，`Object.preventExtensions`在 ES5 环境报错，在 ES6 环境返回传入的参数，而`Reflect.preventExtensions`会报错



#### ownKeys

`Reflect.ownKeys(target)`方法用于返回对象的所有属性，基本等同于`Object.getOwnPropertyNames`与`Object.getOwnPropertySymbols`之和

如果`Reflect.ownKeys()`方法的第一个参数不是对象，会报错



## Class

### 概述

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板

ES6 的`class`可以看作只是一个语法糖，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已

构造函数的`prototype`属性，在 ES6 的“类”上面继续存在，事实上，类的所有方法都定义在类的`prototype`属性上面

和 ES5 所不同的是，类内部定义的方法不可枚举（non-enumerable）

```js
class Point {
    constructor() {
        // ...
    }
    toString() {
        // ...
    }
    toValue() {
        // ...
    }
}

// 等同于
Point.prototype = {
    constructor() {},
    toString() {},
    toValue() {},
}

// 类的内部所有定义的方法不可枚举的
Object.keys(Point.prototype)
// []
Object.getOwnPropertyNames(Point.prototype)
// ["constructor", "toString", "toValue()"]
```

注意：

+ 类和模块内部默认为严格模式，不需要`use strict`
+ 和 ES5 不同，类不存在变量提升
+ class 的`name`属性和 ES5 相同



### constructor( )

类必须有`constructor()`方法，如果没有显式定义，一个空的`constructor()`方法会被默认添加

通过`new`命令生成对象实例时，自动调用该方法

`constructor()`方法默认返回实例对象（即`this`），完全可以指定返回另外一个对象

```js
class Student {
    constructor(name, age) {
        this.name = name
        this.age = age

        // return this
    }
}

new Student('rekaime', 18)
// {name: 'rekaime', age: 18}
```

类的方法除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）

```js
class Student {
    constructor(name, age) {
        this.name = name
        this.age = age

        // return this
    }

    toString() {
        return `Im ${this.name}, ${this.age} year's old`
    }
}

const student = new Student('rekaime', 18)

student.hasOwnProperty('name') // true
student.hasOwnProperty('toString') // false
student.__proto__.hasOwnProperty('toString') // true
```



### 实例属性新写法

可以将属性定义在顶层，此时属性定义在实例本身而不是原型上

```js
class Student {
    _name
    _age
    
    constructor(name) {
        this._name = name
    }
}
```



### getter & setter

与 ES5 一样，class 内部可以使用`get`和`set`关键字，对某个属性设置存值函数（setter）和取值函数（getter），拦截属性的存取行为



### 属性表达式

类的属性名，可以采用表达式

```js
const tag = Symbol('tag')

class Student {
    constructor(name, age) {
        this.name = name
        this.age = age
    }

    get [tag]() {
        return this.name
    }
}
```



### Class 表达式

与函数一样，类也可以使用表达式的形式定义

采用 Class 表达式，可以写出立即执行的 Class

```js
const Student = class {
    constructor(name, age) {
        this.name = name
        this.age = age
    }

    toString() {
        return `Im ${this.name}, ${this.age} year's old`
    }
}

const stu = new class {
    constructor(name, age) {
        this.name = name
        this.age = age
    }

    toString() {
        return `Im ${this.name}, ${this.age} year's old`
    }
} ('rekaime', 18);
```



### 静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承

如果在一个方法前加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为**静态方法**

如果静态方法包含`this`关键字，则指向 class 而不是实例

```js
class Foo {
    static bar() {
        this.baz() // 相当于 Foo.baz()
    }
    static baz() {
        console.log('hello')
    }
    baz() {
        console.log('world')
    }
}
Foo.bar()  // hello
```



### 静态属性

静态属性指的是 Class 本身的属性，即`Class.propName`

旧写法（ES6 也无法实现静态属性）

```js
Class Foo {}

Foo.prop = 'any'
```

新提案

```js
Class Foo {
    static prop = 'any'
}
```



### 私有方法及属性

[ES2022](https://github.com/tc39/proposal-class-fields)正式为`class`添加了私有属性，实现方式是在属性名之前使用`#`表示

私有方法也可以通过`#`来修饰

```js
class Student {
    #wallet // 必须在顶层声明
    constructor(name, age) {
        this.name = name
        this.age = age
        this.#wallet = 0
    }

    toString() {
        return `Im ${this.name}, ${this.age} year's old`
    }
}

const student = new Student('rekaime', 18)

student.#wallet
// 如果在类外部使用就会报错
// index.js:7 Uncaught SyntaxError: Private field '#wallet' must be declared in an enclosing class
```



### in 运算符

直接访问某个类不存在的私有属性会报错，但是访问不存在的公开属性不会报错

这个特性可以用来判断：某个对象是否为类的实例

```js
class Student {
    #wallet
    constructor(name, age) {
        this.name = name
        this.age = age
        this.#wallet = 0
    }

    static isInstance(tar) {
        try {
            tar.#wallet
            return true
        } catch {
            return false
        }
    }
}
const student = new Student('rekaime', 18)

Student.isInstance(student) // true
```

但这么做非常麻烦，因此 ES6 改进了`in`运算符，可以判断**私有**属性是否在类当中

```js
class Student {
    #wallet
    constructor(name, age) {
        this.name = name
        this.age = age
        this.#wallet = 0
    }

    static isInstance(tar) {
        return #wallet in tar
    }
}
```



### 静态块

主要作用是对静态属性进行初始化

允许在类的内部设置一个代码块，在类生成时运行且只运行一次

`this`的指向为 class，不能使用`return`

```js
class C {
    static x = ...
    static y
    static z

    static {
        try {
            const obj = doSomethingWith(this.x)
            this.y = obj.y
            this.z = obj.z
        }
        catch {
            this.y = ...
            this.z = ...
        }
    }
}
```

除了静态属性的初始化，静态块还可以将私有属性与类的外部代码分享

```js
// 以前将 getX() 方法的定义写在 constructor 中
// 现在可以避免 getX 的反复定义
let getX;

export class C {
    #x = 1;
    static {
        getX = obj => obj.#x;
    }
}

getX(new C()) // 1
```



### new.target

一般在构造函数中使用

返回值为`new`作用的构造函数

```js
function Person(name) {
    if (new.target !== undefined) this.name = name
    else throw new Error('必须使用 new 命令生成实例')
}
```

class 内则返回当前类

这样可以定义只能继承不能独立使用的类

```js
class Shape {
    constructor() {
        if (new.target === Shape) throw new Error('本类不能实例化')
    }
}

class Rectangle extends Shape {
    constructor(length, width) {
        super()
        // ...
    }
}
```



### 继承

#### 概述

Class 可以通过`extends`关键字实现继承，让子类继承父类的属性和方法

比 ES5 的原型链继承要清晰和方便很多

ES6 规定，子类必须在`constructor()`方法中调用`super()`，否则就会报错

为什么子类的构造函数，一定要调用`super()`：原因就在于 ES6 的继承机制，与 ES5 完全不同

ES5 的继承机制，是先创造一个独立的子类的实例对象，然后再将父类的方法添加到这个对象上面，即“实例在前，继承在后”

ES6 的继承机制，则是先将父类的属性和方法，加到一个空的对象上面，然后再将该对象作为子类的实例，即“继承在前，实例在后”

这就是为什么 ES6 的继承必须先调用`super()`方法，因为这一步会生成一个继承父类的`this`对象，没有这一步就无法继承父类

```js
class Point {
    constructor(x, y) {
        this.x = x
        this.y = y
    }

    toString() {
        return `(${this.x}, ${this.y})`
    }
}

class TDPoint extends Point {
    constructor(x, y, z) {
        super(x, y) // 必须先调用 super
        this.z = z
    }

    toString() {
        return `(${this.x}, ${this.y}, ${this.z})`
    }
}
```



#### 静态属性和静态方法的继承

静态属性和静态方法通过浅拷贝进行继承



#### Object.getPrototypeOf()

`Object.getPrototypeOf()`方法可以用来从子类上获取父类

```
Object.getPrototypeOf(TDPoint) === Point
```



#### super

`super`可以作为函数或对象使用

作为函数时表示父类构造方法，只能在构造方法中使用

作为对象时，在一般方法中指向父类原型，在静态方法中指向父类

注意，使用`super`的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错



ES6 规定，在子类普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例

```js
class A {
    constructor() {
        this.x = 1
    }
    print() {
        console.log(this.x)
    }
}

class B extends A {
    constructor() {
        super()
        this.x = 2
    }
    m() {
        super.print()
    }
}

const b = new B()
b.m() // 2
```

由于`this`指向子类实例，所以如果通过`super`对某个属性赋值，这时`super`就是`this`

```js
class A {
    constructor() {
        this.x = 1
    }
}

class B extends A {
    constructor() {
        super()
        this.x = 2
        super.x = 3
        // 此时读取的是 A.prototype.x
        console.log(super.x) // undefined
        console.log(this.x)  // 3
    }
}
```

如果`super`作为对象，用在静态方法之中，这时`super`将指向父类，而不是父类的原型对象

在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例

```js
class Parent {
    static myMethod(msg) {
        console.log('static', msg)
    }
    myMethod(msg) {
        console.log('instance', msg)
    }
}

class Child extends Parent {
    static myMethod(msg) {
        super.myMethod(msg)
    }
    myMethod(msg) {
        super.myMethod(msg)
    }
}

Child.myMethod(1) // static 1

const child = new Child()
child.myMethod(2) // instance 2
```



#### 子类的 proto

Class 作为构造函数的语法糖，同时有`prototype`属性和`__proto__`属性：

+ 子类的`__proto__`属性，表示构造函数的继承，总是指向父类
+ 子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性

```js
class A { }
class B extends A { }

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

因为类的继承是按照以下方法实现的

```js
class A { }
class B { }

Object.setPrototypeOf(B.prototype, A.prototype) // B 的实例继承 A 的实例
Object.setPrototypeOf(B, A) // B 继承 A 的静态属性
```

而`Object.setPrototypeOf`方法的实现如下

```js
Object.setPrototypeOf = function (obj, proto) {
    obj.__proto__ = proto;
    return obj;
}
```

因此就得到了开头所提出的结论

可以发现父类`A`其实可以是任意具有`prototype`的函数

进一步，对于子类实例：

```js
const a = new A()
const b = new B()
b.__proto__.__proto__ === a.__proto__ // true
```



#### Mixin

Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口

最简单的实现为：

```js
const a = { a: 'a' }
const b = { b: 'b' }
const c = { ...a, ...b } // {a: 'a', b: 'b'}
```

更具体的实现如下

```js
function mix(...mixins) {
    class Mix {
        constructor() {
            for (let mixin of mixins) {
                copyProperties(this, new mixin()); // 拷贝实例属性
            }
        }
    }

    for (let mixin of mixins) {
        copyProperties(Mix, mixin); // 拷贝静态属性
        copyProperties(Mix.prototype, mixin.prototype); // 拷贝原型属性
    }

    return Mix;
}

function copyProperties(target, source) {
    for (let key of Reflect.ownKeys(source)) {
        if ( key !== 'constructor'
            && key !== 'prototype'
            && key !== 'name'
        ) {
            let desc = Object.getOwnPropertyDescriptor(source, key);
            Object.defineProperty(target, key, desc);
        }
    }
}
```

利用 Mixin 可以将多个类合成为一个类，在使用时继承这个类即可

```js
class DistributedEdit extends mix(Loggable, Serializable) { }
```



## module

### 概述

ES6 的模块自动采用严格模式

严格模式主要有以下限制：

- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用`with`语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀 0 表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
- `eval`不会在它的外层作用域引入变量
- `eval`和`arguments`不能被重新赋值
- `arguments`不会自动反映函数参数的变化
- 不能使用`arguments.callee`
- 不能使用`arguments.caller`
- 禁止`this`指向全局对象
- 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
- 增加了保留字（比如`protected`、`static`和`interface`）

ES6 模块之中，顶层的`this`指向`undefined`，即不应该在顶层代码使用`this`



### export

用于规定对外接口

```js
// 方式一
export const obj = {}

// 方式二
function f() {}
export { f }
```

可以通过`as`对变量重命名

```js
function f() {}

export {
    f as func
}
```

`export`语句输出接口，与对应的值是动态绑定（引用传递）

以下内容被导入后，在 500ms 后`foo === 'baz'`

```js
export let foo = 'bar'
setTimeout(() => foo = 'baz', 500)
```



### import

通过对外接口加载相应的模块

`import`命令输入的变量都是只读的，因为它的本质是输入接口'

`import`具有提升效果，会提升到模块头部首先执行

```js
import { f } from './my_module'
```

通过`as`可以重命名

```js
import { f as func } from './module'
```

`import`命令会执行对应的模块，因此可以只加载不接受任何值

重复执行同一句`import`只会执行一次

```js
import 'lodash'

import { foo } from './my_module'
import { bar } from './my_module'
// 等同于
import { foo, bar } from './my_module'
```

模块允许整体加载，此时模块上的属性`foo`、`bar`不允许更改

```js
import * as MyModule from './my_module'

MyModule.foo
MyModule.bar
```



### export default

默认导出，导出一项表达式：

```js
export default 'rekaiem'
```

接收时：

```js
import name from './profile'
```

同时输入默认值和其他接口：

```js
import _, { each, forEach } from 'lodash'
```



### export&import 复合

先载入一个模块，后导出相同模块：

```js
import { foo, bar } from './my_module'
export { foo, bar }
```

简写：

```js
// foo 和 bar 实际上并没有被导入当前模块
// 只是做了一次转发
// 因此当前模块不能使用 foo 和 bar
export { foo, bar } from './my_module'
```

重命名、默认接口及整体转发：

```js
// 重命名
export { foo as myFoo } from './my_module'

// 默认接口
export { default } from './my_module'

// 整体转发
export * from './my_module'
```

具名接口转为默认接口：

```js
export { foo as default } from './my_module'
```

默认接口转为具名接口：

```js
export { default as foo } from './my_module'
```

重命名导出整体

此时导出的模块上会挂载属性`ns`，而不是模块`my_module`的内容

```js
export * as ns from './my_module'

// 等同于
import * as ns from './my_module'
export { ns }
```



通过以上复合写法，可以更方便实现**模块继承**

```js
// b.js
export * from './a.js' // 继承除了默认属性外的所有属性和方法
export { name as profile } from './a.js' // 重命名
```



### 跨模块常量

利用模块化可以解决`const`常量只在当前代码块生效的局限性，管理需要在多个模块中使用的常量



### import( )

运行时加载，支持动态加载

返回一个`Promise`对象，`data`为模块，如果有`default`接口则取而代之

推荐配合`await`使用

使用场景：

+ 按需加载
+ 条件加载
+ 动态指定模块路径



### import.meta

可以获取模块的元信息

`import.meta`是一个对象，只能在模块内使用

`import.meta`上的属性由各个运行环境决定，没有标准

一般而言包含：

+ `import.meta.url`：返回当前模块的 URL 路径（Node.js 环境中，`import.meta.url`返回的总是本地路径）
+ `import.meta.scriptElement`是浏览器特有的元属性，返回加载模块的那个`<script>`元素




### 加载实现

#### 浏览器

**早期：**

通过`defer`或`async`属性标记

+ `defer`：页面渲染完毕再执行
+ `async`：Js 文件下载完就中断渲染并执行

```js
<script src="path/to/myModule.js" defer></script>
<script src="path/to/myModule.js" async></script>
```

**ES6：**

浏览器加载 ES6 模块，需要加入`type="module"`属性

此时会进行异步加载，相当于开启`defer`

如果手动加上`async`则会引导模块在下载完就立即执行

```js
<script type="module" src="path/to/myModule.js"></script>
```



#### ES6 与 CommonJS

最主要的三大差异：

1. CommonJS 模块输出的是值的拷贝，ES6 模块输出的是值的引用
2. CommonJS 模块运行时加载，ES6 模块是编译时输出接口
3. CommonJS 模块的`require()`是同步加载模块，ES6 模块的`import`命令是异步加载，有一个独立的模块依赖的解析阶段

对于第一点：

**CommonJS** 输出值拷贝，且存在缓存机制，因此模块内部变化无法影响引入的值

`lib.js`

```js
let counter = 3
function incCounter() {
    counter++
}

module.exports = {
    counter: counter,
    incCounter: incCounter,
}
```

`main.js`

```js
const lib = require('./lib')

log(lib.counter) // 3
lib.incCounter()
log(lib.counter) // 3
```

如果需要获取`counter`，需要将其构造为 getter

```js
module.exports = {
    get counter() {
        return counter
    },
    incCounter: incCounter,
}
```

**ES6** 在遇到模块加载命令`import`，就会生成一个只读引用

等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值

因此原始值变化，`import`加载的值也会变化

需要注意的是，ES6 的`export`接口输出的是相同的值，不同脚本加载得到的是同一个实例

```js
// mod.js
class C { }
export const c = new C() // 输出的是同一个 c
```



**如果需要在 CJS 下加载 ESM：**

require() 无法加载 ES6 模块，因为它是同步加载，而 ES6 模块内部可以使用顶层`await`命令，导致无法被同步加载

```js
(async () => {
  await import('./my-app.mjs')
})();
```

**如果需要在 ESM 下加载 CJS：**

`import`命令可以加载 CommonJS 模块，但是只能整体加载，不能只加载单一的输出项

因为 ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是`module.exports`，是一个对象，无法被静态分析，所以只能整体加载

```js
import packageMain from 'commonjs-package' // 正确

import { method } from 'commonjs-package'  // 报错
```

或者使用 Node.js 内置的`module.createRequire()`，但这将 ES6 和 CommonJS 语法混合了，因此不建议使用

```js
// cjs.cjs
module.exports = 'cjs'

// esm.mjs
import { createRequire } from 'module'
const require = createRequire(import.meta.url)
const cjs = require('./cjs.cjs')
cjs === 'cjs'; // true
```



#### NodeJs & Package.json

对于 NodeJS：

+ CJS => `.cjs`
+ ESM => `.mjs`

NodeJS 内置模块可以被整体加载或加载指定方法

```js
// 加载指定的输出项
import { readFile } from 'fs'
```

对于 ES6，不能省略路径的后缀

为了与浏览器的`import`加载规则相同，NodeJS 的`.mjs`文件支持 URL 路径

同一个脚本只要参数不同，就会被加载多次，并且保存成不同的缓存

因此只要文件名中含有`:`、`%`、`#`、`?`等特殊字符，最好对这些字符进行转义

目前，Node.js 的`import`命令只支持加载本地模块（`file:`协议）和`data:`协议，不支持加载远程模块，另外，脚本路径只支持相对路径，不支持绝对路径

```js
import './foo.mjs?query=1'
```



模块可以整理至同一个包下（以下用`lib`指代包），通过`package.json`管理入口

默认模块加载方式取决于`package.json`的`type`字段

```js
{
  "type": "module" | "commonjs"
}
```

`main`字段可以指定模块入口文件

```js
{
  "type": "module",
  "main": "./src/index.js"
}
```

`export`字段优先级高于`main`，用法包括：

1. 指定子目录别名

```js
{
  "exports": {
    "./alias": "./src/submodule.js"
  }
}

// 导入时通过别名加载
import submodule from 'lib/alias'
```

2. main 的别名

`exports`字段只有支持 ES6 的 Node.js 才认识，所以可以搭配`main`字段，来兼容旧版本的 Node.js

`exports`字段的别名如果是`.`则代表模块的主入口，优先级高于`main`字段

```js
{
  "exports": {
    ".": "./main_module.js"
  }
}

// 简写
{
  "exports": "./main_module.js"
}

// 用于兼容旧版 NodeJS
{
  "main": "./main-legacy.cjs",
  "exports": {
    ".": "./main-modern.cjs"
  }
}
```

3. 条件加载

利用`.`可以为 ES6 模块和 CommonJS 指定不同的入口

```js
{
  "type": "module", // 默认 ES6
  "exports": {
    ".": {
      "require": "./main.cjs", // 如果通过 require() 导入
      "default": "./main.js"   // 默认导入
    }
  }
}

// 不含其他别名则可以简写
{
  "type": "module", // 默认 ES6
  "exports": {
    "require": "./main.cjs",
    "default": "./main.js"
  }
}
```



#### 循环加载

“循环加载”（circular dependency）指的是，`a`脚本的执行依赖`b`脚本，而`b`脚本的执行又依赖`a`脚本

在关系复杂的大项目中容易出现循环加载，因此 CJS 和 EMS 分别采用了不同的解决方案

**CommonJS**

`require`命令第一次加载模块，就会执行整个脚本，然后在内存生成一个对象

```js
{
    id: '...',        // 模块名
    exports: { ... }, // 模块输出接口
    loaded: true,     // 模块脚本是否执行完毕
    ...
}
```

由于是加载时执行，即脚本代码在`require`的时候，就会全部执行，一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出

**ES6**

ES6 模块是动态引用，如果使用`import`从模块加载变量，变量不会被缓存，而是成为一个指向被加载模块的引用

需要开发者自己保证，真正取值的时候能够取到值

例如：

```js
// a.mjs
import { bar } from './b'
console.log('a.mjs')
console.log(bar)
export let foo = 'foo'

// b.mjs
import { foo } from './a'
console.log('b.mjs')
console.log(foo)
export let bar = 'bar'
```

输出结果为

```shell
b.mjs
ReferenceError: foo is not defined
```

因为`a.mjs`执行时发现需要引入`b.mjs`，因此优先执行`b.mjs`

`b.mjs`从`a.mjs`输入了接口`foo`，因此`b.mjs`默认接口存在而不会执行`a.mjs`

继续执行`b.mjs`，到了使用`foo`的地方，才发现接口根本没定义，因此报错

解决方案是在`b.mjs`运行前让变量被定义，例如将`foo`改写为函数（不能是函数表达式）

```js
// a.mjs
import { bar } from './b'
console.log('a.mjs')
console.log(bar)
function foo() { return 'foo' }
export { foo }

// b.mjs
import { foo } from './a'
console.log('b.mjs')
console.log(foo)
function bar() { return 'bar' }
export { bar }
```

一个有意思的例子，这个案例只能在 ESM 中实现，在 CJS 中会报错

```js
// even.js
import { odd } from './odd'
export function even(n) {
    return n === 0 || odd(n - 1)
}

// odd.js
import { even } from './even'
export function odd(n) {
    return n !== 0 && even(n - 1)
}
```

