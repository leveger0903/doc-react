# React 基礎

## JSX 入門

HTML 需要有 id="root" 這個 HTML 標籤元素.

```
const element = <h1>Hello, world!</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

## 組件基礎

可以「方法」作為組件基礎,\
也可透過「類」作為組件基礎.

```
# 方法組件
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

# 類組件
class Welcome extends Component {
  render () {
    return <h1>Hello, {props.name}</h1>;
  }
}
```

## 渲染組件

以下為完整渲染組件方式

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

ReactDOM.render(
  <Welcome name="Sara" />,
  document.getElementById('root')
);
```

## 組件與屬性(Props)

React 是階層的樹狀結構實現 View 層的類庫,\
以下為範例

```
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <img className="Avatar"
           src={props.user.avatarUrl}
           alt={props.user.name} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

# 內有 UserInfo 組件
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
    </div>
  );
}

const comment = {
  text: 'I hope you enjoy learning React!',
  author: {
    name: 'Hello Kitty',
    avatarUrl: 'http://placekitten.com/g/64/64'
  }
};

# 內有 Comment 組件
ReactDOM.render(
  <Comment
    text={comment.text}
    author={comment.author} />,
  document.getElementById('root')
);
```

## 狀態(State)與週期

以類組件來說,\
props 的子屬性來自於父層,\
state 的子屬性屬於自身使用,
建議組件存取自身的 state.

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
			name: props.name,
			date: new Date()
		};
  }

  render() {
    return (
      <div>
        <h1>Hello, {this.state.name}!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock name="Sara" />,
  document.getElementById('root')
);
```

## 組件週期性更新

利用 componentDidMount / componentWillUnmount,\
進行組件新狀態替換,\
建議不要在 componentDidMount 同步調用 setState\
(參考來源：https://medium.com/@as790726/b37a12da968b-b37a12da968b)

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  
  # 組件循環 - 掛載
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  # 組件循環 - 卸載
  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState((prevState, prop) => ({
      date: new Date()
    }));
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
  document.getElementById('app')
);
```

## 事件處理

為你的物件綁定事件,\
注意類組件內的 method,\
必須要在建構方法(constructor)或HTML物件上榜定行為.

```
constructor(props) {
  ...
  this.handleClick = this.handleClick.bind(this);
  ...
}
```

或是

```
<button onClick={this.handleClick.bind(this)}>
  {this.state.isToggleOn ? 'ON' : 'OFF'}
</button>
```

如果不想寫 method 綁定,\
可透過 https://babeljs.io/docs/plugins/transform-class-properties/\
\
範例

```
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.state = {
      isToggleOn: true
    };
  }

  handleClick() {
    this.setState((prevState, props) => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('app')
);
```

## 回調事件

有些使用情境下需要取得物件所回調的事件,\
組件上有兩種寫法可取得回掉事件,\
但 method 方法注意帶入 parameters 的順序.\

```
<button onClick={(e) => this.deleteRow(id, e)}>
  Click me
</button>
```

或是

```
<button onClick={this.deleteRow(this, id)}>
  Click me
</button>
```

範例

```
class Popper extends React.Component {
  constructor() {
    super();
    this.state = {
      name: 'Hello World!',
    };
  }

  preventPop(name, e) {
    e.preventDefault();
    alert(name);
  }

  render() {
    return (
      <div>
        <p>hello</p>
        <button onClick={this.preventPop.bind(this, this.state.name)}>
          Click
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Popper />,
  document.getElementById('app')
);
```

## 條件渲染

透過條件判斷式,
可選擇渲染指定的組件.

```
function UserGreeting(props) {
  return <h1>Welcome Back, {props.name}</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

function Greeting(props) {
  if (props.isLoggedIn)
    return <UserGreeting name={props.name} />

  return <GuestGreeting />
}

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

class LoginControl extends React.Component
{
  constructor(props) {
    super(props);
    this.state = {
      isLoggedIn : props.isLoggedIn
    }
  }

  handleLoginClick() {
    this.setState({
      isLoggedIn: true
    });
  }

  handleLogoutClick() {
    this.setState({
      isLoggedIn: false
    });
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;

    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick.bind(this)} />
    } else {
      button = <LoginButton onClick={this.handleLoginClick.bind(this)} />
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} name="Arden" />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl isLoggedIn={false} />,
  document.getElementById('app')
);
```

## && 運算符

在花括弧內,\
可以使用 Javascript 的 && 運算符進行條件渲染

```
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      {
        unreadMessages.length > 0 &&
          <h2>
            You have {unreadMessages.length} unread messages.
          </h2>
      }
    </div>
  );
}

const messages = [
  'React',
  'Re: React',
  'Re:Re React',
];

ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('app')
);
```

## 三目運算符

在花括弧內,\
可以使用三目運算符, 甚至包覆組件.

```
function NoMessage(props) {
  return <h2>No message.</h2>;
}

function HaveMessages(props) {
  return <h2>You have {props.count} messages.</h2>;
}

function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      {
        unreadMessages.length > 0 ? <HaveMessages count={unreadMessages.length} /> : <NoMessage />
      }
    </div>
  );
}

const messages = [
  'React',
  'Re: React',
  'Re:Re React',
];

ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('app')
);
```

## 阻止渲染

某些情況下你希望隱藏組件,
利用 redner 方法返回 null 方式阻止組件渲染.

```
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      showWarning: true
    };
  }

  handleToggleClick() {
    this.setState((prevState, props) => ({
      showWarning: !prevState.showWarning
    }));
  }

  render () {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick.bind(this)}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('app')
);
```

## 列表 & Keys

在花括弧內建立元素集合,\
達到渲染列表組件.

```
function NumberList(props) {
  let numbers = props.numbers;
  let listItems = numbers.map((number) => 
    <li>
      {number}
    </li>
  );
  
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('app')
);
```

Keys 作為 React DOM 索引,\
並識別那些元素被添加或刪除,\
key 值必須是這個組件中獨一無二的字符串.

```
function NumberList(props) {
  let numbers = props.numbers;
  let listItems = numbers.map((number, index) => 
    <li key={index}>
      {number}
    </li>
  );
  
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('app')
);
```

多組件下,\
key 值應該設置於父祖件上.

```
function ListItem(props) {
  let number = props.number;
  return <li>{number}</li>
}

function NumberList(props) {
  let numbers = props.numbers;
  let listItems = numbers.map((number, index) => 
    <ListItem key={index}
              number={number} />
  );
  
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('app')
);
```

在組件中,
數組元素的 key 值只需要在同一個節點是獨一無二的 key 值即可.

```
class Blog extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      posts: props.posts
    };
  }

  render() {
    const sidebar = (
      <ul>
        {this.state.posts.map(post =>
          <li key={post.id}>
            {post.title}
          </li>
        )}
      </ul>
    );

    const content = this.state.posts.map(post => 
      <div key={post.id}>
        <h3>{post.title}</h3>
        <p>{post.content}</p>
      </div>
    );

    return (
      <div>
        {sidebar}
        <hr />
        {content}
      </div>
    );
  }
}

const posts = [
  { id: 1, title: 'Hello World', content: 'Welcome to learning React.' },
  { id: 2, title: 'Installation', content: 'You may install React from npm.' },
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('app')
);
```

## 表單

React 的表單元素(如input, textarea, select等)通常將狀態存在組件 state 屬性中,
並且只能通過 setState 進行組件更新,
此作法稱為受控組件.

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: ''
    };
  }

  handleChange(e) {
    this.setState({
      value: e.target.value,
    });
  }

  handleSubmit(e) {
    alert('提交的名字:' + this.state.value);
    e.preventDefault();
  }

  render() {
    return (
      <form>
        <label>
          姓名：
          <input type="text" value={this.state.value} onChange={this.handleChange.bind(this)} />
        </label>
        <input type="submit" onClick={this.handleSubmit.bind(this)} />
      </form>
    );
  }
}

