# React 基础学习

## 1. 配置环境

**如果没有终端 ： 安装Git Bash（仅限使用Windows，使用Mac和Linux的无需安装）**

https://gitforwindows.org/



**安装 Nodejs**

安装create-react-app

打开Git Bash，执行：

```sh
npm i -g create-react-app
```



**安装VSCode的插件**

- **Simple React Snippets**           ->         **webstorm   :          React Snippets**

- **Prettier - Code formatter**      



**创建React App**

在目标目录下打开Git Bash，在终端中执行：

```sh
create-react-app react-app  # 可以替换为其他app名称

cd react-app
npm start  # 启动应用
```



**JSX**

React中的一种语言，会被Babel编译成标准JavaScript。

https://babeljs.io/repl/



## 2. ES6语法补充

### 2.1. **使用 `bind()` 函数绑定 `this` 取值**

在JavaScript中，**函数里的this指向的是执行时的调用者，而非定义时所在的对象。**

例如：

```js
const person = {
  name: "yxc",
  talk: function() {
    console.log(this);
  }
}

person.talk();

const talk = person.talk;
talk();
```

运行结果：

```js
{name: 'yxc', talk: ƒ}
Window
```


bind()函数，可以绑定this的取值。例如：

```js
const talk = person.talk.bind(person);
```



### 2.2. 箭头函数的简写方式

```js
const f = (x) => {
  return x * x;
};
```

可以简写为：

```js
const f = x => x * x;
```



### 2.3. 箭头函数不重新绑定this的取值

例如：

```js
const person = {
  talk: function() {
    setTimeout(function() {
      console.log(this);
    }, 1000);
  }
};

person.talk();  // 输出Window
```

```js
const person = {
  talk: function() {
    setTimeout(() => {
      console.log(this);
    }, 1000);
  }
};

person.talk();  // 输出 {talk: f}
```



### 2.4. 对象的解构

例如：

```js
const person = {
  name: "yxc",
  age: 18,
  height: 180,
};

const {name : nm, age} = person;  // nm是name的别名
```



### 2.5. 数组和对象的展开

例如：

```js
let a = [1, 2, 3];
let b = [...a];  // b是a的复制 内存上不是用一个地址 一个改变不会改变另外一个的值
let c = [...a, 4, 5, 6];
```

```js
const a = {name: "yxc"};
const b = {age: 18};
const c = {...a, ...b, height: 180};
```



### 2.6. Named 与 Default exports

- **Named Export**：**每个文件可以export多个**，**import的时候需要加大括号，名称需要匹配**

- **Default Export**：**每个文件最多export一个**，**import的时候不需要加大括号，可以直接定义别名**

```js
export default class Person {
    constructor(){
        console.log("new Person....");
    }
}

let id = 10;
export {
	id
}
```

**两者可以同时存在，但普通的需要传一个数组**

```js
import Myperson, {id} from "/static/js/index1.js";
```



## 3. Components

### 3.1. 创建项目

创建项目box-app：

```sh
create-react-app box-app
cd box-app
npm start
```

安装bootstrap库：

```sh
npm i bootstrap
```

bootstrap的引入方式：

**在 index.js 上方写上**

```js
import 'bootstrap/dist/css/bootstrap.css';
```

 

### 3.2. 创建Component

**把除了 index 相关的代码和文件都删了** 

![1](./1 React基础.assets/1.png)

创建包 `/src/components` , 然后在里面创建 `box.jsx`，虽然 `react` 项目会把 `js` ，默认绑定为 `jsx` ，但是自己主动定义看上去更直观。

在 `box.jsx` 里先导入 `React` ，然后写好 `Box` 类，然后对外暴露，然后让 `index.js` 引入进来

![2](./1 React基础.assets/2.png)

```js
import React, {Component} from "react";


class Box extends Component {
    state = {};

    render() {
        return <h1> Hello World </h1>;
    }
}

export default Box;
```

![3](./1 React基础.assets/3.png)

这里看上去，只用到了 Component 的 render 方法，让我们能在 js 里面， 通过这个函数的返回值，在 js 写 html

而 React 没用到，其实用到了，只不过是隐式用到的，jsx 编译成 js 是通过内部的方法做到的



### 3.3. 创建按钮

**React 框架本质是通过递归的 dom 树生成，所以渲染页面的根节点只能是一个，当子节点数量大于1时，可以用`<div>`或`<React.Fragment>`将其括起来。同时最好用 括号把 html 内容包裹起来，这样可以防止编译器错误的解析内容。**

**<font color='red'>最好还是写 `<React.Fragment>` ，因为写 `<div>` 渲染页面还会多一个元素，而前者是虚拟的根节点。</font>**

```js
import React, {Component} from "react";


class Box extends Component {
    state = {};

    render() {
        return <React.Fragment>
            <h1> Hello World </h1>
            <button>left</button>
            <button>right</button>
        </React.Fragment>;
    }
}

export default Box;
```



### 3.4. 内嵌表达式

**JSX中如果想在html中使用 js 使用{}嵌入表达式。**







### 3.5. 设置属性

- class -> className

- CSS属性：background-color -> backgroundColor，其它属性类似



### 3.6. 数据驱动改变Style



### 3.7. 渲染列表

使用map函数

每个元素需要具有唯一的key属性，用来帮助React快速找到被修改的DOM元素。



### 3.8. Conditional Rendering

利用逻辑表达式的短路原则。

与表达式中 expr1 && expr2，当expr1为假时返回expr1的值，否则返回expr2的值

