---
title: react学习笔记
tag: react
keywords: react
categories: 前端框架
---
## 一、react的基本使用



#### 1.react的安装：

安装命令： npm i react react-dom

- react包是核心，提供创建元素、组建等功能
- react-dom包提供DOM相关功能

#### 2.react的使用

- 引入react和react-dom
- 创建react元素
  - const title = react.createElement('h1', null, 'hello react')
  - 第一个参数：创建的元素名称
  - 第二个参数： 元素的属性
  - 第三个和以后的参数： 元素的子节点
- 渲染react元素到页面中
  - ReactDom.render(title, document.querySelector('#app') )
  - 第一个参数：要渲染的元素
  - 第二个参数：DOM对象，用于指定渲染到页面中的位置

## 二、react脚手架的使用

#### 1.使用react脚手架初始化项目

- 命令：npx create-react-app my-app

#### 2.npx命令介绍

目的：提升包内提供的命令行工具的使用体验。无需安装脚手架包，就可以直接使用这个包提供的命令

## 三、JSX语法的基本使用

#### 1.使用步骤

- 使用JSX语法创建react元素

```react
  const title = <h1>hello JSX<h1>
  ```

- 使用ReactDOM.render()方法渲染元素到页面中

```react
  ReactDOM.render(title, document.querySelector('#root'))
  ```

#### 2.为什么脚手架中可以使用JSX语法

create-react-app脚手架中默认有@babel/preset-react，会对项目中的JSX语法进行编译

#### 3.注意点

- 元素的属性名使用驼峰命名法
- 特殊属性名： class -> className, for -> htmlFor, table index -> tabIndex
- 没有子节点的React元素可以用/>结束
- 推荐：使用小括号包裹JSX，从而避免js中的自动插入分号陷阱

## 四、JSX中使用javascript表达式

- 数据存储在js中

- 语法： { javascript表达式 }

```react
  const name = 'jack'
  const div = (
  	<div>hello {name}</div>
  )
  ```

- 注意点

  - 单大括号中可以使用任意的JavaScript表达式
  - JSX自身也是JS表达式，可以在单大括号中使用
  - js中的对象不是表达式，不能直接在单大括号中使用，一般只会出现在style属性中
  - 不能在{}中出现语句（如if/for语句等 ）

## 五、JSX的条件渲染

- 场景：loading效果

- 条件渲染：根据条件渲染特定的JSX结构

- 可以使用if/else或三元运算符或逻辑于运算符来实现

```react
  const isLoading = false
  const loadData = () => {
    return isLoading ?  (<div>加载中...</div>) : (<div>加载完成</div>)
  }
  const title = (
    <div>{loadData()}</div>
  )
  ```

## 六、JSX的列表渲染

- 如果要渲染一组数据，应该使用数组的map方法

- 渲染列表时应该添加key属性，key属性的值应该要保证唯一

- 尽量避免使用索引作为key

```react
  const songs = [
    {id: 1, name: '痴心绝对'},
    {id: 2, name: '童话'},
    {id: 3, name: '演员'},
  ]
  const title = (
    <ul>
      {songs.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  )
  ```

## 七、JSX的样式处理

1. 行内样式
2. 类名

```react
import './index.css'
const name = 'kobe'

const title = (
  <h1 className="title" style={{color: 'red', backgroundColor: 'blue'}}>
    我是{name}
  </h1>
)
```

## 八、react组件的两种创建方式

#### 1.使用函数创建组件

- 使用JS中的函数创建的组件叫做函数组件

- 函数组件必须有返回值

- 组件名称必须以大写字母开头，react据此区分组件和普通的react元素

- 使用函数名作为组件标签名

```react
  function Hello() {
  	return(
  		<div>这是我的第一个函数组件</div>
  	)
  }
  ReactDOM.render(<Hello/>, document.getElementById('root'))
  ```

#### 2.使用类创建组件

- 类组件：使用ES6的class创建的组件

- 约定1：类名称必须以大写字母开头

- 约定2：类组件应该继承React.Component父类，从而可以使用父类中提供的方法和属性

- 约定3：类组件必须提供render（）方法

- 约定4：render（）方法必须有返回值，表示该组件的结构