ReactDOM.render(
  <NameForm posts={posts} />,
  document.getElementById('app')
);
```

一個組件下可以允許多種不同表單元素共用同一個組件方法,
可利用 e.target.name 值處理要執行的動作.

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "",
      drink: "bubbletea",
      noice: false,
      nosugar: false,
    };
  }

  handleChange(e) {
    const target = e.target;
    const value = target.type == 'checkbox' ? target.checked : target.value;
    const name   = target.name;

    this.setState({
      [name]: value
    });
  }

  handleSubmit(e) {
    alert(
      "訂購者: " + this.state.name + 
      ", 訂購飲料: " + this.state.drink + 
      ", " + this.state.noice + " " + this.state.nosugar
    );
    e.preventDefault();
  }

  render() {
    return (
      <form>
        <div>
          姓名：
          <input name="name" 
                 type="text" 
                 value={this.state.value} 
                 onChange={this.handleChange.bind(this)} />
        </div>
        <div>
          飲料類型：
          <select name="drink"
                  value={this.state.value} 
                  onChange={this.handleChange.bind(this)}>
            <option value="blacktea">紅茶</option>
            <option value="milktea">奶茶</option>
            <option value="bubbletea">珍珠奶茶</option>
          </select>
        </div>
        <div>
          客製：
          <input name="noice" 
                 type="checkbox" 
                 value={this.state.noice} 
                 onChange={this.handleChange.bind(this)} /> 去冰
          <input name="nosugar" 
                 type="checkbox" 
                 value={this.state.nosugar} 
                 onChange={this.handleChange.bind(this)} /> 無糖
        </div>
        <input type="submit" onClick={this.handleSubmit.bind(this)} />
      </form>
    );
  }
}

ReactDOM.render(
  <NameForm />,
  document.getElementById('app')
);
```

像是 file 類型的表單元素 value 唯讀,\
因此他屬於非受控組件,\
請閱讀非受控組件章節.

```
<input type="file" />
```

