---
title: JS简明入门教程
published: 2024-07-27
tags: [Study, JavaScript]
description: 一份简单的JS语法教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240727100601.png
category: Study
draft: false
---

# 1.如何安装JS
浏览器运行就可以了，你可以使用一些编辑器编写JS代码，例如VS code或者其他。推荐使用VScode很方便。

你的第一个js文件可以是这样的，创建一个html文件，里面写上下面的内容，用浏览器运行打开控制台F12，就可以看到输出了。

```html
<script>
    Console.log("Hello World")
</script>
```

## 1.1 一些在线运行环境

- freecodecamp.org
- codepen.io
- scrimba.com

# 2.注释
注释是解释代码的，防止你忘记，或者方便其他人阅读的，这部分代码并不会执行。

// 是当行注释

/*
多
行
注
释
*/

带编辑器中这部分解释的内容是灰色或者绿色。

```javascript
// 单行注释，声明变量name赋值为小明

var name = "Xiao Ming" // 你也可以写在代码后面

/*
多行
你好
使得
注释并不会执行
*/

var number = 5;
```
# 3. 数据

```javascript
/*
JS 中有其中数据类型，分别是undefined,null,booelean,string,symbol,number,和 object
*/

// string 是字符串，文本内容
// number 是数字，1，2，3，.。。。。1.23。。。。
// undefined 是未定义的数据，你声明了一个变量但是没有赋值
var name;// 例如这样，name会默认赋值undefinded
// null 是什么也没有。
var name = null;
// boolean  true 和false 表示真和假
var isTrue = true;
var isFalse = false;
// symbol 是一个不可变的原始值，之后会有更多的讨论。
// object 是一个存储很多键值对的内容，例如 名字=小明

// 变量，是指你用var声明的内容
var name // var 声明 name 变量的名称  // JS 是动态数据类型，所以你可以给name赋任何的数值。
var name = 12 // 这是合法的。// 不过这讲不通。
// 变量的内容是可以改变
name = "Hello" //  可以重新赋值

// 还有一种声明变量的方式是let,另外还可以声明一种尽可以赋值一次的变量，称之为常量。使用const声明
let name = "Hello"

const PI = 3.14 // 习惯上将变量名称大写表示常量。
//PI  = 2.0 // 再次赋值会报错

/*
关于var和let以及const，var声明的变量，你可以在整个程序中使用，let，只能在限定的作用于范围中使用。const常量，不会被改变

在下面的讲解中会逐渐对比var和let的不同
*/
```

# 4.运算符

赋值运算符
```javascript
var a;
console.log(a) // 为赋值给a，将会打印默认为赋值的情况，undefined
var b = 2; // = 赋值运算符，将2 赋值给变量b
a = 7; // 将 7 赋值给 a

b = a; // 将b赋值给a

console.log(a) // 将变量a的内容打印输出
```

## 4.1 初始化

```javascript

var name = "Xiao Ming" // 声明name变量，初始化为"Xiao Ming"
var age // 未初始化
const PI = 3.14;//常量必须初始化才能使用
Console.log(PI)
```

## 4.2 大小写敏感

```javascript
var name;
NAME = "Xiao Ming"
// 这里运行会报错，说找不到这个NAME变量
```

## 4.3 加减乘除

```javascript

// 加法
var a = 1;
var b = 2;
var sum  = a + b;
sum = 1+2;

// 字符串也可以加
var preName = "Xiao"
var lastName = "Ming"
var fullName = preName + lastName; // XiaoMing

// 乘法
var mul = 10 * 20 

// 除法
var dividing = 20 / 10 // 2

// 自增 自减
var myNumber = 1;
myNmber = myNumber +1;
// 等价于 equals 
myNumber++;

// 
myNumber--;


//其他运算发
var a = 2;
a = a+2 // 等价于
a+=2

//类似其他的* / - 等都有着这样的规则
```

# 5. 小数

```javascript
// 声明浮点数，小数
var myNumber = 1.2
var myNumber2 = 0.12

// add

var myNumber3 = 1.2 + 3.4

// mul 

var myNumber4 = 2.1 * 3.2

// diving

var myNumber5 = 2.1/3

//...

// 取余数
var remainer = 11 % 3 // result would be 2 
```


# 6.转义

