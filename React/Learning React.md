几个参考网页：[React入门](https://zh-hans.reactjs.org/tutorial/tutorial.html)、[JSX语法](https://zh-hans.reactjs.org/docs/jsx-in-depth.html)，[React核心概念](https://zh-hans.reactjs.org/docs/handling-events.html)



## JSX语法

实际上，JSX 仅仅只是 `React.createElement(component, props, ...children)` 函数的语法糖。如下 JSX 代码

```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

=

```jsx
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)
```

如果没有子节点，你还可以使用自闭合的标签形式，如：

```jsx
<div className="sidebar" />
```

=

```jsx
React.createElement(
  'div',
  {className: 'sidebar'}
)
```

### 用户定义的组件必须以大写字母开头

以小写字母开头的元素代表一个 HTML 内置组件，比如 `<div>` 或者 `<span>` 会生成相应的字符串 `'div'` 或者 `'span'` 传递给 `React.createElement`（作为参数）。大写字母开头的元素则对应着在 JavaScript 引入或自定义的组件，如 `<Foo />` 会编译为 `React.createElement(Foo)`。

我们建议使用大写字母开头命名自定义组件。如果你确实需要一个以小写字母开头的组件，则在 JSX 中使用它之前，必须将它赋值给一个大写字母开头的变量。

例如，以下的代码将无法按照预期运行：

```jsx
import React from 'react';

// 错误！组件应该以大写字母开头：
function hello(props) {
  // 正确！这种 <div> 的使用是合法的，因为 div 是一个有效的 HTML 标签
  return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
  // 错误！React 会认为 <hello /> 是一个 HTML 标签，因为它没有以大写字母开头：
  return <hello toWhat="World" />;
}
```

要解决这个问题, 需要首先将类型赋值给一个大写字母开头的变量：

```jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。  
  const SpecificStory = components[props.storyType];  
  return <SpecificStory story={props.story} />;
}
```

### JSX 中的 Props

#### JavaScript 表达式作为 Props

你可以把包裹在 `{}` 中的 JavaScript 表达式作为一个 prop 传递给 JSX 元素。例如，如下的 JSX：

```
<MyComponent foo={1 + 2 + 3 + 4} />
```

在 `MyComponent` 中，`props.foo` 的值等于 `1 + 2 + 3 + 4` 的执行结果 `10`。

`if` 语句以及 `for` 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中。比如：

```jsx
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {    description = <strong>even</strong>;  } else {    description = <i>odd</i>;  }  return <div>{props.number} is an {description} number</div>;
}
```

```jsx
<MyComponent message="hello world" />
=
<MyComponent message={'hello world'} />
```

#### 属性展开

如果你已经有了一个 props 对象，你可以使用展开运算符 `...` 来在 JSX 中传递整个 props 对象。以下两个组件是等价的：

```jsx
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;}
```

组件属性传递

```jsx
// 你还可以选择只保留当前组件需要接收的 props，并使用展开运算符将其他 props 传递下去。

const Button = props => {
  const { kind, ...other } = props;
  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return <button className={className} {...other} />;
};

const App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked!")}>
        Hello World!
      </Button>
    </div>
  );
};
```



### 子元素

子元素允许由多个 JSX 元素组成。这对于嵌套组件非常有用：

```jsx
<MyContainer>  <MyFirstComponent />  <MySecondComponent /></MyContainer>
```

React 组件也能够返回存储在数组中的一组元素：

```jsx
render() {  // 不需要用额外的元素包裹列表元素！  return [    // 不要忘记设置 key :)    <li key="A">First item</li>,    <li key="B">Second item</li>,    <li key="C">Third item</li>,  ];}
```

#### JavaScript 表达式作为子元素

这对于展示任意长度的列表非常有用。例如，渲染 HTML 列表：

```jsx
function Item(props) {
  return <li>{props.message}</li>;}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}    </ul>
  );
}
```

JavaScript 表达式也可以和其他类型的子元素组合。这种做法可以方便地替代模板字符串：

```jsx
function Hello(props) {
  return <div>Hello {props.addressee}!</div>;
}
```

### 

### 在 JSX 类型中使用点语法

在 JSX 中，你也可以使用点语法来引用一个 React 组件。当你在一个模块中导出许多 React 组件时，这会非常方便。例如，如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：

```jsx
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```



## React核心概念

### 生命周期

#### 原始组件

```
function Clock(props) {  return (    <div>      <h1>Hello, world!</h1>      <h2>It is {props.date.toLocaleTimeString()}.</h2>    </div>  );}function tick() {  ReactDOM.render(    <Clock date={new Date()} />,    document.getElementById('root')  );}setInterval(tick, 1000);
```



#### 将函数组件转换成 class 组件

通过以下五步将 `Clock` 的函数组件转成 class 组件：

1. 创建一个同名的 [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)，并且继承于 `React.Component`。
2. 添加一个空的 `render()` 方法。
3. 将函数体移动到 `render()` 方法之中。
4. 在 `render()` 方法中使用 `this.props` 替换 `props`。
5. 删除剩余的空函数声明。

```jsx
class Clock extends React.Component {  render() {    return (      <div>        <h1>Hello, world!</h1>        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>      </div>    );  }}
```



### state用法

注意点

#### 正确地使用 State

关于 `setState()` 你应该了解三件事：

#### 不要直接修改 State

例如，此代码不会重新渲染组件：

```jsx
// Wrongthis.state.comment = 'Hello';
```

而是应该使用 `setState()`:

```jsx
// Correctthis.setState({comment: 'Hello'});
```

### State 的更新可能是异步的

出于性能考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。

例如，此代码可能会无法更新计数器：

```jsx
// Wrongthis.setState({  counter: this.state.counter + this.props.increment,});
```



#### 正确用法示例

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

```