表單元素若需要包含驗證機制,\
推薦使用 https://jaredpalmer.com/formik/.

## 狀態提升

React 實務上,\
多個組件會需要反映相同的數據變化與共用同一個父組件,\
以下範例為範例.

```
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit',
};


function toCelsius(temperature) {
  return (temperature - 32) * 5 / 9;
}

function toFahrenheit(temperature) {
  return (temperature * 9 / 5) + 32;
}

function tryConvert(temperature, convert) {
  const input = parseFloat(temperature);
  if (Number.isNaN(input))
    return '';
  
  const output = convert(input);
  const rounded = Math.round(output * 1000) / 1000;
  return rounded.toString();
}

class BoilingVerdict extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return this.props.celsius >= 100 ?
      <p>水會沸騰</p>:
      <p>水不會沸騰</p>;
  }
}

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
  }

  handleChange(e) {
    this.props.onTemperatureChange(e.target.value);
  }

  render() {
    let temperature = this.props.temperature;
    let scale = this.props.scale;

    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input type="text"
               value={temperature} 
               onChange={this.handleChange.bind(this)} />
      </fieldset>
    );
  }
}

class Calculator extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      temperature: '',
      scale: 'c',
    };
  }

  handleCelsiusChange(temperature) {
    this.setState({
      scale: 'c',
      temperature
    });
  }

  handleFahrenheitChange(temperature) {
    this.setState({
      scale: 'f',
      temperature
    });
  }

  render() {
    const temperature = this.state.temperature;
    const scale = this.state.scale;
    const celsius = scale === 'f' ? tryConvert(temperature, toCelsius) : temperature;
    const fahrenheit = scale === 'c' ? tryConvert(temperature, toFahrenheit) : temperature;

    return (
      <div>
        <TemperatureInput 
          scale="c"
          temperature={celsius}
          onTemperatureChange={this.handleCelsiusChange.bind(this)} />
        <TemperatureInput 
          scale="f"
          temperature={fahrenheit}
          onTemperatureChange={this.handleFahrenheitChange.bind(this)} />
        <BoilingVerdict celsius={parseFloat(celsius)} />
      </div>
    );
  }
}

ReactDOM.render(
  <Calculator />,
  document.getElementById('app')
);
```

## 組合 vs 繼承

### 包含關係

有時候組件無法完全知道子組件具體內容,\
因此我們會透過 props.children 再由子組件去取得父組件的子結構.

範例:\
WelcomeDialog 為第一層,\
FancyBorder 為第二層,\
WelcomeDialog 內的&lt;h1&gt; ... &lt;p&gt; 為第三層,\
透過 props.children 將三層交互渲染出來.

```
class FancyBorder extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <div className={'FancyBorder FancyBorder-' + this.props.color}>
        {this.props.children}
      </div>
    );
  }
}

class WelcomeDialog extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <FancyBorder color="blue">
        <h1 className="Dialog-title">
          Welcome
        </h1>
        <p className="Dialog-message">
          Thank you for visiting our spacecraft.
        </p>
      </FancyBorder>
    );
  }
}

ReactDOM.render(
  <WelcomeDialog />,
  document.getElementById('app')
);
```

以下為多組交互的 props.children 的範例

```
class Chat extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <div>This is chatbox.</div>
    );
  }
}

class Contacts extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <div>This is contact list.</div>
    );
  }
}

class FancyBorder extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <div>
        <div className="panel-left">
          {this.props.left}
        </div>
        <div className="panel-right">
          {this.props.right}
        </div>
      </div>
    );
  }
}

class WelcomeDialog extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <FancyBorder 
        left={ <Contacts /> }
        right={ <Chat /> }
      />
    );
  }
}

ReactDOM.render(
  <WelcomeDialog />,
  document.getElementById('app')
);
```

以下為另一個多組交互的 props.children 的範例

```
class FancyBorder extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <div className={'FancyBorder FancyBorder-' + this.props.color}>
        {this.props.children}
      </div>
    );
  }
}

class Dialog extends React.Component {
  constructor(props) {
    super(props);
  }
  
  render() {
    return (
      <FancyBorder color="blue">
        <h1 className="Dialog-title">
          {this.props.title}
        </h1>
        <p className="Dialog-message">
          {this.props.message}
        </p>
        {this.props.children}
      </FancyBorder>
    );
  }
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      login: ''
    }
  }

  handleChange(e) {
    this.setState({
      login: e.target.value
    });
  }

  handleSubmit(e) {
    alert(`Welcome! ${this.state.login}`);
    e.preventDefault();
  }
  
  render() {
    return (
      <Dialog
        title="Mars Exploration Program"
        message="How should we refer to you?">
        <input value={this.state.login} onChange={this.handleChange.bind(this)} />
        <button onClick={this.handleSubmit.bind(this)}>Sign me up!</button>
      </Dialog>
    );
  }
}

ReactDOM.render(
  <SignUpDialog />,
  document.getElementById('app')
);
```