如果你给出一个字符串，在字符串中需要用到"引号，那么将无法区分是文本的”还是字符串的边检，转义是处理这种情况的。

```javascript
// \将上转义字符，这种转义对于其他的符号也适用，例如 注释的/ 如果你想在字符串中输入/ 就需要加上反引号
var context = " this is \" double quoted \" string in double quotes"
var context2 = "this is \\  \/" 
// 另一种方法是通过' 和 "间隔使用的方式例如，如果你的文本中出现了"，那么你可以使用'作为包括字符串的边界
var context = ' 这是"一个鱼" '
// 如果你的文本内容中既有" 又有 ' 那么可以使用` 作为包裹
var context = ` '456'  "123" ` 
```

除了上述的内容，还有一些特别的转义符号

```javascript
/*
\r carriage return 
\t tab
\n new line 
\f form fedd
\b backspace
*/

var context = "\r \t \b \n";
```

# 7. 字符串方法

```javascript
// 字符串拼接
var firstName = "Xiao"
var lastName = "Ming"
var fullName = firstName + lastName 

// 获取字符串长度
var length = "XiaoMing".length
var legnth2 = fullName.length

//索引字符串 
var indexOfStringOne = fullName[0] // 0 是你的输入的索引数字，从0开始
var LastletterOfName = fullName[fullName.length-1]
```

# 8.数组array

数组是一个能存储一系列数据的地方

```javascript
var ourArray = ["XiaoMing","ZhangSan"]; // 存储了2个string的数组
var myArray = []; //一个什么也没有存储的数组

var newArray = ["XiaoMing",13]// 一个存储了 字符串和一个数字的数组

// 数组中可以存储数组

var nestArray = [["XiaoMing,",12],["ZhangShan",13],["DaMao",14]] // 一个存储3个数组的数组，其中第一个数组存储了一个str和数字，另外两个相同。

// 获得数组中数据
var ourArray = ["ZS",12]
var name = ourArray[0] // "ZS"
var age = ourArray[1] // 12

// 修改
var ourArray = [12,23,3]
ourArray[2] = 14// [12,23,14]

// 嵌套数组的索引
var ourArray = [[1,2,3],[4,3,2]]
ourArray[0][0] = 5 // [[5,2,3],[4,3,2]]

// 添加新的元素给数组
var ourArray = [["ZhangShan",13],["LiuSi",23]]
ourArray.push(["XiaoMing",25]) // 添加一个新的数组到数组最后

// 移除
var ourArray = [1,2,3]
var removeFromArray = ourArray.pop() // 移除最后一个
console.log(removeFromArray) // 3

// 移除第一个
var ourArray = [1,2,3]
var shiftFromArray = ourArray.shift()
console.log(shiftFromArray) // 1
console.log(ourArray)//[2,3]

//第一个位置添加
var ourArray = [1,2,3]
ourArray.unshift(0)
console.log(ourArray)// [0,1,2,3]
```

# 9. 函数
函数是可以重复利用的代码块，你可以通过定义参数的形式，改变不同的输入，实现不同的功能。

通过function定义一个函数,其中，sayHello是函数名称，（）是参数列表，{}是函数体

参数列表是书写参数的地方， 例如sayHello2需要给name传入数据

函数体是函数调用会执行的指令集合

函数体可以有返回值，返回给函数的调用者

```javascript
// 定义
function sayHello(){
    console.log("Hello");
}

function sayHello2(name){
    console.log("Hello "  + name);
}

function sayHello3(){
    console.log("SayHello3");
    return "Hello";
}
// 调用
sayHello()
//
sayHello2("ZhangShan") // Hello ZhangShan
//多次调用
sayHello2("LiSI") // Hello Lisi
//
var returnFromFunction = sayHello3()
console.log(returnFromFunction)// Hello

//可以定义一些更加有用的函数，两数相加返回，
function add(a,b){
    return a+b
}

```
# 10  作用域
变量的可以使用的范围是作用域，超出该范围不能继续使用，有全局的作用域和局部的作用域。局部的作用域例如函数体内定义的变量只能在函数体内容使用。

```javascript

var myGlobal = 12//全局作用域

