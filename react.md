--------------------------4.7------------------------------

# 1. 模块和组件

## 1.1 如何定义组件

### 1. 函数式组件

React调用组件函数时，this丢失， this本来是指向window的，但是开启严格模式，禁止this指向window

```html
        <script type="text/babel">
            //1.定义一个组件(函数式)
            function Demo(){
                console.log(this) //此处的this是undefined，因为经过babel翻译，开启了严格模式
                return <h2>我是用函数定义的组件（适用于【简单组件】的定义）</h2>
            }
            //2.渲染组件到页面
            ReactDOM.render(<Demo/>,document.getElementById('test'))
            /* 
                执行了ReactDOM.render(<Demo/>......后发生了什么？
                    1.React解析组件标签，寻找Demo组件的定义位置
                    2.React发现Demo组件是用函数定义的，随后React去直接调用Demo函数，将返回的虚拟DOM渲染到页面

            */
        </script>
```

### 2. 类式组件

#### 类的相关知识

```javascript
## js拼串
console.log(`我叫${this.name}，我今年${this.age}了`)

## 类中可以写什么
            class Car {
                //类中能写什么代码？

                //构造器方法
                constructor(name,price){
                    this.name = name
                    this.price = price
                }

                //一般方法
                run(){
                    console.log('我正在前进')
                }

                //赋值语句，例如a=1，给实例自身添加一个属性，名为a，值为1
                a = 1
                b = function(){
                    console.log('我是b函数')
                }
            }

## static 静态属性不放在__proto__原型上了，放在类本身上
```

#### 解决this指针的指向问题

```html
call, apply, bind 都可以改变this指向，
    <body>
        <script type="text/javascript" >
            function demo(a,b,c){
                console.log(this)
            }
            demo()
            demo.call(1,10,20,30) 
            demo.apply(2,[10,20,30])  # 参数写成数组
            const x = demo.bind(3)
            x()
        </script>
    </body>
```

#### 解构赋值

```html
## 一般用于对象，数组较少
```

### 3. 类三大属性

#### state简写

```javascript
        <script type="text/babel">
            //定义一个Weather组件
            class Weather extends React.Component{
                //初始化状态, 直接赋值是在实例上加一个属性
                state = {isHot:true} 
                render(){
                    return <h1 onClick={this.changeWeather}>今天天气很{this.state.isHot ? '炎热' : '凉爽'}</h1>
                }
                //组件类中程序员定义的事件回调，必须写成赋值语句+箭头函数，避免了this为undefined的问题
                changeWeather = ()=> {
                    console.log(this)
                    const isHot = this.state.isHot
                    this.setState({isHot:!isHot}) //取反赋回去
                }
            }
            //渲染组件到页面
            ReactDOM.render(<Weather/>,document.getElementById('test'))
        </script>
```

#### props

state是写死在对象里面的状态初始化，想在外部带进去，在渲染到界面的时候在决定

构造器可以接受外部参数，构造对象. -----> 但是对象是React new出来的，

**props是用来收集外部传入的参数的**

```jsx
ReactDOM.render(<Person name={p1.name} sex={p1.sex} age={p1.age}/>,document.getElementById('test2'))

## 另外一种形式， 防止写的太长， jsx的展开语法 {...p1}
## 模拟后端返回的数据，
const p1 = {
    name:'强哥',
    sex:'女',
    age:19
}
ReactDOM.render(<Person {...p1}/>,document.getElementById('test2'))
```

```javascript
# ...展开运算， 拆包和打包
let arr = [3,4,5,6]
let arr2 = [1,2,...arr,7,8,9,10] //拆包， 展开arr，

# 都是浅拷贝
let obj = {name:'圆圆的海峰',age:18,sex:'女'}
let obj2 = {xingming:'帅气的超哥',nl:19,xb:'男'}

const cloneObj = {...obj,...obj2}
```

限制props， 传入参数的类型

姓名必须传，

```javascript
## static 放在类上，相当于Persion.propTypes

static propTypes = {
    name:PropTypes.string.isRequired,
    age:PropTypes.number,
    sex:PropTypes.string
}

static defaultProps = {
    age:18
}
```

<mark>函数式组件props，函数的参数是props</mark>

#### refs (尽量不使用，其他方式获取标签节点)

拿到真实dom，即简便方法获得标签

