# 安装配置

```shell
npm i vite -D
```

`package.json`

```json
{
    "scripts": {
        "dev": "vite",
        "build": "vite build",
    }
}
```



# 依赖预构建

处理过程中遇到非绝对路径、相对路径的引用，则尝试补全路径，最终路径会变为`"/node_modules/.vite/**/*.js"`

> 源码： 
>
> `import _ from 'lodash'`
>
> 补全后：
>
> `import __vite__cjsImport1_lodash from "/node_modules/.vite/deps/lodash.js?v=b9b872b6";`

寻找依赖的过程自当前目录开始向父级查找，为了方便浏览器根据补全的路径来发起请求，vite 会对依赖包进行预构建

> 因为开发环境和生产环境下 Vite 的运行是不同的
>
> 开发环境中，每次启动 vite 都会进行一次预构建，因此重构的相对路径一定是正确的
>
> 生产环境中 vite 会全权交由 rollup 库去完成生产打包，而不会去进行预构建的处理

**依赖预构建：**

vite 在找到依赖包后，调用 esbuild 将代码进行规范，然后输出到`/node_modules/.vite/deps/`目录下

预构建解决的三个问题：

+ 将不同的包的不同导出方式统一到 ES6 module
+ 对路径的重构可以统一为`/node_modules/.vite/deps/`，方便了路径的重写
+ 解决了网络的多包传输性能问题



# 配置文件

`vite.config.js`

## 开启语法提示

```js
import { defineConfig } from 'vite'

// 可以直接导出对象 但是为了代码规范和提示 建议使用defineConfig
// defineConfig的参数可以是对象 也可以是一个函数 这个函数的返回值必须是UserConfig类型
export default defineConfig({
    // ...
})
```

规范的第二种方式

```js
/**
 * @type import('vite').UserConfig
 */
const viteConfig = {
    
}

export default viteConfig
```



## 环境区分

在项目中将基础配置`vite.base.config.js`、开发配置`vite.dev.config.js`和生产配置`vite.prod.config.js`写到不同文件中

`vite.config.js`

```js
import { defineConfig } from 'vite'
import viteBaseConfig from './vite.base.config'
import viteDevConfig from './vite.dev.config'
import viteProdConfig from './vite.prod.config'

const envResolver = {
    'build': () => Object.assign({}, viteBaseConfig, viteDevConfig),
    'serve': () => Object.assign({}, viteBaseConfig, viteProdConfig),
}

// 函数可以接受参数 通过该参数的command属性可以获取开发环境
// command: 'build' | 'serve'
export default defineConfig(({ command }) => {
    return envResolver[command]()
})
```



## 环境变量

用于在开发、测试、预发布、灰度和生产等环境下，区分根据环境发生变化的变量值

### dotenv

vite 会使用第三方库 dotenv

dotenv会自动读取`.env`文件，解析其中的环境变量

如果是**服务器**（Node.js，如 vite.config.js），会将其注入到`process`对象下（`process.env`）

但是 vite.config.js 文件中会返回项目有关的配置，这些配置可能影响到 .env 的解析，因此在 vite.config.js 中无法通过`process.env`去读取环境变量，因为这个时候环境变量还没挂载到`process`上。相关配置包括：

- root
- envDir

如果希望立即读取环境变量，那么可以通过`loadEnv(mode: string, envDir: string, prefixes?: string | string[])`加载 env 文件内容

其中：

- `mode`：按后缀名覆盖`.env`文件的变量
- `envDir`：指定配置文件所在目录，一般为项目根目录
- `prefixes`：只加载指定前缀的变量，如果为`''`则表示全部加载

`loadEnv()`运行时，首先将读取指定目录的`.env`文件，不解析

```js
import { defineConfig, loadEnv } from 'vite'

// mode参数相当于 npm run dev --mode <mode>
// 开发环境下默认为 development
// 生产环境下默认为 production
export default defineConfig(({mode}) => {
    const env = loadEnv(mode, process.cwd(), '')
    console.log(env)
})
```



