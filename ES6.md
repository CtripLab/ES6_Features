#ES6 New Features

##Introduction
ECMAScript 6，又被称作ECMAScript 2015，是ECMAScript最新标准，已经在2015年6月正式发布了。

它的目标，是使得JavaScript语言可以用来编写复杂的大型应用程序，成为企业级开发语言。

在nodejs中可以使用大多数的ES6特性，只需要在启动应用的时候加上--harmony即可。
```shell
node yourapp.js --harmony
```
如果需要使用更多的ES6特性则需要使用转码器Babel，Tracuer等。[使用方法](http://es6.ruanyifeng.com/#docs/intro#Babel转码器)

ES6特性的支持情况。[Click Here](http://kangax.github.io/es5-compat-table/es6/)

##Features

- Block-Scoped Constructs Let and Const（块级作用域）
- Destructuring Assignment（解构赋值）
- Multi-line Strings（多行字符串）
- Arrow Functions（箭头函数）
- Promises
- Classes（类）
- Enhanced Object Literals（增强的字面量对象）
- Default Parameters（默认参数）
- Modules（模块）
- Template Literals （模板文本）
- Generator

##Details

###Block-Scoped Constructs Let and Const

由于在以前的标准中，JavaScript是有hoisting机制，JavaScript引擎在执行的时候，会把所有变量的声明都提升到**当前作用域**（即function是唯一拥有自身作用域的结构），这其实是一种很不友好的处理，所以ES6中引入了块级作用域的概念。

```JavaScript
var v = "hello";
(function () {
    console.log(v);
    var v = "world";
})();
// 运行结果：undefined
// 上面的代码等价于
var v = "hello";
(function () {
    var v;
    console.log(v);
    v = "world";
})();
```
let是一种新的变量声明方式，它允许你把变量作用域控制在块级里面。我们用大括号定义代码块，在ES5中，块级作用域起不了任何作用：

```JavaScript
// Expression Code
function getLvl(flag) {
    var memberLevel = 'ordinary';
    if (flag === 0) {
        var memberLevel = 'silver';
    }
    {
        var memberLevel = 'gold';
    }
    {
        var memberLevel = 'diamond';
    }
    return memberLevel;
}
console.log(getLvl(0));// 'diamond'
```
此处结果返回diamond，和预期是不一样的，下面用let声明看下结果：
```JavaScript
// Expression Code
function getLvl(flag) {
    let memberLevel = 'ordinary';
    if (flag === 0) {
        let memberLevel = 'silver';
    }
    if (flag === 1) {
        let memberLevel = 'gold';
    }
    if (flag === 2) {
        let memberLevel = 'diamond';
        console.log(memberLevel);
    }
    return memberLevel;
}
console.log(getLvl(2));// 'diamond' 'ordinary'
```
可以看到使用let声明的变量的作用域只有在当前的花括号中，下面简单看下const
```JavaScript
const a = 10;
a = 20;
console.log(a);// 10
```
这里运行的结果说明使用const声明的常量是无法被更改的，这里还有一个小细节，let和const都不可以在同一个作用域中重复声明。

块级作用域的的作用：
```JavaScript
// 变量提升带来的问题
var tmp = new Date();

function f(){
  console.log(tmp);
  if (false){
    var tmp = "hello world";
  }
}

f();

--------分割线----------

// 用来计数的i泄漏为全局变量
var s = 'hello';

for (var i = 0; i < s.length; i++){
  console.log(s[i]);
}

console.log(i); // 5
```
###Destructuring Assignment（解构赋值）

用途：
1、交换变的值
```JavaScript
[x,y] = [y,x];
```
2、函数多个返回值
```JavaScript
function test(){
    return [1,2,3];
}
let [a,b,c] = test();

function test1(){
    return {
        foo : 1,
        bar : 2
    }
}
let { foo, bar} = test1();
```
3、提取json数据
```JavaScript
let jsonData = {
    a: 1,
    b: 'aaa',
    c: [1, 2]
}
let { a, b, c: arr} = jsonData;
console.log(a, b, arr);
```
4、等等。。。。想了解更多。[Click Here](http://es6.ruanyifeng.com/#docs/destructuring)

###Multi-line Strings（多行字符串）

这个功能很实用，如下所示：
```JavaScript
var str = 'this is a trial sentence.this is a trial sentence.this is a trial sentence.\
    this is a trial sentence.this is a trial sentence.this is a trial sentence.\
    this is a trial sentence.this is a trial sentence.this is a trial sentence.\
    this is a trial sentence.this is a trial sentence.this is a trial sentence.\
    this is a trial sentence.this is a trial sentence.this is a trial sentence.';
// 或者
var str = 'this is a trial sentence.this is a trial sentence.this is a trial sentence.'
    + 'this is a trial sentence.this is a trial sentence.this is a trial sentence.'
    + 'this is a trial sentence.this is a trial sentence.this is a trial sentence.'
    + 'this is a trial sentence.this is a trial sentence.this is a trial sentence.'
    + 'this is a trial sentence.this is a trial sentence.this is a trial sentence.';
// in ES6
let str = `this is a trial sentence.this is a trial sentence.this is a trial sentence.
    this is a trial sentence.this is a trial sentence.this is a trial sentence.
    this is a trial sentence.this is a trial sentence.this is a trial sentence.
    this is a trial sentence.this is a trial sentence.this is a trial sentence.
    this is a trial sentence.this is a trial sentence.this is a trial sentence.`;
```

###Arrow Functions（箭头函数）

直接上代码
```JavaScript
var f = v => v;
```
等同于
```JavaScript
var f = function(v){
    return v;
}
```
可以配合默认参数，解构赋值一起使用，简化了回调函数的function关键字。

比较重要的一点，箭头函数this对象，就是定义时所在的对象。这个是很重要的一个信息，看看下面的例子：
```JavaScript
// 或者使用call来解决
var str = 'nihao es6';
var handler = {
    str: 'hello es6',
    upperCase: function(){
        // var self = this; 
        this.str = this.str.toUpperCase();
        return function(){
            // return console.log(self.str);
            return console.log(this.str);
        }
    }
}
handler.upperCase().call(handler);// 'HELLO ES6'
handler.upperCase()();// 'nihao es6'
```
如果使用箭头函数，则完全不会出现这种状况
```JavaScript
var str = 'nihao es6';
var handler = {
    str: 'hello es6',
    upperCase: function(){
        this.str = this.str.toUpperCase();
        return ()=>console.log(this.str);
    }
}
handler.upperCase()();// 'HELLO ES6'
```
###Promises
Promise是异步变成的一种解决方案，比传统的解决方案-回调函数和时间更合理和更强大。

Promise最早是由社区提出，ES6将其写进了语言标准，统一了用法，提供了原生的Promise对象。三种状态resolve, reject以及pending状态。

```JavaScript
// 一个普通的回调函数
setTimeout(function(val){
    console.log(val);
},1000,'ok');
```
使用Promise改写
```JavaScript
var promise = new Promise(function(resolve, reject){
    setTimeout(resolve, 1000,'ok');
});
// 或者使用箭头函数
var promise = new Promise((resolve, reject) => {
    setTimeout(resolve, 1000,'ok');
});
promise.then((value)=>{
    console.log(value);
});
```
其实现在看起来，使用Promise并没有减少代码，或者让代码更具有可读性，其实在比较简单的没有多层嵌套的时候使用Promise未必是明智的，下面我们看下多层嵌套（callback hell）的场景，使用Promise：
```JavaScript
var fn = function (value, callback) {
    console.log(value);
    callback();
};
// deep callback
setTimeout(function (value) {
    fn(value, function () {
        setTimeout(function (value1) {
            fn(value1, function () {
                setTimeout(function (value2) {
                    fn(value2, function () {
                        setTimeout(function (value3) {
                            fn(value3, function () {
                                console.log('done')
                            });
                        }, 1000, 'z');
                        setTimeout(function (value4) {
                            fn(value4, function () {
                                console.log('done1')
                            });
                        }, 500, 'z1');
                    });
                }, 1000, 'y');
            })
        }, 1000, 'x');
    })
}, 1000, 'start');
```
这样的代码基本不可阅读，不忍直视，现在使用Promise改写
```JavaScript
var promise = () => {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, 1000, 'start');
    });
}
var promise1 = () => {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, 1000, 'x');
    });
}
var promise2 = () => {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, 1000, 'y');
    });
};
var promise3 = () => {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, 1000, 'z');
    });
};
var promise4 = () => {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, 500, 'z1');
    });
};

promise()
    .then((value) => {
        console.log(value);
        return promise1();
    })
    .then((value1) => {
        console.log(value1);
        return promise2();
    })
    .then((value2) => {
        console.log(value2);
        promise3().then((value3) => {
            console.log(value3);
            console.log('done')
        });
        promise4().then((value4) => {
            console.log(value4);
            console.log('done1')
        });
    })
```
Promise.all 和 Promise.race

Promise.all 可以接收一个元素为 Promise 对象的数组作为参数，当这个数组里面所有的 Promise 对象都变为 resolve 时，该方法才会返回。

Promise.race  它同样接收一个数组，不同的是只要该数组中的 Promise 对象的状态发生变化（无论是 resolve 还是 reject）该方法都会返回。

Promise.then 和 Promise.catch 可以自己去看下官方解释，这里不深入。[Click Here](http://es6.ruanyifeng.com/#docs/promise)

###Classes（类）

现在javascript里面的保留字class终于有用处了，在原来的开发过程中，写一个类有很多种方法，这里不多讲[Click Here](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)，由于方法很多就造成了混乱。

用ES6则会相对简洁（虽然仍然是使用原型实现类）。下面看下代码，我们：
```JavaScript
class BaseModel {
    constructor(options,data) {
        this.name = 'Base';
        this.url = 'http://m.abc.com';
        this.data = data;
        this.options = options;
    }
    
    getName() {// 方法名不需要添加function关键字 不需要分配this，现在只需要简单的在构造函数中分配就可以了。
         console.log(`Class name:${this.name}`);
    }
}
```
BusinessModel从类BaseModel中继承而来：
```JavaScript
class BusinessModel extends BaseModel {
    constructor(options, data) {
        super({ private: true }, ['zero', 'one']);// 可以很简单的调用super
        this.name = 'Business Model';
        this.url = 'http://m.abc.com/business/';
    }
    
    get dataLength() {
        return this.data.length;
    }
    
    set businessData(data) {
        this.data = data;
    }
}

let business = new BusinessModel(5);
console.log(business.dataCount)
business.data = [1, 2, 3];
console.log(business.data);
console.log(business.dataCount);
```
关于更多Class的内容可以看这里[Click Here](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes);

###Enhanced Object Literals（增强的字面量对象）

内容太多，建议看这里[Click Here](http://es6.ruanyifeng.com/#docs/object)

###Default Parameters（默认参数）

直接上例子
```JavaScript
var painter = function (l, w, h) {
    obj = {
        first: l || 'red',
        second: w || 'green',
        third: h || 'yellow'
    }
    return obj
}
```
in ES6
```JavaScript
let painter = function (one = 'red', two = 'green', three = 'yellow') {
    return {
        first: one,
        second: two,
        third: three
    }
}
```
更多看这里[Click Here](https://github.com/lukehoban/es6features#default--rest--spread)

###Modules（模块）

原来在nodejs中我们是这样加载模块的：
```JavaScript
// 引入模块
var a = require('a');
...
var z = require('z');

// 导出模块
module.exports = {
    a: a,
    z: z
}
```
in ES6
```JavaScript
// 引入模块
import {a,z} from 'module';
// 或者
import * as service from 'module'
service.a();
service.z();
// 导出模块
export var a =100;
export function get(){
    ...
};
```
更多请看这里[Click Here](http://exploringjs.com/es6/ch_modules.html)

###Template Literals （模板文本）
这个就很容易理解了,ES6中优化了字符串的拼接：

in ES5
```JavaScript
var name = 'Your name is ' + first  + ' ' + last  + '!'
```
in ES6 中我们可以使用新的语法${xxx},并把他放在反引号里：
```JavaScript
var name = `Your name is ${first}  ${last}!`
console.log(name);
```

###Generator（生成器）

####基本概念
Generator Function

生成器函数用于生成生成器(Generator)，它与普通函数的定义方式的区别就在于它需要在function后加一个*。

```JavaScript
function* FunctionName() {
  // ...Generator Body
}
```
生成器函数的声明形式不是必须的，同样可以使用匿名函数的形式。
```JavaScript
let FunctionName = function*() { 
    /* ... */ 
}
```
生成器函数的函数内容将会是对应生成器的运行内容，其中支持一种新的语法yield。它的作用与return有点相似，但并非退出函数，而是 切出生成器运行时 。

构建一个生成器函数
```JavaScript
// 根据斐波那契数列定义，构建一个Generator函数
function* fibo() {
    let [a, b] = [1, 1]

    yield a
    yield b

    while (true) {
        [a, b] = [b, a + b]
        yield b
    }
}
// 声明一个gen用于运行生成器内容和接收返回值。
let gen = fibo();

// 得到生成器以后，我们就可以通过它进行数列项生成了。此处演示获得前 10 项。
let arr =[];
for(let i= 0;i< 10;i++)
    arr.push(gen.next().value)
console.log(arr);// =>[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```
再一个例子
```JavaScript
// 模拟一个异步过程
var promise = () => {
    return new Promise((resolve, reject) => {
        setTimeout(function(data){
            if(data){
                resolve(data);
            }else{
                reject(new Error('something wrong!'));
            }
        }, 1000,'data');
    });
}
// 构造一个Generator
function* gen(){
    var result = yield promise();
    return 'ok';
}

var g = gen();
var result = g.next();

result.value.then(function(data){
    console.log(data);
}).then(function(err){
    g.next();
});
```
Gnerator的经典应用，[Koa](https://github.com/koajs/koa)的中间件实现

关于Generator，更多请看[这里](http://lifemap.in/koa-co-and-coroutine/)
### More Information
[Babel的安装和使用](http://www.cnblogs.com/whitewolf/p/4357916.html?utm_source=tuicool&utm_medium=referral)

[ES6官方文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

[ruanyfeng的博客](http://es6.ruanyifeng.com/)

[ES6博文](http://www.cnblogs.com/Wayou/p/es6_new_features.html)

[Traceur](http://google.github.io/traceur-compiler/demo/repl.html#)





