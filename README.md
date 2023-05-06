
> 根据个人亲身面试经验积累的前端面试题库（含答案解析）

# 🚴‍♂️ 基础知识

## 样式布局

#### 1. 水平垂直水平居中有哪些实现方式？

#### 2. rem 是什么？跟 px 如何转换？说下原理及使用场景

#### 3. 同一套代码如何适配PC、移动两个端？

#### 4. position 的几个值代表啥意思？是否脱离文档流？分别是相对于谁定位的？

## javascript

#### 1. promise 是怎么实现的？链式调用是什么，原理是什么？

#### 2. 说说事件委托。

**事件委托**：通过共同的父节点设置事件统一执行子节点的事件，只需设置一个父节点的事件绑定，能减少内存占用。

```html
<!--未进行事件委托-->
<div id="box">
    <input type="button" id="add" value="添加" />
    <input type="button" id="remove" value="删除" />
    <input type="button" id="move" value="移动" />
    <input type="button" id="select" value="选择" />
</div>
<script>
window.onload = function(){
    var Add = document.getElementById("add");
    var Remove = document.getElementById("remove");
    var Move = document.getElementById("move");
    var Select = document.getElementById("select");
    
    Add.onclick = function(){
        alert('添加');
    };
    Remove.onclick = function(){
        alert('删除');
    };
    Move.onclick = function(){
        alert('移动');
    };
    Select.onclick = function(){
        alert('选择');
    }
}
</script>

<!--进行事件委托-->
<div id="box">
    <input type="button" id="add" value="添加" />
    <input type="button" id="remove" value="删除" />
    <input type="button" id="move" value="移动" />
    <input type="button" id="select" value="选择" />
</div>
<script>
window.onload = function(){
    var oBox = document.getElementById("box");
    oBox.onclick = function (ev) {
        var ev = ev || window.event;
        var target = ev.target || ev.srcElement;
        if(target.nodeName.toLocaleLowerCase() == 'input'){
            switch(target.id){
                case 'add' :alert('添加');break;
                case 'remove' :alert('删除');break;
                case 'move' :alert('移动');break;
                case 'select' :alert('选择');break;
            }
        }
    }
}
</script>
```

#### 3. js 事件循环是什么？为什么要引入 js 事件循环，并介绍下它的原理？

**EventLoop**
- 在JavaScript中，任务被分为MacroTask（宏任务）和MicroTask（微任务）两种。
    - MicroTask
        - process.nextTick（node独有）
        - Promise（new Promise()内的程序是同步代码，而 then 部分的程序才是 MicroTask 任务）
        - Object.observe(废弃)
        - MutationObserver
        - 因为 async await 本身就是 promise+generator 的语法糖。所以 await 后面的代码是 microtask。
    - MacroTask
        - script(整体代码)
        - setTimeout
        - setInterval
        - setImmediate（node独有）
        - MessageChannel
        - I/O
        - UI rendering
    - 执行顺序：在同一个上下文（执行环境）中，总的执行顺序为：执行同步代码 —> microTask（微任务） —> macroTask（宏任务）
- 分类
    - 浏览器的Event loop
    - Nodejs的EventLoop

```js
setTimeout(function () {
    console.log(1);
},0);
console.log(2);
process.nextTick(() => {
    console.log(3);
});
new Promise(function (resolve, rejected) {
    console.log(4);
    resolve()
}).then(res=>{
    console.log(5);
})
setImmediate(function () {
    console.log(6)
})
console.log('end');
// 2 4 end 3 5 1 6
```
```js
var outer = document.querySelector('.outer');
var inner = document.querySelector('.inner');
new MutationObserver(function() {
    console.log('mutate');
}).observe(outer, {
    attributes: true
});
function onClick() {
    console.log('click');
    setTimeout(function() {
        console.log('timeout');
    }, 0);
    Promise.resolve().then(function() {
        console.log('promise');
    });
    outer.setAttribute('data-random', Math.random());
}
inner.addEventListener('click', onClick);
outer.addEventListener('click', onClick);

// 点击 click，结果控制台输出：click、promise、mutate、click、promise、mutate、timeout、timeout
// 运行 inner.click()，结果控制台输出：click、click、promise、mutate、promise、timeout、timeout，注意：对一个节点添加观察器，就像使用addEventListener方法一样，多次添加同一个观察器是无效的，回调函数依然只会触发一次，因此此处虽然两次调用 observe(outer, {attributes: true})，但只有一次有效，只打印一次 mutate
```
```js
console.log('1');
async function async1() {
    console.log('2');
    await async2();
    console.log('3');
}
async function async2() {
    console.log('4');
}
 
process.nextTick(function() {
    console.log('5');
})
 
setTimeout(function() {
    console.log('6');
    process.nextTick(function() {
        console.log('7');
    })
    new Promise(function(resolve) {
        console.log('8');
        resolve();
    }).then(function() {
        console.log('9')
    })
})
 
async1();
 
new Promise(function(resolve) {
    console.log('10');
    resolve();
}).then(function() {
    console.log('11');
});
console.log('12');
// 1 2 4 10 12 5 11 3 6 8 7 9
```

