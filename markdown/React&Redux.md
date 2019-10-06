### JSX
JSX是js语法的扩展，让我们可以在js中编写HTML一样的代码；<br>
相比传统的HTML它可以使用不仅仅是HTML标签，也可以是创建的组件，这点是传统HTML不具备的<br>

### 设计理念
UI = render(data)

### 易于维护组件的设计要素
高内聚低耦合

### 组件数据
prop:对外组件使用<br>
state：组件内部使用<br>
*state存在的意义就是用于内部使用，内部修改，因此不应该修改外部prop*
```
prop:
在JSX中是每个标签内的属性值如:
<button id="myId" caption=""first>中的prop就是id和caption<br>
在对应的组件内部是通过构造函数中construtcor定义的的形参
props和super中继承并定义的形参props来接收
具体prop通过props.key（key名）来获取，如：<br>
console.log(props.id) //"myId"
```
```
state:
在构造器constructor中初始化以及定义（给人的感觉像是vue中的data）
如果需要改变默认初始化的state，需要用到this.setState()函数来改变
如果是直接暴力的修改的话，虽然直接改变了组件的内部状态
但是没有驱动组件重新渲染（响应式数据问题），所以不会反应state的变化
既组件使用的仍然是之前的初始化数据而非直接修改后的数据
因为this.setState()函数能驱动组件去重新渲染
```

### 组件的生命周期
生命周期的顺序依次：<br>
Mount（装载）：组件第一次在DOM树中渲染的过程<br>
Update（更新）：当组件被重新渲染的过程<br>
Unmount（卸载）：组件从DOM中删除的过程<br>
*可以看到，使用比较多的应该是前两个过程时候的钩子函数*
<br><br>
下面按顺序先介绍装载过程的钩子函数<br>
#### Mount（装载过程）：<br>
*concstructor:*<br>
顾名思义，就是ES6中每个继承组件类下的构造函数（constructor）<br>
而有些组件是无状态的，不会定义构造函数
而定义构造函数的往往是：<br>
1. 初始化state
2. 绑定this(new绑定，你不知道的javascript上卷有详细说明)<br><br>

*getInitialState和getDefaultProps:*<br>
这两个函数在ES6语法中不会产生作用，只有在React.createClass创建的组件类中才能起到作用<br>
getInitialState：返回值会用来初始化组件的this.state<br>
getDefaultProps: 返回值可以作为props的初始值<br>
使用方法于访问器属性get/set类似<br>
```
const sample = React.createClass({
    getInitialState:function(){
        return {foo:'bar'}
    },
    getDeaultProps:function(){
        return {sampleProp:0}
    }
})

等效于ES6写法：
class sample extend React.Component{
    constructor(props){
        super(props);
        this.state = {foo:'bar'};
    }
};
sample.defaultProps = {
    sampleProp:0
}
```
因为上面两个函数已经给Facebook逐渐废弃，因此不建议使用

*render:*<br>
还是顾名思义，就是渲染的意思，因为UI=render(data),所以我们可以看出这个函数的重要性了<br>
组件内的其他函数都是默认实现，而render函数需要我们手动实现
<br><br>

*componentWillMount和componentDidMount:*
在Mount这个转载过程中，componentWillMount会在render函数调用之前调用<br>componentDidMount会在render函数调用之后调用<br>
因为是componentWillMount在装载前调用，既组件没渲染的时候调用，因此使用的次数可能就没这么多了，重要的还是它的对称函数componentDidMount<br>
该函数是在render之后之后使用，之所以要强调一遍，是因为它是在所有其他组件render之后才进行收尾调用的<br>
bonus:<br>
componentWillMount可以在服务端调用也可以在浏览器端调用<br>
而componentDidMount只能在浏览器端调用<br>
**原因**：<br>
因为是在render之后，此时已经存在DOM树，但是服务端渲染，是不需要构建DOM树，所以就服务端就不需要调用了