### 处理 this Context  [Click here](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)

#### 原始方法

```javascript
var myObj = {specialFunction: function () {},anotherSpecialFunction: function () {},getAsyncData: function (cb) {    cb();},render: function () {    var that = this;    this.getAsyncData(function () {        that.specialFunction();        that.anotherSpecialFunction();    });}myObj.render();
```

If we had left our function calls as `this.specialFunction()`, then we would have received the following error:

```
Uncaught TypeError: Object [object global] has no method 'specialFunction'
```

#### 使用bind

We need to keep the context of the `myObj` object referenced for when the callback function is called. Calling `that.specialFunction()` enables us to maintain that context and correctly execute our function. However, this could be neatened somewhat by using `Function.prototype.bind()`.

使用bind将this绑定到函数里面

```javascript
render: function () {

    this.getAsyncData(function () {

        this.specialFunction();

        this.anotherSpecialFunction();

    }.bind(this));

}
```

原理

```javascript
Function.prototype.bind = function (scope) {
    var fn = this;
    return function () {
        return fn.apply(scope);
    };
}
```

Simple Case

```javascript
var foo = {
    x: 3
}

var bar = function(){
    console.log(this.x);
}

bar(); // undefined

var boundFunc = bar.bind(foo);

boundFunc(); // 3
```



##### The JavaScript apply() Method

The `apply()` method is similar to the `call()` method (previous chapter).

In this example the **fullName** method of **person** is **applied** on **person1**:

```javascript
const person = {  fullName: function() {    return this.firstName + " " + this.lastName;  }}const person1 = {  firstName: "Mary",  lastName: "Doe"}// This will return "Mary Doe":person.fullName.apply(person1);
```

##### The Difference Between call() and apply()

The difference is:

The `call()` method takes arguments **separately**.

The `apply()` method takes arguments as an **array**.

apply

```javascript
const person = {  fullName: function(city, country) {    return this.firstName + " " + this.lastName + "," + city + "," + country;  }}const person1 = {  firstName:"John",  lastName: "Doe"}person.fullName.apply(person1, ["Oslo", "Norway"]);
```

call

```javascript
const person = {  fullName: function(city, country) {    return this.firstName + " " + this.lastName + "," + city + "," + country;  }}const person1 = {  firstName:"John",  lastName: "Doe"}person.fullName.call(person1, "Oslo", "Norway");
```

#### [Understanding Call, Bind and Apply Methods in JavaScript](https://blog.bitsrc.io/understanding-call-bind-and-apply-methods-in-javascript-33dbf3217be#:~:text=The call%2C bind and apply,and calls the function immediately.)

We have learned that how `this` keyword behaves differently in JavaScript than in other object-oriented languages. The call, bind and apply methods can be used to set the `this` keyword independent of how a function is called.

The bind method creates a copy of the function and sets the `this` keyword, while the call and apply methods sets the `this` keyword and **calls the function immediately**.

#### React 中的用法

##### 实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)

```jsx
class LoggingButton extends React.Component {  // 此语法确保 `handleClick` 内的 `this` 已被绑定。  // 注意: 这是 *实验性* 语法。  handleClick = () => {    console.log('this is:', this);  }  render() {    return (      <button onClick={this.handleClick}>        Click me      </button>    );  }}
```

