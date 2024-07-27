---
title: NodeJS简单操作
published: 2024-07-27
tags: [Study, JavaScript]
description: 一份简单的Node.js语法教程
image: https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240727100738.png
category: Study
draft: false
---

# 1.vscode 快捷操作

ctrl+N 新建一个文件

ctrl+s 保存

ctrl+shift+p 打开命令面板

select default shell profile 更改默认的shell，默认是powershell

ctrl + ` 打开一个新的终端

ctrl + b 关闭侧边栏

# 2. 使用node运行js代码

创建一个js文件为app，输入下面的代码

```javascript
console.log("Hello world from  Node JS")
```

可以看到下面的运行结果

Hello world from  Node JS

# 3. 模块
一个js就是一个模块，负责一部分的功能，这样的分模块的设计可以减少耦合。

创建一个新的文件，使用module.exports暴露给外部使用的内容，函数，字段，或者类
tutorial.js

```js
const sum = (num1,num2)=>num1 + num2;

module.exports = sum;
```

在app.js中导入这个方法

```js
const tutorial = require("./tutorial")

console.log("Hello World From Node.js")
console.log(tutorial) // [Function: sum]
console.log(tutorial(1,2)) // 3
```

# 4. 如何导出更多的内容

tutorial.js中输入下面的内容
```js

const sum = (num1,num2)=>num1 + num2;
const PI =3.14;
class someMathObject{
    constructor(){
        console.log("objcet created");
    }
}

module.exports.sum = sum;
module.exports.PI = PI;
module.exports.someMathObject = someMathObject;
```

如何导入多这个内容

```js
const tutorial = require("./tutorial")


console.log("Hello World From Node.js")
console.log(tutorial)
/*
{
  sum: [Function: sum],
  PI: 3.14,
  someMathObject: [class someMathObject]
}
*/
console.log(tutorial.sum(1,2)) // 3
console.log(tutorial.PI) // 3.14
console.log(new tutorial.someMathObject()) // someMathObject {}

```

一种更加简单的导出方式

```js
const sum = (num1,num2)=>num1 + num2;
const PI =3.14;
class someMathObject{
    constructor(){
        console.log("objcet created");
    }
}

module.exports = {sum:sum,PI:PI,someMathObject:someMathObject}
```


# 5.node 事件模块

定义事件，和回调函数，以及触发事件

```js
const  EventEmitter = require('events'); // 导出事件监听器
const eventEmitter = new EventEmitter();// 创建事件监听器

eventEmitter.on("tutorial",()=>{//监听事件tutorial，第二个参数是回调函数
    console.log("tutorial event has occured");
});

eventEmitter.emit("tutorial"); // 发送一个事件tutorial
```

是否回调函数可以传入参数，当然是可以的。

```js
const  EventEmitter = require('events'); // 导出事件监听器
const eventEmitter = new EventEmitter();// 创建事件监听器

eventEmitter.on("tutorial",(num1,num2)=>{//监听事件tutorial，第二个参数是回调函数
    console.log("tutorial event has occured");
    console.log(num1+num2)
});

eventEmitter.emit("tutorial",1,2); // 发送一个事件tutorial，并传入参数
```

我们可以同类继承的方式获得自己的事件类，添加额外的属性和方法，

```js
class Person extends EventEmitter{// 定义了一个类，该类继承于EventEmitter
    constructor(name){
        super();
        this._name = name 
    }

    get name(){
        return this._name 
    }

}

const jack = new Person("jack")
jack.on("name",()=>{
    console.log("my name is "+jack.name)
})

jack.emit("name")
```

# 6. readLine

我们来做一个简单的猜数小游戏，输入两数相加之后数值，然后你给出他们的计算之和，如果算错乐会提示你错误，然后从新输入。

```js
const readline = require("readline")
const rl = readline.createInterface({ input: process.stdin, output: process.stdout })// 初始输入读取readline的对象，使用标准输入输出作为参数
let num1 = Math.floor((Math.random() * 10) + 1);
let num2 = Math.floor((Math.random() * 10) + 1);
let answer = num1 + num2

rl.question(`what is ${num1} + ${num2}? \n`, (userInput) => {
    if(userInput.trim() == answer){// == 回自动转型，判断将数值转为字符串然后比较
        rl.close(); // 如果回答对乐，我们将关闭rl
    }else{
         // 设置提示符
        rl.setPrompt("Incorrect response please try again !\n")
        // 提示
        rl.prompt()
        // 监听line事件，添加一个回调函数
        rl.on("line",(userInput)=>{ // 判断是否正确，不正确就继续提示玩家
            if(userInput.trim()==answer){
                rl.close();
            }else{
                rl.setPrompt(`Your answer of ${userInput} is incorrect \n`);
                rl.prompt()
            }
        })
    }
});

