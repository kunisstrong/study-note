# React

## JSX语法规则

1. 定义虚拟DOM，不要写引号。
2. 标签中要混入JS代码，要用{}包裹
3. 样式的类名指定不能用class，要用className
4. 内联样式，要用`style={{key:value}}`形式，第一个花括号表示里面要写js代码，大二哥花括号表示一个js对象，例如font-size,要写成驼峰命名fontSize
4. 只有一个跟标签 
5. 标签必须闭合
6. 标签首字母
   - 小写， 会转换成对应的html标签
   - 大写，会被解析成组件

## react中定义组件

### 函数式组件

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    a<title>react函数式组件</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建函数式组件
    function MyComponent() {
      // 加行内样式
      return <h2 style{{marginLeft: "10px"}}>我是用函数定义的「函数式组件」</h2>
    }
    // 渲染组件
    ReactDOM.render(<MyComponent/>, document.querySelector('.text'))
</script>
</body>
</html>

```

### 类式组件

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>类式组件</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class MyComponent extends React.Component {
        render() {
            console.log(this)
            return <h2>我是用函数定义的「类式组件」</h2>
        }
    }

    // 渲染组件到页面
    ReactDOM.render(<MyComponent/>, document.querySelector('.text'))
</script>
</body>
</html>
```

执行了`ReactDOM.rend(<MyComponent>.......)`之后发生了什么

1. React解析组件标签，找到了MyComponent组件
2. 发现组件是使用类定义的，随后new出来类的实例。并通过该实例调用到原型上的render方法
3. 将render返回的虚拟DOM转为真实DOM，随后呈现在页面中

## 组件三大核心属性

### state

相当于vue2中的data

#### 增加点击事件的问题

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>state</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Weather extends React.Component {
        constructor(props) {
            super(props)
            this.state = {isHot: true}
        }

        render() {
            const isHot = this.state.isHot
            return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '寒冷'}</h1>
        }

        changeWeather() {
            /**
             * 因为changeWeather是作为onClick的回调函数调用的，
             * 回调函数就是作为参数传递给另一个函数的函数
             * 又因为类中的方法会在局部开启严格模式，所以this是undefined
             */
            console.log(this)
            console.log('标题被点击了')
        }
    }

    // 将类渲染成组件
    ReactDOM.render(<Weather/>, document.querySelector('.text'))

</script>
</body>
</html>
```

相当于：

```javascript
class Person {
    constructor(name, age) {
        this.name = name
        this.age = age
    }

    speak() {
        console.log(this)
    }
}

const p = new Person('lilei', 12)
// 增加一个对于speak函数的引用
const x = p.speak
x()  // undefined
```

所以想要获取正确的this需要借助`bind`，具体看代码

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>state</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Weather extends React.Component {
        constructor(props) {
            super(props)
            this.state = {isHot: true}
            // 在实例对象本身创建一个函数，并通过bind强行将this指向Weather的实例对象
            this.changeWeather = this.changeWeather.bind(this)
        }

        render() {
            const {isHot} = this.state
            console.log('render中的isHot', isHot)
            return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '寒冷'}</h1>
        }

        changeWeather() {
						console.log(this)  // Weather
        }
    }

    // 将类渲染成组件
    ReactDOM.render(<Weather/>, document.querySelector('.text'))
</script>
</body>
</html>
```

想要修改state的值，还要使用`setState`

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>state</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Weather extends React.Component {
        constructor(props) {
            super(props)
            this.state = {isHot: true}
            // 在实例对象本身创建一个函数，并通过bind强行将this指向Weather的实例对象
            this.changeWeather = this.changeWeather.bind(this)
        }

        render() {
            const {isHot} = this.state
            console.log('render中的isHot', isHot)
            return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '寒冷'}</h1>
        }

        changeWeather() {
            /**
             * 因为changeWeather是作为onClick的回调函数调用的，
             * 又因为类中的方法会在局部开启严格模式，所以this是undefined
             */
            this.setState({isHot: !this.state.isHot})
        }
    }

    // 将类渲染成组件
    ReactDOM.render(<Weather/>, document.querySelector('.text'))
</script>
</body>
</html>
```

#### state最佳用法

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>state</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Weather extends React.Component {
        // 相当于直接定义在实例上
        state = {isHot: true}

        render() {
            const {isHot} = this.state
            return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '寒冷'}</h1>
        }

        // 用箭头函数解决this指向问题
        changeWeather = () => {
            this.setState({isHot: !this.state.isHot})
        }
    }

    // 将类渲染成组件
    ReactDOM.render(<Weather/>, document.querySelector('.text'))
</script>
</body>
</html>

```

### props

>  跟vue中的props一样

基本使用

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>props</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Person extends React.Component {
        render() {
            const {name, sex, age} = this.props
            return (
                <ul>
                    <li>姓名: {name}</li>
                    <li>性别: {sex}</li>
                    <li>年龄: {age}</li>
                </ul>
            )
        }
    }

    // 将类渲染成页面
    ReactDOM.render(<Person name='zhangsan' age='12' sex='男'/>, document.querySelector('.text'))
</script>
</body>
</html>
```

将值一起传

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>props</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Person extends React.Component {
        render() {
            const {name, sex, age} = this.props
            return (
                <ul>
                    <li>姓名: {name}</li>
                    <li>性别: {sex}</li>
                    <li>年龄: {age}</li>
                </ul>
            )
        }
    }

    const person = {
        name: 'zhangsan',
        sex: '男',
        age: 12
    }
    
    // 将类渲染成页面
    ReactDOM.render(<Person {...person}/>, document.querySelector('.text'))
</script>
</body>
</html>
```

