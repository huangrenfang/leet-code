### 开始
cdn引入react各项文件
```
<script src="https://cdn.staticfile.org/react/16.4.0/umd/react.development.js"></script>
<script src="https://cdn.staticfile.org/react-dom/16.4.0/umd/react-dom.development.js"></script>
<script src="https://cdn.staticfile.org/babel-standalone/6.26.0/babel.min.js"></script>
```
### JSX
JSX语法是js的一个扩展，它实现在js中编写html代码，而不是eval动态插入<br>
### RenderDOM.render()
渲染DOM树
```
<div id="root"><div>
<script type="text/babel">
ReactDOM.render(
    <div>
        <h1>Hello World!</h1>
    </div>,
    document.getElementById("root")
)
</script>
```
由上面的代码可以看到，语法是简洁易懂的<br>
需要注意的是：<br>
1. render函数下的根节点只能存在一个（VUE借鉴了该特性）
2. 根节点末尾需添加逗号“，”来分隔开需要挂在的id选择器元素
3. document.getElementById("")来获取renderDOM树的挂在节点

其实我们也可以把render内的HTML代码封装成一个组件（变量），通过组件来渲染,代码如下
```
<div id="root"><div>
<script type="text/babel">
const item = 
    <div>
        <h1>Hello World!</h1>
    </div>
ReactDOM.render(
    item,
    document.getElementById("root")
)
</script>
```

#### 组件化
组件定义： 组件从概念上看就像是函数，它可以接收任意的输入值，并返回一个需要在页面上展示的React元素<br>
语言是苍白的，还是show my code吧：
```
function Welcome(){
    return <h1>Hello World!</h1>
}
const element = <Welcome/>;
ReactDOM.render(
    element,
    document.getElementById("root")
)

当然Welcome组件也可以使用ES6的语法
箭头函数或者是Class定义一个类（推荐使用ES6语法，尽量别ES5,ES6混杂）
```

上述代码没有定义prop，也就是所谓的无状态的组件<br>
有prop的状态的组件是这样的：
```
function  Welcome(props) {
        return (<h1>Hello{props.test}</h1>) 4
}
let test = "World!" 3
const item = <Welcome test={test}/> // 2
ReactDOM.render(
    item,  //1
    document.getElementById("root")
)
```
正确的代码阅读顺序是上面标注的注释来阅读的
1. 渲染组件item
2. item变量是<Welcome>组件，期间通过prop下的test来进行传值
3. props.test的值等于"World!"
4. 将props.test的值渲染传入组件中 <br><br>

React中应该是将所有的prop都放在了类中的第一个形参里了，当然，咱们的prop可不仅仅的test一个，还可以定义很多很多，自由发挥，最后它们都会在props对象的同一级属性中<br><br><br>

**ES6语法**：<br>
无状态组件
```
class Welcome extends React.Component{
    render(){
        return <h3>hello world!<h3>
    }
}
ReactDOM.render(
    <Welcome />,
    document.getElmentById("root")
)
```
仔细比较，是否发现，完成一样的功能，更少的代码，更简单的理解和更少的变量（空间复杂度不就低了嘛）<br>
代码解读：<br>
> 这里使用到的class extends是ES6的语法，学过java的同学肯定不会陌生，这不就是照搬过来的嘛<br>React.Component是React中已经定义好的组件类，有能力的同学可以去翻读一下源码，这样理解和思维能更上一台阶<br>
实现的功能大概就是在render（）方法里，已经直接把里面的内容封装成一个组件了，只要在生命周期函数的render下直接使用组件即可，当然了，class下的super,construtor和定义的各式函数都可以的，后面会介绍

ES6语法是推荐使用，上面的低版本语法可以使用，但是逐渐被废弃，知道是什么内容了解是怎么发展，用来帮助自己理解就行<br><br>