##### [箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 此语法确保 `handleClick` 内的 `this` 已被绑定。
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```



#### 向事件处理程序传递参数

在循环中，通常我们会为事件处理函数传递额外的参数。例如，若 `id` 是你要删除那一行的 ID，以下两种方式都可以向事件处理函数传递参数：

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上述两种方式是等价的，分别通过[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)和 [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 来实现。

在这两种情况下，React 的事件对象 `e` 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 `bind` 的方式，事件对象以及更多的参数将会被隐式的进行传递。



### 条件渲染

#### 元素变量

你可以使用变量来储存元素。 它可以帮助你有条件地渲染组件的一部分，而其他的渲染部分并不会因此而改变。

观察这两个组件，它们分别代表了注销和登录按钮：

```
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```

在下面的示例中，我们将创建一个名叫 `LoginControl` 的[有状态的组件](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#adding-local-state-to-a-class)。

它将根据当前的状态来渲染 `<LoginButton />` 或者 `<LogoutButton />`。同时它还会渲染上一个示例中的 `<Greeting />`。

```jsx
class LoginControl extends React.Component {  constructor(props) {    super(props);    this.handleLoginClick = this.handleLoginClick.bind(this);    this.handleLogoutClick = this.handleLogoutClick.bind(this);    this.state = {isLoggedIn: false};  }  handleLoginClick() {    this.setState({isLoggedIn: true});  }  handleLogoutClick() {    this.setState({isLoggedIn: false});  }  render() {    const isLoggedIn = this.state.isLoggedIn;    let button;    if (isLoggedIn) {      button = <LogoutButton onClick={this.handleLogoutClick} />;    } else {      button = <LoginButton onClick={this.handleLoginClick} />;    }    return (      <div>        <Greeting isLoggedIn={isLoggedIn} />        {button}      </div>    );  }}ReactDOM.render(  <LoginControl />,  document.getElementById('root')
```

#### 与运算符 &&

通过花括号包裹代码，你可以[在 JSX 中嵌入表达式](https://zh-hans.reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。这也包括 JavaScript 中的逻辑与 (&&) 运算符。它可以很方便地进行元素的条件渲染：

```jsx
function Mailbox(props) {  const unreadMessages = props.unreadMessages;  return (    <div>      <h1>Hello!</h1>      {unreadMessages.length > 0 &&        <h2>          You have {unreadMessages.length} unread messages.        </h2>      }    </div>  );}const messages = ['React', 'Re: React', 'Re:Re: React'];ReactDOM.render(  <Mailbox unreadMessages={messages} />,  document.getElementById('root'));
```

#### 三目运算符

另一种内联条件渲染的方法是使用 JavaScript 中的三目运算符 [`condition ? true : false`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

在下面这个示例中，我们用它来条件渲染一小段文本

```jsx
render() {  const isLoggedIn = this.state.isLoggedIn;  return (    <div>      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.    </div>  );}
```

同样的，它也可以用于较为复杂的表达式中，虽然看起来不是很直观：

```jsx
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
```

就像在 JavaScript 中一样，你可以根据团队的习惯来选择可读性更高的代码风格。需要注意的是，如果条件变得过于复杂，那你应该考虑如何[提取组件](https://zh-hans.reactjs.org/docs/components-and-props.html#extracting-components)。

#### 阻止组件渲染

在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，你可以让 `render` 方法直接返回 `null`，而不进行任何渲染。

### 列表 & Key

#### 列表 

##### 渲染多个组件——map方法

你可以通过使用 `{}` 在 JSX 内构建一个[元素集合](https://zh-hans.reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。

下面，我们使用 Javascript 中的 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 方法来遍历 `numbers` 数组。将数组中的每个元素变成 `<li>` 标签，最后我们将得到的数组赋值给 `listItems`：

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>  <li>{number}</li>);
```

##### 基础列表组件

通常你需要在一个[组件](https://zh-hans.reactjs.org/docs/components-and-props.html)中渲染列表。

我们可以把前面的例子重构成一个组件，这个组件接收 `numbers` 数组作为参数并输出一个元素列表。

```jsx
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

当我们运行这段代码，将会看到一个警告 `a key should be provided for list items`，意思是当你创建一个元素时，必须包括一个特殊的 `key` 属性。我们将在下一节讨论这是为什么。

让我们来给每个列表元素分配一个 `key` 属性来解决上面的那个警告：key用于局部更新

```jsx
function NumberList(props) {  const numbers = props.numbers;  const listItems = numbers.map((number) =>    <li key={number.toString()}>      {number}    </li>  );  return (    <ul>{listItems}</ul>  );}const numbers = [1, 2, 3, 4, 5];ReactDOM.render(  <NumberList numbers={numbers} />,  document.getElementById('root'));
```

#### key

key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。

一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用数据中的 id 来作为元素的 key：

```jsx
const todoItems = todos.map((todo) =>
  <li key={todo.id}>    
    {todo.text}
  </li>
);
```

当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key：

```jsx
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs  
  <li key={index}>    
    {todo.text}
  </li>
);
```

如果列表项目的顺序可能会变化，我们**不建议使用索引来用作 key 值**，因为这样做会导致性能变差，还可能引起组件状态的问题。可以看看 Robin Pokorny 的[深度解析使用索引作为 key 的负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)这一篇文章。如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。

##### 用 key 提取组件

元素的 key 只有放在就近的数组上下文中才有意义。

比方说，如果你[提取](https://zh-hans.reactjs.org/docs/components-and-props.html#extracting-components)出一个 `ListItem` 组件，你应该把 key 保留在数组中的这个 `<ListItem />` 元素上，而不是放在 `ListItem` 组件中的 `<li>` 元素上。

**例子：不正确的使用 key 的方式**

```jsx
function ListItem(props) {
  const value = props.value;
  return (
    // 错误！你不需要在这里指定 key：
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 错误！元素的 key 应该在这里指定：
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

**例子：正确的使用 key 的方式**

```jsx
function ListItem(props) {  // 正确！这里不需要指定 key：  return <li>{props.value}</li>;}function NumberList(props) {  const numbers = props.numbers;  const listItems = numbers.map((number) =>    // 正确！key 应该在数组的上下文中被指定    <ListItem key={number.toString()} value={number} />  );  return (    <ul>      {listItems}    </ul>  );}const numbers = [1, 2, 3, 4, 5];ReactDOM.render(  <NumberList numbers={numbers} />,  document.getElementById('root'));
```

##### key 值在兄弟节点之间必须唯一

数组元素中使用的 key 在其兄弟节点之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的 key 值：

```jsx
function Blog(props) {  const sidebar = (    <ul>      {props.posts.map((post) =>        <li key={post.id}>          {post.title}        </li>      )}    </ul>  );  const content = props.posts.map((post) =>    <div key={post.id}>      <h3>{post.title}</h3>      <p>{post.content}</p>    </div>  );  return (    <div>      {sidebar}      <hr />      {content}    </div>  );}const posts = [  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},  {id: 2, title: 'Installation', content: 'You can install React from npm.'}];ReactDOM.render(  <Blog posts={posts} />,  document.getElementById('root'));
```

**key 会传递信息给 React ，但不会传递给你的组件**。如果你的组件中需要使用 `key` 属性的值，请用其他属性名显式传递这个值：

```jsx
const content = posts.map((post) =>  <Post    key={post.id}    id={post.id}    title={post.title} />);
```

上面例子中，`Post` 组件可以读出 `props.id`，但是不能读出 `props.key`。

### 表单

#### 受控组件

在 HTML 中，表单元素（如`<input>`、 `<textarea>` 和 `<select>`）通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 [`setState()`](https://zh-hans.reactjs.org/docs/react-component.html#setstate)来更新。

我们可以把两者结合起来，使 React 的 state 成为“唯一数据源”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

例如，如果我们想让前一个示例在提交时打印出名称，我们可以将表单写为受控组件：

```jsx
class NameForm extends React.Component {  constructor(props) {    super(props);    this.state = {value: ''};    this.handleChange = this.handleChange.bind(this);    this.handleSubmit = this.handleSubmit.bind(this);  }  handleChange(event) {    this.setState({value: event.target.value});  }  handleSubmit(event) {    alert('提交的名字: ' + this.state.value);    event.preventDefault();  }  render() {    return (      <form onSubmit={this.handleSubmit}>        <label>          名字:          <input type="text" value={this.state.value} onChange={this.handleChange} />        </label>        <input type="submit" value="提交" />      </form>    );  }}
```

##### textarea 标签

类似input

```jsx
<textarea value={this.state.value} onChange={this.handleChange} />
```

##### select 标签

在 HTML 中，`<select>` 创建下拉列表标签。例如，如下 HTML 创建了水果相关的下拉列表：

```jsx
<select>  <option value="grapefruit">葡萄柚</option>  <option value="lime">酸橙</option>  <option selected value="coconut">椰子</option>  <option value="mango">芒果</option></select>
```

请注意，由于 `selected` 属性的缘故，椰子选项默认被选中。React 并不会使用 `selected` 属性，而是在根 `select` 标签上使用 `value` 属性。这在受控组件中更便捷，因为您只需要在根标签中更新它。例如：

```jsx
class FlavorForm extends React.Component {  constructor(props) {    super(props);    this.state = {value: 'coconut'};    this.handleChange = this.handleChange.bind(this);    this.handleSubmit = this.handleSubmit.bind(this);  }  handleChange(event) {    this.setState({value: event.target.value});  }  handleSubmit(event) {    alert('你喜欢的风味是: ' + this.state.value);    event.preventDefault();  }  render() {    return (      <form onSubmit={this.handleSubmit}>        <label>          选择你喜欢的风味:          <select value={this.state.value} onChange={this.handleChange}>            <option value="grapefruit">葡萄柚</option>            <option value="lime">酸橙</option>            <option value="coconut">椰子</option>            <option value="mango">芒果</option>          </select>        </label>        <input type="submit" value="提交" />      </form>    );  }}
```

##### 处理多个输入

当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

例如：

```jsx
class Reservation extends React.Component {  constructor(props) {    super(props);    this.state = {      isGoing: true,      numberOfGuests: 2    };    this.handleInputChange = this.handleInputChange.bind(this);  }  handleInputChange(event) {    const target = event.target;    const value = target.type === 'checkbox' ? target.checked : target.value;    const name = target.name;    this.setState({      [name]: value    });  }  render() {    return (      <form>        <label>          参与:          <input            name="isGoing"            type="checkbox"            checked={this.state.isGoing}            onChange={this.handleInputChange} />        </label>        <br />        <label>          来宾人数:          <input            name="numberOfGuests"            type="number"            value={this.state.numberOfGuests}            onChange={this.handleInputChange} />        </label>      </form>    );  }}
```

这里使用了 ES6 [计算属性名称](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)的语法更新给定输入名称对应的 state 值：

例如：

```jsx
this.setState({  [name]: value});
```

等同 ES5:

```jsx
var partialState = {};partialState[name] = value;this.setState(partialState);
```

另外，由于 `setState()` 自动[将部分 state 合并到当前 state](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#state-updates-are-merged), 只需调用它更改部分 state 即可。

#### 非受控组件

##### 文件 input 标签

在 HTML 中，`<input type="file">` 允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行控制。

```jsx
<input type="file" />
```

因为它的 value 只读，所以它是 React 中的一个**非受控**组件。将与其他非受控组件[在后续文档中](https://zh-hans.reactjs.org/docs/uncontrolled-components.html#the-file-input-tag)一起讨论。

在 React 中，`<input type="file" />` 始终是一个非受控组件，因为它的值只能由用户设置，而不能通过代码控制。

您应该使用 File API 与文件进行交互。下面的例子显示了如何创建一个 **[DOM 节点的 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)** 从而在提交表单时获取文件的信息。

```jsx
class FileInput extends React.Component {  constructor(props) {    super(props);    this.handleSubmit = this.handleSubmit.bind(this);    this.fileInput = React.createRef();  }  handleSubmit(event) {    event.preventDefault();    alert(      `Selected file - ${this.fileInput.current.files[0].name}`    );  }  render() {    return (      <form onSubmit={this.handleSubmit}>        <label>          Upload file:          <input type="file" ref={this.fileInput} />        </label>        <br />        <button type="submit">Submit</button>      </form>    );  }}ReactDOM.render(  <FileInput />,  document.getElementById('root'));
```



例如，下面的代码使用非受控组件接受一个表单的值：

```
class NameForm extends React.Component {  constructor(props) {    super(props);    this.handleSubmit = this.handleSubmit.bind(this);    this.input = React.createRef();  }  handleSubmit(event) {    alert('A name was submitted: ' + this.input.current.value);    event.preventDefault();  }  render() {    return (      <form onSubmit={this.handleSubmit}>        <label>          Name:          <input type="text" ref={this.input} />        </label>        <input type="submit" value="Submit" />      </form>    );  }}
```



### 状态提升

多个组件state 共享

在 React 中，将多个组件中需要共享的 state 向上移动到它们的最近共同父组件中，便可实现共享 state。这就是所谓的“状态提升”。接下来，我们将 `TemperatureInput` 组件中的 state 移动至 `Calculator` 组件中去。

```jsx
class Calculator extends React.Component {  constructor(props) {    super(props);    this.handleCelsiusChange = this.handleCelsiusChange.bind(this);    this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);    this.state = {temperature: '', scale: 'c'};  }  handleCelsiusChange(temperature) {    this.setState({scale: 'c', temperature});  }  handleFahrenheitChange(temperature) {    this.setState({scale: 'f', temperature});  }  render() {    const scale = this.state.scale;    const temperature = this.state.temperature;    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;    return (      <div>        <TemperatureInput          scale="c"          temperature={celsius}          onTemperatureChange={this.handleCelsiusChange} />        <TemperatureInput          scale="f"          temperature={fahrenheit}          onTemperatureChange={this.handleFahrenheitChange} />        <BoilingVerdict          celsius={parseFloat(celsius)} />      </div>    );  }
```

```jsx
class TemperatureInput extends React.Component {  constructor(props) {    super(props);    this.handleChange = this.handleChange.bind(this);  }  handleChange(e) {    this.props.onTemperatureChange(e.target.value);  }  render() {    const temperature = this.props.temperature;    const scale = this.props.scale;    return (      <fieldset>        <legend>Enter temperature in {scaleNames[scale]}:</legend>        <input value={temperature}               onChange={this.handleChange} />      </fieldset>    );  }}
```

[**在 CodePen 上尝试**](https://codepen.io/gaearon/pen/WZpxpz?editors=0010)

#### 学习小结

在 React 应用中，任何可变数据应当只有一个相对应的唯一“数据源”。通常，state 都是首先添加到需要渲染数据的组件中去。然后，如果其他组件也需要这个 state，那么你可以将它提升至这些组件的最近共同父组件中。你应当依靠[自上而下的数据流](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html#the-data-flows-down)，而不是尝试在不同组件间同步 state。

虽然提升 state 方式比双向绑定方式需要编写更多的“样板”代码，但带来的好处是，排查和隔离 bug 所需的工作量将会变少。由于“存在”于组件中的任何 state，仅有组件自己能够修改它，因此 bug 的排查范围被大大缩减了。此外，你也可以使用自定义逻辑来拒绝或转换用户的输入。

如果某些数据可以由 props 或 state 推导得出，那么它就不应该存在于 state 中。举个例子，本例中我们没有将 `celsiusValue` 和 `fahrenheitValue` 一起保存，而是仅保存了最后修改的 `temperature` 和它的 `scale`。这是因为另一个输入框的温度值始终可以通过这两个值以及组件的 `render()` 方法获得。这使得我们能够清除输入框内容，亦或是，在不损失用户操作的输入框内数值精度的前提下对另一个输入框内的转换数值做四舍五入的操作。

当你在 UI 中发现错误时，可以使用 [React 开发者工具](https://github.com/facebook/react/tree/main/packages/react-devtools) 来检查问题组件的 props，并且按照组件树结构逐级向上搜寻，直到定位到负责更新 state 的那个组件。这使得你能够追踪到产生 bug 的源头：

![img](https://raw.githubusercontent.com/DomicZhong/pictures/main/img/react-devtools-state.gif)

### [组合 vs 继承](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html)

#### 包含关系

有些组件无法提前知晓它们子组件的具体内容。在 `Sidebar`（侧边栏）和 `Dialog`（对话框）等展现通用容器（box）的组件中特别容易遇到这种情况。

我们建议这些组件使用一个特殊的 `children` prop 来将他们的子组件传递到渲染结果中：

```
function FancyBorder(props) {  return (    <div className={'FancyBorder FancyBorder-' + props.color}>      {props.children}    </div>  );}
```

这使得别的组件可以通过 JSX 嵌套，将任意组件作为子组件传递给它们。

```
function WelcomeDialog() {  return (    <FancyBorder color="blue">      <h1 className="Dialog-title">        Welcome      </h1>      <p className="Dialog-message">        Thank you for visiting our spacecraft!      </p>    </FancyBorder>  );}
```

`<FancyBorder>` JSX 标签中的所有内容都会作为一个 `children` prop 传递给 `FancyBorder` 组件。因为 `FancyBorder` 将 `{props.children}` 渲染在一个 `<div>` 中，被传递的这些子组件最终都会出现在输出结果中。

少数情况下，你可能需要在一个组件中预留出几个“洞”。这种情况下，我们可以不使用 `children`，而是自行约定：将所需内容传入 props，并使用相应的 prop。

```
function SplitPane(props) {  return (    <div className="SplitPane">      <div className="SplitPane-left">        {props.left}      </div>      <div className="SplitPane-right">        {props.right}      </div>    </div>  );}function App() {  return (    <SplitPane      left={        <Contacts />      }      right={        <Chat />      } />  );}
```

#### 特例关系

有些时候，我们会把一些组件看作是其他组件的特殊实例，比如 `WelcomeDialog` 可以说是 `Dialog` 的特殊实例。

在 React 中，我们也可以通过组合来实现这一点。“特殊”组件可以通过 props 定制并渲染“一般”组件：

```
function Dialog(props) {  return (    <FancyBorder color="blue">      <h1 className="Dialog-title">        {props.title}      </h1>      <p className="Dialog-message">        {props.message}      </p>    </FancyBorder>  );}function WelcomeDialog() {  return (    <Dialog      title="Welcome"      message="Thank you for visiting our spacecraft!" />  );}
```

组合也同样适用于以 class 形式定义的组件。

```
function Dialog(props) {  return (    <FancyBorder color="blue">      <h1 className="Dialog-title">        {props.title}      </h1>      <p className="Dialog-message">        {props.message}      </p>      {props.children}    </FancyBorder>  );}class SignUpDialog extends React.Component {  constructor(props) {    super(props);    this.handleChange = this.handleChange.bind(this);    this.handleSignUp = this.handleSignUp.bind(this);    this.state = {login: ''};  }  render() {    return (      <Dialog title="Mars Exploration Program"              message="How should we refer to you?">        <input value={this.state.login}               onChange={this.handleChange} />        <button onClick={this.handleSignUp}>          Sign Me Up!        </button>      </Dialog>    );  }  handleChange(e) {    this.setState({login: e.target.value});  }  handleSignUp() {    alert(`Welcome aboard, ${this.state.login}!`);  }}
```

#### 那么继承呢？

在 Facebook，我们在成百上千个组件中使用 React。我们并没有发现需要使用继承来构建组件层次的情况。

Props 和组合为你提供了清晰而安全地定制组件外观和行为的灵活方式。注意：组件可以接受任意 props，包括基本数据类型，React 元素以及函数。

如果你想要在组件间复用非 UI 的功能，我们建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（import）而无需通过 extend 继承它们。

## [React 哲学](https://zh-hans.reactjs.org/docs/thinking-in-react.html)

我们认为，React 是用 JavaScript 构建快速响应的大型 Web 应用程序的首选方式。它在 Facebook 和 Instagram 上表现优秀。

React 最棒的部分之一是引导我们思考如何构建一个应用。在这篇文档中，我们将会通过 React 构建一个可搜索的产品数据表格来更深刻地领会 React 哲学。

### 从设计稿开始

假设我们已经有了一个返回 JSON 的 API，以及设计师提供的组件设计稿。如下所示：

![Mockup](https://raw.githubusercontent.com/DomicZhong/pictures/main/img/thinking-in-react-mock.png)

该 JSON API 会返回以下数据：

```
[  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}];
```

### 第一步：将设计好的 UI 划分为组件层级

首先，你需要在设计稿上用方框圈出每一个组件（包括它们的子组件），并且以合适的名称命名。如果你是和设计师一起完成此任务，那么他们可能已经做过类似的工作，所以请和他们进行交流！他们的 Photoshop 的图层名称可能最终就是你编写的 React 组件的名称！

但你如何确定应该将哪些部分划分到一个组件中呢？你可以将组件当作一种函数或者是对象来考虑，根据[单一功能原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)来判定组件的范围。也就是说，一个组件原则上只能负责一个功能。如果它需要负责更多的功能，这时候就应该考虑将它拆分成更小的组件。

在实践中，因为你经常是在向用户展示 JSON 数据模型，所以如果你的模型设计得恰当，UI（或者说组件结构）便会与数据模型一一对应，这是因为 UI 和数据模型都会倾向于遵守相同的*信息结构*。将 UI 分离为组件，其中每个组件需与数据模型的某部分匹配。

![组件嵌套图示](https://raw.githubusercontent.com/DomicZhong/pictures/main/img/thinking-in-react-components.png)

你会看到我们的应用中包含五个组件。我们已经将每个组件展示的数据标注为了斜体。图片中的序号与下方列表中的序号对应。

1. **`FilterableProductTable` (橙色):** 是整个示例应用的整体
2. **`SearchBar` (蓝色):** 接受所有的*用户输入*
3. **`ProductTable` (绿色):** 展示*数据内容*并根据*用户输入*筛选结果
4. **`ProductCategoryRow` (天蓝色):** 为每一个*产品类别*展示标题
5. **`ProductRow` (红色):** 每一行展示一个*产品*

你可能注意到，`ProductTable` 的表头（包含 “Name” 和 “Price” 的那一部分）并未单独成为一个组件。这仅仅是一种偏好选择，如何处理这一问题也一直存在争论。就这个示例而言，因为表头只起到了渲染*数据集合*的作用——这与 `ProductTable` 是一致的，所以我们仍然将其保留为 `ProductTable` 的一部分。但是，如果表头过于复杂（例如，我们需为其添加排序功能），那么将它作为一个独立的 `ProductTableHeader` 组件就显得很有必要了。

现在我们已经确定了设计稿中应该包含的组件，接下来我们将把它们描述为更加清晰的层级。设计稿中被其他组件包含的子组件，在层级上应该作为其子节点。

- `FilterableProductTable`
  - `SearchBar`
  - `ProductTable`
    - `ProductCategoryRow`
    - `ProductRow`

## 

### 第二步：用 React 创建一个静态版本

参阅 [CodePen](https://codepen.io/) 上的 [React 哲学：第二步](https://codepen.io/gaearon/pen/BwWzwm)。

现在我们已经确定了组件层级，可以编写对应的应用了。最容易的方式，是先用已有的数据模型渲染一个不包含交互功能的 UI。最好将渲染 UI 和添加交互这两个过程分开。这是因为，编写一个应用的静态版本时，往往要编写大量代码，而不需要考虑太多交互细节；添加交互功能时则要考虑大量细节，而不需要编写太多代码。所以，将这两个过程分开进行更为合适。我们会在接下来的代码中体会到其中的区别。

在构建应用的静态版本时，我们需要创建一些会重用其他组件的组件，然后通过 *props* 传入所需的数据。*props* 是父组件向子组件传递数据的方式。即使你已经熟悉了 *state* 的概念，也**完全不应该使用 state** 构建静态版本。state 代表了随时间会产生变化的数据，应当仅在实现交互时使用。所以构建应用的静态版本时，你不会用到它。

你可以自上而下或者自下而上构建应用：自上而下意味着首先编写层级较高的组件（比如 `FilterableProductTable`），自下而上意味着从最基本的组件开始编写（比如 `ProductRow`）。当你的应用比较简单时，使用自上而下的方式更方便；对于较为大型的项目来说，自下而上地构建，并同时为低层组件编写测试是更加简单的方式。

到此为止，你应该已经有了一个可重用的组件库来渲染你的数据模型。由于我们构建的是静态版本，所以这些组件目前只需提供 `render()` 方法用于渲染。最顶层的组件 `FilterableProductTable` 通过 props 接受你的数据模型。如果你的数据模型发生了改变，再次调用 `ReactDOM.render()`，UI 就会相应地被更新。数据模型变化、调用 `render()` 方法、UI 相应变化，这个过程并不复杂，因此很容易看清楚 UI 是如何被更新的，以及是在哪里被更新的。React **单向数据流**（也叫*单向绑定*）的思想使得组件模块化，易于快速开发。

如果你在完成这一步骤时遇到了困难，可以参阅 [React 文档](https://zh-hans.reactjs.org/docs/)。

#### 补充说明: 有关 props 和 state

在 React 中，有两类“模型”数据：props 和 state。清楚地理解两者的区别是十分重要的；如果你不太有把握，可以参阅 [React 官方文档](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)。你也可以查看 [FAQ: state 与 props 的区别是什么？](https://zh-hans.reactjs.org/docs/faq-state.html#what-is-the-difference-between-state-and-props)

## 

### 第三步：确定 UI state 的最小（且完整）表示

想要使你的 UI 具备交互功能，需要有触发基础数据模型改变的能力。React 通过实现 **state** 来完成这个任务。

为了正确地构建应用，你首先需要找出应用所需的 state 的最小表示，并根据需要计算出其他所有数据。其中的关键正是 [DRY: *Don’t Repeat Yourself*](https://en.wikipedia.org/wiki/Don't_repeat_yourself)。只保留应用所需的可变 state 的最小集合，其他数据均由它们计算产生。比如，你要编写一个任务清单应用，你只需要保存一个包含所有事项的数组，而无需额外保存一个单独的 state 变量（用于存储任务个数）。当你需要展示任务个数时，只需要利用该数组的 length 属性即可。

我们的示例应用拥有如下数据：

- 包含所有产品的原始列表
- 用户输入的搜索词
- 复选框是否选中的值
- 经过搜索筛选的产品列表

**通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：**

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。

包含所有产品的原始列表是经由 props 传入的，所以它不是 state；搜索词和复选框的值应该是 state，因为它们随时间会发生改变且无法由其他数据计算而来；经过搜索筛选的产品列表不是 state，因为它的结果可以由产品的原始列表根据搜索词和复选框的选择计算出来。

综上所述，属于 state 的有：

- 用户输入的搜索词
- 复选框是否选中的值



### 第四步：确定 state 放置的位置

参阅 [CodePen](https://codepen.io/) 上的 [React 哲学：第四步](https://codepen.io/gaearon/pen/qPrNQZ)。

我们已经确定了应用所需的 state 的最小集合。接下来，我们需要确定哪个组件能够改变这些 state，或者说*拥有*这些 state。

注意：React 中的数据流是单向的，并顺着组件层级从上往下传递。哪个组件应该拥有某个 state 这件事，**对初学者来说往往是最难理解的部分**。尽管这可能在一开始不是那么清晰，但你可以尝试通过以下步骤来判断：

对于应用中的每一个 state：

- 找到根据这个 state 进行渲染的所有组件。
- 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
- 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。

根据以上策略重新考虑我们的示例应用：

- `ProductTable` 需要根据 state 筛选产品列表。`SearchBar` 需要展示搜索词和复选框的状态。
- 他们的共同所有者是 `FilterableProductTable`。
- 因此，搜索词和复选框的值应该很自然地存放在 `FilterableProductTable` 组件中。

很好，我们已经决定把这些 state 存放在 `FilterableProductTable` 组件中。首先，将实例属性 `this.state = {filterText: '', inStockOnly: false}` 添加到 `FilterableProductTable` 的 `constructor` 中，设置应用的初始 state；接着，将 `filterText` 和 `inStockOnly` 作为 props 传入 `ProductTable` 和 `SearchBar`；最后，用这些 props 筛选 `ProductTable` 中的产品信息，并设置 `SearchBar` 的表单值。

你现在可以看到应用的变化了：将 `filterText` 设置为 `"ball"` 并刷新应用，你能发现表格中的数据已经更新了。



### 第五步：添加反向数据流

参阅 [CodePen](https://codepen.io/) 上的 [React 哲学：第五步](https://codepen.io/gaearon/pen/LzWZvb)。

到目前为止，我们已经借助自上而下传递的 props 和 state 渲染了一个应用。现在，我们将尝试让数据反向传递：处于较低层级的表单组件更新较高层级的 `FilterableProductTable` 中的 state。

React 通过一种比传统的双向绑定略微繁琐的方法来实现反向数据传递。尽管如此，但这种需要显式声明的方法更有助于人们理解程序的运作方式。

如果你在这时尝试在搜索框输入或勾选复选框，React 不会产生任何响应。这是正常的，因为我们之前已经将 `input` 的值设置为了从 `FilterableProductTable` 的 `state` 传递而来的固定值。

让我们重新梳理一下需要实现的功能：每当用户改变表单的值，我们需要改变 state 来反映用户的当前输入。由于 state 只能由拥有它们的组件进行更改，`FilterableProductTable` 必须将一个能够触发 state 改变的回调函数（callback）传递给 `SearchBar`。我们可以使用输入框的 `onChange` 事件来监视用户输入的变化，并通知 `FilterableProductTable` 传递给 `SearchBar` 的回调函数。然后该回调函数将调用 `setState()`，从而更新应用。

### 这就是全部了

希望这篇文档能够帮助你建立起构建 React 组件和应用的一般概念。尽管你可能需要编写更多的代码，但是别忘了：比起写，代码更多地是给人看的。我们一起构建的这个模块化示例应用的代码就很易于阅读。当你开始构建更大的组件库时，你会意识到这种代码模块化和清晰度的重要性。并且随着代码重用程度的加深，你的代码行数也会显著地减少。:)







