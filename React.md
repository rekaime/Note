# React

[官网](https://react.dev/)

[中文官网](https://react.docschina.org/)



## 快速上手

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Title</title>
    <meta charset="UTF-8">
</head>
<body>
    <div id="app"></div>
    
    <script src="./js/react.development.js"></script>
    <script src="./js/react-dom.development.js"></script>
    <script src="./js/babel.min.js"></script>
    
    <script type="text/babel">
        // 使用bable帮助转化jsx语法
        const VDOM = <h1>Hello, React</h1>
        ReactDOM.render(VDOM, document.getElementById('app'))
    </script>
</body>
</html>
```

通过 js 创建虚拟节点

```html
<div id="app"></div>

<script src="./js/react.development.js"></script>
<script src="./js/react-dom.development.js"></script>

<script type="text/javascript">
    const VDOM = React.createElement('h1', { id: 'title '}, 'Hello, React')
    ReactDOM.render(VDOM, document.getElementById('app'))
</script>
```



## jsx

+ 标签中引入 js 表达式需要用`{}`包裹
+ 类名使用`className`作为标识
+ 内联样式使用`style={ {[key]: value} }`的形式
+ 一个虚拟结点只能有一个根标签
+ 单标签必须自闭合
+ 大写字母开头的标签会被视为组件，进行渲染

```jsx
const id = '0001'
const name = 'rekaime'

const VDOM = (
    <div>
        <h2 className="title" id={id}>
            <span style={{color: 'white', fontSize: '2em'}}>name - {name}</span>
        </h2>
        <input />
    </div>
)
```

数组作为数据渲染时会自动拆包
```jsx
<script type="text/babel">
    const arr = [1, 4, 5, 2, 3]
    const VDOM = (
        <div>{arr /* 14523 */ }</div>
        <ul> {arr.map((el, index) => <li key={index}>{el}</li>)}</ul>
    )
    ReactDOM.render(VDOM, document.getElementById('app'))
</script>
```



## 组件

## 创建

### 函数式

适用于定义简单组件

```jsx
function MyComponent() {
    return <h1>Hello, React</h1>
}

ReactDOM.render(<MyComponent/>, document.getElementById('app'))
```



### 类式

适用于定义复杂组件

```jsx
class MyComponent extends React.Component {
    render() {
        return <h1>Hello, React</h1>
    }
}

ReactDOM.render(<MyComponent/>, document.getElementById('app'))
```