```react
  class Hello extends React.Component {
  	render() {
  		return <div>我是类组件<div>
  	}
  }
  ReactDOM.render(<Hello/>, document.getElementById('root'))
  ```

#### 3.抽离为独立的js模块

1. 创建Hello.js

2. 在Hello.js中导入react

3. 创建组件（函数或类组件）

4. 在Hello.js中到处该组件

5. 在index.js中导入Hello组件

6. 渲染组件

```react
  // Hello.js
  import React from 'react'
  class Hello extends React.Component {
    render() {
      return <div>我是Hello组件</div>
    }
  }
  // index.js
  import Hello from './Hello'
  ReactDOM.render(<Hello/>, root)
```

## 九、react事件处理

- react事件绑定语法与DOM事件语法相似

- 语法：on + 事件名称 = {事件处理程序}，比如： onClick = { () => {} }

- 注意：react事件才用驼峰命名法

- 类组件中绑定事件

```react
  class App extends React.Component {
    handleClick() {
      console.log('click')
    }
  	render() {
      return <button onClick={this.handleClick}>点击</button>
    }
  }
  ```

- 函数组件中绑定事件

```react
  function App () {
    handleClick() {
      console.log('click')
    }
    return <button onClick={handleClick}>点击</button>
  }
  ```

- 事件对象

  - 可以通过事件处理程序的参数获取到事件对象

  - react中的事件对象叫做：合成事件（对象）

  - 合成事件：兼容所有浏览器，无需担心跨浏览器兼容性问题

  ```react
    class Hello extends React.Component {
      handleClick(e) {
        e.preventDefault()
      }
      render() {
        return <a onClick={this.handleClick} href="http://www.baidu.com">百度一下</a>
      }
    }
    ```

## 十、有状态组件和无状态组件

- 函数组件又叫做无状态组件，类组件又叫做有状态组件
- 状态（state）即数据
- 函数组件没有自己的状态，只负责数据展示（静）
- 类组件有自己的状态，负责更新UI，让页面动起来

## 十一、组件中的state和setState

#### 1.state的基本使用

- 状态即数据

- 状态是私有的，只能在组件内部使用

- 通过this.state来获取状态

```react
  class Hello extends React.Component {
    // constructor() {
    //  super(); //
    //  this.state = {
    //    count: 1
    // }
    // }
    // 简写
    state = {
  		count: 1
    }
    render() {
      return <h2>计数器： {this.state.count}</h2>
    }
  }
  ```

#### 2.setState()修改状态

- 状态是可变的

- 语法：this.setState({要修改的数据})

- 注意：不要直接修改state的值

- setState()作用：修改state、更新UI

```react
  this.setState({
    count: this.state.count + 1
  })
  ```


## 十二、事件绑定this指向

1. 利用箭头函数改变this指向

  ```react
     class Hello extends React.Component {
     	state = {
         count: 1
       }
     	render() {
         return (
         	<div>
           	<h2>计数器：{this.state.count}</h2>
             <button onClick={() => this.handleClick()}>+1</button>
           </div>
         )
       }
     	handleClick() {
         this.setState({
           count: this.state.count + 1
         })
       }
     }
     ```

2. 利用bind改变this指向

  ```react
     class Hello extends React.Component {
     	constructor() {
         this.state = {
     			count: 1
         }
         this.handleClick = this.handleClick.bind(this)
       }
     	render() {
     		return (
     			<div>
           	<h2>计数器：{this.state.count}</h2>
             <button onClick={this.handleClick}>-1</button>
           </div>
     		)
     	}
     	handleClick() {
     		this.setState({
           count: this.state.count - 1
         })
     	}
     }
     ```

3. class的箭头函数实例方法（推荐方式，脚手架配置的babel对这种语法进行了转换）

  ```react
     class Hello extends React.Component {
     	constructor() {
         this.state = {
     			count: 1
         }
         this.handleClick = this.handleClick.bind(this)
       }
     	render() {
     		return (
     			<div>
           	<h2>计数器：{this.state.count}</h2>
             <button onClick={this.handleClick}>-1</button>
           </div>
     		)
     	}
     	handleClick = () => {
     		this.setState({
           count: this.state.count - 1
         })
     	}
     }
     ```

