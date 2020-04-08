## iterable

------

遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。

具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。

`for ... of`循环是ES6引入的新的语法，请测试你的浏览器是否支持：

```
'use strict'; var a = [1, 2, 3]; 
for (var x of a) { } 
console.log('你的浏览器支持for ... of'); 
```

用`for ... of`循环遍历集合，用法如下：

```
var a = ['A', 'B', 'C'];
var s = new Set(['A', 'B', 'C']);
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
for (var x of a) { // 遍历Array
    console.log(x);
}
for (var x of s) { // 遍历Set
    console.log(x);
}
for (var x of m) { // 遍历Map
    console.log(x[0] + '=' + x[1]);
}
```

你可能会有疑问，`for ... of`循环和`for ... in`循环有何区别？

`for ... in`循环由于历史遗留问题，它遍历的实际上是对象的属性名称。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

当我们手动给`Array`对象添加了额外的属性后，`for ... in`循环将带来意想不到的意外效果：

```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x in a) {
    console.log(x); // '0', '1', '2', 'name'
}
```

`for ... in`循环将把`name`包括在内，但`Array`的`length`属性却不包括在内。

`for ... of`循环则完全修复了这些问题，它只循环集合本身的元素：

```
var a = ['A', 'B', 'C'];
a.name = 'Hello';
for (var x of a) {
    console.log(x); // 'A', 'B', 'C'
}
```

这就是为什么要引入新的`for ... of`循环。

然而，更好的方式是直接使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。以`Array`为例：

```
'use strict';
var a = ['A', 'B', 'C']; 
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
```

*注意*，`forEach()`方法是ES5.1标准引入的，你需要测试浏览器是否支持。

`Set`与`Array`类似，但`Set`没有索引，因此回调函数的前两个参数都是元素本身：

```
var s = new Set(['A', 'B', 'C']);
s.forEach(function (element, sameElement, set) {
    console.log(element);
});
```

`Map`的回调函数参数依次为`value`、`key`和`map`本身：

```
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    console.log(value);
});
```

如果对某些参数不感兴趣，由于JavaScript的函数调用不要求参数必须一致，因此可以忽略它们。例如，只需要获得`Array`的`element`：

```
var a = ['A', 'B', 'C'];
a.forEach(function (element) {
    console.log(element);
});
```

for in 循环的是对象的属性 ; for of 循环的是迭代器中的每一个元素 

obj is not iterable 普通对象不能使用 for of 迭代器 

```
    var m = new Map();
	m.hehe = "haha";
	m.set("name","wangjie");
	m.set("hehe","dada");
	m.set("hoho","lolo");
	for(var element in m){
		console.log(element); // hehe
	}

	for(var element of m){
		console.log(element); //["name", "wangjie"] ["hehe", "dada"] ["hoho", "lolo"]
	}

    
```

迭代器迭代 数组 map set

```
    var m = new Map();
	m.set("name","wangjie");
	m.set("hehe","dada");
	m.set("hoho","lolo");
	m.forEach(function(element,key,map){
		console.log(`element:${element};key:${key};map:${map}`);
	});

	var set = new Set(["hello","world","java"]);
	set.forEach(function(element,index,set){
		console.log(`element:${element};index:${index};set:${set}`);
	});

	var arr = ["hello","world","java"];
	arr.forEach(function(element,index,array){
		console.log(`element:${element};index:${index};array:${array}`);
	})

    输出结果console:
    element:wangjie;key:name;map:[object Map]
    demo1.html:195 element:dada;key:hehe;map:[object Map]
    demo1.html:195 element:lolo;key:hoho;map:[object Map]

    demo1.html:200 element:hello;index:hello;set:[object Set]
    demo1.html:200 element:world;index:world;set:[object Set]
    demo1.html:200 element:java;index:java;set:[object Set]

    demo1.html:205 element:hello;index:0;array:hello,world,java
    demo1.html:205 element:world;index:1;array:hello,world,java
    demo1.html:205 element:java;index:2;array:hello,world,java
```

## **for in 和for of的区别**

#### **1 遍历数组通常用for循环**

ES5的话也可以使用forEach，ES5具有遍历数组功能的还有map、filter、some、every、reduce、reduceRight等，只不过他们的返回结果不一样。但是使用foreach遍历数组的话，使用break不能中断循环，使用return也不能返回到外层函数。

Array.prototype.method=function(){

　　console.log(this.length);

}

var myArray=[1,2,4,5,6,7]

myArray.name="数组"

for (var index in myArray) {

 console.log(myArray[index]);

}

#### **2 for in遍历数组的毛病**