限制props

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>props</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Person extends React.Component {
        static propTypes = {
            name: PropTypes.string.isRequired,  // name是必须的
            sex: PropTypes.string,  // 限制sex是string
            age: PropTypes.number,  // 限制age是number
            speak: PropTypes.func   // 限制speak是一个函数类型
        }
        static defaultProps = {
            sex: '男',
            age: 18
        }

        render() {
            const {name, sex, age, speak} = this.props
            return (
                <ul>
                    <li>姓名: {name}</li>
                    <li>性别: {sex}</li>
                    <li>年龄: {age}</li>
                </ul>
            )
        }
    }

    const person = {
        name: 'lisan',
        sex: '男',
        age: 12,
        speak() {
        }
    }

    // 将类渲染成页面
    ReactDOM.render(<Person {...person}/>, document.querySelector('.text'))
</script>
</body>
</html>
```

函数式组件使用props

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>props</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建函数组件
    function Person(props) {
        const {name, age, sex} = props
        return (
            <ul>
                <li>姓名: {name}</li>
                <li>性别: {sex}</li>
                <li>年龄: {age}</li>
            </ul>
        )
    }

    Person.propTypes = {
        name: PropTypes.string.isRequired,  // name是必须的
        sex: PropTypes.string,  // 限制sex是string
        age: PropTypes.number,  // 限制age是number
        speak: PropTypes.func   // 限制speak是一个函数类型
    }
    Person.defaultProps = {
        sex: '男',
        age: 18
    }
    const person = {
        name: '张三',
        sex: '男',
        age: 12,
    }

    // 渲染成页面
    ReactDOM.render(<Person {...person}/>, document.querySelector('.text'))
</script>
</body>
</html>
```

### refs

> 跟vue中的ref一样，获取DOM(真实DOM)

基本使用

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>refs</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Demo extends React.Component {
        showInput1 = () => {
            const {input1} = this.refs
            alert(input1.value)
        }
        showInput2 = () => {
            const {input2} = this.refs
            alert(input2.value)
        }

        render() {
            return (
                <div>
                    <input ref="input1" type="text" placeholder="点击按钮提示数据"/>
                    &nbsp;
                    <button onClick={this.showInput1}>点击显示左侧内容</button>
                    &nbsp;
                    <input onBlur={this.showInput2} ref="input2" type="text" placeholder="失去焦点显示数据"/>
                </div>
            )
        }
    }

    // 渲染界面
    ReactDOM.render(<Demo/>, document.querySelector('.text'))
</script>
</body>
</html>
```

string类型的ref会被废弃

**回调形式的ref：**

原理就是将当前节点挂到实例上

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>refs</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Demo extends React.Component {
        showInput1 = () => {
            const {input1} = this
            alert(input1.value)
        }
        showInput2 = () => {
            const {input2} = this
            alert(input2.value)
        }

        render() {
            return (
                <div>
                    <input ref={c => this.input1 = c} type="text" placeholder="点击按钮提示数据"/>
                    &nbsp;
                    <button onClick={this.showInput1}>点击显示左侧内容</button>
                    &nbsp;
                    <input onBlur={this.showInput2} ref={c => this.input2 = c} type="text"
                           placeholder="失去焦点显示数据"/>
                </div>
            )
        }
    }

    // 渲染界面
    ReactDOM.render(<Demo/>, document.querySelector('.text'))
</script>
</body>
</html>
```

 **createRef形式：**

跟vue3中的ref一样   

React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点，该容器是「专人专用」

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>refs</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类组件
    class Demo extends React.Component {
        myRef1 = React.createRef()
        myRef2 = React.createRef()
        showInput1 = () => {
            alert(this.myRef1.current.value)
        }
        showInput2 = () => {
            alert(this.myRef2.current.value)
        }

        render() {
            return (
                <div>
                    <input ref={this.myRef1} type="text" placeholder="点击按钮提示数据"/>
                    &nbsp;
                    <button onClick={this.showInput1}>点击显示左侧内容</button>
                    &nbsp;
                    <input onBlur={this.showInput2} ref={this.myRef2} type="text"
                           placeholder="失去焦点显示数据"/>
                </div>
            )
        }
    }

    // 渲染界面
    ReactDOM.render(<Demo/>, document.querySelector('.text'))
</script>
</body>
</html>
```

## 事件处理

1. 通过形如`onClick`指定事件处理函数(注意大小写)
   - React使用的是自定义(合成)事件，而不是使用原生DOM事件—为了更好的兼容性
   - React的事件是通过事件委托方式处理的(委托给组件的最外层的元素)—为了高效

2. 通过event.target得到发生事件的DOM元素

当事件与事件源是同一个元素的时候可以省略ref

### 非受控组件

> 像input这种是用户填写内容的组件就是非受控组件

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>非受控组件</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Login extends React.Component {
        username = React.createRef()
        password = React.createRef()

        handleSubmit = (event) => {
            event.preventDefault()
            const {username, password} = this
            alert(`你的用户名为：${username.current.value}，你的密码是：${password.current.value}`)
        }

        render() {
            return (
                <form onSubmit={this.handleSubmit}>
                    用户名：<input ref={this.username} type="text"/>
                    密码：<input ref={this.password} type="text"/>
                    <button>登陆</button>
                </form>
            )
        }
    }

    // 渲染页面
    ReactDOM.render(<Login/>, document.querySelector('.text'))
</script>
</body>
</html>
```

### 受控组件

就是vue中的双向数据绑定

输入类dom随着 输入，内容就同步到state中

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>非受控组件</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Login extends React.Component {
        // 初始化state
        state = {
            username: '',
            password: ''
        }
        // 保存username的回调
        saveUsername = (event) => {
            this.setState({username: event.target.value})
        }
        // 保存password的回调
        savePassword = (event) => {
            this.setState({password: event.target.value})

        }
        handleSubmit = (event) => {
            event.preventDefault()
            const {username, password} = this.state
            alert(`你的用户名为：${username}，你的密码是：${password}`)
        }

        render() {
            return (
                <form onSubmit={this.handleSubmit}>
                    用户名：<input onChange={this.saveUsername} type="text"/>
                    密码：<input onChange={this.savePassword} type="text"/>
                    <button>登陆</button>
                </form>
            )
        }
    }

    // 渲染页面
    ReactDOM.render(<Login/>, document.querySelector('.text'))