## 十三、表单处理

#### 1.受控组件（常用）

其值收到react控制的表单元素

实现一个受控组件（类似于vue中的v-model）：

1.表单value绑定state中的状态

2.监听表单的change事件，修改state中的状态

```react
class Hello extends React.Component {
	state = {
    text: ''
  }
	render() {
    return <input type="text" value={this.state.text} onClick={ this.inputChange }></input>
  }
	inputChange = e => {
    this.setState({
      text: e.target.value
    })
	}
}
```

#### 2.非受控组件（不常用）

- 通过React.createRef()方法创建ref对象

```react
  constructor() {
    super()
    this.txtRef = React.createRef()
  }
  ```

  

- 表单使用ref对象

```react
  render() {
    return (
    	<div>
      	<input type="text" ref={ this.txtRef }></input>
        <button onClick={ this.getValue }>获取value</button>
      </div>
    )
  }
  ```

  

- 通过ref对象获取表单的值

```react
  getValue = () => {
  	console.log(this.txtRef.current.value)
  }
  ```

## 十四、组件通讯

#### 1.组件的props

- props的作用： 与vue的props类似，父组件向子组件传递数据，子组件用来接收数据

- 接收数据：函数组件直接用props，类组件用this.props（和作用域有关，类组件的props加在实例上，函数组件直接通过参数接收）

```react
   // 1.函数组件props
  const Hello = props => {
    return (
      <h2>func props: { props.name }</h2>
    )
  }
  // 2.类组件props
  class Hello extends React.Component {
    render() {
      return (
        <h2>class props: { this.props.name }</h2>
      )
    }
  }
  
  ReactDOM.render(<Hello name="component props" />, document.querySelector('#root'))
  ```

- 特点：

  - 可以给组件穿任意类型的数据（数组、对象、jsx表达式、函数等）
  - props只读属性，只能读取属性的值，无法修改对象
  - ⚠️在constructor中使用props时，需要接收props并传递给super，否则无法在构造函数中获取到props

#### 2.父组件传递数据给子组件

```react
class Parent extends React.Component {
  state = {
    name: 'kobe'
  }
  render() {
    return (
      <div>
        父组件
        <Child name={ this.state.name }></Child>
        </div>
    )
  }
}
class Child extends React.Component {
  render() {
    return (
      <div>父组件传递给子组件的数据: { this.props.name}</div>
    )
  }
}
ReactDOM.render(
  <Parent/>,
  document.querySelector("#root")
);
```

#### 3.子组件传递数据给父组件

思路：父组件提供回调函数，同时将回调通过props传递给子组件，子组件通过props接收回调，执行回调并将需要传递的数据作为回调的参数传递给父组件

```react
class Father extends React.Component {
  state = {
    childMsg: ''
  }
  render() {
		return (
    	<div>
      	<h2>子组件传递的数据: { this.state.childMsg }</h2>
        <Child getMsg={ this.getChildMsg }></Child>
      </div>
    )
  }
	getChildMsg(childMsg) {
		this.setState({
      childMsg:  childMsg
    })
  }
}
class Son extends React.Component {
	state = {
		childMsg: 'child'
  }
	render() {
		return (
    	<button onClick={ this.emitMsg }></button>
    )
  }
	emitMsg = () => {
		this.props.getMsg(this.state.childMsg)
  }
}
```

注意：此处在Child组件中，render函数的onClick上不能直接调用this.props.getMsg()，而要在抽离到实例方法中。原因：react会在props和state改变的时候调用render对DOM进行diff后更新渲染，如果在render中再次调用getMsg修改state，会陷入死循环并报错

#### 4.兄弟组件通讯

- 将共享状态提升到最近的公共的父组件中，又公告父组件管理这个状态

- 父组件：提供共享状态、提供操作共享状态的方法

- 子组件：通过props获取共享状态和操作共享状态