如果是**客户端**，vite 会将环境变量注入到`import.meta.env`中

而 vite 做了拦截，防止我们将隐私变量注入到 env 中

因此默认前缀为 VITE_ 的变量才会被挂载

如果希望自定义前缀，可以通过 envPrefix 去配置

```js
import { defineConfig } from 'vite'

export default defineConfig({
    envPrefix: 'VITE_',
})
```



# 处理 CSS

## 一般处理

vite 天生支持对 css 的处理，在 vite 遇到 css 文件时会进行如下操作：

如果是`<link>`发起的请求，则原封不动返回 css 文件

如果是在 js 文件中通过模块引用的 css 文件，则会进行如下操作：

1. 在接收到 css 文件请求后，通过 fs 模块去读取对应的 css 文件内容
2. 创建一个`<style>`，然后将 css 文件内容直接复制到标签中
3. 将`<style>`插入到 html 文件的`<head>`中
4. 将该 css 文件中的内容直接替换为 js 脚本，便于热更新或 css 模块化，同时设置 Content-Type 为 text/javascript，从而让浏览器以 js 的形式去解析该 css 后缀的文件



关于 css 的模块化，设计目的在于解决多人开发时，不同组件下 css 文件类名冲突的问题

当文件名后缀为`.module.css`时，表示需要开启 css 模块化

在 js 文件中引入`.module.css`文件时（`import CssModule from 'a.module.css'`），会进行如下操作：

1. 将所有类名进行一定的规则替换（如`.footer ===> ._footer_i22st_1`）
2. 创建一个映射对象，将`footer`映射到`_footer_i22st_1`
3. 将替换后的内容写入`<script>`中，再插入`<head>`
4. 将`a.module.css`的内容全部抹除，替换为 js 脚本，默认导出映射对象



如果希望使用的是 less，那么需要自己安装插件，帮助解析 less，如果需要对 less 进行模块化，需要将后缀改为`.module.less`



## modules

在 vite 中可以通过 css 配置项来控制整个项目对 css 文件的处理方式