rl.on("close", () => {//监听close事件，如果close说明回答正确
    console.log("Correct!!")
});
```

# 7. 文件系统
文件系统可以让你操作本地的文件和文件目录

如何写出一个文件

```js
const fs = require('fs');

fs.writeFile("example.txt","this is an example",err=>{// 写出的路径和文件名，写出的内容，错误的处理
    if(err){
        console.log(err);
    }else{
        console.log("file written successfully");

    }
})

```

读取一个文件

```js
const fs = require('fs');

fs.writeFile("example.txt","this is an example",err=>{//向指定的文件路径写入第二个参数的内容
    if(err){
        console.log(err);
    }else{
        console.log("file written successfully");
        fs.readFile('example.txt',(err,file)=>{ // 读取一个文件，第一个参数是文件的路径，第二个参数是回调函数
           // 回调函数中的第一个是错误，第二个是读取的文件内容，file是二进制数据 
            if(err){
                console.log(err)
            }else{
                console.log(file) // <Buffer 74 68 69 73 20 69 73 20 61 6e 20 65 78 61 6d 70 6c 65>  Buffer 读入的是一个二进制数据
            }
        })
    }
})

```

有没有什么方法指定读入的文件的按照字符的形式读入，可以的， 我们可以在读入的方法中指定读入的方法是utf-8

```js
const fs = require('fs');

fs.writeFile("example.txt","this is an example",err=>{//向指定的文件路径写入第二个参数的内容
    if(err){
        console.log(err);
    }else{
        console.log("file written successfully");
        fs.readFile('example.txt','utf-8',(err,file)=>{ // 读取一个文件，第一个参数是文件的路径，第二个指定utf-8编码的方式读取，第三个参数是回调函数
            if(err){
                console.log(err)
            }else{
                console.log(file) // this is an example
            }
        })
    }
})


```

其他的一些功能

```js

const fs = require('fs');


// 重命名一个文件

fs.rename("example.txt","example2.txt",err=>{
    if(err) {
        console.log(err);
    }else{
        console.log("success");
    }
});

// 向一个文件的末尾追加新的内容

fs.appendFile('example2.txt','some data being appended',(err)=>{
    if(err){
        console.log(err)
    }else{
        console.log("successfully append")
    }
});

// 删除一个文件

fs.unlink("example2.txt",(err)=>{
    if(err){
        console.log(err);
    }else{
        console.log("successfully delete");
    }
})
```

如何使用fs操作文件目录

```js

// 创建一个目录
fs.mkdir("tutorial",(err)=>{
    if(err){
        console.log(err)
    }else{
        console.log("folder successfully created!")
    }
})

// 删除
fs.rmdir("tutorial",(err)=>{
    if(err){
        console.log(err)
    }else{
        console.log("folder successfully deleted!")
    }
})

// rmdir只能删除空文件夹，如何删除一个带有子文件的文件夹呢，首先获得文件夹下的所有文件，删除所有文件后在删除文件夹

fs.readdir("example",(err,files)=>{
    if(err){
        console.log(err);
    }else{
        for(file in files){
            fs.unlink("./example" + file,(err)=>{
                if(err){
                    console.log(err);
                }else{
                    console.log("success!")
                }
            });
        }
    }
})
```

# 8.写入流和写出流

使用流读入文件可以将一个大文件分为很多小分读取，减少乐加载时间。

```js

const fs = require('fs');

const readStream = fs.ReadStream('./example.txt',"utf8");//创建流
const writeStream = fs.WriteStream('./example2.txt');
readStream.on('data',(chuck)=>{//每次读取数据时候回发送data的回调事件
    writeStream.write(chuck);//写出流，将读入的内容写出到对应的文件，这样的方式能减少内存的使用。
})

// 一种更加简单的写法
readStream.pipe(WriteStream)
```

使用管道和压缩模块实现压缩和解压

```js
const fs = require('fs');
const zlib = require('zlib');
const gzip = zlib.createGzip();


const readStream = fs.ReadStream('./example.txt',"utf8");//创建流
const writeStream = fs.WriteStream('./example2.txt.gz');

// 压缩
readStream.pipe(gzip).pipe(writeStream);

// 解压
const fs = require('fs');
const zlib = require('zlib');
const gunzip = zlib.createGunzip();


const readStream = fs.ReadStream('./example.txt.gz');//创建流
const writeStream = fs.WriteStream('./example2.txt');

readStream.pipe(gunzip).pipe(writeStream);

```

# 9. http
Http模块可以让你创建一个http服务器，

```js
const http = require("http")
const server = http.createServer((req,res)=>{ // 处理请求
    res.write("hello world from node js");//响应
    res.end(); // 结束
});

