---
title: React 学习笔记
date: 2018-08-13 10:59:31
tags: react
categories: web
---

## 安装

        npm install -g create-react-app
        create-react-app my-app
        cd my-app
        npm start

<!--more-->

## 项目文件夹结构分析

-   package.json:这个文件是管理下载的依赖包,在项目中经常用到的是"react"库，“react-dom”（将 jsx 语法渲染到 dom 中）在项目中最重要的命令是“start”启动项目，"bulid"的作用就是将项目打包。
-   public 文件夹
    -   index.html:项目的入口文件，引用了第三方类库啊，还可以引入 cdn。`<div id="root"></div>`是项目的总容器，所有的内容存储在这个容器中。这个容器有且只能有一个。
    -   favicon.ico:是浏览器 tab 上图标，也是这个项目的一个标志，也可以说是代表一个公司的标志。可以替换。
-   src 文件夹
    -   index.js:存放的是这个项目的核心内容，也就是我们的主要工作区域。其中，index.js 文件是和 index.html 进行关联的文件的唯一接口。 ReactDOM.render()的作用是将<App/>的内容渲染到根“root”中去。document.getElementById('root')中的"root"便是 index.html 中的"root"了，<App />便是引用页面内容了。在这里，<App />也可以写一些内容(结构,样式,逻辑)是整个项目的根组件，比如:`ReactDOM.render(<p >Hello World</p>, document.getElementById('root'))`;能够引用<App />的原因是文档内容的头部，有`import App from './App'`;内容，就是为了将 App.js 的内容引入到 index.js 文件中。
    -   App.js:该类是继承 react 提供的 component，`export default App;`是为了将 App 公开，index.js 才能够引用。App.js 继承了 component 的话，必须使用 render 进行渲染。return 的内容是类似于 html 结构的内容，就是`jsx`，jsx 语法是 react 的主要语法。内部的 div 的 className 是为了区分 html 语法的一个类名，这个是 div 的样式引用。在这个文件中，只能用一个 div 容器，如果在 div 的同级目录添加别的内容，便会报错。

## 定义组件（函数、类）

> 组件从概念上看就像是函数，它可以接收任意的输入值（称之为“props”），并返回一个需要在页面上展示的 React 元素。

-   函数定义组件

```
    function Welcome(props){
          return <h1>Hello,{props.name}</h1>;
        }
```

该函数是一个 React 组件，它接收一个"props"对象并返回了一个 React 元素。

-   类定义组件

        class Welcome extends Component{
          render(){
            rturn return <h1>Hello,{this.props.name}</h1>;
          }
        }

*   如何使用这个组件？  
    在需要这个组件的地方，必须定义或引入它。并且<Welcome />，如果要用到上述组件定义的 name 属性，即<Welcome name="Indge"/>来进行传值。  
    组件名称必须是以大写字母开头。

> Props 的只读性:所有的 React 组件必须像纯函数那样使用它们的 props。

## State & 生命周期

> 这种只在使用类定义组件时可以使用其它特性。例如局部状态，生命周期钩子。

### 为一个类添加局部状态和生命周期 （利用局部状态，则不需要在组件引用时再写 date={...}）

      class Demo extends Component{
          constructor(props){  //第二步：添加一个类构造函数来初始化状态 this.state
              super(props)     //注意：super(props)的目的，在constructor中可以使用this.props
              this.state = {date:new Date()}
          }
          componentDidMount(){ //当组件输出到DOM后执行
          this.timerID = setInterval(()=>{
              this.tick()
          },1000)
          }
          componentWillUnmount(){ //当组件被移除出DOM时执行
          clearInterval(this.timerID)
          }

          tick(){           //第三步：更新局部状态
              this.setState({
                  date:new Date()
              })
          }
          render(){
              return(
                <div>
                <h1>挖坑埋神经病,{this.props.list}</h1>
                <h2>现在时间为{this.state.date.toLocaleTimeString()}</h2>  //第一步：在 render() 方法中使用 this.state.date 替代 this.props.date
                </div>
              )
          }
      }