</script>
</body>
</html>
```

## 高阶函数—函数柯里化

> Tip: 如果注释折叠不起来。   在代码前后加`//#regios`与`//#endregion`

高阶函数：如果一个函数符合下面两个规范中的任何一个，那该函数就是高阶函数

1. 若A函数，接收的参数是一个函数。那么A就可以称之为高阶函数
2. 若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数

如:` arr.map(()=>{})`,` arr.reduce((preVal,newVal)=>{return preVal + newVal})`

函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式

如：

```javascript
function sum(a) {
    return (b) => {
        return (c) => {
            return a + b + c
        }
    }
}

console.log(sum(1)(2)(3))
```

改造上面的登陆

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>函数的柯里化</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Login extends React.Component {
        // 初始化state
        state = {
            username: '',
            password: ''
        }

        // 运用函数柯里化处理
        saveFormData = (dataType) => {
            return (event) => {
                this.setState({[dataType]: event.target.value})
            }
        }
        handleSubmit = (event) => {
            event.preventDefault()
            const {username, password} = this.state
            alert(`你的用户名为：${username}，你的密码是：${password}`)
        }

        render() {
            return (
                <form onSubmit={this.handleSubmit}>
                    用户名：<input onChange={this.saveFormData('username')} type="text"/>
                    密码：<input onChange={this.saveFormData('password')} type="text"/>
                    <button>登陆</button>
                </form>
            )
        }
    }

    // 渲染页面
    ReactDOM.render(<Login/>, document.querySelector('.text'))
</script>
</body>
</html>
```

## 生命周期钩子

### 旧版本

#### 正常的路线

1. constructor：构造器函数
2. componentWillMount：DOM将要挂载
3. render  (必须)： 挂载DOM
4. componentDidMount. (常用  ) ：DOM挂载完毕
5. componentWillUnmount ==> ReactDOM.unmountComponentAtNode  （常用）：将要卸载组件

#### 更新时的路线

1. shouldComponentUpdate ==>  setState()：控制组件更新的「阀门」，如果将它返回值设为false，DOM永远不会更新
2.  componentWillUpdate  ==> forceUpdate()「不让状态做出更改，就想让界面更新一下」：组件将要更新
3. render：更新DOM
4. componentDidUpdate：DOM更新完毕
5. componentWillUnmount ==> ReactDOM.unmountComponentAtNode：将要卸载组件

#### 在父组件更新子组件信息

子组件生命周期钩子

1. componentWillRecriveProps：子组件再次接受父组件的props
2. shouldComponentUpdate ==>  setState()：控制组件更新的「阀门」，如果将它返回值设为false，DOM永远不会更新
3.  componentWillUpdate  ==> forceUpdate()「不让状态做出更改，就想让界面更新一下」：组件将要更新
4. render：更新DOM
5. componentDidUpdate：DOM更新完毕
6. componentWillUnmount ==> ReactDOM.unmountComponentAtNode：将要卸载组件

![react生命周期(旧)](/Users/wangkun/Desktop/前端技术栈/React/尚硅谷react/react全家桶资料/02_原理图/react生命周期(旧).png)

### 新版本

与旧版本相比，废除了三个(will)，新增了两个(getDeriveStateFromProps、getSnapshotBeforeUpdate)

1. getDeriveStateFromProps：当state的值在任何时候都取决于props时用

2. getSnapshotBeforeUpdate： 可以用来获取DOM更新之前的信息

![react生命周期(新)](/Users/wangkun/Desktop/前端技术栈/React/尚硅谷react/react全家桶资料/02_原理图/react生命周期(新).png)

#### 重要的勾子

1. render：初始化渲染或更新重新渲染
2. componentDidMound：开启坚挺，发送ajax请求
3. componentWillUnmount：做一些收尾工作，如：清理定时器

## for循环key的问题

当状态中数据发生改变时，react会根据「新数据」生成「新虚拟DOM」

随后React进行「新虚拟DOM」与「就虚拟DOM」的diff比较，规则如下

1. 旧的虚拟DOM找到了与新虚拟DOM相同的key
   - 若虚拟DOM的内容没有变，直接使用之间的真实DOM
   - 若虚拟DOM的内容发生改变，生成新的真实DOM，随后替换页面之前的真实DOM

2. 旧的虚拟DOM没有找到与新虚拟DOM相同的key
   - 直接生成新的真实DOM，渲染到页面

### 使用index作为key会出现的问题

#### 性能的损失

如果在数组前面加入数据，会导致所有数据都重新生成真实DOM，无法享用key相同，内容不变直接用之前的真实DOM diff算法的特性。造成性能损失。

举例：

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>key问题</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Person extends React.Component {
        // 初始化state
        state = {
            person: [
                {id: 1, name: '小张', age: 18},
                {id: 2, name: '小红', age: 19}
            ]
        }

        // add函数
        add = () => {
            const {person} = this.state
            const p = {id: person.length + 1, name: '小王', age: 20}
            this.setState({person: [p, ...person]})
        }

        render() {
            return (
                <div>
                    <h1>key相关问题</h1>
                    <ul>
                        {this.state.person.map((item, index) => {
                            return <li key={index}>{`ID：${item.id}，名字：${item.name}，年龄：${item.age}`}</li>
                        })}
                    </ul>
                    <br/>
                    <br/>
                    <ul>
                        {this.state.person.map(item => {
                            return <li key={item.id}>{`ID：${item.id}，名字：${item.name}，年龄：${item.age}`}</li>
                        })}
                    </ul>
                    <button onClick={this.add}>点击增加小王</button>
                </div>
            )
        }
    }

    // 渲染页面
    ReactDOM.render(<Person/>, document.querySelector('.text'))
</script>
</body>
</html>
```

问题在于

```
初始数据：
{id: 1, name: '小张', age: 18},
{id: 2, name: '小红', age: 19}
初始虚拟DOM
<li key=0>小张---------18</li>
<li key=1>小红---------19</li>
更新后数据
{id: 3, name: '小王', age: 20},
{id: 1, name: '小张', age: 18},
{id: 2, name: '小红', age: 19}
更新后DOM
<li key=0>小王---------20</li>
<li key=1>小张---------18</li>
<li key=2>小红---------19</li>
```