css 配置项最终会传给 [postcss-modules](https://www.npmjs.com/package/postcss-modules)，所以配置项可以在官方文档查询

```js
export default defineConfig({
    css: {
        modules: {
            // css文件默认导出的对象上属性的类型 默认包含dashes
            // 'camelCase' | 'camelCaseOnly' | 'dashes' | 'dashesOnly'
            localsConvention: '<type>',
            
            // 一般不用 默认为local 会开启模块化
            // 值为global时关闭css文件默认导出 同时css直接插入<head>
            // 'local' | 'global'
            scopeBehaviour: 'local',
            
            // 定义类名展示的格式 具体占位方式去官方文档看
            generateScopedName: '', // string || (name, filename, css) => string
            
            // 设定哈希前缀 参与类名哈希的生成
            hashPrefix: '<prefix>',
            
            // 希望挂至全局的css文件路径
            globalModulePaths: [],
        }
    }
})
```



## preprocessorOptions

通过 preprocessorOptions 配置项可以设定 css 的预处理器参数，参数具体如何设置看各预处理器的官网，如 [less](https://lesscss.org/usage/#/less-options-math)

```js
export default defineConfig({
    css: {
        preprocessorOptions: {
            less: {

            },
            sass: {
                
            }
        }
    }
})
```



## devSourcemap

关于 source map 解决的问题：因为代码经过编译后，会压缩为一行简单的代码，通过这行压缩的代码运行前端程序，此时如果发生报错，显示出错的行为压缩代码的行数

如果添加了 source map，那么编译后的代码会添加一行注释，指向原文件的索引，当发生报错时，会返回原文件实际行数

在 vite 中 devSourcemap 默认为`false`，可以配置开启

```js
export default defineConfig({
    css: {
        devSourcemap: true
    }
})
```



# 关于 [postcss](https://github.com/postcss/postcss)

由于 vite 对 postcss 有良好的支持，所以需要对 postcss 有足够的了解



## 作用

postcss 设计的目的类比于 bable，也就是保证 css 代码运行的万无一失

预处理器无法考虑到大部分问题，比如：

+ 对 css 新语法的降级处理
+ 前缀补齐（`--webkit`等），实现兼容性

postcss 希望维护一个管道，通过这个管道实现 css 的处理，使得 css 能够通过一些插件编译成目标文件，规避运行时可能出现的大部分问题（原本计划取代 less 与 sass 等预处理器，后来考虑到预处理器更新时自身插件也需要同步更新，因此民间维护的预处理器插件停止维护，此后 postcss 被称为后处理器）

postcss 所做的工作，就是将开发者提供的 css --> 利用 less、sass 等预处理器将语法编译 --> 对未来高级 css 语法进行降级 --> 前缀补全 --> 浏览器客户端



## 使用

### 安装

```shell
# 安装脚手架和 postcss 本体
# 其中脚手架提供了一系列命令
npm i postcss-cli postcss -D
```



### 配置文件

配置项具体参考 [postcss-cli](https://www.npmjs.com/package/postcss-cli/v/2.4.1)

插件预设的目的是，集成一系列开发常用的插件，帮助开发者快速搭建好 postcss 环境，插件预设详见 https://github.com/postcss/postcss/blob/main/docs/plugins.md

```shell
# 假设安装插件
npm i postcss-preset-env -D
```

`postcss.config.js`

```js
const postcssPresetEnv = require('postcss-preset-env')

module.exports = {
    plugins: [postcssPresetEnv(/* pluginOptions */)],
}
```



### 运行

```shell
postcss index.css -o result.css
```



## 在 vite 中配置

可以直接在`css.postcss`中对 postcss 进行配置

也可以在 postcss.config.js 配置文件中编辑，但是优先级方面，vite.config.js 更高一些

```js
export default defineConfig({
    css: {
        postcss: {
            
        }
    }
})
```



# 别名

通过别名可以简化路径配置，vite 内部会通过一系列字符串替换，将我们设定的别名替换为实际路径

`vite.config.js`

```js
import { defineConfig } from 'vite'
import path from 'path'

export default defineConfig({
    resolve: {
        alias: {
            '@': path.resolve(__dirname, './src'),
            '@assets': path.resolve(__dirname, './src/assets'),
        }
    }
})
```



# 静态资源

## 定义

在服务器视角，除了动态 API，大部分的资源都可以被视作静态资源

而 vite 为了服务客户端，默认下静态资源应该包括视频、图像资源

vite 对于静态资源是开箱即用的



## 加载

### 图片

加载文件路径

```js
import imgUrl from './assets/images/test.png'

const img = document.createElement('img')
img.src = imgUrl
```

加载二进制流

```js
// 不提供参数时 默认参数为url 此时提供参数raw
import bf from './assets/images/test.png?raw' // 此时获取到的是 buffer
```



### JSON

```js
import student from './assets/json/test.json'
// or
import { name, age } from './assets/json/test.json'
```



### 关于 svg

vite 对 svg（scalable vector graphics，可伸缩矢量图）也是开箱即用的

svg 特点包括：

- svg 不会失真
- 尺寸小

缺点：无法包含更多图层以凸显层次丰富的图片信息



加载方式：

- 直接加载

```js
import svgIconUrl from './assets/svgs/test.svg'

const img = document.createElement('img')
img.src = svgIconUrl
```

- 加载标签

```js
import svgRaw from './assets/svgs/test.svg/raw'
// 此时获取到的是<svg>的字符串形式
document.body.innerHTML = svgRaw

// 如此一来可以通过svg特有方法来控制样式等
const svgElement = document.querySelector('svg')
svgElement.onmouseenter = function () {
    this.style.fill = 'cyan'
}
```



## vite 对静态资源的处理

关于打包后的文件会包含 hash：

因为浏览器有缓存机制，如果请求的文件和之前缓存的文件同名，那么不会发起请求，而是去缓存中查找文件，因此打包的结果要避免文件同名

哈希的产生和文件内容有关，如果内容没有发生变化，产生的哈希值就一致



# 生产环境配置

```js
export default defineConfig({
    build: {
        rollupOptions: { // rollup 相关配置
            output: {
                assetFileNames: '[name].[hash].[ext]', // 控制输出静态资源命名格式
            }
        },
        assetsInlineLimit: 4096, // 当静态资源体积小于4kb时会转为 base64
        outDir: 'dist', // 输出目录名称
        assetsDir: 'assets', // 静态资源目录名称
        emptyOutDir: true, // 每次构建项目前先清空输出目录
    }
})
```



# 插件

## 定义

vite 会在生命周期不同阶段调用不同的插件，达到不同的目的

[官方插件](https://cn.vitejs.dev/plugins/)

[社区插件](https://github.com/vitejs/awesome-vite#plugins)



## 开发插件

[插件开发规范](https://vite.dev/guide/api-plugin.html)

### config

```js
export default function () {
    return {
        config(config, env) {
            // config为vite所读取但是未生效的vite config
            // env下包含两个属性
            // env.mode: string ===> production || development
            // env.command: string ===> build || serve
            return { /* 你所希望新增或修改的配置 */ }
        }
    }
}
```



### transformIndexHtml

```js
export default function (options) {
    return {
        transformIndexHtml(html, ctx) {
            // html为index.html的原始内容
            // ctx为vite请求的执行期上下文
            
            // 返回的内容会应用到真实的html当中
            return html
        }
    }
}
```

调整插件的执行顺序

```js
export default function (options) {
    return {
        transformIndexHtml: {
            enforce: 'pre',
            transform(html, ctx) {
                // html为index.html的原始内容
                // ctx为vite请求的执行期上下文

                // 返回的内容会应用到真实的html当中
                return html
            }
        }
    }
}
```



### configureServer

```js
export default function (options) {
    return {
        configureServer(server) {
            // 可以对vite服务器注册中间件
            server.middlewares.use((req, res, next) => {
                // ...
            })
        }
    }
}
```





## vite-aliases

可以通过监测当前目录下包括 src 在内的所有目录，帮助我们自动生成别名

[官方文档](https://github.com/subwaytime/vite-aliases)



### 引入

```shell
npm i vite-aliases -D
```

`package.json`

```json
{
    "type": "module"
}
```

`vite.config.js`

```js
import { defineConfig } from 'vite'
import { ViteAliases } from 'vite-aliases'

export default defineConfig({
    plugins: [
        ViteAliases()
    ]
})
```



### 功能

假设目录结构如下：

```content
src
  assets
  components
  pages
  store
  utils
```

则会对应地生成别名：

```ts
[
    {
        find: '@',
        replacement: '${your_project_path}/src'
    },
    {
        find: '@assets',
        replacement: '${your_project_path}/src/assets'
    },
    {
        find: '@components',
        replacement: '${your_project_path}/src/components'
    },
    {
        find: '@pages',
        replacement: '${your_project_path}/src/pages'
    },
    {
        find: '@store',
        replacement: '${your_project_path}/src/store'
    },
    {
        find: '@utils',
        replacement: '${your_project_path}/src/utils'
    },
]
```



## vite-plugin-html

主要帮助我们控制`index.html`模板

[官方文档](https://github.com/vbenjs/vite-plugin-html)

### 引入

```shell
npm i vite-plugin-html -D
```

`vite.config.js`

```js
import { defineConfig } from 'vite'
import { createHtmlPlugin } from 'vite-plugin-html'

export default defineConfig({
    plugins: [
        createHtmlPlugin({
            inject: { // 可以通过ejs注入数据
                data: {
                    title: '首页',
                },
            },
        })
    ]
})
```

`index.html`

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <title><%= title %></title>
</head>

<body>
    <script type="module" src="./test.js"></script>
</body>

</html>
```



## vite-plugin-mock

利用 mockjs 模拟海量数据

> 写死数据的缺陷：
>
> - 无法进行海量数据测试
> - 无法获取标准数据
> - 无法感知 http 请求的异常

[官方文档](https://github.com/vbenjs/vite-plugin-mock)

### 引入

```shell
npm i  mockjs -S
npm i vite-plugin-mock -D
```

`vite.config.js`

```js
import { defineConfig } from 'vite'
import { viteMockServe } from 'vite-plugin-mock'

export default defineConfig({
    plugins: [
        viteMockServe({
            mockPath: 'mock', // 目录
            localEnabled: 'serve', // 限定在开发环境下 才启用mock拦截请求
        })
    ]
})
```

`mock/index.js`

[mockjs](http://mockjs.com/)

```js
import mockjs from "mockjs"

const userList = mockjs.mock({
    'list|100': [{
        'id|+1': 1,
        'name': '@cname',
        'age|1-100': 1,
        'email': '@email',
        'phone': /^1[3-9]\d{9}$/,
        'address': '@county(true)',
        'createTime': '@datetime',
        'updateTime': '@datetime'
    }]
})

export default [
    {
        method: 'post',
        url: '/api/users',
        response: ({ body }) => {
            return {
                code: 200,
                msg: 'success',
                data: userList
            }
        }
    }
]
```



# vite + ts

借助 ts 为项目引入类型检查和语法提示



## 将错误输出到控制台

```shell
npm i vite-plugin-checker -D
npm i typescript -D
tsc --init
```

`vite.config.js`

```js
import { defineConfig } from 'vite'
import VitePluginChecker from 'vite-plugin-checker'

export default defineConfig({
    plugins: [
        VitePluginChecker({
            typescript: true
        })
    ]
})
```

当发生错误时，禁止生产环境的输出：

`package.json`

```json
{
    "script": {
        "build": "tsc --noEmit && vite build"
    }
}
```



## 环境变量的配置

我们在客户端使用 .env 配置环境变量时，需要通过`import.meta.env`去读取，因此需要额外配置以下信息

`vite-env.d.ts`

```ts
/// <reference types='vite/client' />

interface ImportMetaEnv {
    readonly <Key>: string
}
```



# 性能优化

## 概念

性能优化包括以下几个方面

- 页面性能指标
  - 首屏渲染时：fcp（first content paint）
    - 懒加载：通过代码实现
    - http 优化
      - 强缓存：服务器给响应头追加字段（expires），客户端在截止日期前只从缓存里读取数据以代替请求发起
      - 协商缓存：对于浏览器的请求，服务器比对文件的修改日期，如果文件未变化，则响应 304
  - 页面中最大元素的响应时长：lcp（largest content paint）
- js
  - 注意组件计时器的清楚，如果组件在频繁的挂载和卸载中没有对计时器进行特殊处理，则有可能在一次挂载中开启多个线程
  - 动画上活用 requestAnimationFrame、requestIdleCallback 等，对齐浏览器帧率（16.6ms）
  - 防抖、节流以及数组遍历等方法尽量使用 lodash
- css
  - 能继承就不重复写
  - 避免层级过深的嵌套
- 构建优化
  - 优化体积：压缩、treeshaking、图片资源压缩、cnd 加载、分包



## 分包策略

浏览器缓存策略基于文件名称，如果文件名称未变化则不会重新请求资源

而有相当一部分代码（尤指第三方库）在整个工程中不会经常发生变化，我们可以对这些资源进行单独打包处理，避免浏览器对这些资源的反复请求

`vite.config.js`

```js
import { defineConfig } from 'vite'

export default defineConfig({
    build: {
        rollupOptions: {
            output: {
                manualChunks(id) {
                    // id为被打包的所有文件的路径
                    // 对路径包含node_modules的目录返回vendor
                    // 则第三方库的文件会统一打包到vendor.[hash].js中
                    if (id.includes('node_modules')) return 'vendor'
                }
            }
        }
    }
})
```

分包原先有另一个好处，特别体现在多入口文件场景

`vite.config.js`

```js
import { defineConfig } from 'vite'
import path from 'path'

export default defineConfig({
    build: {
        rollupOptions: {
            input: {
                main: path.resolve(__dirname, './index.html'),
                product: path.resolve(__dirname, './product.html')
            }
        }
    }
})
```

如果像这样配置了多入口，且没有做好分包，然后在入口文件中都对同名 js/ts 文件进行引用，那么对于脚本文件中引入的相同第三方库，原先 vite 会将第三方库的内容分别打包到两个 js 文件中，这样会造成代码重复的资源占用问题

但是现在 vite 做出了优化：将第三方库打包结果输出到一个 js 文件中，然后其他文件`import { l as lodash } from './product.[hash].js'`



## gzip 压缩

打包中块的概念可以大致等效于输出的文件，需要注意虽然表面上输出的文件后缀为 js，但他们是块而不是 js 文件

首先下载插件 [vite-plugin-compression](https://github.com/vbenjs/vite-plugin-compression/tree/main#readme)

```shell
npm i vite-plugin-compression -D
```

`vite.config.js`

```js
import ViteCompression from 'vite-plugin-compression'

export default defineConfig({
    plugins: [
        ViteCompression()
    ]
})
```

后续打包时，会同时输出较大文件的 .gz 文件

此时服务器响应该 js 文件时，不需要进行额外压缩操作，然后还需要设置响应头为`content-encoding: gzip`，使得浏览器识别出压缩文件并进行解压



## 动态导入

动态导入是 ES6 的一个新特性

```js
import app from './app'
{
    component: app
}
// 将以上代码改为
{
    component: import('./app')
}
```



## cdn 加速

cdn（content delivery network，内容分发网络）

将第三方库包装为 cdn 的形式，使得代码体积变小，且请求速度快

```shell
npm i vite-plugin-cdn-import -D
```

`vite.config.js`

```js
import { defineConfig } from 'vite'
import ImportToCDN from 'vite-plugin-cdn-import'

export default defineConfig({
    plugins: [
        importToCDN({
            modules: [
                {
                    name: 'lodash',
                    var: '_',
                    path: 'https://cdn.jsdelivr.net/npm/lodash@4.17.21/lodash.min.js'
                }
            ]
        })
    ]
})
```

原理：在开发环境下通过`<script>`注入 cdn 地址，在生产环境下不会插入`<script>`，而是更改 rollup config：

```js
{
    build: {
        rollupOptions: {
            external: ['lodash'],
            externalGlobal: {
                var: '_',
                path: 'https://cdn.jsdelivr.net/npm/lodash@4.17.21/lodash.min.js'
            }
        }
    }
}
```



# 跨域

跨域仅发生在浏览器上，当出现不同源请求时，浏览器会允许请求的发出，对方服务器也可以对请求进行响应，但是如果响应头不包含标记信息，则浏览器会截获包并提示跨域问题

在 vite 中可以配置以下选项，实现在生产环境下绕过同源策略：

以下配置中，匹配配置的请求地址，在浏览器向这些请求地址发起请求后，vite 会接收请求，然后转而向目标地址发起真正的请求，然后将响应信息响应给浏览器，达到绕过同源的目的

```js
import { defineConfig } from 'vite'

export default defineConfig({
    server: {
        proxy: {
            '/api': {
                target: 'https://www.baidu.com',
                changeOrigin: true,
                rewrite: path => path.replace(/^\/api/, '')
            }
        }
    }
})
```

```js
fetch('/api').then(data => {
    // 此时拿到的是https://www.baidu.com的数据
})
```

跨域的解决方案：

- 开发：利用构建工具，或脚手架，或第三方库的 proxy 代理配置，或自己搭建服务器去解决跨域
- 生产：交给后端处理跨域问题
  - nginx：代理服务，本质上和 vite 服务器处理跨域问题方式一样
  - 配置身份标记：`Access-Control-Allow-Origin`
