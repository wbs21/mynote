## 字符串

------

JavaScript的字符串就是用`''`或`""`括起来的字符表示。

如果`'`本身也是一个字符，那就可以用`""`括起来，比如`"I'm OK"`包含的字符是`I`，`'`，`m`，空格，`O`，`K`这6个字符。

如果字符串内部既包含`'`又包含`"`怎么办？可以用转义字符`\`来标识，比如：

```
'I\'m \"OK\"!';
```

表示的字符串内容是：`I'm "OK"!`

转义字符`\`可以转义很多字符，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`。

ASCII字符可以以`\x##`形式的十六进制表示，例如：

```
'\x41'; // 完全等同于 'A'
```

还可以用`\u####`表示一个Unicode字符：

```
'\u4e2d\u6587'; // 完全等同于 '中文'
```

### 多行字符串

由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号 *`\* ... \*`* 表示：

```
`这是一个
多行
字符串`;
```

*注意*：反引号在键盘的ESC下方，数字键1的左边：

```ascii
┌─────┐ ┌─────┬─────┬─────┬─────┐
│ ESC │ │ F1  │ F2  │ F3  │ F4  │
│     │ │     │     │     │     │
└─────┘ └─────┴─────┴─────┴─────┘
┌─────┬─────┬─────┬─────┬─────┐
│  ~  │  !  │  @  │  #  │  $  │
│  `  │  1  │  2  │  3  │  4  │
├─────┴──┬──┴──┬──┴──┬──┴──┬──┘
```

练习：测试你的浏览器是否支持ES6标准，如果不支持，请把多行字符串用`\n`重新表示出来：

`// 如果浏览器不支持ES6，将报SyntaxError错误: ` Run

### 模板字符串

要把多个字符串连接起来，可以用`+`号连接：

```
var name = '小明';
var age = 20;
var message = '你好, ' + name + ', 你今年' + age + '岁了!';
alert(message);
```

如果有很多变量需要连接，用`+`号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

```
var name = '小明';
var age = 20;
var message = `你好, ${name}, 你今年${age}岁了!`;
alert(message);
```

练习：测试你的浏览器是否支持ES6模板字符串，如果不支持，请把模板字符串改为`+`连接的普通字符串：

```
'use strict';
 // 如果浏览器支持模板字符串，将会替换字符串内部的变量:
 var name = '小明';
 var age = 20;
console.log(`你好, ${name}, 你今年${age}岁了!`);
```



### 操作字符串

字符串常见的操作如下：

```
var s = 'Hello, world!';
s.length; // 13
```

要获取字符串某个指定位置的字符，使用类似Array的下标操作，索引号从0开始：

```
var s = 'Hello, world!';
s[0]; // 'H'
s[6]; // ' '
s[7]; // 'w'
s[12]; // '!'
s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined
```

利用字符串的索引，可以把字符串push到数组中：

```let s = 'Hello, world!';
var s = 'Hello, world!';
arr = [];
for (let i = 0; i < s.length; i++) {
arr.push(s[i]);
}
console.log(arr);// ["H", "e", "l", "l", "o", ",", " ", "w", "o", "r", "l", 
```


需要特别注意的是*，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：

```
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```

JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串：

### toUpperCase

`toUpperCase()`把一个字符串全部变为大写：

```
var s = 'Hello';
s.toUpperCase(); // 返回'HELLO'
```

### toLowerCase

`toLowerCase()`把一个字符串全部变为小写：

```
var s = 'Hello';
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
lower; // 'hello'
```

### indexOf

`indexOf()`会搜索指定字符串出现的位置：

```
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1
```

### substring

`substring()`返回指定索引区间的子串：

```
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```

## js 字符串操作函数

走进前端行业已有两年之久，对于字符串的操作也是家常便饭了，但也总在查查找找，如今对于我这个强迫症患者开始爆发了。

对字符串的操作做以下整理，废话不多说直接走起来。

####  1、字符串转换

字符串转换是最基础的要求和工作，你可以将任何类型的数据都转换为字符串，你可以用下面三种方法的任何一种：

```
var num=24;
var mystr=num.toString();    //"24"
```

你同样可以这么做：

```
var num=24;
var mystr=String(num);    //"24"
```