我们发现，因为是在前面增加数据，所以导致新虚拟DOM的key都错位了，导致新旧虚拟DOM对不上，所有会把所有新虚拟DOM全都重新渲染成真实DOM

如果换成id为key值

```
初始数据：
{id: 1, name: '小张', age: 18},
{id: 2, name: '小红', age: 19}
初始虚拟DOM
<li key=1>小张---------18</li>
<li key=2>小红---------19</li>
更新后数据
{id: 3, name: '小王', age: 20},
{id: 1, name: '小张', age: 18},
{id: 2, name: '小红', age: 19}
更新后DOM
<li key=3>小王---------20</li>
<li key=1>小张---------18</li>
<li key=2>小红---------19</li>
```

这时新旧虚拟DOM的key就对上了，并且因为key为1、2的新旧虚拟DOM的内容没有变，所有用之前的真实DOM就可以，就将key为3的虚拟DOM渲染成真实DOM即可

#### 界面出现错乱

结构中还包含输入类DOM，会产生错误DOM更新

举例：

```go
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>key问题</title>
</head>
<body>
<div class="text"></div>
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react操作DOM库 -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入props类型检查库 -->
<script src="../js/prop-types.js"></script>
<!-- 引入babel库 -->
<script src="../js/babel.min.js"></script>

<script type="text/babel">
    // 创建类式组件
    class Person extends React.Component {
        // 初始化state
        state = {
            person: [
                {id: 1, name: '小张', age: 18},
                {id: 2, name: '小红', age: 19}
            ]
        }

        // add函数
        add = () => {
            const {person} = this.state
            const p = {id: person.length + 1, name: '小王', age: 20}
            this.setState({person: [p, ...person]})
        }

        render() {
            return (
                <div>
                    <h1>key相关问题</h1>
                    <ul>
                        {this.state.person.map((item, index) => {
                            return <li key={index}>{`ID：${item.id}，名字：${item.name}，年龄：${item.age}`}<input
                                type="text"/></li>
                        })}
                    </ul>
                    <br/>
                    <br/>
                    <ul>
                        {this.state.person.map(item => {
                            return <li key={item.id}>{`ID：${item.id}，名字：${item.name}，年龄：${item.age}`}<input
                                type="text"/></li>
                        })}
                    </ul>
                    <button onClick={this.add}>点击增加小王</button>
                </div>
            )
        }
    }

    // 渲染页面
    ReactDOM.render(<Person/>, document.querySelector('.text'))
</script>
</body>
</html>
```

因为diff算法会递归的比较key相同的虚拟DOM，所以以index为key，并且key为0的新虚拟DOM直接用旧虚拟DOM的input标签作为真实DOM，所以我们会发现，以index为key，后添加的小王后面input框中的内容是小张的信息

总结

1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作，会产生没有必要的真实DOM更新  ==> 界面效果没问题，但效率低
2. 如果结构中还包含输入类DOM，会产生错误DOM更新 ==> 界面会有问题
3. 如果不存在对数据逆序添加、逆序删除等破坏顺序的操作，仅用于渲染列表用于展示，使用index作为key是没有问题的

## react脚手架

### 脚手架

1. 用于帮助程序员快速创建一个基于xxx库的模版项目
   - 包含所有需要的配置(语法检查、jsx编译、devServer)
   - 下载好了所有相关的依赖
   - 可以直接运行一个简单效果

2. react提供了一个用于创建react项目的脚手架库`create-react-app`
3. 项目的整体技术架构为：react + webpack + es6 + eslint

4. 使用脚手架开发的项目特点：模块话、组件化、工程化

### 初始化项目

1. 全局安装react脚手架：`npm i create-react-app -g`
2. 创建react项目：`create-react-app hello-react`

3. 一个代理：在`package.json`中添加`"proxy":"http://localhost:5000"`

## React-router

下载依赖：`npm add react-router-dom`

### 路由式跳转最基本的路由

```react
<BrowserRouter>
    <Link to="/about">About</Link>
    <Link to="/home">Home</Link>
  	// NavLink 在点击的时候可以动态添加类名
		// <NavLink activeClassName="currentClick" className="list-group-item" to="/about">About</NavLink>
    // <NavLink activeClassName="currentClick" className="list-group-item" to="/home">Home</NavLink>
    <Route path="/about" component="About"></Route>
    <Route path="/home" component="Home"></Route>
</BrowserRouter>
```

使用时一般将`<BrowserRouter>`，包裹在App组件下

```react
const root = ReactDOM.createRoot(document.getElementById('root'))
root.render(
    <React.StrictMode>
        <BrowserRouter>
            <App/>
        </BrowserRouter>
    </React.StrictMode>
)
```

### 路由匹配效率问题

react会拿点击的路径跟所有路由都匹配一遍，通常情况下，path和component事一一对应关系

可以通过`Switch`就控制匹配到就不继续匹配了

```react
<Switch>
    <Route path="/about" component={About}></Route>
    <Route path="/home" component={Home}></Route>
</Switch>
```

### 解决样式丢失

1. `<link rel="stylesheet" href="/css/bootstrap.css"/>`：路径前面不写`.`
2. `<link rel="stylesheet" href="%PUBLIC_URL%/css/bootstrap.css"/>`：用脚手架自带的路径前缀
3. 用hash模式的路由

### 路由模糊匹配与严格匹配

react路由默认是模糊匹配，从前往后匹配

exact开启严格匹配，**如果路由正常跳转，不要开启严格匹配，嵌套组件会失效**

```java
<Switch>
    <Route exact path="/about" component={About}></Route>
    <Route exact path="/home" component={Home}></Route>
</Switch>
```

### 重定向

```java
<Switch>
    <Route exact path="/about" component={About}></Route>
    <Route exact path="/home" component={Home}></Route>
    <Redirect to="about"></Redirect>
</Switch>
```