有状态组件(constructor,super)
```
class Welcome extends React.coponent{
    constructor(props){
        super(props);
        this.state = {
            info:"this is hrf's world"
        }
    }
    render(){
        return (
        <h1> hello {this.state.info}
        )
    }
}
```
代码解读：<br>
> constructor构造器构造函数，形参props（起的作用和上面所述一致）<br>
super()来改变this指向，内在的原理应该是通过new绑定，来改变this指向（也可能是call,bind显示绑定来实现）。<br>

*要想多了解this绑定和this指向问题，可以参考我之前写的文章《this解析以及误区问题》，是参考《你不知道的javascript上卷》，这本书实在精彩，看完了估计你也会鼓掌喝彩的，推荐你们阅读*

construtor已经继承了父类的属性，如果想多定义属性，可以额外再定义；<br>
React中对状态的管理，外部的props传入组件内，而state只应用与组件内使用，提供给组件进行访问和修改,使用的方式与props一致，只能作用区域不同，而且props,state都可以通过this直接访问<br>
需要注意的是在有状态组件中需要添加this来进行访问

*实际上props vue应该也是借鉴了它的设计思想，vue中的props也是实现了由外向内的传值方式（父组件向子组件）*

#### 添加方法
```
class Count extends React.Coponent{
    constructor(props){
        super(props)
        this.state = {
            count:0
        }
        this.add = this.add.bind(this); //1
    }
    add(){
        this.setState({
            count:++this.state.count //2
        })
    }
    render(){
        return(
        <div>
            <h1>{this.state.count}</h1>
            <button onClick={this.add}>+1</button> //3
        </div>
        )
    }
}
```
代码解读：<br>
1. 在类中，定义的方法中，this指向不是指向我们所定义的类本身，而是undefined(this指向中严格模式下，默认绑定为undefined)，所以这一步是必要的，需要改变this指向，因为bind不会立即执行，所以使用bind而不是call
2. 想要改变内部state状态，最好的用this.setState方法来进行改变；原因是this.setState会驱动组件重新渲染（有vue基础的朋友可以理解为响应式数据原理），如果不重新渲染，那么可能会报错或者直接改变的state引用的值依然是初始值，因为它的值不会得到更新
3. 类中的任何变量，应该通过this访问来进行调动（第一步中执行的this显示绑定，也可以在这一步中实现,传参的时候用这种方法）

```
class Count extends React.Coponent{
    constructor(props){
        super(props)
        this.state = {
            count:0
        }
    //    this.add = this.add.bind(this); //1
    }
    add(num){
        this.setState({
            count:this.state.count+num //2
        })
    }
    render(){
        return(
        <div>
            <h1>{this.state.count}</h1>
            <button onClick={this.add.bind(this,10)}>+1</button> //3
        </div>
        )
    }
}
```
代码解读：<br>
1. 如果需要在方法中传入参数，和上述的一样，可以有多种方法，但是这里推荐的是第三个的显示绑定方法；当然，第一步显示绑定也不是不可以，但是复用性就低了一些，因为传入的参数固定了，第三步的绑定的话，可以调用多次，传入不同的参数，复用性高
2. 添加参数
3. 改变this指向的另外实现方法


#### 组件嵌套 
```
class Count extends React.Component{
    constructor(props){
        super(props)
        this.state = {
            count:0
        }
    //    this.add = this.add.bind(this); //1
    }
    add(num){
        this.setState({
            count:this.state.count+num //2
        })
    }
    render(){
        return(
        <div>
            <h1>{this.state.count}</h1>
            <button onClick={this.add.bind(this,10)}>+1</button> //3
        </div>
        )
    }
}
class Add extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            count:0
        }
    }
    render(){
        return(
        // content
        <Count/>
        // content
        )
    }
}
ReactDOM.render(
    <Add/>,
    document.getElementById("root");
)
```
代码解读：<br>
只需要在另外一个组件中直接调用需要嵌套的组件即可<br>
需要注意的是最后渲染的组件（父组件）问题

#### 组件间传值
通过在子组件中新定义props，props的值为父组件的方法，在方法中实现传值<br>