#### Update（更新过程）:
当this.state或者props改变的数据，会驱动组件重新渲染，这就是更新的过程<br>

*componentWillReceiveProps(nextProps):*<br>
实际上只要是触发更新过程的情况存在，该函数就会被调用<br>
不外乎this.state/props以及调用render函数，该函数即使不修改两个状态值也会触发重新渲染的过程。<br>
**需要注意的是，this.setState()触发的更新过程不会调用这个函数<br>
这个是因为这个函数适合根据新的props值（传入的参数nextProps）来确定是否更新this.state**
*试想一下，如果内部状态this.state改变会调用componentWillReceiveProps，如果componentWillReceivePros中存在this.setState（）修改的话，就会出现一个死循环，无限调用的情况*<br>
<br>
*shouldComponentUpdate(netProps,nextState):*<br>
重要级别堪比render,它决定了一个组件是否渲染<br>
它于render是react中唯二需要返回结果的函数，render返回的结果是return的用于构造DOM树的对象，而shouldComponentUpdate则是返回一个布尔值<br>
如果返回true那么在更新过程中，React库首先调用该函数，继续更新该过程，接下来调用render;<br>
如果返回值是false的话，就不要渲染了，就不会继续调用render了<br>
*感觉上和vue的v-if挺像的*<br><br>
*componentWillUpdate和componentDidUpdate：*<br>
前面介绍的shouldComponentUpdate如果返回值为true的时候<br>
调用顺序为componentWillupdate、render、componentDidupdate<br>
和之前谈到的componentDidMount只能在浏览器端调用不同，它既可以在服务器端调用也可以在浏览器端调用

#### UnMount(卸载过程)：
该过程只设计到一个函数：componentWillUnmount
当组件从DOM树中移除时调用

### Flux和Redux


#### MVC（Model View Controller）框架的缺陷
M层：负责管理数据逻辑，大部分的业务逻辑就在M层<br>
V层：负责页面渲染
C层：接受用户输入的数据再交接调用M层的部分逻辑，把逻辑处理的数据交付给V层进行展示

当整体代码复杂且巨大的时候，产生的bug因为大量模块间的耦合关系，使得维护起来变得举步维艰（特别是新人）<br>
而当MV层之间彼此进行通信的时候，这更是一种灾难<br>



#### Flux
*Flux中最重要的思想：单向数据流*<br>
*Flux相对于传统的MVC框架，特点是对数据的控制更加严格*<br>
一个Flux应用包含四个部分：<br>
Dispatcher：处理动作分发，维持Store之间的依赖关系<br>
Store: 负责存储数据和处理数据相关逻辑<br>
Action：驱动Dispatcher的js对象<br>
View：负责页面展示<br>

相对于传统MVC框架而言，多了一个Action；Dispatcher相当于Controller，Store相当于Model

#### Flux的优势
单向数据流禁绝了MV层通信的问题

#### Flux的不足
1. Store之间的依赖关系
2. 难以进行服务端渲染
3. Store混杂了逻辑和状态

### redux
Redux的三个基本原则：<br>
1. 唯一的数据源
2. 保持状态可读
3. 数据改变只能通过纯函数实现

<br><br>
*唯一数据源：*<br>
唯一数据源指的是应用的状态数据应该只存储再唯一的一个Store上（与Flux不同的是只保存一个Store而不是多个Store）。<br><br>
*保持状态已读：*<br>
不能直接修改状态，要修改也要通过派发Action对象完成<br><br>
*数据改变只能通过纯函数实现：*<br>
这里所说的纯函数指的的Reducer这个函数概念，实现参考js的reduce函数<br>
reducer(state,action)<br>
第一个函数是当前状态，第二个参数是接收的action对象；<br>
reducer函数就是通过state和action的值来产生一个新的对象<br>
reducer不改变state和action，只通过它们来返回一个新的值
