### 嵌套路由

```java
function Home() {
    return (
        <div>
            <div>
                <ul className="nav nav-tabs">
                    <li>
                        <NavLink to="/home/news">News</NavLink>
                    </li>
                    <li>
                        <NavLink to="/home/message">Message</NavLink>
                    </li>
                </ul>
            </div>
            <Switch>
                <Route path="/home/news" component={News}></Route>
                <Route path="/home/message" component={Message}></Route>
                <Redirect to="/home/news"></Redirect>
            </Switch>
        </div>
    )
}
```

### 路由传参

#### params参数

传参

```java
render() {
    const {msgArr} = this.state
    return (
        <div>
            <ul>
                {
                    msgArr.map((item) => {
                        return <li key={item.id}>
                          	// 传参
                            <Link to={`/home/message/detail/${item.id}/${item.title}`}>{item.title}</Link>
                        </li>
                    })
                }
            </ul>
      			// 声明
            <Route path="/home/message/detail/:id/:title" component={Detail}></Route>
        </div>
    )
}
```

接收参数

```java
import {Component} from "react";

const DetailData = [
    {id: "01", content: "你好，中国"},
    {id: "02", content: "你好，黑龙江"},
    {id: "03", content: "你好，哈尔滨"},
]

class Detail extends Component {
    render() {
        console.log(this.props.match.params)
        // 接收参数
        const {id, title} = this.props.match.params
        const findResult = DetailData.find((item) => {
            return id === item.id
        })
        return (
            <ul>
                <li>id:{id}</li>
                <li>title:{title}</li>
                <li>content:{findResult.content}</li>
            </ul>
        )
    }
}

export default Detail
```

#### search参数

传递

```
import {Component} from "react";
import {Link, Route} from "react-router-dom";
import Detail from "./Detail";

class Message extends Component {
    state = {
        msgArr: [
            {id: "01", title: "消息1"},
            {id: "02", title: "消息2"},
            {id: "03", title: "消息3"}
        ]
    }

    render() {
        const {msgArr} = this.state
        return (
            <div>
                <ul>
                    {
                        msgArr.map((item) => {
                            return <li key={item.id}>
                            		// 以这种形式传递参数
                                <Link to={`/home/message/detail?id=${item.id}&title=${item.title}`}>{item.title}</Link>
                            </li>
                        })
                    }
                </ul>
                // 在这里不用声明
                <Route path="/home/message/detail" component={Detail}></Route>
            </div>
        )
    }
}

export default Message


```

接收

```java
import {Component} from "react";
import qs from 'qs'

const DetailData = [
    {id: "01", content: "你好，中国"},
    {id: "02", content: "你好，黑龙江"},
    {id: "03", content: "你好，哈尔滨"}
]

class Detail extends Component {
    render() {
        console.log(this.props.location.search)
        // 接收参数
        const {search} = this.props.location
        const {id, title} = qs.parse(search.slice(1))
        const findResult = DetailData.find((item) => {
            return id === item.id
        })
        return (
            <ul>
                <li>id:{id}</li>
                <li>title:{title}</li>
                <li>content:{findResult.content}</li>
            </ul>
        )
    }
}

export default Detail
```

#### state参数

```
import {Component} from "react";
import {Link, Route} from "react-router-dom";
import Detail from "./Detail";

class Message extends Component {
    state = {
        msgArr: [
            {id: "01", title: "消息1"},
            {id: "02", title: "消息2"},
            {id: "03", title: "消息3"}
        ]
    }

    render() {
        const {msgArr} = this.state
        return (
            <div>
                <ul>
                    {
                        msgArr.map((item) => {
                            return <li key={item.id}>
                            		// 传参
                                <Link to={{
                                    pathname: "/home/message/detail",
                                    state: {id: item.id, title: item.title}
                                }}>
                                    {item.title}
                                </Link>
                            </li>
                        })
                    }
                </ul>
                // 声明
                <Route path="/home/message/detail" component={Detail}></Route>
            </div>
        )
    }
}

export default Message


```

接收

```
import {Component} from "react";

const DetailData = [
    {id: "01", content: "你好，中国"},
    {id: "02", content: "你好，黑龙江"},
    {id: "03", content: "你好，哈尔滨"}
]

class Detail extends Component {
    render() {
        console.log(this.props)
        // 接收
        const {id, title} = this.props.location.state
        const findResult = DetailData.find((item) => {
            return id === item.id
        })
        return (
            <ul>
                <li>id:{id}</li>
                <li>title:{title}</li>
                <li>content:{findResult.content}</li>
            </ul>
        )
    }
}

export default Detail
```

### 编程式导航

```java
import {Component} from "react";
import {Link, Route} from "react-router-dom";
import Detail from "./Detail";

class Message extends Component {
    state = {
        msgArr: [
            {id: "01", title: "消息1"},
            {id: "02", title: "消息2"},
            {id: "03", title: "消息3"}
        ]
    }
    replaceShow = (id, title) => {
        this.props.history.replace("/home/message/detail", {id, title})
    }

    render() {
        const {msgArr} = this.state
        return (
            <div>
                <ul>
                    {
                        msgArr.map((item) => {
                            return <li key={item.id}>
                                <Link to={{
                                    pathname: "/home/message/detail",
                                    state: {id: item.id, title: item.title}
                                }}>
                                    {item.title}
                                </Link>
                                <button onClick={() => this.replaceShow(item.id, item.title)}>
                                    点击进行replace跳转
                                </button>
                            </li>
                        })
                    }
                </ul>
                <Route path="/home/message/detail" component={Detail}></Route>
            </div>
        )
    }
}

export default Message
```

### withRouter

```javascript
export default withRoutr(Header)
```

1. `withRoutr`可以加工一般组件，让一般组件具有路由组件所特有的API
2. `withRouter`的返回值是一个新组件

### BrowserRouter与HashRouter的区别

1. 底层原理不同：
   - BrowserRouter使用的是H5的history API，不兼容IE9及以下版本
   - HashRouter使用的是URL的哈希值