server.listen('3000'); // 监听端口
```

输入上述的内容之后，你可以在浏览器中输入对于的地址，显示你写的内容。

下面我们来看怎么处理路由

```js
const http = require("http")
const server = http.createServer((req,res)=>{ // 处理请求
    if(req.url === '/'){// 处理/路由
        res.write("hello world from node js web server")
        res.end()
    }else{//如果是其他的路由就另外处理例如/asd 
        res.write('using some other domian')
        res.end()        
    }
});

server.listen('3000'); // 监听端口
```
# 10 托管静态资源
就是你给http服务器发送请求，给你返回对应的文件内容

我们准备了一个static的目录，其中存放了一个html文件，一个exapmle.png的图片，和一个example.json的文件

首先显示如何将html显示在浏览器的页面上
```js
const http = require('http');
const fs = require('fs');

http.createServer((req,res)=>{
    const readStream = fs.createReadStream('./static/index.html');
    res.writeHead(200,{"Content-type":"text/html"}); // 设置返回头，200表示请求成功，返回的内容是html格式
    readStream.pipe(res);// res是输入流，所以可以直接使用pipe
}).on(3000);

```

另外我们来看怎么发送json

```js
const http = require('http');
const fs = require('fs');

http.createServer((req,res)=>{
    const readStream = fs.createReadStream('./static/example.json');
    res.writeHead(200,{"Content-type":"application/json"}); // 设置返回头，200表示请求成功，返回的内容是html格式
    readStream.pipe(res);// res是输入流，所以可以直接使用pipe
}).on(3000);
```

以及图片
```js
const http = require('http');
const fs = require('fs');

http.createServer((req,res)=>{
    const readStream = fs.createReadStream('./static/example.png');
    res.writeHead(200,{"Content-type":"image/png"}); // 设置返回头，200表示请求成功，返回的内容是html格式
    readStream.pipe(res);// res是输入流，所以可以直接使用pipe
}).on(3000);
```

# 11. package.json以及npm的介绍
package.json是一个重要的文件，包含了项目的版本号，名称，等等内容，

使用
- npm init 初始化一个项目
  - 这里面会有很多提示内容
    - 例如 项目的名称
    - 版本
    - 描述
    - 项目的入口文件
    - 测试命令
    - git仓库
    - 关键词
    - 作者
    - 协议

对应一个初始化的项目，我们可以使用npm安装第三方的依赖包

npmjs.com 是一个网站，在这个网站上你可以找到各种软件包，在这里你可以找你需要的包

安装
- npm install 包名字
安装成功之后，你可以在package.json中看到对应的依赖包,文件的目录下回多一个node_modules文件夹，你会发现你的安装包会在该文件夹下

```json
{
  "name": "js",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "lodash": "^4.17.21"
  }
}

```

如何使用安装库

```js
const _ = require('lodash')

let example = _.fill([1,2,3,4,5],"apple",1,4);
console.log(example)
// $ node app.js 
// [ 1, 'apple', 'apple', 'apple', 5 ]
```

删除软件包
- npm uninstall 软件包名称

# 12 语义化

```json

  "dependencies": {
    "lodash": "^4.17.21"
  }
  // major.minor.patch 版本信息
  // patch主要是更新bug
  // minor 次要版本更新，添加一定的新功能，非破坏性更新
  // major 主要版本更新，破坏更新，可能两个版本不兼容
  // ^是指可以接受的版本是4.x.z版本，对minor更新版本和patch不做限制
  
  "dependencies": {
    "lodash": "~4.17.21"
  }
  // ~是指可以接受的版本是4.17.x版本，对patch版本不做限制
  // 如果没有符号
  
  "dependencies": {
    "lodash": "4.17.21"
  }
  //  指定必须是改版本
```

# 13. express 服务器框架
express是一个基于Node的web服务器框架，之前使用http和fs建立一个web服务器，但是express更加强大，更加健壮

npm init --yes 

全选默认配置 通过--yes来设置。

npm install express 安装 express

写一个hello world

```js
const  express = require('express');
const app = express();
 
app.get('/',(req,res)=>{ // 接受/请求
    res.send("Hello world"); // 返回hello world
});

app.listen(3000);


```

# 14.http get请求

传入参数

```js
const  express = require('express');
const app = express();
 
app.get('/',(req,res)=>{ // 接受/请求
    res.send("Hello world"); // 返回hello world
});

app.get('/example',(req,res)=>{ // 添加一个新的路由请求/example
    res.send("hitting example route") // 返回一个字符串
});

// 如何可以获得前端发送的参数

app.get("/example/:name/:age",(req,res)=>{ // 请求路径：http://localhost:3000/example/zhangsan/13
    console.log(req.params) // 获得的内容{ name: 'zhangsan', age: '13' }
    // 
    res.send(req.params.name + " : " + req.params.age); //将获得数据返回回去。
    // query 传入数据 xxx?tutorial=xxxxx
    // 多个参数 xxx?name=xiaoming&age=3
    console.log(req.query)//{ tutorial: 'xxxxx' }
});