或者，在简单点儿：

```
var num=24;
var mystr="" + num;    //"24"
```

####  2、字符串分割

将字符串进行拆分返回一个新的数组，JavaScript就给我们提供了一个非常方便的函数：

```
var mystr="qingchenghuwoguoxiansheng,woaishenghuo,woaiziji";
var arr1=mystr.split(",");    //["qingchenghuwoguoxiansheng","woaishenghuo","woaiziji"];
var arr2=mystr.split("");        //["q","i","n","g","c","h","e","n","g","h","u","w","o","g","u","o","x","i","a","n","s","h","e","n","g",",","w","o","a","i","s","h","e","n","g","h","u","o",",","w","o","a","i","z","i","j","i"];
```

split()的第二个参数，表示返回的字符串数组的最大长度

```
var mystr="qingchenghuwoguoxiansheng,woaishenghuo,woaiziji";
var arr1=mystr.split(",",2); //["qingchenghuwoguoxiansheng","woaishenghuo"];
var arr2=mystr.split("",8); //["q","i","n","g","c","h","e","n"];
```

####  3、字符串替换

仅仅查找到字符串并不会是题目的停止，一般题目还经常会要求你去进行替换操作，那就继续看以下代码:

```
var mystr="wozaijinxingzifuchuantihuancaozuo,zifuchuantihuano";
var replaceStr=mystr.replace("zifuchuan"," ");    //wozaijinxing tihuancaozuo,zifuchuantihuano
var replaceStr=mystr.replace(/zifuchuan/," ");    //wozaijinxing tihuancaozuo,zifuchuantihuano
var replaceStr=mystr.replace(/zifuchuan/g," ");    //wozaijinxing tihuancaozuo, tihuano
```

默认只进行第一次匹配操作的替换，想要全局替换，需要置上正则全局标识g

####  4、获取字符串长度

获取字符串的长度经常会用到，方法很简单：

```
var mystr="qingchenghuwoguoxiansheng,woaishenghuo,woaiziji";
var arrLength=mystr.length;    //47
```

####  5、查询子字符串

判断字符串内是否包含子串，不少开发者会使用for循环来判断，而忘记了JavaScript提供子串函数：

- indexOf()，该Of() 方法对大小写敏感。返回字符串中一个子串第一处出现的索引（从左到右搜索）。如果没有匹配项，返回 -1 。

```
var mystr="Hello world!";
var index=mystr.indexOf("llo");    //2
var index1=mystr.indexOf("l");    //2
var index2=mystr.indexOf("l",3);    //3
```

- lastIndexOf()，该方法对大小写敏感。返回字符串中一个子串最后一处出现的索引（从右到左搜索），如果没有匹配项，返回 -1 。

```
var mystr="Hello world!";
var index=mystr.lastIndexOf("llo");    //2
var index1=mystr.lastIndexOf("l");    //9
var index2=mystr.lastIndexOf("l",4);    //3
```

####  6、返回指定位置的字符或其字符编码值

查找给定位置的字符，可以使用如下函数：

```
var mystr="Hello World!";
var index=mystr.charAt(7);    //o
```

同样，它的一个兄弟函数就是查找对应位置的字符编码值，如：

```
var mystr="Hello World!";
var charCode=mystr. charCodeAt(7);    //111
```

#### 7、 字符串匹配

可以直接通过字符串进行匹配，也可以通过正则进行匹配，可能需要你对正则表达式有一定的了解，先来看看match()函数：

```
var mystr="hi,mynameisguoxiansheng6,33iswho?";
var matchStr=mystr.match("guo");    //guo
var matchStr1=mystr.match("Guo");    //null
var regexp1=/\d+/g;
var regexp2=/guo/g;
var regexp3=/guo/;
var matchStr2=mystr.match(regexp1);    //["6","33"]
var matchStr3=mystr.match(regexp2);    //["guo"]
var matchStr3=mystr.match(regexp3);    //["guo",index:11,input:"hi,mynameisguoxiansheng6,33iswho?"]
matchStr3.index    //11
matchStr3.input    //hi,mynameisguoxiansheng6,33iswho?
```

