​								 															 						

# map/reduce

------

如果你读过Google的那篇大名鼎鼎的论文“[MapReduce: Simplified Data Processing on Large Clusters](http://research.google.com/archive/mapreduce.html)”，你就能大概明白map/reduce的概念。

### map

举例说明，比如我们有一个函数f(x)=x2，要把这个函数作用在一个数组`[1, 2, 3, 4, 5, 6, 7, 8, 9]`上，就可以用`map`实现如下：

![map](15map_reduce.assets/0.png)

由于`map()`方法定义在JavaScript的`Array`中，我们调用`Array`的`map()`方法，传入我们自己的函数，就得到了一个新的`Array`作为结果：

```
'use strict'; 
function pow(x) {
    return x * x; 
    } 
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]
console.log(results);   
```

注意：`map()`传入的参数是`pow`，即函数对象本身。

你可能会想，不需要`map()`，写一个循环，也可以计算出结果：

```
var f = function (x) {
    return x * x;
};

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var result = [];
for (var i=0; i<arr.length; i++) {
    result.push(f(arr[i]));
}
```

的确可以，但是，从上面的循环代码，我们无法一眼看明白“把f(x)作用在Array的每一个元素并把结果生成一个新的Array”。

所以，`map()`作为高阶函数，事实上它把运算规则抽象了，因此，我们不但可以计算简单的f(x)=x2，还可以计算任意复杂的函数，比如，把`Array`的所有数字转为字符串：

```
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
arr.map(String); // ['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

只需要一行代码。

### reduce

再看reduce的用法。Array的`reduce()`把一个函数作用在这个`Array`的`[x1, x2, x3...]`上，这个函数必须接收两个参数，`reduce()`把结果继续和序列的下一个元素做累积计算，其效果就是：

```
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
```

比方说对一个`Array`求和，就可以用`reduce`实现：

```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
}); // 25
```

练习：利用`reduce()`求积：

```
'use strict';
function product(arr) { 
  return x * y;
} 

```

要把`[1, 3, 5, 7, 9]`变换成整数13579，`reduce()`也能派上用场：

```
var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x * 10 + y;
}); // 13579
```

如果我们继续改进这个例子，想办法把一个字符串`13579`先变成`Array`——`[1, 3, 5, 7, 9]`，再利用`reduce()`就可以写出一个把字符串转换为Number的函数。

```
var s = "13579";
let arr = [];
for (let t of s) {
  arr.push(t);
 }
let aaa=arr.reduce(function (x, y) {
  return x*10+y*1;
}); // 25
console.log(aaa);
```

练习：不要使用JavaScript内置的`parseInt()`函数，利用map和reduce操作实现一个`string2int()`函数：

```
'use strict'; 
function string2int(s) {
  let arr = [];
  for (let t of s) {
    arr.push(t);
 }
let intArr=arr.reduce(function (x, y) {
  return x*10+y*1;
}); // 25
return intArr;
} 
```

### 练习

请把用户输入的不规范的英文名字，变为首字母大写，其他小写的规范名字。输入：`['adam', 'LISA', 'barT']`，输出：`['Adam', 'Lisa', 'Bart']`。

```
'use strict'; 
function normalize(arr) { 
  return arr.map(function(x){
    x = x.toLowerCase();
    x = x[0].toUpperCase() + x.substring(1)
    return x;
})
} 
```

小明希望利用`map()`把字符串变成整数，他写的代码很简洁：

```
'use strict'; 
var arr = ['1', '2', '3']; 
var r; 
r = arr.map(function (x) {
  return parseInt(x);
});
console.log(r); 
```

结果竟然是`1, NaN, NaN`，小明百思不得其解，请帮他找到原因并修正代码。

提示：参考[Array.prototype.map()的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)。



------



------



 					