2. path表现形式不同
   - BrowserRouter的路径中没有#，例如：localhost:3000/demo/test
   - HashRouter的路径中有#，例如：localhost:3000/#/demo/test

3. 刷新后对路由state参数的影响
   - BrowserRouter没有任何影响，因为state保存在一个history对象中
   - HashRouter刷新后会导致理由state参数的丢失

4. 备注：HashRouter可以用于解决一些路径错误相关的问题

### 普通组件与路由组件的区别

1. 写法不同
   - `<Header/>`
   - `<Route path="/about" component={About}></Route>`
2. 存放的位置不同
   - compoments(普通组件)
   - pages(路由组件) 
3. 接收到的props参数不同
   - 普通话组件：写组件标签时，写什么就能收到什么
   - 路由组件
     - history
     - location
     - match

## react-router6

跟vue3一样

`routes/index.js`

```react
import About from "../pages/About";
import Home from "../pages/Home";
import {Navigate} from "react-router-dom";
import News from "../pages/Home/News";
import Message from "../pages/Home/Message";

export default [
    {
        path: '/about',
        element: <About/>
    },
    {
        path: '/home',
        element: <Home/>,
        children: [
            {
                path: 'news',
                element: <News/>
            },
            {
                path: 'message',
                element: <Message/>
            }
        ]
    },
    {
        path: '/',
        element: <Navigate to="/about"/>
    }
]
```

`App.js`

```react
import {NavLink, useRoutes} from "react-router-dom"
import Header from "./components/Header"
import routes from "./routes";

function App() {
    const element = useRoutes(routes)
    return (
        <div className="App">
            <div>
                <div className="row">
                    <div className="col-xs-offset-2 col-xs-8">
                        <Header/>
                    </div>
                </div>
                <div className="row">
                    <div className="col-xs-2 col-xs-offset-2">
                        <div className="list-group">
                            <NavLink className="list-group-item" to="/about">About</NavLink>
                            <NavLink className="list-group-item" to="/home">Home</NavLink>
                        </div>
                    </div>
                    <div className="col-xs-6">
                        <div className="panel">
                            <div className="panel-body">
                                {element}
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    )
}

export default App
```

### 嵌套路由

```react
import {NavLink, Outlet} from "react-router-dom";

function Home() {
    return (
        <div>
            <div>
                <ul className="nav nav-tabs">
                    <li>
                        <NavLink to="news">News</NavLink>
                    </li>
                    <li>
                        <NavLink to="message">Message</NavLink>
                    </li>
                </ul>
            </div>
            <Outlet/>
        </div>
    )
}

export default Home
```

### 路由传参

#### params传参

```react
// 传参
<Link to={`detail/${item.id}/${item.title}/${item.content}`}>
    {item.title}
</Link>

// 声明
{
    path: 'message',
    element: <Message/>,
    children: [
        {
            path: 'detail/:id/:title/:content',
            element: <Detail/>
        }
    ]
}

// 接收
const {id, title, content} = useParams()
```

#### search传参

```react
// 传参
<Link to={`detail?id=${item.id}&title=${item.title}&content=${item.content}`}>
    {item.title}
</Link>

// 声明
{
    path: 'message',
    element: <Message/>,
    children: [
        {
            path: 'detail',
            element: <Detail/>
        }
    ]
}

// 接收参数
const [search] = useSearchParams()
const id = search.get('id')
const title = search.get('tile')
const content = search.get('content')
```

#### state传参

```react
// 传参
<Link
    to={`detail?id`}
    state={{
        id: item.id,
        title: item.title,
        content: item.content
    }}
>
    {item.title}
</Link>

// 声明
{
    path: 'message',
    element: <Message/>,
    children: [
        {
            path: 'detail',
            element: <Detail/>
        }
    ]
}

// 接收参数
const {state: {id, title, content}} = useLocation()
```

### 编程式导航 

```react
const navigate = useNavigate()

<button onClick={() => showDetail(item)}>点击跳转详情页</button>

function showDetail(item) {
    navigate('detail', {
        replace: true,
        state: {id: item.id, title: item.title, content: item.content}
    })
}
```

## redux

### 创建文件夹

1. `redux`

2. `redux/count_reducer.js`

   ```jsx
   const initState = 0
   
   export default function countReducer(preState = initState, action) {
       const {type, data} = action
       switch (type) {
           case 'increment':
               return preState + data
           case 'decrement':
               return preState - data
           default:
               return preState
       }
   
   }
   ```

3. `redux/store.js`

   ```jsx
   import {createStore} from "redux";
   import countReducer from './count_reducer'
   
   export default createStore(countReducer)
   ```

## 函数式组件 Hook

### useState

用于设置与更新state

```jsx
import { useState } from 'react'

function UseState() {
  // 创建state
	const [count, setCount] = useState(10)

	// 点击+1
	const add = () => {)
  	// 设置state，是替代
		setCount(count + 1)
	}
	return (
		<div>
			<h1>当前和为{count}</h1>
			<h1>当前和为{count}</h1>
			<button onClick={add}>点击加1</button>
		</div>
	)
}
export default UseState

```

### useEffect

常用于模拟生命周期

#### 第二个参数(数组)，不传参数，相当于 `componentDIdMount`

```jsx
import React, { useEffect } from 'react'

export default function UseEffect() {
    useEffect(() => {
        console.log(1)  // 只会输出一个1
        return () => {

        }
    }, [])

    return (
        <div>useEffct</div>
    )
}
```

#### 在`[]`，传入参数

**不仅页面初始化会执行一次，之后只要数组中依赖发生更新，回调函数中的代码都会执行一次**

**回调函数中返回的函数在依赖发生更新或者组件销毁的时候会执行**

```jsx
import React, { useEffect, useState } from 'react'

export default function UseEffect() {
    const [name, setName] = useState("梅西")
    useEffect(() => {
        console.log(1)
        return () => {
            console.log("组件销毁了")
        }
    }, [name])

    return (
        <div>
            <h1>useEffct</h1>
            <h3>{name}</h3>
            <button onClick={() => { setName("C罗") }}>click</button>
        </div>
    )
}

// 控制台输出（点击click之后）
1
组件销毁了
1
```