注意：1.此处使用字符串直接进行匹配，被匹配的字符串内包含要匹配的字符串时，返回所要匹配的字符串。

　　　2.如果使用正则匹配字符串时，如果正则表达式没有 g (全局标识)标志，返回与正则匹配相同的结果。而且返回的数组拥有一个额外的 input 属性，该属性包含原始字符串。另外，还拥有一个 index 属性，该属性表示匹配结果在被字符串中的索引（以0开始）。如果正则表达式包含 g 标志，则该方法返回匹配字符串的数组。

再来看看使用exec()函数： 

```
var mystr="hi,mynameisguoxiansheng6,33iswho?";
var regexp1=/guo/g;
var matchStr=regexp1.exec(mystr);  //["guo"]
var regexp2=/guo/;
var matchStr1=regexp2.exec(mystr);    //["guo",index:11,input:"hi,mynameisguoxiansheng6,33iswho?"]
matchStr1.index    //11
matchStr1.input    //hi,mynameisguoxiansheng6,33iswho?
```

简单吧，仅仅是把正则和字符串换了个位置，即exec()函数是在正则上调用，传递字符串的参数。对于上面两个方法，匹配的结果都是返回第一个匹配成功的字符串，如果匹配失败则返回null。

再来看一个类似的函数search()：

```
var mystr = "hi,mynameisguoxiansheng6,33iswho?";
var regexp1 = /guo/;
var matchStr = mystr.search(regexp1);    //11
```

进行正则匹配查找。如果查找成功，返回字符串中匹配的索引值。否则返回 -1

####  8、字符串连接

可以将两个或多个字符串进行加法操作，同时可以使用JavaScript提供的concat函数：

先看加法操作进行字符串连接：

```
var mystr1="Hello";
var mystr2="world!";
var newStr=mystr1+" "+mystr2;    //Hello world!
```

是不是很简单呀，那继续看看concat函数吧：

```
var mystr1="Hello";
var mystr2=" world,";
var mystr3="Hello";
var mystr4="guoxiansheng";
var newStr=mystr1.concat(mystr2+mystr3+" "+mystr4);    //Hello world,Hello guoxiansheng
```

concat()函数可以有多个参数，传递多个字符串，拼接多个字符串。

####  9、字符串切割和提取

有三种可以从字符串中抽取和切割的方法：

第一种，slice()函数：

```
var mystr="hello world!";
var sliceStr1=mystr.slice(-3);    //ld!
var sliceStr2=mystr.slice(-3,-1);    //ld
var sliceStr3=mystr.slice(3);    //lo world!
var sliceStr4=mystr.slice(3,7);    //lo w
```

第二种：substring()函数：

```
var mystr="hello world!";
var sliceStr1=mystr.substring(3);    //lo world!
var sliceStr2=mystr.substring(3,7);    //lo w
```

第三种：substr()函数：

```
var mystr="hello world!";
var sliceStr1=mystr.substr(3);    //lo world!
var sliceStr2=mystr.substr(3,7);    //lo wo
```

注：1.slice() 可以为负数，如果起始位置为负数，则从字符串最后一位向前找对应位数并且向后取结束位置，如果为正整数则从前往后取起始位置到结束位置。
　　2.substring()只能非负整数，截取起始结束位置同slice()函数一致。

　　3.substr()与第一、第二种函数不同，从起始位置开始截取，结束位置为第二个参数截取的字符串最大长度。

以上三种函数未填第二参数时，自动截取起始位置到字符串末尾。

####  10、字符串大小写转换

```
var mystr="Hello World!";
var lowCaseStr=mystr.toLowerCase();    //hello world!
var upCaseStr=mystr. toUpperCase();    //HELLO WORLD!
```

####   11、字符串去空格

trim方法用来删除字符串前后的空格 

```
var mystr="     hello world      ";  
var trimStr=mystr.trim();    //hello world
```

## 常用的字符串操作

#### 1、字符串去重

```
var str="aahhgggsssjjj";
function removeRepeat(msg){  
    var res=[];  
    var arr=msg.split("");  
    for(var i=0;i<arr.length;i++){  
        if(res.indexOf(arr[i])==-1){  
            res.push(arr[i]);  
        }  
    }  
    return res.join("");  
}  
removeRepeat(str);    //ahgsj 
```