或表达式中 expr1 || expr2，当expr1为真时返回expr1的值，否则返回expr2的值



### 3.9. 绑定事件

注意妥善处理好绑定事件函数的this



### 3.10. 修改state

需要使用this.setState()函数

每次调用this.setState()函数后，会重新调用this.render()函数，用来修改虚拟DOM树。React只会修改不同步的实际DOM树节点。

### 3.11. 给事件函数添加参数



## 4. 组合Components

### 4.1. 创建Boxes组件

**Boxes**组件中包含一系列**Box**组件。



### 4.2. 从上往下传递数据

通过**this.props**属性可以从上到下传递数据。



### 4.3. 传递子节点

通过this.props.children属性传递子节点



### 4.4. 从下往上调用函数

注意：每个组件的this.state只能在组件内部修改，不能在其他组件内修改。



### 4.5. 每个维护的数据仅能保存在一个this.state中

不要直接修改this.state的值，因为setState函数可能会将修改覆盖掉。



### 4.6. 创建App组件

**包含：**

- **导航栏组件**
- **Boxes组件**

**注意：**

**要将多个组件共用的数据存放到最近公共祖先的this.state中。**



## 4.7. 无状态函数组件

当组件中没有用到this.state时，可以简写为无状态的函数组件。

函数的传入参数为props对象



## 4.8. 组件的生命周期

- Mount周期，执行顺序：constructor() -> render() -> componentDidMount()
- Update周期，执行顺序：render() -> componentDidUpdate()
- Unmount周期，执行顺序：componentWillUnmount()



## 5. 路由

### 5.1. Web分类

- 静态页面：页面里的数据是写死的

- 动态页面：页面里的数据是动态填充的

- 后端渲染：数据在后端填充

- 前端渲染：数据在前端填充



### 5.2. 安装环境

VSCODE安装插件：`Auto Import - ES6, TS, JSX, TSX`

安装Route组件：

```sh
npm i react-router-dom
```



### 5.3. Route组件介绍

- BrowserRouter：所有需要路由的组件，都要包裹在BrowserRouter组件内

- Link：跳转到某个链接，to属性表示跳转到的链接

- Routes：类似于C++中的switch，匹配第一个路径

- Route：路由，path属性表示路径，element属性表示路由到的内容



### 5.4. URL中传递参数

解析URL：

```js
<Route path="/linux/:chapter_id/:section_id/" element={<Linux />} />
```

获取参数，类组件写法：

```js
import React, { Component } from 'react';
import { useParams } from 'react-router-dom';

class Linux extends Component {
    state = {  } 
    render() {
        console.log(this.props.params);
        return <h1>Linux</h1>;
    }
}

export default (props) => (
    <Linux
        {...props}
        params={useParams()}
    />
)
```

函数组件写法：

```js
import React, { Component } from 'react';
import { useParams } from 'react-router-dom';

const Linux = () => {
    console.log(useParams());
    return (<h1>Linux</h1>);
}

export default Linux;
```



### 5.5. Search Params传递参数

类组件写法：

```js
import React, { Component } from 'react';
import { useSearchParams } from 'react-router-dom';

class Django extends Component {
    state = {
        searchParams: this.props.params[0],  // 获取某个参数
        setSearchParams: this.props.params[1],  // 设置链接中的参数，然后重新渲染当前页面
    }

    handleClick = () => {
        this.state.setSearchParams({
            name: "abc",
            age: 20,
        })
    }

    render() {
        console.log(this.state.searchParams.get('age'));
        return <h1 onClick={this.handleClick}>Django</h1>;
    }
}

export default (props) => (
    <Django
        {...props}
        params={useSearchParams()}
    />
);
```

函数组件写法：

```js
import React, { Component } from 'react';
import { useSearchParams } from 'react-router-dom';

const Django = () => {
    let [searchParams, setSearchParams] = useSearchParams();
    console.log(searchParams.get('age'));
    return (<h1>Django</h1>);
}

export default Django;
```



### 5.6. 重定向

使用Navigate组件可以重定向。

```js
<Route path="*" element={ <Navigate replace to="/404" /> } />
```



### 5.7. 嵌套路由

```js
<Route path="/web" element={<Web />}>
    <Route index path="a" element={<h1>a</h1>} />
    <Route index path="b" element={<h1>b</h1>} />
    <Route index path="c" element={<h1>c</h1>} />
</Route>
```

注意：需要在父组件中添加`<Outlet />`组件，用来填充子组件的内容。



## 6. Redux

redux将所有数据存储到树中，且树是唯一的。

### 6.1. Redux基本概念

- store：存储树结构。
- state：维护的数据，一般维护成树的结构。
- reducer：对state进行更新的函数，每个state绑定一个reducer。传入两个参数：当前state和action，返回新state。
- action：一个普通对象，存储reducer的传入参数，一般描述对state的更新类型。
- dispatch：传入一个参数action，对整棵state树操作一遍。



### 6.2. React-Redux基本概念

- Provider组件：用来包裹整个项目，其store属性用来存储redux的store对象。
- connect(mapStateToProps, mapDispatchToProps)函数：用来将store与组件关联起来。
- mapStateToProps：每次store中的状态更新后调用一次，用来更新组件中的值。
- mapDispatchToProps：组件创建时调用一次，用来将store的dispatch函数传入组件。



### 6.3. 安装

```sh
npm i redux react-redux @reduxjs/toolkit
```









