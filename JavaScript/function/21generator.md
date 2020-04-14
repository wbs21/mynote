​								 															 						

# Generator 函数的含义与用法

在ES6之前，javascript没有直接的异步编程方法，异步基本都是靠回调函数来实现的。如果要实现多次异步，就要多个回调函数不停的嵌套，这会使得函数编写丑陋不堪。

Promise就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的横向加载，改成纵向加载。Promise 的写法只是回调函数的改进，使用then方法以后，异步任务的两段执行看得更清楚了，除此以外，并无新意。

Promise 的最大问题是代码冗余，原来的任务被Promise 包装了一下，不管什么操作，一眼看去都是一堆 then，原来的语义变得很不清楚。

直到ES6，Generator 函数出现，一切变的美好了。

- Generator 函数是为异步编程而出现的，它是一个异步任务封装器。
- 调用 Generator 函数，并不会执行函数，而是会返回一个内部指针对象。
- 调用指针对象的 next 方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的 yield 语句。
- yield会返回一个对象，表示当前阶段的信息（ value 属性和 done 属性）。
- value 属性是 yield 语句后面表达式的值，表示当前阶段的值；
- done 属性是一个布尔值，false表示 Generator 函数还有下一个阶段，true表示函数执行完毕。

## Generator函数的概念

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

> ```javascript
> function* gen(x){
>   var y = yield x + 2;
>   return y;
> }
> ```

上面代码就是一个 Generator 函数。它不同于普通函数，是可以暂停执行的，所以函数名之前要加星号，以示区别。

整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 yield 语句注明。Generator 函数的执行方法如下。

> ```javascript
> var g = gen(1);
> g.next() // { value: 3, done: false }
> g.next() // { value: undefined, done: true }
> ```