1.index索引为字符串型数字，不能直接进行几何运算

2.遍历顺序有可能不是按照实际数组的内部顺序

3.使用for in会遍历数组所有的可枚举属性，包括原型。例如上栗的原型方法method和name属性

所以for in更适合遍历对象，不要使用for in遍历数组。

那么除了使用for循环，如何更简单的正确的遍历数组达到我们的期望呢（即不遍历method和name），ES6中的for of更胜一筹.

Array.prototype.method=function(){

　　console.log(this.length);

}

var myArray=[1,2,4,5,6,7]

myArray.name="数组";

for (var value of myArray) {

 console.log(value);

}

*记住，for in遍历的是数组的索引（即键名），而for of遍历的是数组元素值value。*

for of遍历的只是数组内的元素，而不包括数组的原型属性method和索引name

### **3 遍历对象**

遍历对象 通常用for in来遍历对象的键名

Object.prototype.method=function(){

　　console.log(this);

}

var myObject={

　　a:1,

　　b:2,

　　c:3

}

for (var key in myObject) {

 console.log(key);

}

for in 可以遍历到myObject的原型方法method,如果不想遍历原型方法和属性的话，可以在循环内部判断一下,hasOwnPropery方法可以判断某属性是否是该对象的实例属性

for (var key in myObject) {

　　if（myObject.hasOwnProperty(key)){

　　　　console.log(key);

　　}

}

同样可以通过ES5的Object.keys(myObject)获取对象的实例属性组成的数组，不包括原型方法和属性

Object.prototype.method=function(){

　　console.log(this);

}

var myObject={

　　a:1,

　　b:2,

　　c:3

}

### **总结**

- for..of适用遍历数/数组对象/字符串/map/set等拥有迭代器对象的集合.但是不能遍历对象,因为没有迭代器对象.与forEach()不同的是，它可以正确响应break、continue和return语句
- for-of循环不支持普通对象，但如果你想迭代一个对象的属性，你可以用for-in循环（这也是它的本职工作）或内建的Object.keys()方法：

for (var key of Object.keys(someObject)) {

 console.log(key + ": " + someObject[key]);

}

- 遍历map对象时适合用解构,例如;

for (var [key, value] of phoneBookMap) {

  console.log(key + "'s phone number is: " + value);

}

- **当你为对象添加myObject.toString()方法后，就可以将对象转化为字符串，同样地，当你向任意对象添加myObject**[Symbol.iterator](https://www.jianshu.com/p/c43f418d6bf0)**方法，就可以遍历这个对象了。**

  

  举个例子，假设你正在使用jQuery，尽管你非常钟情于里面的.each()方法，但你还是想让jQuery对象也支持for-of循环，你可以这样做：

jQuery.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];

所有拥有[Symbol.iterator](https://www.jianshu.com/p/c43f418d6bf0)的对象被称为可迭代的。在接下来的文章中你会发现，可迭代对象的概念几乎贯穿于整门语言之中，不仅是for-of循环，还有Map和Set构造函数、解构赋值，以及新的展开操作符。

- for...of的步骤

  

  or-of循环首先调用集合的[Symbol.iterator](https://www.jianshu.com/p/c43f418d6bf0)方法，紧接着返回一个新的迭代器对象。迭代器对象可以是任意具有.next()方法的对象；for-of循环将重复调用这个方法，每次循环调用一次。举个例子，这段代码是我能想出来的最简单的迭代器：

var zeroesForeverIterator = {

[Symbol.iterator]: function () {

  return this;

 },

 next: function () {

 return {done: false, value: 0};

}

};

for…of是在for…in之后推出的新特性,弥补for…in中的一些不足.

JSON 数据的标的达方式是key:value

for…of遍历出的结果是value

for…in遍历出的结果是key

遍历数组的区别

遍历数组var和let类型的比较

这里使用let声明变量,不要使用var,存在变量提升问题

//使用var类型遍历数组

var a=[1,2,3]

for(var i=;i<3;i++){

  setTimeout(function(){

​    consloe.log(i);

  },0)

}

//输出结果是333

//使用let类型遍历数组

var a=[1,2,3]

for(let i=;i<3;i++){

  setTimeout(function(){

​    consloe.log(i);

  },0)

}

//输出结果是123

使用for…in遍历数组

遍历结果是key,数组下标

var a=[1,2,3];

for(let i in a){

  console.log(i);//0 1 2

  console.log(a[i]);//1 2 3

}

使用for…of遍历数组

遍历结果是value,数组值

var a=[1,2,3];

for(let i of a){

  console.log(i);//1 2 3

}			