#### 4. 哪些地方存在 this 指向问题？如何解决？

#### 什么是闭包

**闭包**：闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式就是在一个函数内部创建一个引用了函含数内部的私有变量的函数。
- 作用域链：包含闭包的作用域、包含该闭包的外部函数的作用域、全局作用域
- 闭包的缺点：当该闭包（函数）一直存在时，这个闭包的引用的外部变量的函数的作用域会一直保留在内存中而不会销毁。
- 闭包的用途
    - 块级作用域（匿名自执行函数 IIFE）
    - 私有变量
        - 私有变量的问题：定义特权方法需要使用构造函数
        - 静态私有变量：静态私有变量可以解决定义特权需要使用构造函数的问题
    - 静态私有变量
    - 模块模式（单例）
    - 结果缓存
    - 柯里化


```js
// 示例：闭包的用途

// 块级作用域（匿名自执行函数 IIFE）
function func(){
    // 块级作用域
    (function(){
        for(var i=0;i<10;i++){
            console.log(i)
        }
    })()
    // i 不存在
    console.log(i)
}

// 私有变量
function Person(){
    // 私有变量
    var _name
    // 特权方法
    this.getName = function(){
        return _name
    }
    // 特权方法
    this.setName = function(name){
        _name = name
    }
}
var person = new Person()
person.setName('person')
person.getName()

// 静态私有变量
function Person(){}
(function(){
    Person = function(){}
    // 私有变量
    var _name
    // 公有方法
    Person.prototype.getName = function(){
        return _name
    }
    // 公有方法
    Person.prototype.setName = function(name){
        _name = name
    }
})()
var person = new Person()
person.setName('person')
person.getName()

// 模块模式（单例）
var application = function(){
    // 私有变量
    var components = new Array()
    // 返回公有属性和方法
    return {
        getComponents: function(){
            return components
        }
    }
}

// 增强模块模式
var application = function(){
    // 私有变量
    var components = new Array()
    // 公有
    var app = {}
    app.getComponents = function(){
        return components
    }
    // 返回公有副本
    return app
}
```
```js
// 测试题
function fun(n, o) {
    console.log(o);
    return {
        fun: function(m) {
            return fun(m, n);
        }
    };
}

var a = fun(0); // ?
a.fun(1); // ?
a.fun(2); // ?
a.fun(3); // ?
var b = fun(0).fun(1).fun(2).fun(3); // ?
var c = fun(0).fun(1); // ?
c.fun(2); // ?
c.fun(3); // ?
```

## 前端存储

#### 1. 前端存储有哪些方式？cookie 跟 indexDB 有啥区别？是否持久化？

# 🤺 前端框架/库

## React

#### 1. react hooks 跟之前的 class 写法有什么不同？有哪些优势？

#### 2. react hooks 为什么没有生命周期？如果要使用生命周期怎么做？

#### 3. 了解 fiber 吗？它的原理是什么？

#### 4. 讲讲 React 技术栈的最佳实践

#### 5. React 性能优化需要考虑哪些？

#### 6. React 依赖注入是怎么实现的？

## Vue

#### 1. 讲讲 Vue 技术栈的最佳实践。

## 微前端

#### 1. 微前端框架（例如qiankun）实现js隔离的原理是什么？css 是如何隔离？常见微前端框架有哪些？能否比较下他们的实现原理？为什么选择 qiankun？

#### 2. 微前端使用过程中遇到哪些问题？挑一个难点讲下。

## 低代码

#### 1. 低代码开发平台如何实现？比如说从拖动组件到生成元素到我们应用页面上的过程是怎样的？

## 跨端开发（移动端/桌面端）

#### 1. 介绍下 Flutter，并说下 Flutter 和 RN 的区别。

# ⚔️ 工程化

## 模块化

## 组件化

####  1. 封装一个模态弹窗需要考虑哪些点？

## 错误监控

#### 1. 错误上报/捕获全局 sdk 如何写？需要捕获哪些东西？在哪写？



## 构建工具

#### 1. webpack plugin 跟 loader 有何不同？有没有使用 plugin 写过一些插件？
## devOps

#### 1. 说说你对 devOps 的理解。

#### 2. CI/CD这些了解吗？说下自己之前的使用经验。

# ☎️ 数据通信
## http & http2

#### 1. http2 有什么新的东西，哪些场景下适用？

## JSBrange

#### 1. web 与原生应用如何交互通信？

## websocket

#### 1. websocket 是什么？有哪些应用场景？

# 👑 性能优化

## 语义化 & SEO

## 高性能页面