```react
  class Parent extends React.Component {
    // 状态提升
    state = {
      count: 1
    }
    render() {
      return (
        <div>
          <Child1 count={ this.state.count }></Child1>
          <Child2 increment = { this.increment}></Child2>
        </div>
      )
    }
    // 提供修改状态的方法
    increment = () => {
      this.setState({
        count: this.state.count + 1
      })
    }
  }
  
  const Child1 = props => {
    return (
      <h2>计数器: { props.count }</h2>
    )
  }
  
  const Child2 = props => {
    const increment = () => {
      props.increment()
    }
    return (
      <button onClick={ increment }>+1</button>
    )
  
  }
    ReactDOM.render(
      <Parent/>,
      document.querySelector("#root")
    )
  ```


#### 5.组件树传递数据

- Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。

- 使用 context, 我们可以避免通过中间元素传递 props：

```react
  // Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
  // 为当前的 theme 创建一个 context（“light”为默认值）。
  const ThemeContext = React.createContext('light');
  class App extends React.Component {
    render() {
      // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
      // 无论多深，任何组件都能读取这个值。
      // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
      return (
        <ThemeContext.Provider value="dark">
          <Toolbar />
        </ThemeContext.Provider>
      );
    }
  }
  
  // 中间的组件再也不必指明往下传递 theme 了。
  function Toolbar() {
    return (
      <div>
        <ThemedButton />
      </div>
    );
  }
  
  class ThemedButton extends React.Component {
    // 指定 contextType 读取当前的 theme context。
    // React 会往上找到最近的 theme Provider，然后使用它的值。
    // 在这个例子中，当前的 theme 值为 “dark”。
    static contextType = ThemeContext;
    render() {
      return <Button theme={this.context} />;
    }
  }
  ```

- 使用方法：

  - 1.React.createContext方法创建context

  ```react
    const MyContext = React.createContext(defaultValue)
    // 创建一个 Context 对象。当 React 渲染一个订阅了这个 Context 对象的组件，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。
    // 只有当组件所处的树中没有匹配到 Provider 时，其 defaultValue 参数才会生效。此默认值有助于在不使用 Provider 包装组件的情况下对组件进行测试。注意：将 undefined 传递给 Provider 的 value 时，消费组件的 defaultValue 不会生效
    // or
    const { Provider, Consumer } = React.creactContext()
    ```

  - 2.使用Context.Provider提供数据

  ```react
    <MyContext.Provider value={/* 某个值 */} />
    // or
    <Provider value={} />
    // 每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化。
    
    // Provider 接收一个 value 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
    
    // 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染。Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。
    
    
    ```

  - value提供为对象时注意事项：

  - 因为 context 会使用参考标识（reference identity）来决定何时进行渲染，这里可能会有一些陷阱，当 provider 的父组件进行重渲染时，可能会在 consumers 组件中触发意外的渲染。举个例子，当每一次 Provider 重渲染时，以下的代码会重渲染所有下面的 consumers 组件，因为 `value` 属性总是被赋值为新的对象：

  ```react
    class App extends React.Component {
      render() {
        return (
          <MyContext.Provider value={{something: 'something'}}>
            <Toolbar />
          </MyContext.Provider>
        );
      }
    }
    ```

  - 为了防止这种情况，将 value 状态提升到父节点的 state 里：

  ```react
    class App extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          value: {something: 'something'},
        };
      }
    
      render() {
        return (
          <MyContext.Provider value={this.state.value}>
            <Toolbar />
          </MyContext.Provider>
        );
      }
    }
    ```

  - 3.消费数据

    - 方式1：Class.contextType

    ```react
       // 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象。此属性能让你使用 this.context 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。
      class MyClass extends React.Component {
        static contextType = MyContext;
        render() {
          let value = this.context;
          /* 基于这个值进行渲染工作 */
        }
      }
      ```

    - 方式2：Context.Consumer

    ```react
      // 一个 React 组件可以订阅 context 的变更，此组件可以让你在函数式组件中可以订阅 context。
      
      // 这种方法需要一个函数作为子元素（function as a child）。这个函数接收当前的 context 值，并返回一个 React 节点。传递给函数的 value 值等价于组件树上方离这个 context 最近的 Provider 提供的 value 值。如果没有对应的 Provider，value 参数等同于传递给 createContext() 的 defaultValue。
      <MyContext.Consumer>
        {value => /* 基于 context 值进行渲染*/}
      </MyContext.Consumer>
      // or 
      <Consumer>
      	{value => /* 基于 context 值进行渲染*/}
      </Consumer>
      ```

      