function sayHello(){
    var a = 1
    console.log(a)// 找得到a
    console.log(myGlobal) // 找的到在全局作用域/

    // 如果全局和局部有同名的变量，那么就近原则会使用局部作用域的数据
    
    //此外如果你定义数据时候不适用var，例如下面
    b = 20 // 这个变量将会成为全局作用域，不过一般严格的语法检测这里不会通过的，但是在浏览器中输入这样的语句将是合法的。
}

console.log(a) // error 找不到a
console.log(myGlobal)//找的到
```
# 11. Boolean
boolean 只能赋值为true或者false，表示真和假，两种状态，他们也是各种比较运算符的结果，例如大于，等于，不等于的结果，返回true表示成立， false表示不成立。

```javascript
function big(){
    return 2>3;// return false
}

function samml(){
    return 2<3; // return true
    // or 
    return true;
}

// 使用条件判断语句

function ourTrueOrFalse(isTrue){
    if(isTrue){// () 是条件判断，如果判断结果是true执行下面的语句，否则执行else语句
        console.log("It's True")
    }else{
        console.log("Not True,It's False")
    }
    // 或者 不使用 else

    if(isTrue){
        console.log("It's true" )
    }

    // 或者连续判断
    if(2>3){
        
    }else if(2<4){

    }else if(5>3){

    }else{

    }

    // 
}
```

# 12. 判断表达式

返回treu或者false表示表达式是否成立

```javascript

1==2  // 1 等于 2 ？ 结果是false 表示不等于
1 == 1 // 1 等于 1？ 结果是true 表示等于 小同
1 > 2 
1 < 2
1 >= 2
1 <= 2 


// === 判断是否一个对象
// == 判断数值
1=='1' //这个是式子会返回true
1==='1' //这个会返回false

1 != 2// 1 不等于2 ？ 结果是true
1 !== 2 // 
```

# 13.swtich

除了if的判断分支以外还有一种switch的分支

```javascript
function caseInSwitch(val){// 会对val进行匹配，如果匹配到了1，进入第一个分支，遇到break后停止执行，如果没有break，会继续向下执行.
    var answer = "",
    switch(val){
        case 1:
            answer = "alpha";
            break;
        case 2:
            answer = "beta";
            break;
        default：
            answer = "normal";
            break ;//如果所有分支都没有匹配上，就执行默认分支，默认分支可以写可以不写。
    }
}

// 如果你要匹配多个选项可以这样操作