上面代码中，调用 Generator 函数，会返回一个内部指针（即[遍历器](http://es6.ruanyifeng.com/#docs/iterator) ）g 。这是 Generator 函数不同于普通函数的另一个地方，即执行它不会返回结果，返回的是指针对象。调用指针 g 的 next 方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的 yield 语句，上例是执行到 x + 2 为止。

换言之，next 方法的作用是分阶段执行 Generator 函数。每次调用 next 方法，会返回一个对象，表示当前阶段的信息（ value 属性和 done 属性）。value 属性是 yield 语句后面表达式的值，表示当前阶段的值；done 属性是一个布尔值，表示 Generator 函数是否执行完毕，即是否还有下一个阶段。

## Generator 函数的数据交换和错误处理

Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，它还有两个特性，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。

next 方法返回值的 value 属性，是 Generator 函数向外输出数据；next 方法还可以接受参数，这是向 Generator 函数体内输入数据。

> ```javascript
> function* gen(x){
>   var y = yield x + 2;
>   return y;
> }
> 
> var g = gen(1);
> g.next() // { value: 3, done: false }
> g.next(2) // { value: 2, done: true }
> ```

上面代码中，第一个 next 方法的 value 属性，返回表达式 x + 2 的值（3）。第二个 next 方法带有参数2，这个参数可以传入 Generator 函数，作为上个阶段异步任务的返回结果，被函数体内的变量 y 接收。因此，这一步的 value 属性，返回的就是2（变量 y 的值）。

Generator 函数内部还可以部署错误处理代码，捕获函数体外抛出的错误。

> ```javascript
> function* gen(x){
>   try {
>     var y = yield x + 2;
>   } catch (e){ 
>     console.log(e);
>   }
>   return y;
> }
> 
> var g = gen(1);
> g.next();
> g.throw（'出错了'）;
> // 出错了
> ```

上面代码的最后一行，Generator 函数体外，使用指针对象的 throw 方法抛出的错误，可以被函数体内的 try ... catch 代码块捕获。这意味着，出错的代码与处理错误的代码，实现了时间和空间上的分离，这对于异步编程无疑是很重要的。

## Generator 函数的用法

下面看看如何使用 Generator 函数，执行一个真实的异步任务。

> ```javascript
> var fetch = require('node-fetch');
> 
> function* gen(){
>   var url = 'https://api.github.com/users/github';
>   var result = yield fetch(url);
>   console.log(result.bio);
> }
> ```

上面代码中，Generator 函数封装了一个异步操作，该操作先读取一个远程接口，然后从 JSON 格式的数据解析信息。就像前面说过的，这段代码非常像同步操作，除了加上了 yield 命令。

执行这段代码的方法如下。

> ```javascript
> var g = gen();
> var result = g.next();
> 
> result.value.then(function(data){
>   return data.json();
> }).then(function(data){
>   g.next(data);
> });
> ```

上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用 next 方法（第二行），执行异步任务的第一阶段。由于 [Fetch 模块](https://github.com/bitinn/node-fetch)返回的是一个 Promise 对象，因此要用 then 方法调用下一个next 方法。

可以看到，虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。本系列的[后面部分](http://www.ruanyifeng.com/blog/2015/05/thunk.html)，就将介绍如何自动化异步任务的流程管理。另外，本文对 Generator 函数的介绍很简单，详尽的教程请阅读我写的[《ECMAScript 6入门》](http://es6.ruanyifeng.com/#docs/generator)。





# generator

------

generator（生成器）是ES6标准引入的新的数据类型。一个generator看上去像一个函数，但可以返回多次。

ES6定义generator标准的哥们借鉴了Python的generator的概念和语法，如果你对Python的generator很熟悉，那么ES6的generator就是小菜一碟了。如果你对Python还不熟，赶快恶补[Python教程](http://www.liaoxuefeng.com/wiki/1016959663602400/1017318207388128)！。

我们先复习函数的概念。一个函数是一段完整的代码，调用一个函数就是传入参数，然后返回结果：

```
function foo(x) {
    return x + x;
}

var r = foo(1); // 调用foo函数
```

函数在执行过程中，如果没有遇到`return`语句（函数末尾如果没有`return`，就是隐含的`return undefined;`），控制权无法交回被调用的代码。

generator跟函数很像，定义如下：

```
function* foo(x) {
    yield x + 1;
    yield x + 2;
    return x + 3;
}
```

generator和函数不同的是，generator由`function*`定义（注意多出的`*`号），并且，除了`return`语句，还可以用`yield`返回多次。

大多数同学立刻就晕了，generator就是能够返回多次的“函数”？返回多次有啥用？

还是举个栗子吧。

我们以一个著名的斐波那契数列为例，它由`0`，`1`开头：

```
0 1 1 2 3 5 8 13 21 34 ...
```

要编写一个产生斐波那契数列的函数，可以这么写：

```
function fib(max) {
    var
        t,
        a = 0,
        b = 1,
        arr = [0, 1];
    while (arr.length < max) {
        [a, b] = [b, a + b];
        arr.push(b);
    }
    return arr;
}

// 测试:
fib(5); // [0, 1, 1, 2, 3]
fib(10); // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

函数只能返回一次，所以必须返回一个`Array`。但是，如果换成generator，就可以一次返回一个数，不断返回多次。用generator改写如下：

```
function* fib(max) {
    var
        t,
        a = 0,
        b = 1,
        n = 0;
    while (n < max) {
        yield a;
        [a, b] = [b, a + b];
        n ++;
    }
    return;
}
```

直接调用试试：

```
fib(5); // fib {[[GeneratorStatus]]: "suspended", [[GeneratorReceiver]]: Window}
```

直接调用一个generator和调用函数不一样，`fib(5)`仅仅是创建了一个generator对象，还没有去执行它。

调用generator对象有两个方法，一是不断地调用generator对象的`next()`方法：

```
var f = fib(5);
f.next(); // {value: 0, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 2, done: false}
f.next(); // {value: 3, done: false}
f.next(); // {value: undefined, done: true}
```

`next()`方法会执行generator的代码，然后，每次遇到`yield x;`就返回一个对象`{value: x, done: true/false}`，然后“暂停”。返回的`value`就是`yield`的返回值，`done`表示这个generator是否已经执行结束了。如果`done`为`true`，则`value`就是`return`的返回值。

当执行到`done`为`true`时，这个generator对象就已经全部执行完毕，不要再继续调用`next()`了。

第二个方法是直接用`for ... of`循环迭代generator对象，这种方式不需要我们自己判断`done`：

```
'use strict'

function* fib(max) {
    var
        t,
        a = 0,
        b = 1,
        n = 0;
    while (n < max) {
        yield a;
        [a, b] = [b, a + b];
        n ++;
    }
    return;
}
for (var x of fib(10)) {
    console.log(x); // 依次输出0, 1, 1, 2, 3, ...
}
```

generator和普通函数相比，有什么用？

因为generator可以在执行过程中多次返回，所以它看上去就像一个可以记住执行状态的函数，利用这一点，写一个generator就可以实现需要用面向对象才能实现的功能。例如，用一个对象来保存状态，得这么写：

```
var fib = {
    a: 0,
    b: 1,
    n: 0,
    max: 5,
    next: function () {
        var
            r = this.a,
            t = this.a + this.b;
        this.a = this.b;
        this.b = t;
        if (this.n < this.max) {
            this.n ++;
            return r;
        } else {
            return undefined;
        }
    }
};
```

用对象的属性来保存状态，相当繁琐。

generator还有另一个巨大的好处，就是把异步回调代码变成“同步”代码。这个好处要等到后面学了AJAX以后才能体会到。

没有generator之前的黑暗时代，用AJAX时需要这么写代码：

```
ajax('http://url-1', data1, function (err, result) {
    if (err) {
        return handle(err);
    }
    ajax('http://url-2', data2, function (err, result) {
        if (err) {
            return handle(err);
        }
        ajax('http://url-3', data3, function (err, result) {
            if (err) {
                return handle(err);
            }
            return success(result);
        });
    });
});
```

回调越多，代码越难看。

有了generator的美好时代，用AJAX时可以这么写：

```
try {
    r1 = yield ajax('http://url-1', data1);
    r2 = yield ajax('http://url-2', data2);
    r3 = yield ajax('http://url-3', data3);
    success(r3);
}
catch (err) {
    handle(err);
}
```

看上去是同步的代码，实际执行是异步的。

### 练习

要生成一个自增的ID，可以编写一个`next_id()`函数：

```
var current_id = 0;

function next_id() {
    current_id ++;
    return current_id;
}
```

由于函数无法保存状态，故需要一个全局变量`current_id`来保存数字。

不用闭包，试用generator改写：

```
'use strict';
function* next_id () {
  var current_id = 1;
  while(true){
    yield current_id;
    current_id++;
  }
}
// 测试:
var
    x,
    pass = true,
    g = next_id();
for (x = 1; x < 100; x ++) {
    if (g.next().value !== x) {
        pass = false;
        console.log('测试失败!');
        break;
    }
}
if (pass) {
    console.log('测试通过!');
}
```



------

# js异步编程之Generator

### Generator介绍

Generator 的中文名称是生成器，它是ECMAScript6中提供的新特性。在过去，封装一段运算逻辑的单元是函数。函数只存在“没有被调用”或者“被调用”的情况，不存在一个函数被执行之后还能暂停的情况，而Generator的出现让这种情况成为可能。

通过`function*`来定义的函数称之为“生成器函数”（generator function），它的特点是可以中断函数的执行，每次执行`yield`语句之后，函数即暂停执行，直到调用返回的生成器对象的`next()`函数它才会继续执行。

也就是说Generator 函数是一个状态机，封装了多个内部状态。执行 Generator 函数返回一个遍历器对象（一个指向内部状态的指针对象），调用遍历器对象的next方法，使得指针移向下一个状态。每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。

### yield关键字

真正让Generator具有价值的是`yield`关键字，这个`yield`关键字让 Generator内部的逻辑能够切割成多个部分。



```jsx
let compute = function* (a, b) {
  let sum = a + b;
  yield console.log(sum);
  let c = a - b;
  yield console.log(c);
  let d = a * b;
  yield console.log(d);
  let e = a / b;
  console.log(e);
};
// 执行一下这个函数得到 Generator 实例
let generator = compute(4, 2);
// 要使得函数中封装的代码逻辑得到执行，还得需要调用一次next方法。
generator.next();

----------
var compute = function* (a, b) {
  let sum = a + b;
  yield sum;
  let c = a - b;
  yield c;
  let d = a * b;
  yield d;
  let e = a / b;
  e;
};
```

输出结果：
 6
 {value: undefined, done: false}

发现函数执行到第一个`yield`关键字的时候就停止了。要让业务逻辑继续执行完，需要反复调用`.next()`。



```css
generator.next();
generator.next();
generator.next();
generator.next();
```

可以简单地理解为`yield`关键字将程序逻辑划分成几部分，每次`.next()`执行时执行一部分。这使得程序的执行单元再也不是函数，**复杂的逻辑可以通过yield来暂停**。

1. `.next()`调用时，返回一个对象

`yield`除了切割逻辑外，它与`.next()`的行为息息相关。每次`.next()`调用时，返回一个对象，这个对象具备两个属性。
 其中一个属性是布尔型的`done`。它表示这个Generator对象的逻辑块是否执行完成。
 另一个属性是`value`，它来自于`yield`语句后的表达式的结果。



```jsx
function * getNumbers(num) {
    for(let i=0; i<num;i++) {
        yield i
    }
    return 'ok';
}
const gen = getNumbers(10);
function next() {
    let res = gen.next();
    console.log(res);
    if(res.done) {
        console.log('done');
    } else {
        setTimeout(next,300)
    }
}
next();
```

输出结果：
 {value: 0, done: false}
 undefined
 {value: 1, done: false}
 {value: 2, done: false}
 {value: 3, done: false}
 {value: 4, done: false}
 {value: 5, done: false}
 {value: 6, done: false}
 {value: 7, done: false}
 {value: 8, done: false}
 {value: 9, done: false}
 {value: "ok", done: true}
 done

1. 可以通过向`.next()`传递参数



```jsx
let compute = function* (a, b) {
  var foo = yield a + b;
  console.log(foo);
};

let generator = compute(4, 2);
generator.next(); // {value: 6, done: false}
generator.next("Hello world!"); //Hello world! {value: undefined, done: true}
```

通过.next()传递参数，可以赋值给`yield`关键字前面的变量声明。

所以，对于Generator而言，它不仅可以将逻辑的单元切分得更细外，还能在暂停和继续执行的间隔中，动态传入数据，使得代码逻辑可以更灵活。

### 使用 Generator 编写状态切换逻辑代码



```tsx
function * loop(list, max=Infinity) {
    for(let i=0; i<max;i++) {
        yield list[i % list.length];
    }
}

function toggle(...actions) {
    let gen = loop(actions); 
  //错误写法：先调用loop(actions).next(); 
    return function(...args) {
        return gen.next().value.apply(this, args);
    }
}

// switcher.addEventListener('click', toggle(
//     e => e.target.className = 'off',
//     e => e.target.className = 'on'
// ));

switcher.addEventListener('click', toggle(
    e => e.target.className = 'off',
    e => e.target.className = 'warn',
    e => e.target.className = 'on'
));
```

# 用ES6 Generator替代回调函数

目前，已经有很多文章讨论过了如何使用ES6 generators来取代JavaScript中经常遇到的“回调金字塔”。但是，其中提到的绝大多数方法都需要依赖于某个库，而对于其中的原理却提及甚少。

在本文中，我们将一步一步的将一个基于回调函数的例子修改为一个基于generator的例子。本文的目标是让你透彻地理解使用generator替代回调函数的原理。

Generator是JavaScript中一个新概念，但在编程语言中已经存在已久。你可能已经在其他的编程语言例如Python使用过它。如果没有，也不要害怕，我们在后面已经为你准备了一个简单明了的入门介绍。

## 如何运行例子

在我们开始之前，你需要安装Node 0.11.* 来运行文章中的例子。当你在运行这些例子时，你需要告诉Node使用ES6（也就是Harmony）来运行：`node -harmony example.js`。

## 什么是一个generator

在我们深入讲述如何使用generator替代回调函数之前，我们先来说说什么是generator。

Generator很像是一个函数，但是你可以暂停它的执行。你可以向它请求一个值，于是它为你提供了一个值，但是余下的函数不会自动向下执行直到你再次向它请求一个值。

取号机也许是对generator的一个绝佳的比喻。你可以通过取一张票来向机器请求一个号码。你接收了你的号码，但是机器不会自动为你提供下一个。换句话说，取票机“暂停”直到有人请求另一个号码，此时它才会向后运行。

## ES6中的generator

Generator在ES6中像一个函数一样被声明，除了在之前有一个星号的差别外：

function* ticketGenerator(){}

当你想要一个generator提供一个值然后暂停时，你需要使用yield关键字。yield有点像是return关键字，因为它们都返回一个值，但是函数在yield之后会进入暂停状态。

```javascript
function ticketGenerator(){
    yield 1;
    yield 2;
    yield 3;
}   
```

在我们的例子中，我们定义了一个叫做ticketGenerator的迭代器。如果你向它请求一个值，它会返回1然后暂停。如果你再次向它发出一个请求，我们将得到2，然后是3。

当你调用一个generator时，它将返回一个迭代器对象。这个迭代器对象拥有一个叫做next的方法来帮助你重启generator函数并得到下一个值。

next方法不仅返回值，它返回的对象具有两个属性：done和value。value是你获得的值，done用来表明你的generator是否已经停止提供值。

现在我们从我们的取号机中取一些号码：

```javascript
var takeANumber = ticketGenerator();   

takeANumber.next();   

//>{value: 1, done: false}   

takeANumber.next();   
//>{value: 2, done: false}   

takeANumber.next();  
//>{value: 3, done: false}   

takeANumber.next();   
//>{value: undefined, done: true}  
```

现在我们的取号系统只能提供最多到3的号码，这实在是没什么用。我们想要让它无线增加下去，因此我们来创建一个循环。

```javascript
function* ticketGenerator(){
    for(var i=0; true; i++){
        yield i;
    }
}   
```

现在，如果这是一个普通的函数，我们每次只会得到0。但是使用generator却不一样：

```javascript
var takeANumber = ticketGenerator();   
console.log(takeANumber.next().value); //0   
console.log(takeANumber.next().value); //1   
console.log(takeANumber.next().value); //2  
console.log(takeANumber.next().value); //3  
console.log(takeANumber.next().value); //4  
```

每一次当我们调用next()时，generator执行下一个循环迭代然后暂停。这意味着我们拥有一个可以无限向下运行的generator。因为这个generator只是发生了暂停，你并没有冻结你的程序。事实上，generator是一个创建无限循环的好方法。

### 影响generator的状态

进一步探究迭代迭代generator对象的话，next()实际上还有另一个用途。如果你给next传递一个值，它会被视为generator中的一个yield语句的结果来对待。

因此next是一个在generator运行过程中向其传递信息的方式。我们将以此来修改我们的取号generator以便它能够被重置到0.我们希望能在任何时间点来重置取号机。

```javascript
function* ticketGenerator(){
    for(var i=0; true; i++){
        var reset = yield i;
        if(reset) {i = -1;}
    }
}  
```

正如你所看到的，如果yield返回了一个true然后我们将i设置为-1。那么for循环将会在循环的结尾将i增加1，因此下一次返回的i变成了0。

我们来看看实际情况如何：

```javascript
var takeANumber = ticketGenerator(); console.log(takeANumber.next().value); //0  console.log(takeANumber.next().value); //1 console.log(takeANumber.next().value); //2 console.log(takeANumber.next(true).value); //0 console.log(takeANumber.next().value); //1     
```

## 用generator替代回调函数

既然我们已经学到了一些关于generator的知识，现在让我们来谈谈generator和回调函数。正如你所知道的，当我们调用例如AJAX请求这样的异步代码时我们会使用回调函数。为了简单起见我们在例子中定义一个delay函数。

我们的delay函数将会是异步的 – 在指定的时间过后我们提供给delay的回调函数才会被执行，然后delay会给你的回调函数传递一个字符串告诉它究竟“沉睡”了多久。

在此期间你的其余代码将会继续执行下去。这就好像是进行一个AJAX请求一样 – 你发出请求，你的代码继续执行，当服务器返回一个结果时你的回调函数才执行。

现在，我们来定义delay函数：

```javascript
function delay(time, callback){
    setTimeout(function(){
        callback("Slept for "+time);
    },time);
}   
```

到目前为止，还没有什么特别的东西。现在我们来使用它来delay两次。首先我们将delay1000ms，然后当delay结束后我们再另外delay 1200ms。

```javascript
delay(1000,function(msg){
    console.log(msg);
    delay(1200,function(msg){
        console.log(msg);
    });
})   

//...waits 1000ms   
// > "Slept for 1000"    
//...waits another 1200ms    
// > "Slept for 1200   
```

确保我们的两个delay依次被调用的唯一方法就是确保第二个delay在第一个delay的回调函数中。

如果我们要依次delay 12次，我们将需要嵌套的调用12次delay函数。这时你就会碰到“回调金字塔”，代码也变得丑陋不堪。

### 引入generator

Generator是解决“回调地狱”的有效方法之一。异步调用是很困难的事情，因为我们的函数不会等待异步调用完成，因此我们需要回调函数。

使用generator，我们可以让我们的代码进行等待。无需嵌套回调函数，我们可以使用generator确保当异步调用在我们的generator函数运行一下行代码之前完成时暂停函数的执行。

因此，如果我们可以在一个异步调用完成时暂停执行，这就意味着我们可以依次调用delay函数 – 就像delay函数是同步执行的一样。

### 我们应该怎么做

首先，我们知道我们进行异步调用的代码需要在一个generator而不是一个一般的函数中进行，因此我们来定义一个generator。

```javascript
function* myDelayedMessages() {
/* delay 1000ms然后打印结果 */    

/* delay 1000ms然后打印结果 */    
}   
```

接下来我们需要在我们的generator中调用delay。记住，delay接收一个回调函数。这个回调函数需要继续我们的generator，但是我们现在还没有一个generator因此我们先放上一个空函数。

```javascript
function* myDelayedMessages(){
    console.log(delay(1000,function(){}));
    console.log(delay(1200,function(){}));
}   
```

我们代码依然是异步的。这是因为我们还没有将放入任何的yield语句。Generator只是在它们看大一个yield语句时才暂停。

```javascript
function* myDelayedMessages() { 
    console.log(yield delay(1000, function(){})); 
    console.log(yield delay(1200, function(){}));
}   
```

我们现在已经更接近了一点了。然而，如果我们运行我们的generator什么也不会发生。因为没有什么东西告诉它要向下运行。

在这里你需要理解的最重要的概念是：generator需要在delay中的回调函数运行完成后继续往下运行，这就是它们如何知道暂停应该结束了的原因。

这意味着回调函数中的东西需要知道如何向前推动generator。我们在其中传递一个叫做resume的函数来为我们做这件事。记住我们现在还依然没有定义resume。

```javascript
function* myDelayedMessages(resume) { 
    console.log(yield delay(1000, resume));
    console.log(yield delay(1200, resume));
}  
```

OK，现在我们的generator将会接收一个resume函数，这个函数将会向前推动generator。

现在到了关键步骤了，我们如何来编写resume，它又是怎么来了解我们的generator的。

如果你看看其他使用generator代替回调函数的例子，你会看到generator函数总是被另一个函数包裹着 – 通常是一个叫做“run”或者“execute”的函数。这些函数的目的有以下几个：

- 接收一个generator作为参数

- 使用这个generator来创建一个新的generator迭代器对象，我们将调用它的next方法

- 创建一个resume函数来使用这个generator迭代器对象来推进generator

- 将resume函数传递给这个generator以便generator能够访问resume

- 在最开始时调用next()函数，以便我们的代码在碰到第一个yield之前开始执行

  现在我们来创建run函数：

  ```javascript
  function run(generatorFunction) { 
    var generatorItr = generatorFunction(resume); 
    function resume(callbackValue) { 
      generatorItr.next(callbackValue); 
      } 
    generatorItr.next()
  }
  ```

  

现在我们有了一个能够接收一个generator函数的函数，并为它传递了一个了解如何推进generator迭代器对象的函数。

注意到我们在resume函数中用到了next的第二个特性。resume是被传递给delay的回调函数，因此它接收delay函数提供的值。resume将这个值传递给next，因此yield语句的结果实际上是我们异步函数的结果！

我们现在要做的只是用run包裹上我们的generator函数，然后我们就能看到以下结果：

```javascript
run(function* myDelayedMessages(resume) {
 console.log(yield delay(1000, resume)); 
 console.log(yield delay(1200, resume));
})
//...waits 1000ms
// > "Slept for 1000" //...waits 1200ms
// > "Slept for 1200"   
```

现在，你能看到我们调用delay两次，并没有使用嵌套回调函数。如果你依然看到疑惑，我们现在概括的来讲述以下究竟发生了什么：

- run接收了我们的generator并创建了一个resume函数
- run创建了一个generator迭代器对象（我们在它上面调用next方法），提供了resume函数。接着它推动了generator迭代器向前运行。
- 我们的generator碰到了第一个yield语句并且调用delay。接着这个generator暂停。
- delay在1000ms之后完成然后调用resume。
- resume告诉我们的generator进行下一步。它将结果传递给delay以便console能够将它打印出来。
- 我们的generator碰到了第二个yield，调用delay然后再次暂停。
  delay等待1200ms之后调用resume回调函数。
- resume再次推进generator。
- 再也没有yield的调用，这个generator完成执行。

## 结论

我们已经成功的使用generator替代了回调嵌套方法。总结一下，使用generator替代回调函数要包含以下几个步骤：

- 创建一个run函数来接受一个generator，并为这个generator提供resume函数。
- 创建一个resume函数来推进generator，然后在resume被异步函数调用时将这个resume函数传递给generator。
- 将resume作为回调传递给我们所有的异步回调函数。这些异步函数在完成时执行resume，这使得我们的generator在每个异步调用完成之时仅仅向前一步。

虽然generator究竟是不是一个处理“回调地狱”的好方法还在讨论之中，但是它确实是一个加强你对ES6中generator和迭代器理解的练习。如果你在寻找一些不需要用到ES6的处理嵌套回调函数的方法，可以考虑promises。

# Generator 与异步编程 					

在《深入浅出 Node.js》的第 4 章里，笔者深入地介绍了当前盛行在 Node 和前端 JavaScript 中的几种异步编程的解决方案，唯独对 Generator 的解决方案没有介绍。但随着 Node 版本的升级和 ECMAScript harmony 的特性不断得到支持，在 0.11 版本中，我们可以通过启用`--harmory`参数让 V8 支持 Generator。最近 Connect/Express 背后的开发团队也将精力转移到新的库和框架上，这个核心库和框架就是`co`和`koa`，它们最主要的特点主要就是基于 ECMAScript harmony 中的 Generator 特性，这使得它在异步编程方面有较优雅的实现。

本文将深度介绍下 Generator 是如何实现将异步编程从原始的嵌套式代码转换成扁平的顺序式代码。

## 异步编程问题回顾

简单地回顾下，异步编程的问题主要有两个：一个是必须通过回调函数进行返回值的处理，另一个是复杂情况下会造成嵌套过深。下面简单的给出两种典型的异步场景。

异步串行读取文件：

```
fs.readFile('file1.txt', 'utf8', function (err, txt) {
  if (err) {
    throw err;
  }
  fs.readFile(txt, 'utf8', function (err, content) {
    if (err) {
      throw err;
    }
    console.log(content);
  });
});
```

异步并行读取文件：

```
fs.readFile('file1.txt', 'utf8', function (err, txt) {
  if (err) {
    throw err;
  }
  console.log(txt);
});
fs.readFile('file2.txt', 'utf8', function (err, content) {
  if (err) {
    throw err;
  }
  console.log(content);
});
```

上述两种场景下，可以看到串行时由于代码嵌套，调用越多，会造成代码越糟糕。对于异步并行读取文件的代码，难点是无法获知并行异步调用完全完成的时间点，要解决这个问题需要借助各种异步流程库。

目前解决异步流程控制问题的主流方案有以下三种：

1. 自定义事件式方案
2. Promise/Deferred
3. 高阶函数篡改回调函数

由于在《异步编程》章节中已经充分介绍了上述三种方式的实现形式，这里不再详细展开三种方式的细节。但是为了行文承前启后，这里简单回顾下第三种方案的实现。

### 高阶函数在异步编程中的使用

高阶函数在异步编程中的使用，最广泛、最知名的莫过于`async`和`step`这两个库，它将用户正常传递进来的回调函数替换成自己包装了特殊逻辑的函数，然后再传递给异步调用。当异步调用结束后，先执行的是特殊逻辑，然后才是用户传入的回调函数。以一个简单的场景为例，假设需要等待所有异步回调执行完成后，才能执行某个逻辑，这时通过高阶函数篡改回调函数的方式就大为受用，也相当简单。以下为简单实现：

```
var pending = (function () {
  var count = 0;
  return function (callback) {
    count++;
    return function () {
      count--;
      if (count === 0) {
        callback();
      }
    };
  };
}());

var done = pending(function () {
  console.log('all is over');
});

fs.readFile('file1.txt', 'utf8', done());
fs.readFile('file2.txt', 'utf8', done());
```

上述代码中，`done`执行了两次，每次执行的过程中，将计数器`count`加一，然后返回一个函数。当`fs.readFile`这个异步调用结束后，`done`执行后的回调函数会得到执行，计数器减一。当计数器回到 0 的时候，意味着多个异步调用的回调函数都已经执行，此时执行传入的回调函数。因为非阻塞的原因，`done()`生成的函数不会立即执行，使得计数器可以正常地增加值，结束后才慢慢减少值。

抛开异步调用不谈，高阶函数的试用上，要让用户传入的函数能得到执行。需要如下这种方式的调用：

```
var done = pending(function () {
  console.log('all is over');
});

done()();
```

这里生成的函数被立即调用了，`count`这个计数器加一后，立即减一，然后触发等于零的条件，于是回调函数被执行了。

在通过 Generator 解决异步编程问题的方案中，高阶函数与 Generator 之间会产生相当微妙的化学反应。

## Generator 扫盲

Generator 的中文翻译是生成器，它是 ECMAScript6（代号 harmory）中提供的新特性。在过去，封装一段运算逻辑的单元是函数。函数只存在“没有被调用”或者“被调用”的情况，不存在一个函数被执行之后还能暂停的情况，而 Generator 的出现让这种情况成为可能。

### Generator 的定义

Generator 的定义十分简单，与普通的函数相比，它只多出一个`*`号。以下为简单例子：

```
var compute = function* (a, b) {
  var sum = a + b;
  console.log(sum);
  var c = a - b;
  console.log(c);
  var d = a * b;
  console.log(d);
  var e = a / b;
  console.log(e);
};
```

这个星号只要出现在关键字`function`和函数名之间即可。如果是匿名函数，出现在`function`和参数列表的起始括号之间即可。

定义的 Generator 实际上可以理解为定义了一种特殊的数据结构，要得到 Generator 实例，还需要执行它一次：

```
var generator = compute(4, 2);
```

这样我们能得到一个 Generator 对象。Generator 对象具有一个`next`方法。要使得定义中封装的代码逻辑得到执行，还得需要调用一次`next`方法才行。

```
generator.next();
```

调用之后的输出结果如下：

```
$ node --harmony-generators examples/compute.js 
6
2
8
2
```

### yield 关键字

单独地介绍 Generator 没有太大价值，因为它除了更复杂外，功能与普通函数没有太大差别。真正让 Generator 具有价值的是`yield`关键字，这个`yield`关键字让 Generator 内部的逻辑能够切割成多个部分。下面是简单的示例：

```
var compute = function* (a, b) {
  var sum = a + b;
  yield console.log(sum);
  var c = a - b;
  yield console.log(c);
  var d = a * b;
  yield console.log(d);
  var e = a / b;
  console.log(e);
};
```

加入`yield`关键字后，我们继续将其实例化，然后调用`.next()`方法：

```
var generator = compute(4, 2);
generator.next();
```

可以看到输出如下：

```
$ node --harmony-generators examples/compute.js 
6
```

上面的输出意味着代码执行到第一个`yield`关键字的时候就停止了。要让业务逻辑继续执行完，需要反复调用`.next()`。

```
var generator = compute(4, 2);
generator.next(); // 6
generator.next(); // 2
generator.next(); // 8
generator.next(); // 2
```

可以简单地理解为`yield`关键字将程序逻辑划分成几部分，每次`.next()`执行时执行一部分。这使得程序的执行单元再也不是函数，复杂的逻辑可以通过`yield`来暂停。从朴素的角度来看，这类似于将一个函数的逻辑分拆为四个函数，但它们共享上下文。

`yield`除了切割逻辑外，它与`.next()`的行为息息相关。每次`.next()`调用时，返回一个对象，这个对象具备两个属性，其中一个属性是布尔型的`done`。它表示这个 Generator 对象的逻辑块是否执行完成。另一个属性是`value`，它来自于`yield`语句后的表达式的结果。我们将代码简单修改，可以看到效果：

```
var compute = function* (a, b) {
  var sum = a + b;
  yield sum;
  var c = a - b;
  yield c;
  var d = a * b;
  yield d;
  var e = a / b;
  return e;
};
var generator = compute(4, 2);
console.log(generator.next()); // { value: 6, done: false }
console.log(generator.next()); // { value: 2, done: false }
console.log(generator.next()); // { value: 8, done: false }
console.log(generator.next()); // { value: 2, done: true }
```

上述是`yield`对`.next()`行为的影响，反之，`.next()`也能影响到`yield`。可以简单地猜测下如下代码中会打印出什么结果：

```
var compute = function* (a, b) {
  var foo = yield a + b;
  console.log(foo);
};
```

也许读者会以为是`a + b`的值，但是这里不是：默认情况下，这个`foo`打印出来是`undefined`。那么`.next()`如何影响`yield`的呢？答案在于可以通过`.next()`传递参数，赋值给`yield`关键字前面的变量声明。见下面的简单示例：

```
var compute = function* (a, b) {
  var foo = yield a + b;
  console.log(foo);
};

var generator = compute(4, 2);
generator.next();
generator.next("Hello world!"); // Hello world!
```

所以，对于 Generator 而言，它不仅可以将逻辑的单元切分得更细外，还能在暂停和继续执行的间隔中，动态传入数据，使得代码逻辑可以更灵活。相比普通函数，Generator 的特性相当令人期待。

## Generator 与异步编程

初见之下，Generator 似乎与异步编程之间还八竿子打不着。但前文的高阶函数和 Generator 的介绍已经为我们准备好了基础。我们继续揭开 Generator 是如何与异步编程摩擦出闪亮的火花的。

在拥有了`yield`关键字后，我们可以很巧妙地处理异步调用的回调函数。以顺序读取两个文件的场景为例：

```
fs.readFile('file1.txt', 'utf8', function (err, txt) {
  if (err) {
    throw err;
  }
  fs.readFile(txt, 'utf8', function (err, content) {
    if (err) {
      throw err;
    }
    console.log(content);
  });
});
```

如果我们要完成这两个操作，而且不以嵌套的方式进行，我们可以很自然想到以`yield`来分割两个操作。

```
var flow = function* () {
  var txt = yield fs.readFile('file1.txt', 'utf8');
  var content = yield fs.readFile(txt, 'utf8');
  console.log(content);
};
```

这里虽然没有写入回调函数，但我们可以想象，如果回调函数执行的时候触发这个 Generator 执行一次`.next()`，然后将返回结果通过`.next(result)`这样的形式传入，这样尽管是异步调用，但代码编写形式已经近乎顺序式了。

想象是美好的，现实能否实现是另一回事。要完成上面的目的，需要做的事情有两步：

1. 需要在回调函数中置入逻辑，用于收集回调函数传递的数据。
2. 通过`.next()`传入异步执行的结果，传递给`yield`，让业务流程继续进行。

### 改造异步方法

为了完成收集异步调用的结果数据，我们必须得借助高阶函数。如下是一个修改回调函数逻辑的函数：

```
var helper = function (fn) {
  return function () {
    var args = [].slice.call(arguments);
    var pass;
    args.push(function () { // 在回调函数中植入收集逻辑 
      if (pass) {
        pass.apply(null, arguments);
      }
    });
    fn.apply(null, args);

    return function (fn) { // 传入一个收集函数 
      pass = fn;
    };
  };
};
```

这个函数的参数是一个异步调用函数，调用之后，得到一个新的函数，这个函数在重新整理了参数列表后，添加了一个实际被调用到的回调函数。这个新的函数执行后，会调用真正的异步函数，然后再次返回一个函数，最后返回的函数的作用是为了随时注入新的逻辑（`pass`）。在参数列表后添加的回调函数中，它会将结果传递给最终给到的函数。

用语言来解释这段代码有点吃力，我们以`fs.readFile`调用为实际例子，重点参见下文的注释：

```
var readFile = helper(fs.readFile);
// => 
// function () {
//   var args = [].slice.call(arguments);
//   var pass;
//   args.push(function () { // 在回调函数中植入收集逻辑 
//     if (pass) {
//       pass.apply(null, arguments);
//     }
//   });
//   fn.apply(null, args);
//
//   return function (fn) { // 传入一个收集函数 
//     pass = fn;
//   };
// }

var flow = function* () {
  var txt = yield readFile('file1.txt', 'utf8');
  console.log(txt);
};

var generator = flow();
var ret = generator.next(); // 执行 readFile('file1.txt', 'utf8');
// ret.value =>
// function (fn) {
//   pass = fn;
// }
```

可以看到上面的代码，我们已经成功的将`flow`这个 Generator 的第一部分代码执行起来，我们可以通过`ret.value`来尝试植入一段特殊的逻辑，同时在异步调用结束后，将数据取出，同时执行 Generator 的下一部分逻辑。完整代码如下：

```
var generator = flow();
var ret = generator.next(); // 执行 readFile('file1.txt', 'utf8');
ret.value(function (err, data) {
  if (err) {
    throw err;
  }
  generator.next(data);
});
```

通过这样置入特殊逻辑后，使得`flow`中的代码能够按期望顺利执行，通过`yield`巧妙地将回调函数得到的值转换为类似返回值。

为了让所有的 Generator 能适应这种情况，我们设计一个流程控制函数，用来专门控制此类操作。

### 设计流程控制函数

为了向 TJ 大神致敬，这个函数我们暂时命名为`co`。它要进行的操作是让 Generator 启动，在 Generator 暂停的时候，植入逻辑。简单实现如下：

```
var co = function (flow) {
  var generator = flow();
  var next = function (data) {
    var result = generator.next(data);
    if (!result.done) {
      result.value(function (err, data) {
        if (err) {
          throw err;
        }
        next(data);
      });
    }
  };
  next();
};
```

代码中通过递归调用来完成 Generator 中流程的执行，调用示例如下：

```
co(function* () {
  var txt = yield readFile('file1.txt', 'utf8');
  console.log(txt);
  var txt2 = yield readFile('file2.txt', 'utf8');
  console.log(txt2);
});
```

执行结果如下：

```
$ node --harmony-generators flow.js 
I am file1.

I am file2.
```

如此我们就完成从嵌套函数的写法转换为顺序式的编写，深度嵌套带来的“地狱之门”将成为历史。

### 并行执行

前面的例子中，已经成功地将嵌套代码转换为顺序的扁平的代码，但是异步调用之间仍然是串行执行，这使得我们无法享受到 Node 中并行 I/O 的好处。为此我们还需要改进`co`函数，使得异步调用能并行执行，同时依旧保证代码的顺序。

为此，我们约定当`yield`后的表达式结果是一个数组时，表示里面为多个异步调用。简单的修改如下：

```
var co = function (flow) {
  var generator = flow();
  var next = function (data) {
    var ret = generator.next(data);
    if (!ret.done) {
      if (Array.isArray(ret.value)) {
        var count = 0;
        var results = [];
        ret.value.forEach(function (item, index) {
          count++;
          item(function (err, data) {
            count--;
            if (err) {
              throw err;
            }
            results[index] = data;
            if (count === 0) {
              next(results);
            }
          });
        });
      } else {
        ret.value(function (err, data) {
          if (err) {
            throw err;
          }
          next(data);
        });
      }
    }
  };
  next();
};
```

调用示例如下，当为数组时，应当并行执行：

```
co(function* () {
  var results = yield [readFile('file1.txt', 'utf8'), readFile('file2.txt', 'utf8')];
  console.log(results[0]);
  console.log(results[1]);
  var file3 = yield readFile('file3.txt', 'utf8');
  console.log(file3);
});
```

执行结果如下：

```
$ node --harmony-generators parallel.js 
I am file1.

I am file2.

I am file3.
```

为了验证异步调用是并行进行的，我们换用定时器来测试：

```
var _sleep = function (ms, fn) {
  setTimeout(fn, ms);
};
var sleep = helper(_sleep);

co(function* () {
  console.time('sleep1');
  yield sleep(1000);
  yield sleep(1000);
  console.timeEnd('sleep1');
  console.time('sleep2');
  yield [sleep(1000), sleep(1000)];
  console.timeEnd('sleep2');
});
```

执行结果如下：

```
$ node --harmony-generators sleep.js 
sleep1: 2004ms
sleep2: 1005ms
```

从`sleep2`的输出来看，`yield`关键字后的数组中的所有异步调用得到并行执行。

## 小结

至此，我们小结一下通过 Generator 进行流程控制的几个要点。首先，每个异步方法都需要标准化为`yield`关键字能接受的方法，使我们有机会注入特殊逻辑，这个过程被 TJ 称为`thunkify`。其次，需要巧妙地将异步调用执行完成得到的结果通过`.next()`传递给下一段流程。最后，需要递归地将业务逻辑执行完成。

需要注意的是`yield`只能暂停 Generator 内部的逻辑，它并不是真正暂停整个线程，Generator 外的业务逻辑依然会继续执行下去。

## 向下兼容

我们知道 Generator 和`yield`关键字的特性来自于 ECMAScript6，这意味着目前 Node 主流的 0.10 版本上无法享受到这些令人激动的特性；另外，Generator 的语法与现行的语法不兼容，导致无法进行 shim 式的兼容。但是事情也并不绝对，这些难题并不能难倒 geek 们丰富的想象力。来自 Facebook 的工程师发布了一个[ regenerator ](http://facebook.github.io/regenerator/)的工具，它将 ECMAScript6 中的 Generator 和`yield`语法重新编译，使得编译出的代码能够在 ECMAScript5 下执行，同时达到相同的效果。

下面的代码，我们尝试通过`regenerator`进行编译：

```
var flow = function* () {
  console.time('sleep1');
  yield sleep(1000);
  yield sleep(1000);
  console.timeEnd('sleep1');
  console.time('sleep2');
  yield [sleep(1000), sleep(1000)];
  console.timeEnd('sleep2');
};
```

结果如下：

```
var flow = wrapGenerator.mark(function() {
  return wrapGenerator(function($ctx0) {
    while (1) switch ($ctx0.prev = $ctx0.next) {
    case 0:
      console.time('sleep1');
      $ctx0.next = 3;
      return sleep(1000);
    case 3:
      $ctx0.next = 5;
      return sleep(1000);
    case 5:
      console.timeEnd('sleep1');
      console.time('sleep2');
      $ctx0.next = 9;
      return [sleep(1000), sleep(1000)];
    case 9:
      console.timeEnd('sleep2');
    case 10:
    case "end":
      return $ctx0.stop();
    }
  }, this);
});
```

这段代码中的`wrapGenerator.mark`和`wrapGenerator`来自于`regenerator`生成的依赖中，由于代码太多，暂不给出。如需查看，可以尝试以下命令：

```
$ regenerator -r generator.js
```

如果编写的模块想兼容 ECMAScript5 和 ECMAScript6，可以尝试通过`regenerator`生成两套代码，在 ECMAScript5 下，引入编译后的代码，在 ECMAScript6 下，引入 Generator 和`yield`语法的代码。示例如下：

```
module.exports = supportES6 ? require('./lib/index.js') : require('./es5/index.js');
```

如果要直接体验 Generator 的特性，可以尝试安装`gnode`这个模块。它的原理在 0.11.x 的版本上是启用`--harmory-generators`，在 0.10.x 下则是调用`regenerator`将代码编译为 ECMAScript5 支持的代码，然后再执行。

## 总结

Generator 的出现，使得流程控制可以更细腻，通过`co`和`suspend`库，我们几乎已经完全实现了流程控制的线性处理，同时还能享受到并行异步的性能提升，在不损失性能的情况下，大大提升了编程体验。简单而言，即使只为这个特性，ECMAScript6 也值得期待。