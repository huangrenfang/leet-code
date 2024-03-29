####什么是this<br>
this是在运行时进行绑定的，而非在编写时绑定的<br>
所以它的上下文取决与函数调用时的各种条件，取决与函数的调用方式<br>
this只是函数被调用时创建的活动记录（或者说执行上下文）下面的一个属性罢了，这个记录记录了调用栈（函数在哪里被调用）、函数的调用方式和传入的参数


#### 常见错误的两种对于this的解释
1.指向自身<br>
以下代码证明
```
function foo(nums){
    console.log(this)
}
```
然而输出的是顶层对象window(this默认绑定)
> 如果需要绑定自身的函数可以通过具名函数.属性进行对内部成员变量对调用
另外一种方法是arguments.calle，但是已经弃用了，不建议使用

2.this指向函数对作用域

*需要明确的是在任何时候this都不指向函数作用域，但是在某种情况下是正确的*
因为函数作用域实际上只存在与javascript的引擎中，是无法通过代码来进行访问的（只能说作用域“对象”和对象类似罢了）

#### this调用位置解析
*调用栈（为了达到当前执行位置所调用的所有函数，可以理解为函数调用顺序执行链）*

#### 绑定规则
* 默认绑定<br>
*函数独立调用，不代任何修饰代函数。因为无法应用其他代规则，所以只能绑定默认值（严格模式下会绑定undfined）*

* 隐式绑定<br>
```
let obj = {
    a:2,
    foo:foo
}
function foo(){
    console.log(this.a);
    console.log(this)
}
obj.foo();
```
这段代码会输出<br>
this.a // 2<br>
this // {a:2,foo:foo} 也就是obj
因为在那个位置函数调用是在对象内段，所以会调用对象段执行上下文<br>
这时候this会绑定到obj<br>
如果有多个对象嵌套到话，this绑定到依然还是上一级对象的执行上下文

*隐式丢失*：<br>
```
function foo(){
    console.log(this.a);
}
var obj = {
    a:2,
    foo:f00
}
var bar = obj.foo;
var a = "not obj";
bar() //输出not obj
```
**隐式丢失问题**<br>
虽然bar是对obj.foo对引用，但实际上引用但是foo函数本身
因此此时但bar是一个不带修饰但函数调用，即上面所述过的默认绑定<br>不管是一个变量名，或者是新的函数进行LHS将函数赋值给该变量，最后绑定的方式一定是隐式绑定<br>
* 显式绑定
<br>通过call和apply进行执行上下文的绑定<br>
如果this绑定null或者undefined（第一个参数）的话，不会改变原先的this指向,实际应用的还是默认绑定——————这种绑定一般是用来传递参数的，但是使用过多会引起别的问题，如默认绑定的顶层对象修改问题<br>
#### 显示绑定下的硬绑定和软绑定（都是通过call，bind实现的）
*出现的目的就是解决隐式丢失问题，如果是通过隐式丢失的格式（将函数赋值给另外的变量，企图连带着改变this指向）来进行赋值会出现下面的情况*
> 硬绑定：绑定第一个this指向后，该指向即使是优先级高于显示绑定的new绑定也不会改变它的绑定<br>
软绑定：给默认绑定的全局对象或者严格模式下的undefined以外的值，既能实现硬绑定的解决隐式丢失问题也可以

* new绑定
在使用new实例化的时候<br>

    1.创建一个全新对象<br>
    2.这个新对象会被执行[[prototype]]连接
    3.新对象会绑定到函数调用的this
    4.如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象
    
#### 判断this
1.是否有关键字new生成的实例，是的话this指向实例对象<br>
2.是否显式绑定过，this指向绑定对象
3.是否隐式绑定过，是的话指向传参对象
4.以上都不是，即为默认绑定

#### 绑定优先级问题
> new绑定>显示绑定>隐式绑定>默认绑定

#### 有意思的科普环节来啦
> null一般我们认为是一个空对象指针，但是并不等于{}<br>因为{}还有原型链指向,还有Object.prototype。<br>而null是比{}更加空的对象，可以理解为初始化的对象，最本源的空对象。<br>具体通过Object.create(null)来创建最为可靠且安全的空对象来进行显式绑定的this绑定

#### 关于箭头函数的this绑定问题
> 其实箭头函数并没有this指向，它的this是继承过来的<br>一般是指向调用箭头函数的对象，也就是我们所谓的指向父级作用域（调用对象）<br>有意思的是，一旦被显示绑定一次后，后续将无法改变this指向，即使是优先级高于显示绑定的new绑定也不好使