### setState 的正确使用

1. 不要直接更新状态。`this.state.comment = 'Hello';` 此代码不会重新渲染组件。应使用 setState()来更新组件，`this.setState({comment: 'Hello'});`，构造函数是唯一能够初始化 this.state 的地方。

2. 状态更新可能是异步的，this.props 和 this.state 是异步更新的，不能直接用来计算

```
    // Wrong
    this.setState({
      counter: this.state.counter + this.props.increment,
    });
```

请使用第二种形式的 setState() 来接受一个函数而不是一个对象。 该函数将接收先前的状态作为第一个参数，将此次更新被应用时的 props 做为第二个参数。

    // Correct
    this.setState((prevState, props) => ({
      counter: prevState.counter + props.increment
    }));

3. 状态更新合并

你的状态可能包含一些独立的变量：

```
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```

你可以调用 setState() 独立地更新它们：

```
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

## 事件绑定

> 由于类的方法默认不会绑定 this，因此在调用的时候如果忘记绑定，this 的值将会是 undefined。
> 通常如果不是直接调用，应该为方法绑定 this。绑定方式有以下几种：

1.  在构造函数中使用 bind 绑定 this

        class Button extends React.Component {
        constructor(props) {
            super(props);
            this.handleClick = this.handleClick.bind(this);
          }
          handleClick(){
            console.log('this is:', this);
          }
          render() {
            return (
              <button onClick={this.handleClick}>
                Click me
              </button>
            );
          }
        }

2.  在调用的时候使用 bind 绑定 this

        class Button extends React.Component {
          handleClick(){
            console.log('this is:', this);
          }
          render() {
            return (
              <button onClick={this.handleClick.bind(this)}>
                Click me
              </button>
            );
          }
        }

3.  在调用的时候使用箭头函数绑定 this

        class Button extends React.Component {
          handleClick(){
            console.log('this is:', this);
          }
          render() {
            return (
              <button onClick={()=>this.handleClick()}>
                Click me
              </button>
            );
          }
        }

4.  使用属性初始化器语法绑定 this(实验性)

        class Button extends React.Component {
          handleClick=()=>{
            console.log('this is:', this);
          }
          render() {
            return (
              <button onClick={this.handleClick}>
                Click me
              </button>
            );
          }
        }

## 条件渲染

1.  包含三目运算符的条件渲染
    例子：

        class HelloWord extends React.Component {
            constructor(props) {
                super(props);
                this.state = {
                    show: false
                }
            }

            // 渲染函数，this 指向实例本身
            render() {
                let display = this.display.bind(this)
                return <div>
                    {/* 这种方法省略了 this 绑定的过程 */}
                    <button onClick={display}>{this.state.show ? '点击隐藏' : '点击显示'}</button>
                    {
                        this.state.show
                            ?
                            <p>显示出来啦</p>
                            :
                            null
                    }
                </div>
            }

            display() {
                this.setState({
                    show: !this.state.show
                })
            }
        }

2.  与（&&）运算符的条件渲染

            function Mailbox(props) {
              const unreadMessages = props.unreadMessages;
              return (
                <div>
                  <h1>Hello!</h1>
                  {unreadMessages.length > 0 &&
                    <h2>
                      You have {unreadMessages.length} unread messages.
                    </h2>
                  }
                </div>
              );
            }

            const messages = ['React', 'Re: React', 'Re:Re: React'];
            ReactDOM.render(
              <Mailbox unreadMessages={messages} />,
              document.getElementById('root')
            );

之所以能这样做，是因为在 JavaScript 中，`true && expression` 总是返回 `expression`，而 `false && expression` 总是返回 `false。`

因此，如果条件是 true，&& 右侧的元素就会被渲染，如果是 false，React 会忽略并跳过它。