function sequentialSizes(val){
    var answer = ""
    switch(val){
        case 1:
        case 2:
        case 3:
            answer = "Low";
            break;
    }
    return answer;
}
```

# 14 Object
Object是用来定义一组数据的，和数组不同，这组数据使用键值对的方式定义例如下面的

```javascript
// 定义 {} 定义 数据通过键值对设置
var ourDog={
    "name":"Camper",
    "legs":4,
    "tails":1,
    "friends":["everything“]
}

// 获得object中存储的数据，需要通过键来获取

var testObj = {
    "hat":"ballcap",
    "shirt":"jereay",
    "shoes":"cleats"
}

var hatValue = testObj.hat;
var shirtValue = testObj.shirt;

// 另一种访问的方式，如果输入的键有 空格
var testObj = {
    "an entree":"hamburger"
}

var entreeValue = testObj["an entree"]

// 如果键是数字
var testObj = {
    11:"Namath"
}

var playerNumber = 11
var player = testObj[playerNumber]

// 修改object中的数值

var ourDog={
    "name":"Camper".
    "legs":4
}

ourDog.name = "Happy Camper"
ourDog["name"] = "Unhappy Camper"

// 删除一个键值对

delete ourDog.legs

// 检测是否含有某个键
function checkObj(checkProp){
    if(myObj.hasOwnProperty(checkProp)){
        return myObj[checkProp]
    }else{
        return "Not Found"
    }
    return iSHave;
}


```

数组中也可以存储object

```javascript

var myMusic=[
    {
        "artist":"Billy Joel",
        "title":"Piano Man",
        "release_type":1973,
        "formats":[
            "CD",
            "8T",
            "LP" // object中也可以定义数组
        ],
        "gold":true
    }
]
```

object中可以定义object，嵌套

```javascript
var myStorage={
    "car":{
        "inside":{
            "glove box":"maps",
            "passenger seat":"crumbs"
        },
        "outside":{
            "trunk":"jack"
        }
    }
}

// 访问
var gloveBoxConetents = myStorage.car.inside["glove box"];

console.log(gloveBoxConetents)
```


array 和 object 的嵌套访问

```javascript

var myPlants = [
    {
        type:"flowers",
        list:[
            "rose",
            "tulip",
            "dandelion"
        ]
    },
    {
        type:"trees",
        list:[
            "fir",
            "pine",
            "birch"
        ]
    }
]

// 
var sencondTree = myPlants[1].list[1]
```

# 15 循环分支 

循环分支

```javascript

// 循环分支，在不满足条件时候可以一直执行{}中的内容
// while(条件){}

var myArray = [] 

var i = 0;
while(i<5){ // i 不满足 小于 5 则一直运行{}的内容
    myArray.push(i) 
    i++;
}

console.log(myArray) // [1,2,3,4]
```

for 循环分支

```javascript
// for(初始语句，条件判断，循环后执行){}
// 执行流程是这样的
// 初始语句
// while(条件){  循环执行后语句}

var myArray = [] 
for(var i = 0;i<5;i++){
    myArray.push(i)
}

console.log(myArray)
```

通过循环迭代一个数组

```javascript
var ourArr = [1,2,3,4,5]
var outToral = 0;

for(var i=0;i<ourArr.length;i++){
    ourTotal += ourArr[i]
}

// 迭代嵌套数组

var ourArr = [[1,2,3],[4,4,5],[3,2,1]]

for(var i = 0;i<ourArr.length;i++){
    for(var j =0;j<ourArr[0].length;j++){
        console.log(ourArr[i][j])
    }
}
```

# 16 生成随机数

介绍一些生成随机数的内容

```javascript
function randomWholeNum(){
    return Math.floor(Math.random() * 10) // floor  对一个数字来说向下取整，例如10.3 向下取整 是10 
    // random是一个获得0-1的随机数，* 10 就是 0 -10 
    // 向下取整，就是0-9的随机数了。
}

console.log(randomWholeNum())


// 生成在某个区间的随机数
function ourRnandomRange(ourMin,ourMax){
    return Math.floor(Math.random() * (ourMax - ourMin)) + ourMin;
}
```

# 17 转化

有时候我们会需要将一个"15”的文本转为对应的数值15

```javascript
function converToInteger(str){
    return parseInt(str)
}

// 输入是二进制的文本数值
function converToInteger(str){
    return parseInt(str,2)
}

converToInteger("123")
```
# 18 三目运算符

```javascript

function checkEqual(a,b){
    if(a===b){
        return true;
    }else{
        return false;
    }
}

// 三木运算符

function checkEqual(a,b){
    return a===b? true:false
    // more simpty
    return a===b
}

function checkSign(num){
    return num>0?"positive":num<0?"negative":"zero"
}


```

# 19.let var const

对于var 你可以在同一个变量的作用域中声明同一个名称的变量两次。
但是如果你使用let在同一个作用域中声明同一个变量名两次会报错。

```javascript

var name = 1
var name = "XiaoMing" //  正常工作

let age = 1
let age = "XiaoMing" // 报错，声明了已存在的变量
age = 2//可以正常赋值
```

另一个区别是作用域不一样

```javascript
function checkScope(){
    "use strict"
    var i = " function scope"
    if(true){
        i = "block scope"
        console.log("Block scope i is: ",i)
    }
    console.log("Function scope i is :",i)
    return i 
}
// block scope i is block scope
// functio nscope i is : block scope 
```


```javascript

function checkScope(){
    "use strict"
    let i = " function scope"
    if(true){
        let i = "block scope"
        console.log("Block scope i is: ",i)
    }
    console.log("Function scope i is :",i)
    return i 
}
// block scope i is block scope 
// fucntion scope i is function scope 
function checkScope(){
    "use strict"
    // let i = " function scope"
    if(true){
        let i = "block scope"
        console.log("Block scope i is: ",i)
    }
    console.log("Function scope i is :",i)
    return i 
}
// block scope is is block scope
// can't find 'i' error 
```


const只能初始化赋值一次，无法修改，只读

```javascript
const PI = 3.14

// 对于数组

const s = [3,2,1]

function editInplace(){
    "use strict"

    s[0] = 1
    s[1] = 2
    s[2] = 3
}

editInplace() // [1,2,3] 你可以改变数组的内容

s = [1,32,3] // error s 只读不能改变

//这对于object也是同样的道理

function freeObj(){
    "use strict"

    const MATH_CONSTANTS = {
        PI:3.14
    }

    try{
        MATH_CONSTANTS.PI = 99
    }catch(ex){
        console.log(ex)
    }

    return MATH_CONSTANTS.PI;
}

const PI = freeObj()// PI=99 

// 哪有没有什么方法可以让PI的数值不发生改变的?

function freeObj(){
    "use strict"

    const MATH_CONSTANTS = {
        PI:3.14
    }

    Object.freeze(MATH_CONSTANTS)// 加上这段代码，代表冻结这个对象

    try{
        MATH_CONSTANTS.PI = 99// 修改PI数值时候会报错 进入catch代码块中，打印ex错误信息。
    }catch(ex){
        console.log(ex)
    }

    return MATH_CONSTANTS.PI;
}

```
# 20 lambda 函数


```javascript
// 可以将函数定义赋值给一个变量
var magic = function(){
    return new Date();
}

// lambda 写方法
var magic2 = ()=>{
    return new Date();
}
// 对于单行
const magic3 = () => new Date();

// 此后这个变量可以当作一个函数去使用
magic()
magic2()
magic3()


//例子

const myConcat = (arr1,arr2)=>arr1.concat(arr2)

var arr = myConcat([1,2],[1,2,3]) // [1,2,1,2,3]
```

# 21 链式调用

有lambda我们可以将一个函数当成一个变量传递给一些方法，这里说一些链式调用的方法。针对一个数组我们可以很快完成一些操作。


```javascript
// 我们希望将这个数组中的所有正整数取出来，并且平方返回一个list
const realNumberArray = [4,5.6,-9.8,3.14,42,6,8.34,-2]

const squareList = (arr)=>{
    const squaredIntegers = arr.filter(num=>Number.isInteger(num) && num >0).map(num=>num * num);
    return squaredIntegers;
}

const squaredInterges = squareList(realNumberArray);
console.log(squaredInterges)

```

# 22 高阶函数
我们之前说了可以将函数赋值给一个变量，这个变量可以传递，那么自然可以当作函数的返回值，这种使用叫做高阶函数

```javascript
const increment = (function(){//定义了一个函数，返回increment函数，并且这个函数直接执行，increment函数输入两个数值,其中value的数值不输入时候默认为1，相加返回，
    return function increment(number,value=1){
        return number+value;
    };
})();

console.log(increment(5,2))
console.log(increment(5))
```

## 23 可变参数

可变参数...args 表示可以输入多个参数，整个参数可以打包为一个list传递给args

```javascript

const sum = (function(){
    return function sum(...args){
        return args.reduce((a,b)=>a+b,0)//reduce是一个方法，其中a表示当前的累加值，b表示当前数值
    }
})();

console.log(sum(1,2,3,4,5,6))
```

## 24 使用...解包

使用解包解决引用问题

```javascript

var arr1 = ['JAN','FEB','MAR']
var arr2;
(function(){
    arr2 = arr1
    arr2[0]="potato"
})();

console.log(arr1)
// arr[0] = 'potato arr2直接引用了arr1，所以对arr2的修改会导致arr1也发生变化。所以这里可以通过上述的解包的方式

var arr1 = ['JAN','FEB','MAR']
var arr2;
(function(){
    arr2 = [...arr1]//对arr1解包，将数据从新输入到arr2
    arr2[0]="potato"
})();

console.log(arr1)
```


使用解包实现对object的快速取值

```javascript

var voxel = {x:3.6,y:7.4,z:6.54}

var x = voxel.x
var y = voxel.y
var z = voxel.z

const {x:a,y:b,z:c} = voxel // a=3.6,b=7.4,c=6.54

const  AVG_TEMPERATURES = {
    today:77.5,
    tomorrow:79
}

// 解包
funciton getTempOfTmrw(avgTemperatures){
    "use strict"
    const {tomorrow:tempOfTomorrow}= avgTemperatures;
    return tempOfTomorrow;

}

```

对于嵌套的数据解包

```javascript
const LOCAL_FORRCAST = {
    today:{min:72,max:83},
    tomorrow:{min:73.3,max:84.6}
}

function getMaxOfTmrw(forcast){
    "use strict"
    const{tomorrow:{max:maxOfTomorrow}} = forcast

    return maxOfTomorrow
}

```

对于数组的选择数值的解包

```javascript
// example 1 可以省略跳过某个数值

const [z,x, ,y] = [1,2,3,4,5] // z=1,x=2,y=4

// example 2 可以跳过两个数值通过...args的方式获取其他的数值
const source = [1,2,3,4,5,6,7]

function removeFirstTwo(){
    const [a,b,..arr] = list;
    // or const[ , , ...arr] // arr = [3,4,5,6,7]
    return arr;
}

const arr = removeFirstTwo(source)
console.log(arr);
console.log(source);


// 快速交换两个变量的值
let a=8,b=6
(()=>{
    [a,b] =[b,a]
})(); // a=6,b=8

// 在函数的阶段直接解包
const stats = {
    max:56.78,
    standard_deviation:4.34,
    median:34.54,
    mode:23.87,
    min:-0.75,
    average:35.85
}

const half = (function(){
    return function half({max,min}){
        return (max + min) /2.0;
    }
})();

console.log(stats)
console.log(half(stats))
```

# 25 字符串模板

字符串模板是可以让你按照你的想要的格式输出字符串

```javascript

const perosn ={
    name:"Zodiac Hasbro"
    age:56
}
// 字符串模板使用``包裹，其中的数据将会原封不动的输出，例如我在name后面敲了回车，这个回车也会被输出。其中你看与同${中写代码}
const greeting = `Hello my name is ${person.name}
I am ${person.age} year's old`
```

# 26 简化创建object

```javascript

const creatPerson = （name,age,gender)=>{
    return {
        name:name,
        age:age,
        geneder:gender
    }
}