#### 可以多次调用

```jsx
import React, { useEffect, useState } from 'react'

export default function UseEffect() {
    const [name, setName] = useState("梅西")
    useEffect(() => {
        console.log(1)
        return () => {
        }
    }, [name])
    useEffect(() => {


        return () => {
            console.log("组件销毁了")
        }
    }, [])

    return (
        <div>
            <h1>useEffct</h1>
            <h3>{name}</h3>
            <button onClick={() => { setName("C罗") }}>click</button>
          </div>
    )
}

// 控制台输出(点击click)
1
1
// 当组件销毁时
组件销毁了
```

### useLayoutEffect

如果想进行DOM操作就使用这个函数

区别：

1. useEffect时DOM已经渲染到页面了
2. useLayoutEffect是生成了DOM树，但是没和渲染树结合呢

### useRef

跟vue3中ref使用方法一样

1. 获取DOM节点

   ```jsx
   <div ref={nameNode} onClick={getName}>wangkun</div>
   
   const nameNode = useRef(null)
   ```

2. 设置响应式数据

   注意：react是单向数据流！使用ref设置的数据更新后不会渲染到页面上

   ```jsx
   import React, { useCallback, useState, useRef } from 'react'
   
   export default function UseEffect() {
       // useRef
       let count = useRef(0)
       function add() {
           count.current++
           console.log(count.current)
       }
       return (
           <div>
               <h1>useEffct</h1>
               <div>{count.current}</div>
               <button onClick={add}>点击加1</button>
           </div>
       )
   }
   
   ```

### useReducer与useContext

模拟redux

```jsx
// App.jsx
import Child1 from "./pages/Child1";
import Child2 from "./pages/Child2";
import Child3 from "./pages/Child3";
import {createContext, useReducer} from "react";

// 初始化状态
const initialState = {
    a: "111",
    b: "222"
}

function reducer(prevState, action) {
    let newState = {...prevState}
    switch (action.type) {
        case "changeA":
            newState.a = action.value
            return newState
        case "changeB":
            newState.b = action.value
            return newState
        default:
            return newState
    }
}

export let GlobalContext = createContext(null)
export default function App() {
    const [state, dispatch] = useReducer(reducer, initialState)
    return (
        <GlobalContext.Provider value={
            {state, dispatch}
        }>
            <div>
                <Child1/>
                <Child2/>
                <Child3/>
            </div>
        </GlobalContext.Provider>
    )
}
```

```jsx
// Child1.jsx

import {useContext} from "react";
import {GlobalContext} from "../../App";

export default function Child1() {
    const {dispatch} = useContext(GlobalContext)
    return (
        <div>
            <button onClick={() => {
                dispatch({
                    type: "changeA",
                    value: "2222"
                })
            }}>
              改变a
            </button>
            <button onClick={() => {
                dispatch({
                    type: "changeB",
                    value: "3333"
                })
            }}>
              改变b
            </button>
        </div>
    )
}
```

```jsx
// Child2.jsx
import {useContext} from "react";
import {GlobalContext} from "../../App";

export default function Child2() {
    const {state} = useContext(GlobalContext)
    console.log(state)
    return (
        <div>
            <div>childA-{state.a}</div>
        </div>
    )
}
```

```jsx
// Child3.jsx
import {useContext} from "react";
import {GlobalContext} from "../../App";

export default function Child3() {
    const {state} = useContext(GlobalContext)
    return (
        <div>
            <div>childB-{state.b}</div>
        </div>
    )
}
```

### 自定义Hooks

就是将函数声明到组件外侧，并用use开头

## 插槽

1. 为了复用
2. 减少父子组件通信

### 默认插槽

父组件

```jsx
return (
    <div>
        <Child1>
            我是王琨
        </Child1>
    </div>
);
```

子组件

```jsx
return (
    <div>
        Child1
        {props.children}
    </div>
);
```

### 传多个结构

相当于vue的具名插槽

App.jsx

```jsx
return (
    <div>
        <Child1>
            <div>
                我是小明
            </div>
            <div>
                我是小红
            </div>
            <div>
                我是小张
            </div>
        </Child1>
    </div>
)
```

子组件

```jsx
return (
    <div>
        Child1
        {props.children[1]} // 小红
        {props.children[0]} // 小明
        {props.children[2]} // 小张
    </div>
)
```

## 解决样式冲突

相当于vue中的 scope

### 用CSSModule

1. css文件命名为`xxx.module.css`

2. 在组件中使用

   ```jsx    
   import style from './css/Film.module.css'
   
   <div className="style.box"></div>
   ```

3. 在CSSModule声明全局变量

   ```jsx
   :global(.box) { 
   	color: red;
   }
   ```

## immutable

### `...`扩展运算符

只能进行一层的深拷贝

```javascript
const obj = {
	name: 'xiaohong',
	arr: [1, 2, 3]
}

const obj2 = {
  ...obj
}

obj2.arr.splice(1, 1)
console.log(obj.arr) // [1, 3]
```

### JSON

数据中不能有函数和undefined，并且性能不好

### 下载依赖

`npm i immutable`

### 使用

```js
const obj = {
    name: '小名',
    age: 100
}

const oldImmutable = Map(obj)
const newImmutable = oldImmutable.set("name", "小红")
console.log("oldImmutable-----:", oldImmutable.get("name"))
console.log("newImmutable-----:", newImmutable.get("name"))

// 将immutable对象转换回js对象
console.log(oldImmutable.toJS(), newImmutable.toJS())
```

## Styled-components

### 下载依赖

` npm i styled-components `

### 使用

```jsx
import React from 'react'
import styled from "styled-components";

function App() {
    const StyledDiv = styled.div`
      background: red;

      li {
        height: 30px;
        line-height: 30px;
        background: blue;
      }
    `
    return (
        <StyledDiv>
            <ul>
                <li>王琨</li>
                <li>程浩</li>
                <li>徐琦</li>
            </ul>
        </StyledDiv>
    )
}

export default App
```