#### 1. web缓存有哪些？

#### 2. 如何全链路优化应用访问性能？

#### 3. svg、base64 格式图片跟 png、jpg 等格式有啥不同？

#### 4. 如果一个页面有很多图片，怎么优化这个页面的加载速度？

## 架构设计

#### 1. 高性能页面如何实现？高可用方面前端能做些啥？

#### 2. 讲一下哪个项目最能体现你的技术架构设计能力？

#### 3. 一个好的架构设计需要关注哪些方面？

#### 4. 官网类应用是如何提升 SLA 实现高可用的？你采取了哪些措施？

#### 5. 架构上如何保证 node 服务的高可用？

# 🎯 web 安全

#### 1. web 安全中跟前端有关的攻击有哪些？是什么原理？

**跨站脚本攻击（XSS攻击）**
- 解决方法：过滤用户输入脚本，对特殊字符如”<”,”>”转义

**跨站请求伪造(CSRF攻击)**
- 解决办法：请求头增加一个token字段校验cookie是否被劫持

**点击劫持**：最常见的是恶意网站使用<iframe>标签把我方的一些含有重要信息类如交易的网页嵌入进去，然后把iframe设置透明，用定位的手段的把一些引诱用户在恶意网页上点击。这样用户不知不觉中就进行了某些不安全的操作。
- 解决办法
    - 使用JS防范：判断顶层视口的域名是不是和本页面的域名一致，如果不一致就让恶意网页自动跳转到我方的网页。当然你还可以恶心一下这些恶意网站，比如说弹窗十几次，或者跳转到某些404页面。
    - 使用HTTP头防范：通过配置nginx发送X-Frame-Options响应头，这样浏览器就会阻止嵌入网页的渲染。更详细的可以查阅MDN上关于X-Frame-Options响应头的内容。

```js
// 使用JS防范
if (top.location.hostname !== self.location.hostname) {
    alert("您正在访问不安全的页面，即将跳转到安全页面！");
    top.location.href = self.location.href;
}

// 使用HTTP头防范
add_header X-Frame-Options SAMEORIGIN;
```

**请求劫持**
- DNS劫持，顾名思义，DNS服务器(DNS解析各个步骤)被篡改，修改了域名解析的结果，使得访问到的不是预期的ip。
- HTTP劫持：此时只能升级HTTPS了。

**SQL注入**：攻击者在HTTP请求中注入恶意的SQL代码，并在服务端执行。比如用户登录，输入用户名camille，密码 ‘ or ‘1‘=‘1 ，如果此时使用参数构造的方式，就会出现。
- 解决办法
    - 客户端：有效性检验，限制字符串输入的长度。
    - 服务端
        - 使用预编译的PrepareStatement（java）；
        - 有效性检验，防止攻击者绕过客户端请求；
        - 永远不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取；
        - 过滤SQL需要的参数中的特殊字符，比如单引号、双引号。
```sql
/* SQL注入场景示例：不管用户名和密码是什么，查询出来的用户列表都不为空，这样可以随意看其他用户的信息。 */
select * from user where name = ‘camille‘ and password = ‘‘ or ‘1‘=‘1‘
```
```js
// 解决办法
// 客户端：过滤URL非法SQL字符，或者过滤文本框非法字符。
var sUrl = location.search.toLowerCase();
var sQuery = sUrl.substring(sUrl.indexOf("=")+1);
reg=/select|update|delete|truncate|join|union|exec|insert|drop|count|‘|"|;|>|<|%/i;
if(reg.test(sQuery))
{
    alert("请勿输入非法字符");
    location.href = sUrl.replace(sQuery,"");
}
```

**OS命令注入**：不是针对数据库，针对操作系统的。

**DDOS分布式拒绝服务攻击(英文意思是Distributed Denial of Service，简称DDoS)**：指处于不同位置的多个攻击者同时向一个或数个目标发动攻击，或者一个攻击者控制了位于不同位置的多台机器并利用这些机器对受害者同时实施攻击。由于攻击的发出点是分布在不同地方的，这类攻击称为分布式拒绝服务攻击，其中的攻击者可以有多个。

#### 2. cookie 的安全问题怎么避免？
# 🤡 兼容
（暂未遇到，先占位后续再补充）

# ⚓️ 后端知识等

## node.js

#### 1. node 的动态扩展如何实现？

#### 2. 如何去避免 node 内存泄露这种问题？如何更好的监控？
## linux运维

#### 1. 服务器操作、Linux 命令这块有哪些实践？

# 📚 项目管理

## 项目经验

#### 1. 如果给你一个代码年久失修、文档缺失的项目，你准备如何去整理文档，方便后续维护？
## 沟通协作

#### 1. 前端如何与后端更高效的协作开发？
## 敏捷Scrum

#### 1. 敏捷 scrum 是如何开展各项活动的？逐个介绍下。