const creatPerson2 = (name,age,gender)=>({name,age,gender});
```

# 26 在object中书写function

之前说的函数可以赋值给变量，那么同样的函数可以赋值给object的变量

```javascript
const bicycle ={
    gear:2,
    setGear:fucntion(newGear){// 将函数赋值了个setGear
        "use strict"
        this.gear = newGear // this 指代当前的obj
    }

};

bicycle.setGear(3);

// 更将简便的写法

const bicycle = {
    gear:2,
    setGear(newGear){
        this.gear = newGear
    }//可以实现OOP了
}

bicycle.setGear(3)
```

# 27 构造方法

JS也有class和构造方法，实现面向对象的内容

```javascript

class SpaceShutle{
    constructor(targetPlanet){
        this.targetPlanet = targetPlanet
    }
}

var zeus = new SpaceShutle('Jupiter');

console.log(zeus.targetPlanet)
```

通过函数的方式返回一个class,通过这个返回值创建对象

```javascript
function makeClass(){
    class Vegetable{
        constructor(name){
            this.name = name
        }
    }
    return Vegetable
}


const Vegetable = makeClass()
const carrot = new Vegetable('carrot');
console.log(carrot.name)
```

# 28 使用set和get控制字段的访问权限

```javascript
function makeClass(){
    class Thermostat{
        constructor(temp){
            this._temp = 5/9 * (temp-32);
        }
        get temperature(){ // 设置get和set，在这里可以限制传入和返回的内容
            return this._temp;
        }
        set temperature(updatedTemp){
            this._temp = updatedTemp;
        }
    }

    return Themostat
}

const Termostat = makeClass()
const thermos = new Termostat(76);
let temp = thermos.temperature; // field 使用了get方法获得对于数值，调用了类的对应get方法。
termos.temperature =26;
temp = thermos.temperature;

```


# 29 理解import和require的区别
import和require都是导包的方法。

string_function.js
```javascript
export const capitalizeString = str=>str.toUpperCase();
```

index.js

```javascript
import {capitalizeString} from "./string_function"

const cap = capitalizeString("hello!")

console.log(cap) // HELLO
```


其他的导出方法

```javascript
const capitalizeString = str=>str.toUpperCase();

// 也可以这样导出
export { capitalizeString};

//常量也可以导出
export const foo = "bar";
export const bar = "foo";
```

使用*导入所有

```javascript
import * as capitalizeStrings from "capitalize_strings"

```


导出的默认字段

math_functions.js
```javascript
export default function subtract(x,y){return x-y}

```

导入默认字段

```javascript
import substract from "math_functions"

substarct
```