#### 2、判断字符串中字符出现的次数

```
/*  
    1.先实现字符串去重  
    2.然后对去重后的数组用for循环操作，分别与原始数组中各个值进行比较，如果相等则count++,循环结束将count保存在sum数组中，然后将count重置为0  
    3.这样一来去重后的数组中的元素在原数组中出现的次数与sum数组中的元素是一一对应的  
*/  
var str="aacccbbeeeddd";  
var sum=[];  
var res=[];  
var count=0;  
var arr=str.split("");  
for(var i=0;i<arr.length;i++){  
    if(res.indexOf(arr[i])==-1){  
        res.push(arr[i]);  
    }  
}  
for(var i=0;i<res.length;i++){  
    for(var j=0;j<arr.length;j++){  
        if(arr[j]==res[i]){  
            count++;  
        }  
    }  
    sum.push(count);  
    count=0;  
}  
console.log(res);    //["a", "c", "b", "e", "d"]  
for(var i=0;i<res.length;i++){  
    var str=(sum[i]%2==0)?"偶数":"奇数";  
    console.log(res[i]+"出现了"+sum[i]+"次");  
    console.log(res[i]+"出现了"+str+"次");  
}  
```

#### 字符串search方法

这是一个很久以前的事情了，好像是安心兄弟在学习js的时候做的练习。

具体记不清了，今天就来简单分析下 search 究竟是什么用的。

从字面意思理解，一个是搜索字符串吧。

| 12   | var str = "123456789abcde";console.log( str.search("abc") ); // 9 |
| ---- | ------------------------------------------------------------ |
|      |                                                              |



确实是搜索指定字符在一个字符串中出现的位置，如果不存在就返回 -1

可是这样就跟 indexOf 功能一样了，何必单独搞一个 search 出来呢？

| 12345 | var str = "123456789abcde";console.log( str.search("abc") ); // 9console.log( str.indexOf("abc") ); // 9console.log( str.search("xxx") ); // -1console.log( str.indexOf("xxx") ); // -1 |
| ----- | ------------------------------------------------------------ |
|       |                                                              |

点击右侧运行可查看输出结果。



其实区别在于 search 是强制正则的，而 indexOf 只是按字符串匹配的。

来看一个例子：

| 1234567 | var str = "123456789.abcde"; // 比刚才多了一个 . 而已console.log( str.search(".") );  // 0 因为正则 . 匹配除\n以外任意字符console.log( str.indexOf(".") );  // 9 只能匹配字符 .console.log( str.search("\\.") ); // 9 相当于 new RegExp("\\.")console.log( str.indexOf("\\.") ); // -1 匹配字符 \. 所以不存在console.log( str.search(/\./) );  // 9 正则匹配转以后的 . 字符console.log( str.indexOf(/\./) ); // -1 相当于匹配字符串 "/\./" 所以不存在 |
| ------- | ------------------------------------------------------------ |
|         |                                                              |

这个例子可以很好的说明 search 强制正则匹配模式。

## 字符串转换总结

总结一下，有这么几条规则需要遵守：

- 不要使用new Number()、new Boolean()、new String()创建包装对象；
- 用parseInt()或parseFloat()来转换任意类型到number；
- 用String()来转换任意类型到string，或者直接调用某个对象的toString()方法；
- 通常不必把任意类型转换为boolean再判断，因为可以直接写if (myVar) {...}；
- typeof操作符可以判断出number、boolean、string、function和undefined；
- 判断Array要使用Array.isArray(arr)；
- 判断null请使用myVar === null；
- 判断某个全局变量是否存在用typeof window.myVar === 'undefined'；
- 函数内部判断某个变量是否存在用typeof myVar === 'undefined'。

最后有细心的同学指出，任何对象都有toString()方法吗？null和undefined就没有！确实如此，这两个特殊值要除外，虽然null还伪装成了object类型。

更细心的同学指出，number对象调用toString()报SyntaxError：

```
123.toString(); // SyntaxError
```

遇到这种情况，要特殊处理一下：

```
123..toString(); // '123', 注意是两个点！
(123).toString(); // '123'
```

不要问为什么，这就是JavaScript代码的乐趣！