## Redux

### 一、创建文件

#### 模块一

1. NumStatus/index.ts

   ```typescript
   const store = {
       state: {
           num: 20
       },
       actions: {
           add1(newState: { num: number }) {
               newState.num++
           },
           add10(newState: { num: number }, action: { type: string, val: number }) {
               newState.num += action.val
           }
       },
       actionNames: {}
   }
   let actionNames = {}
   for (let key in store.actions) {
       actionNames[key] = key
   }
   store.actionNames = actionNames
   
   export default store
   ```

2. NumStatus/reducer.ts

   ```typescript
   import handleNum from './index.ts'
   
   
   const reducer = (state = { ...handleNum.state }, action: { type: string, val: number }) => {
       let newState = JSON.parse(JSON.stringify(state))
       // switch (action.type) {
       //     case 'add1':
       //         handleNum.actions.add1(newState)
       //         break
       //     case 'add10':
       //         handleNum.actions.add10(newState, action)
       //     default:
       //         break
       // }
       
       //  解放双手  以后就不用动这里的东西了
       for (let key in handleNum.actionNames) {
           console.log(typeof key);
           if (action.type === key) {
               // console.log(key);
               handleNum.actions[key](newState, action)
               break
           }
       }
   
       return newState
   }
   
   export default reducer
   ```

#### 模块二

1. StrStatus/index.ts

   ```typescript
   const store = {
       state: {
           str: 'Hello'
       },
       actions: {
           addStr(newState: { str: string }, action: { type: string, val: string }) {
               newState.str = newState.str + action.val
           }
       },
       actionNames: {}
   }
   let actionNames = {}
   for (let key in store.actions) {
       actionNames[key] = key
   }
   store.actionNames = actionNames
   export default store
   ```

2. StrStatus/index.ts

   ```typescript
   import handleStr from './index.ts'
   
   
   const reducer = (state = { ...handleStr.state }, action: { type: string, val: number }) => {
       let newState = JSON.parse(JSON.stringify(state))
       // switch (action.type) {
       //     case 'addStr':
       //         handleStr.actions.addStr(newState,action)
       //         break
       //     default:
       //         break
       // }
       for (let key in handleStr.actionNames) {
           if (action.type === key) {
               handleStr.actions[key](newState, action)
           }
       }
       return newState
   }
   
   export default reducer
   ```

#### 整合模块

```typescript
import { legacy_createStore, combineReducers } from 'redux'
import NumStatusReducer from './NumStatus/reducer.ts'
import StrStatusReducer from './StrStatus/reducer.ts'
// import  from ''
const reducers = combineReducers({
    NumStatusReducer,
    StrStatusReducer
})
const store = legacy_createStore(reducers)

export default store
```

### 二、用标签包裹App组件

```tsx
ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
    <Provider store={store}>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </Provider>
)
```

### 三、使用数据

```tsx
import { useDispatch, useSelector } from 'react-redux'



const About = () => {
    const dispatch = useDispatch()

    const { num, str } = useSelector((state: RootType) => ({
        num: state.NumStatusReducer.num,
        str: state.StrStatusReducer.str
    }))
    // 加1
    const add1 = () => {
        dispatch({ type: 'add1' })
    }
    // 加10
    const add10 = () => {
        dispatch({ type: 'add10', val: 10 })
    }
    // 改变str
    const changeStr = () => {
        dispatch({ type: 'addStr', val: ' Word' })
    }
    return (
        <div>
            <p>{num}</p>
            <button onClick={add1}>点击加1</button>
            <button onClick={add10}>点击加10</button>

            <p>{str}</p>
            <button onClick={changeStr}>点击改变str</button>
        </div>
    );
};

export default About;
```

### 异步

`npm i redux-thunk`

store/index.ts

```typescript
import { legacy_createStore, combineReducers,applyMiddleware } from 'redux'
import reduxThunk from 'redux-thunk'
import NumStatusReducer from './NumStatus/reducer.ts'
import StrStatusReducer from './StrStatus/reducer.ts'
// import  from ''
const reducers = combineReducers({
    NumStatusReducer,
    StrStatusReducer
})
const store = legacy_createStore(reducers,applyMiddleware(reduxThunk))

export default store
```

NumStatus/index.ts

```typescript
const store = {
    state: {
        num: 20
    },
    actions: {
        add1(newState: { num: number }) {
            newState.num++
        },
        add10(newState: { num: number }, action: { type: string, val: number }) {
            newState.num += action.val
        }
    },
 		// 异步的逻辑
    asyncAction: {
        asyncAdd1(dispatch: Function) {
            setTimeout(() => {
                dispatch({ type: 'add1' })
            }, 1000)
        }
    },
    actionNames: {}
}
let actionNames = {}
for (let key in store.actions) {
    actionNames[key] = key
}
store.actionNames = actionNames

export default store
```

view/About/index.tsx

```typescript
// 点击等一秒再加1
const add1 = () => {
    dispatch(NumStatus.asyncAction.asyncAdd1)
}
```



## React踩坑

### img不显示

> 通过img标签的src属性引入图片时，相对路径经过页面渲染后变成了相对于项目的根目录路径。

**两种解决方案：**

1. import引入：

   ```javascript
   import background from '@/assets/images/background.webp'
   
   <img src={background} alt="背景图片"/>
   ```

2. require引用：

   ```jsx
   <img src={require("@/assets/images/background.webp")} alt="背景图片"/>
   ```

### 渲染目标不能是对象

> Uncaught Error: Objects are not valid as a React child (found: object with keys {element}). If you meant to render a collection of children, use an array instead.

**原因：**

```jsx
function App() {
    const element = useRoutes(routes)
    return (
        {element}
    )
}
```

**解决办法：**

```jsx
function App() {
    const element = useRoutes(routes)
    return (
        <div>
            {element}
        </div>
    )
}
```