```javascript
# 字符串形式的ref， 已过时
<script type="text/babel">
    class Demo extends React.Component{
        render(){
            return (
                <div>
                    <input type="text" ref="input1"/> 
                    <button onClick={this.show}>点我提示左侧数据</button> 
                    <input type="text" ref="input2" onBlur={this.show2} placeholder="失去焦点提示数据"/>
                </div>
            )
        }
        show = ()=>{
            // const {refs:{input1:{value:a}}} = this
            const {input1} = this.refs
            alert(input1.value)
        }

        show2 = ()=>{
            const {input2} = this.refs
            alert(input2.value)
        }
    }
    ReactDOM.render(<Demo/>,document.getElementById('test'))
</script>

## 回调函数方式
<input type="text" ref={c => this.input1 = c}/> 
把标签放到this属性上，input1可以拿到， c是传入这个回调函数的参数，是当前标签

## createRef方式
container = React.createRef()
<input type="text" ref={this.container} /> 
```

#### 关于类式组件的构造器

```javascript
## 参数props接收标签属性， 直接使用props即可，不需要this，
constructor(props){
    // console.log('constructor',props)
    super()
    console.log('constructor',props)
}
```

### 4. React中的事件处理

js 中自定义事件

```javascript
<body>
    <div id="div" style="background-color: orange; width: 200px; height: 200px;">我是一些内容</div>

    <script type="text/javascript" >
        const div = document.getElementById('div')
        //创建一个haha事件
        const haha = new Event('onClick')

        div.addEventListener('haha',()=>{
            console.log('你笑了')
        })
        ## 定时器，setTimeout
        setTimeout(()=>{
            div.dispatchEvent(haha)
        },2000)
    </script>
</body>
```

React中事件处理, 事件冒泡

```javascript
<script type="text/babel">
    /* 
        1.    通过onXxx属性指定事件处理函数(注意大小写) 
                        1).React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 ——————  为了更好的兼容性
                        2).React中的事件是通过事件委托方式处理的(委托给组件最外层的元素) ———————— 效率高
        2.    通过event.target得到发生事件的DOM元素对象
    */

    class Demo extends React.Component{
        render(){
            return (
                <div className="container" onClick={this.test}>
                    <button onClick={this.show1}>按钮1</button>    
                    <button onClick={this.show2}>按钮222222</button>
                    <div onClick={this.show3} className="child">xxxx</div>    
                </div>
            )
        }
        show1 = (event)=>{
            // 接到的event是React自定义的事件对象,
            // 这个event拥有着和原生event同样的属性
            event.stopPropagation()
            console.log('按钮1',event)
        }
        show2 = (event)=>{
            console.log(event.target.innerText)
        }
        show3 = ()=>{
            console.log('xxxxx')
        }
        test = ()=>{
            console.log('test')
        }
    }

    ReactDOM.render(<Demo/>,document.getElementById('test'))

</script>
```

## 1.2  收集表单

### 1.2.1. 非受控组件收集

* 非受控组件：表单中的数据，在需要的时候，“现用现取” (通过ref获得到节点，进而访问到value值)

* 受控指的是和state建立联系，

```javascript
<script type="text/babel">
	class Login extends React.Component{
		render(){
			return (
				<form onSubmit={this.handleLogin}>
					用户名：<input type="text" ref={c => this.userNameNode = c}/><br/><br/>
					密码：<input type="password" ref={c => this.passwordNode = c}/><br/><br/>
					<button>登录</button>
				</form>
			)
		}
		handleLogin = (event)=>{
			event.preventDefault()   /*防止表单提交页面刷新默认行为*/
			const {userNameNode,passwordNode} = this
			alert(`用户名是${userNameNode.value}，密码是${passwordNode.value}`)
		}
	}

	ReactDOM.render(<Login/>,document.getElementById('test'))
</script>
```

### 1.2.2. 受控组件收集

* 受控组件：表单中输入类的DOM，随着用户的输入，将值收集到state中，那么就称为受控组件

```javascript
class Login extends React.Component{
	state = {
		username:'',
		password:''
	}
	
	render(){
		return (
			<form onSubmit={this.handleLogin}>
				用户名：<input type="text" onChange={this.saveUsername}/><br/><br/>
				密码：<input type="password" onChange={this.savePassword}/><br/><br/>
				<button>登录</button>
			</form>
		)
	}

	//保存用户名到state中
	saveUsername = (event)=>{
		this.setState({username:event.target.value}) //setState不会造成无关数据的丢失
	}

	//保存密码到state中
	savePassword = (event)=>{
		this.setState({password:event.target.value})
	}

	handleLogin = (event)=>{
		event.preventDefault()
		const {username,password} = this.state
		alert(`用户名是${username}，密码是${password}`)
	}
}
```