app.listen(3000);


```

# 15. 托管静态文件


```js
const express = require('express');
const path = require('path');

const app = express();

app.use("/public",express.static(path.join(__dirname,'static'))); // 有时候我们不想外部知道服务器的文件目录结构，通过这样的方式将static映射到public下

app.get('/',(req,res)=>{
    res.sendFile(path.join(__dirname,'static','index.html'))//返回static下面的index.html文件作为页面
})
app.listen(3000)
```
# 16 处理post请求

 npm install body-parser 我们要处理POST请求需要线安装这个库。

 ```js
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const app = express();

app.use("/public",express.static(path.join(__dirname,'static')));
app.use(bodyParser.urlencoded({extended:false}));//使用中间件

app.get('/',(req,res)=>{
    res.sendFile(path.join(__dirname,'static','index.html'))
})

app.post('/',(req,res)=>{
    // 打印post请求的消息
    console.log(req.body);// 这里可以拿到消息
    //
    res.send("successfully posted data");
})
app.listen(3000)
 ```


# 17 使用body-parser解析json数据

```js
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const app = express();

app.use("/public",express.static(path.join(__dirname,'static')));
app.use(bodyParser.urlencoded({extended:false}));//使用中间件
app.use(bodyParser.json()); // 使用body-parser解析json


app.get('/',(req,res)=>{
    res.sendFile(path.join(__dirname,'static','index.html'))
})

app.post('/',(req,res)=>{
    // 打印post请求的消息
    console.log(req.body);// 这里可以拿到消息
    //
    res.json({success:true}) // 返回一个json
})
app.listen(3000)
```

# 18.用户验证逻辑

使用@hapi/joi 实现验证用户的逻辑

npm install @hapi/joi

```js
const express = require('express');
const path = require('path');
const Joi = require('@hapi/joi');
const bodyParser = require('body-parser');
const app = express();

app.use("/public",express.static(path.join(__dirname,'static')));
app.use(bodyParser.urlencoded({extended:false}));//使用中间件
app.use(bodyParser.json()); // 使用body-parser解析json


app.get('/',(req,res)=>{
    res.sendFile(path.join(__dirname,'static','index.html'))
})

app.post('/',(req,res)=>{
    // 打印post请求的消息
    console.log(req.body);// 这里可以拿到消息
    //
    const schema = Joi.object().keys({ // 使用Joi创建对输入的验证
        email:Joi.string().trim().email().required(),// email 要求字符串无空格，邮箱格式，不能传空
        password:Joi.string().min(5).max(10).required() // passworld，最少5字符，最多10字符，不能穿空
    });
    Joi.validate(req.body,schema,(err,value)=>{ 
        if(err){
            console.log(err);
            res.send("an error has occurred");
        }
        console.log(value);
        res.send("successfully posted data");
    });
    res.json({success:true}) // 返回一个json
})
app.listen(3000)
```

# 19.ESJ 模板
EJS 是一个简单的模板引擎，使用它我们可以在html中嵌入js代码


```js
const express = require('express');
const path = require('path');
const app = express();

app.use("/public",express.static(path.join(__dirname,'static')));
app.set('view engine','ejs');

app.get('/:userQuery',(req,res)=>{ // 传参，将将ejs解析为html然后返回
    res.render('index',{data:{userQuery:req.params.userQuery}});
})

// app.get('/', (req, res) => { // 将将ejs解析为html然后返回
//     res.render('index');
// })

app.listen(3000)
```


# 19 中间件就是函数

之前讲解了一个bodyparser的中间件，现在来看下中间怎么自己写。

```js
const express = require('express');
const app = express();
app.use((req,res,next)=>{ // 中间件会先于路由执行
    console.log(req.url,req.method);
    next(); //必须调用，传递这个请求，不然服务器没有处理完这个请求，也不会有返回信息给客户端，知道超市中断连接
});

app.use('/example',(req,res,next)=>{
    // 只有包含了example的路由回使用该中间件
    next();
})
app.listen(3000)
```

# 20 一般的模块化方法


routes/people.js

```js
const express = require('express');
const route = express.Router();

route.get('/',(req,res)=>{
    res.send("/ bing hit ");
})


route.get('/example',(req,res)=>{
    res.send('/example being hit')
});

module.exports = route;
```

app.js
```js
const express = require('express');
const path = require('path');
const app = express();
app.use('/public',express.static(path.join(__dirname)));
app.set('view engine','ejs')
const people = require('./routes/people')
app.use('/people',people)
app.listen(3000)
```

/people   -> / binging hits
/people/example -> /example being hit