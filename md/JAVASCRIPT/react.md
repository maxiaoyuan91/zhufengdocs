## 1. 什么是react
React 是一个用于<span class="text-danger">构建用户界面</span>的JavaScript<span class="text-danger">库</span>

## 2. 安装react
```sh
$ bower install react babel --save
```

## 3. 直接在浏览器中使用React
```javascript
  <script src="../bower_components/react/react.js"></script>
  <script src="../bower_components/react/react-dom.js"></script>
  <script src="../bower_components/babel/browser.js"></script>
  <script type="text/babel" src="index.js"/>
```

* react.js 是 React 的*核心*库
* react-dom.js 是提供与*DOM*相关的功能,会在window下增加`ReactDOM`属性
* browser.js 的作用是将`JSX`语法转为`JavaScript`语法

> `script`中的`type`属性为`text/babel`,因为React独有的JSX语法,跟JavaScript不兼容

## 4. ReactDOM.render
`ReactDOM.render` 是 `React` 的最基本方法,用于将标签模板转为HTML语言，并插入指定的DOM节点
### 4.1. index.html

```diff
+   <div id="app"></div>
+   <script type="text/babel" src="js/1.react.js"/>
```

### 4.2. js/1.react.js

```javascript
    ReactDOM.render(
    <h1>珠峰培训</h1>,
        document.getElementById('app')
    );
```
> 上面代码将一个h1标题，插入app元素内部

## 5. JSX 语法
是一种JS和HTML混合的语法,将组件的*结构*、*数据*甚至*样式*都聚合在一起定义组件。
* 遇到HTML标签(以 < 开头)，就用*HTML*规则解析
* 遇到代码块(以 { 开头)，就用*JavaScript*规则解析
* 使用样式时可以让style等于一个样式对象

```javascript
var persons = ['刘德华', '范冰冰', '郭跃'];
var style = {color:'red'};
ReactDOM.render(
  <div>
  {
    persons.map(function (person) {
      return <div style={style}>Hello, {person}!</div>
    })
  }
  </div>,
  document.getElementById('app')
);
```


## 6. 组件(React.createClass)
我们可以很直观的将一个复杂的页面分割成若干个独立组件  
再将这些独立组件组合完成一个复杂的页面。  
这样既减少了逻辑复杂度，又实现了代码的重用
- 可组合：一个组件可以和其他的组件一起使用或者可以直接嵌套在另一个组件内部
- 可重用：每个组件都是具有独立功能的，它可以被使用在多个场景中
- 可维护：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护

### 6.1 定义组件
React允许将代码封装成<span class="text-danger">组件</span>，然后像插入普通<span class="text-danger">HTML标签</span>一样，在网页中插入这个组件

* 组件类的第一个字母必须<span class="text-danger">大写</span>  {:&.rollIn}
* 组件类能且只能包含<span class="text-danger">一个顶层标签</span> 
* 组件<span class="text-danger">实例中的属性</span>可以在组件类中通过`this.props.属性名`获取 

```javascript
var Message = React.createClass({
    render: function() {
        return <h1>Hello</h1>;
    }
});
ReactDOM.render(
    <Message/>,
    document.getElementById('app')
);
```

### 6.2 组件的属性

```javascript
    var Message = React.createClass({
        propTypes: {
            msg: React.PropTypes.string.isRequired,
        },
    
        render: function() {
            return <h1> {this.props.msg} </h1>;
        }
    });
    
    ReactDOM.render(
        <Message msg="珠峰培训" />,
        document.getElementById('app')
    );
```

> React中数据的流向是单向的，即从<span class="text-danger">父节点流向子节点</span>（子组件只需要从父组件获取props渲染即可）
> getDefaultProps 定义初始化`props`钩子函数
> props 通过`props`可以把任意类型的数据属性传递给组件
> 把props当成是组件的数据源，一般用来存放组件初始后<span class="text-danger">不变的数据和属性</span>

### 6.1 PropTypes
组件类的`PropTypes`属性，就是用来验证传入组件实例的属性是否符合要求
```
 propTypes: {
        title: React.PropTypes.string.isRequired,
    }
```

### 6.2 this.props.children
`this.props`对象的属性与组件实例的属性一一对应，但是有一个例外，就是`this.props.children`属性。它表示组件的<span class="text-danger">所有子节点</span>
```javascript
 React.Children.map(this.props.children, function (child) {
                        return <li>{child}</li>;
                    })
```

### 6.3 state状态
state 每一个组件都有自己的state，一开始有一个初始状态，然后用户互动，导致<span class="text-danger">状态变化</span>，从而触发<span class="text-danger">重新渲染UI</span> 
> state存放的是不稳定的，<span class="text-danger">容易变化</span>的组件数据，而且state只存在于组件的内部

### 6.3 属性和状态
 `this.props`表示那些一旦定义，就不再改变的特性，而`this.state`是会随着用户互动而产生变化的特性。
 
 
 