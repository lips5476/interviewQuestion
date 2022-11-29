## 词法作用域
作用域共有两种主要的工作模型——词法作用域和动态作用域，JS采用词法作用域
词法作用域是由什么决定的：变量和函数作用域定义的位置决定的
## 闭包
产生:当一个函数可以记住并访问其所在的词法作用域的时候，就产生闭包
可以理解定义在一个函数内部的函数
作用：避免变量被垃圾回收
      闭包可以在一定程度上减少全局变量污染 
应用：立即执行函数也算是一定意义上的小闭包，防抖和节流
缺点：会造成内存泄漏


## 回流和重绘
   当render树的一部分或全部因为某个或多个元素大小等因素发生改变而需要重建页面的过程，叫做回流
   如颜色背景等不引起页面布局变化，而只要重新渲染的过程叫做重绘
   两者区别:回流必会发生重绘，但重绘可以单独发生

   什么会引起回流：页面初始化 如删除某个节点(dom结构的变化) 宽高或者padding及margin的变化(render树)

   最好尽量使用transform等不影响其他节点的布局方式

## 节流和防抖
   防抖
   function debounce(fn,wait,immediate){
     let timeout;
     const debounced =function(){
       let context = this;
       let args =arguments;
       if(timeout){
         clearTimeout(timeout)
       }
       if(immediate){
         let fnNow = !timeout
         timeout = setTimeout(()=>{
           timeout =null
         },wait)
         if(fnNow){
           fn.apply(context,args)
         }
       }else{
          timeout = setTimeout(()=>{
           timeout = fn.apply(context,args)
          },wait)
       }
       debounced.cancel = function(){
         clearTimeout(timeout)
         timeout = null
       }
       return debounced
     }
   }

节流  分为两种  leading和tralling
      leading 在触发事件时马上进行一次回调执行，并在固定时间内不再进行调用。
          function throttle(fn, wait) {
        let timeout;

        return function () {
            const context = this;
            const args = arguments;
    
            if (!timeout) {
                fn.apply(context, args);
                timeout = setTimeout(function () {
                    timeout = null;
                }, wait);
            }
    
        }
    }
    tralling  在触发事件的一定时间后执行回调函数，期间的事件不会造成任何影响。
      function throttle(fn, wait) {
        let timeout
        return function () {
            const context = this;
            const args = arguments;
            if (!timeout) {
                timeout = setTimeout(function () {
                    fn.apply(context, args);
                    timeout = null;
                }, wait);
            }
        }
    }


结合两者
    function throttle(fn, wait, options) {
        let timeout1;
        let timeout2;
        options = options ? options : {};
        const throttle = function () {
            const context = this;
            const args = arguments;
            if (!timeout1) {
                if(options.leading !== false){
                    fn.apply(context, args);
                }
                timeout1 = setTimeout(function () {                        
                    if(options.trailing !== false){
                        fn.apply(context, args);
                    }
                    if(options.leading !== false){
                        //当leading和trailing都开启的时候 保证尾部调用跟下一次调用之间有一个间隔
                        timeout2 = setTimeout(function() {
                            timeout1 = null;
                        }, wait);
                    }else{
                        timeout1 = null;
                    }
                }, wait);

            }
        }
        throttle.cancel = function(){
            clearTimeout(timeout1);
            clearTimeout(timeout2);
            timeout1 = timeout2 = null;
        }
        
    }


​    
## 深拷贝/浅拷贝
   浅拷贝:  var obj={                           深拷贝:obj={                     
                    a:1,                                  a:1,                  
                    b:[1,2],                              b:[1,2],              
                    c:true,                               c:true,               
                    d:{aa:1,bb:2,cc:[1,2]}                d:{aa:1,bb:2,cc:[1,2]},
                    }                                     e:obj
                                                          }                     
            function nomalCopy(oldObj){          function deepCopy(obj,map=new Map()){
                 newObj={}                             if(map.has(obj)){
                 for(var key in oldObj){                  return obj
                   newObj[key] =oldObj[key]             }
                 }                                     var needObj={}
                 return newObj                         map.set(obj,needObj)
            }                                          for(var key in obj){
                                                           var val =obj[key]
            或者                                            if(val&&typeof val =='object'){ 
                                                                needObj[key] =deepCopy(val,map)
            Object.assign(newObj,oldObj)                    }
                                                            else{
                                                              needObj[key]=val
                                                            }
                                                        }
                                                        return needObj
                                                   }

## 什么是 FOUC 以及 FOUT？如何产生的？如何避免？
Flash Of Unstyled Content
  无样式内容闪铄，指css文件未加载时页面无样式显示
                 使用import 样式表在页面底部书写及引入 有好几个样式表在页面不同位置书写及引入
                 将css嵌入html中（淘宝首页）
                 解决方案：用link标签引入样式表放在header标签里

Flash Of Unstyled Text
  无样式字体闪铄，指字体文件未下载完成时文字不显示或显示默认字体


## 标签语义化
         让代码书写者更容易读懂标签含义(写给人看的)
         更方便做seo优化，可以搜索排名更靠前(写给机器看的)
## 可访问性 role  aria—
## 为什么js是单线程的  
    线程是操作系统能够进行运算调度的最小单位 如果多线程操作某个dom节点浏览器不知道听谁的



## generator生成器如何使用及其各个阶段如何变化
    是个函数 返回迭代器  
    生成器函数调用后不会立即执行 通过next方法控制迭代器一步步执行的
    next方法会返回一个对象 有两个属性 done和value
    next()执行到yield运算符会处于非阻塞挂起状态


​    
​    过程：调用生成器函函数(不执行) 调用next方法开始执行 碰到yield 生成器就会处于非阻塞挂起状态
​          返回一个对象   接着调用next方法就继续执行 直到碰到yield 生成器又会处于非阻塞挂起状态
​          依次类推  直到碰不到yield 这时候返回的对象里的done就为true  value为undefined
​          生成器函数里可以有return  return的值作为最后一个yield执行完后多一个{value:值,done:true}
​    
    next()方法的传参
          当yield运算符执行完后的内容被一个变量接住 next就可以传参 
          var x =yield 2    当第一次next(888)执行到这一句挂起并且传入参数888 
          然后执行下一次next接着执行时，888就会成为 yield 2运算完成后的返回值传给x
    
    生成器函数还有别的方法如：return()  throw()
    当生成器函数里有多个yield 在其中用return(888)调用某个yield执行时  
    他会直接结束余下所有的yield运算  返回value为return参数的值和done为true的对象后不再往下执行了
    
    当通过调用throw('err')接着往下执行yield时 该yield的返回值会变成一个抛错 此时若没有被trycatch接住就会抛出在浏览器  但是并不影响下面的yield接着执行
    
    若在执行某个yield阶段有语法错误  那错误会在下一个next中被抛出
    
    yield 实现一个斐波那契数列
     
    function * fibbs(n){                            function fibbs(n){           
      var a =0;var b =1                               var a =1;b=0
      while(n-->0){                                   return {
        yield b                                            next(){
        b=a+b                                                 if(n-- >0){
        a=b-a                                                    b=a+b
      }                                                          a=b-a
      return                                                     return b
    }                                                         }
                                                           }
                                                       }
                                                     }             
## new 的实现
            function new(constructor,...args){
              var  obj =Object.create(constructor.prototype)
              var  res =constructor.call(obj,...args)
              if(res && typeof res ==='object'){
                return res
              }else{
                return obj
              }
            }

## 箭头函数不能作为构造函数的原因 
          它没有单独的this
          不绑定arguments
          他没有原型和new使用会抛错
          var obj = {
            say: function() {
              var f1 = ()=>{
                  console.log("1111",this);	
              }
              f1();
            }
          }
          var o = obj.say;   o()  this指向window
                            obj.say()   this指向obj

 



## 中间件
      当浏览器请求服务器时，会向服务器传输一些数据，那么中间件就是验证这些请求的内容是否存在或正确的一个程序
      简单来说app.use()传入的任何函数都可以叫做中间件 
      不可能每个请求都写一段验证,所以这些重复验证可以提取出来,由专门的中间件实现
      express.static 快速托管静态资源
      express.json 解析 JSON 格式的请求体数据
      express.urlencoded 解析 URL-encoded 格式的请求体数据
---------------------------------------------9月28日------------------------------------------
##  let  var变量提升  const
   let有四大特性 
            块级作用域
            无变量提升
            暂时性死区
            let不可在同一作用域内重复声明同一个变量
            即使全局let i或者const i  i也不属于全局  要访问到i就直接用 i 用window.i 访问不到
            es5想实现块级作用域可以通过立即执行函数(function(){var a = 1}())
            const定义的普通变量一旦定义不可再修改值 但定义的是复合数据类型则还可以往里修改数据
            let可以在声明时候不给初始值但是const必须有初始值
##  数据类型概念
          Number； String；Boolean；Null；Undefined；Symbol（ES6 新增数据类型）；bigInt
          引用数据类型统称为 Object 类型，细分的话有
          Object；Array；Date；Function；RegExp
          基本数据类型    栈；引用数据类型   堆
          引用类型的存放：   
                在栈中保存数据的引用地址，这个引用地址指向堆内存中的对象。
                栈内存是自动分配内存的。而堆内存是动态分配内存的，不会自动释放。
                所以每次使用完对象都要把它设为 null，从而减少内存的消耗

##  任务队列
    class TaskQueue {                         new TaskQueue()  //这种写法保证不会出现两次next函数重复调用
      constructor() {                               .addTask(next => {
          this.tasks = []                              console.log(1)
          this.running = false                         next()
        }                                              next()
        createNext() {                              .addTask(next => {
         var called = false                           console.log(2)
         return () => {                                next()
          if (called) return                          })
          called = true                              
          if (this.tasks.length) {                   
            var needTask = this.tasks.shift()        
            needTask(this.createNext())       
          } else {                            
            this.running = false              
          }                                   
        }                                     
      }                                       
      addTask(task) {                         
        if (this.running) {                   
          this.task.push(task)                
        } else {                              
          this.running = true                 
          task(this.createNext())             
        }                                     
        return this                           
      }                                       
    }                                         



##  宏任务 微任务  任务队列  事件循环

执行栈 事件队列(任务队列)

事件循环
 主线程-->主线程有异步事件-->该异步事件被挂起等待结果返回-->主线程-->异步事件结果返回要callback将callback加入事件队列等待主线程执行完-->主线程执行完-->查看任务队列的callback并执行


宏任务（macro-task）：
主线程代码（script中的代码），setTimeout，setInterval，setImmediate，requestAnimationFrame，I/O流，UI render（UI页面渲染），ajax请求

微任务（micro-task）：
process.nextTick（node v8机制引擎里有个专门的宏任务去解析）
Promise(准确的说是new Promise().then())
Async/Await(实际就是promise)
MutationObserver(html5新特性)
var mo =new MutationObserver(()=>{
  console.log(1)
})
mo.observe(box,{attributes:true})   //MutationObserver这个函数用来监控某个dom节点及其可选择的后代的变化如果变化了就触发回调函数

异步事件返回结果后执行的callback会被放到事件队列里  但是会按照优先级分为宏任务微任务 ，执行栈空了就先去把微任务队列清空再从宏任务中拿出一个以此类推

同一次事件循环中微任务永远早于宏任务






## node端的事件循环
在node有自己的事件循环模型。事件循环是靠的libuv引擎同时用chrome v8引擎作为js解释器，v8引擎将js代码分析后去调用对应的node api，这些api会执行对应的任务，并把不同的事件放在不同的队列中等待主线程执行

setTimeout()方法是定义一个回调，该回调并不会在我们预期的时间间隔后精准的执行

setImmediate()方法从意义上将是立刻执行的意思，但是实际上它却是poll阶段之后执行回调

setTimeout(() => {
    console.log('timeout');
}, 0);

setImmediate(() => {
    console.log('immediate');
});
答案是不一定

但是在I/O事件的回调中(如fs的回调)，setImmediate方法的回调永远在timer的回调前执行。
nodejs就是通过事件循环来实现非阻塞式的异步IO

事件循环执行的操作顺序 
   ┌───────────────────────────┐
┌─>│           timers          │   这个阶段执行setTimeout和setInterval安排的回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  执行上一轮poll阶段没执行完的IO相关的回调
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │这一阶段等待IO 或者执行IO相关的回调 没执行完的会到下一轮的 pending callbacks 阶段执行  
│  │           poll            │<─────┤  connections, │事件循环可能会在这一阶段阻塞掉(block)  这一阶段会计算最多要阻塞多久
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │执行由setImmediate执行的回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │执行close事件
   └───────────────────────────┘


事件循环每执行完一圈它都会去检查一下还有没有异步执行的IO  检查完就退出了nodejs

libuv实现事件循环还有c语言下的异步操作

如你调用fs.readFile 安排回调 fs调用libuv  libuv调用操作系统  操作系统基于阻塞式
但是在这个阶段阻塞到一定数量的函数他就一定离开进入下一个阶段继续转圈

process.nextTick()不存在某个阶段顺序中，它会在某一阶段的某个函数运行结束之后如果调用了nextTick就执行





## 排序算法
冒泡
    function bubble(arr) {
      for (var j = 0; j < arr.length - 1; j++) {
        for (var i = 0; i < arr.length - 1 - j; i++) {
          if (arr[i] > arr[i + 1]) {
            let a = arr[i]
            let b = arr[i + 1]
            [a, b] = [b, a]
            done = false
          }
        }
        if (done) break
      }
      return arr
    }
选择排序
      function selectsort(arr) {
      for (var i = 0; i < arr.length - 1; i++) {
        var minIndex = i
        for (var j = i + 1; j < arr.length; j++) {
          if (arr[j] < arr[minIndex]) {
            minIndex = j
          }
        }
        var a = arr[i]
        var b = arr[minIndex]
        [a, b] = [b, a]
      }
      return arr
    }
插入排序
    function insertsort(arr) {              [4,5,8,2,1,6,9,3,7]
      for (var i = 0; i < arr.length; i++) {
        var temp = arr[i]
        for (var j = i - 1; j >= 0; j--) {
          if (arr[j] > temp) {
            arr[j + 1] = arr[j]
          } else {
            break
          }
        }
        arr[j + 1] = temp
      }
      return arr
    }
归并排序
     function mergeSort(arr) {
      if (arr.length < 2) {
        return arr
      }
      var mid = arr.length >> 1
      var leftArr = arr.slice(0, mid)
      var rightArr = arr.slice(mid)
      leftArr = mergeSort(leftArr)
      rightArr = mergeSort(rightArr)
      var i = 0, j = 0, k = 0;
      while (i < leftArr.length && j < rightArr.length) {
        if (leftArr[i] < rightArr[j]) {
          arr[k++] = leftArr[i++]
        }
        else {
          arr[k++] = rightArr[j++]
        }
      }
      while (i < leftArr.length) {
        arr[k++] = leftArr[i++]
      }
      while (j < rightArr.length) {
        arr[k++] = rightArr[j++]
      }
      return arr
    }

快速排序
     function qiuckSort(arr) {
      if (arr.length < 2) { return arr }
      var randIdx = Math.floor(Math.random() * arr.length)
      var randItem = arr[randIdx]
      var a = [], b = [], c = []
      for (var i = 0; i < arr.length; i++) {
        if (arr[i] < randItem) {
          a.push(arr[i])
        }
        else if (arr[i] > randItem) {
          b.push(arr[i])
        }
        else {
          c.push(arr[i])
        }
      }
      a = qiuckSort(a); b = qiuckSort(b)
      return a.concat(c, b)
    }


## 判断一个数组是否有序
 functin isSorted(arr){
   for(var i =0;i<arr.length-1;i++){
     if(arr[i]>arr[i+1]){
       return false
     }
   }
   return true
 }


## 数组去重方法
   function unique (arr) {
     return Array.from(new Set(arr))
   }

   function unique(arr){            
        for(var i=0; i<arr.length; i++){
            for(var j=i+1,len=arr.length; j <len; j++){
                if(arr[i]==arr[j]){         //第一个等同于第二个，splice方法删除第二个
                    arr.splice(j,1);
                    j--;
                    len--
                }
            }
        }
    return arr;
   }


   function unique(arr) {
    var array = [];
    for (var i = 0; i < arr.length; i++) {
        if (array .indexOf(arr[i]) === -1) {
            array .push(arr[i])
        }
    }
    return array;
   }
   function unique(arr) {
    var array =[];
    for(var i = 0; i < arr.length; i++) {
            if( !array.includes( arr[i]) ) {//includes 检测数组是否有某个值
                    array.push(arr[i]);
              }
    }
    return array
   } 

   function unique(arr) {
      return arr.filter(function(item, index, arr) {
          return arr.indexOf(item) === index;
      });
   }  

  


## 手写ajax ajax状态码  
    function ajax(option) {
        var ajax = new XMLHttpRequest();
        if (option.type == 'get') {
            ajax.open('get', option.url + '?' + JsonTostring(option.data), true); //
            ajax.send();
        } else if (option.type == 'post') {
            ajax.open('post', option.url, true);
            ajax.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
            ajax.send(JsonTostring(option.data));
        }
        ajax.onreadystatechange = function () { //onreadystatechnage事件可以监听ajax状态码的改变过程
            if (ajax.readyState == 4) {
                if (ajax.status >= 200 && ajax.status < 300 || ajax.status == 304) {
                    option.success(ajax.responseText)
                } else {
                    option.error()
                }
            }
        }
    }
    
    function JsonTostring(json) {
        var arr = [];
        for (var i in json) {
            arr.push(i + "=" + json[i]) //[leo=123,leo1=1234]
        }
        return arr.join("&") //"leo=123&leo1=1234"
    }


    // ajax({
    //     type: 'get',
    //     url: 'http://www.baidu.com',
    //     data: {
    //         'leo': '123',
    //         'buckey': 'hello'
    //     },
    //     success: function (data) {
    //         console.log(data)
    //     },
    //     error: function (err) {
    //         console.log('服务器错误！')
    //     }
    // })
ajax状态值  readyState
    0 - (未初始化)还没有调用send()方法
    1 - (载入)已调用send()方法，正在发送请求
    2 - (载入完成)send()方法执行完成，
    3 - (交互)正在解析响应体
    4 - (完成)响应体解析完成，可以在客户端调用了 
    5 - 问题出在服务器上
ajax状态码  status

---------------------------------------------9月29日-------------------------------------------

## xss  sql注入  csrf  网络安全漏洞 以及解决
xss在前端的  其实在前端对用户输入的东西转义就好了  或者通过csp限制只执行指定的js或者限制js链接哪里  
   在你写的页面的输入框内输入代码内容导致成功运行
   <script> alert</script>    

sql注入的话使用占位符 ?
   在你的账号密码等输入框输入sql语句导致强制成功执行登录
   "select * from users where name  ="+"'foo' or 1=1 or name ='2'"+" and password ="+'乱填'
   1=1就始终成立  黑客就可以随意登录你的网页

csrf  通过cookie的选项samesite让浏览器每次访问服务器不带上cookie
       sameSite 只在我自己的站点请求资源的时候才发送这个cookie 如果是别的页面请求这个站点不发
   你用你的浏览器登录了银行网站，银行网站向银行服务器请数据会下发cookie 
   浏览器记住了cookie  你再登录了黑客的网站  黑客的网站用你的cookie给服务器发送请求
   服务器就误认为此cookie是你本人发的  虽然黑客的网站收不到服务器的响应 但是黑客的网站只需要向服务器发送类似转账的指令就好了
   它不需要服务器给他回应  





##  居中方式
table居中
   td {
      vertical-align: middle;
    }
    td>div {
      height: 200px;
      width: 200px;
      margin: 0 auto;
    }
定位居中
    fatherbox {
      width: 200px;
      height: 200px;
      margin: auto;
      position: relative;
    }
    sonBox {
      width: 100px;
      height: 100px;
      left: 0;
      top: 0;
      right: 0;
      bottom: 0;
      position: absolute;
    }
单行文本居中
    设置line-height和父级高度一样
多行文本居中
     1.    
    ​    fatherbox {
      ​      width: 200px;
      ​      height: 200px;
      ​      line-height: 200px;
      ​      font-size: 0;
    ​    }
    ​    sonBox {
      ​      display: inline-block;
      ​      line-height: normal;
      ​      vertical-align: middle;
      ​      font-size: 16px;
    ​    }
       
flex完美居中
            fatherBox {
                  width: 200px;
                  height: 200px;
                  display: flex;
                  justify-content: center;
                  align-items: center;
            }
            sonBox {
                  width: 100px;
                  height: 100px;
            }
transform 居中
flex 的margin auto自动居中
            fatherBox {
                  height: 200px;
                  width: 200px;
                  display: flex;
            }
            sonBox {
                  height: 100px;
                  width: 100px;
                  margin: auto;
            }



## 为什么 typeof null 是 Object
    因为在 JS 的最初版本中，使用的是 32 位系统，为了性能考虑使用低位存储了变量的类型信息，000 开头代表是对象，然而 null 表示为全零，所以将它错误的判断为 object 
    Object.prototype.toString.call(xx)


## 什么是原型？什么是原型链？如何理解
   原型是一种方法，主要作用是帮助构造函数共享方法
   原型链是实现继承的主要方法
   原理：每个构造函数都有一个prototype属性指向原型对象 prototype本身也是一个对象，这个对象中的属性和方法都可以被构造函数使用  每个构造实例上都有一个__proto__指向其构造函数上的prototype对象
         因此  实例.__proto__===实例的构造函数.prototype
   constructor: 实例.__proto__.constructor===实例的构造函数.prototype.constructor ===实例的构造函数本身
              因此可以通过constructor.name得知其构造函数的名称
  注意  除了null和undefind每个值都有原型
  应用: function isArray(val){
     return object.prototype.toString().call(val)=='[object Array]'
  }


## JS 中的常用的继承方式有哪些？以及各个继承方式的优缺点。
    es6之前利用call继承
    function Father(name,age){
      this.name=name;this.age=age
    }
    function Son(name,age,score){
      Father.call(this,name,score)
      this.score=score
    }
    var son =new Son('李四',18,100)
    console.log(son.age)   //18
    借助原型对象结合call的继承方法
    function Father(){}
    function Son(){}
    Father.prototype.fatherFn =function(){console.log('this is father fn')}
    son.prototype=new Father()
     son.prototype.constructor =Son
    
    var son= new Son()
    console.log(son.fatherFn())
    
    es6的class继承
     class Father{
       constructor(x,y){
         this.x =x
         this.y =y
       }
       sum(){
         console.log(this.x+this.y)
       }
     }
     class Son extends Father{
        constructor(x,y){
        super(x,y)
         this.x =x
         this.y =y
       }
       subStruct(){
         console.log(this.x-this.y)
       }
     }
    
    var son =new Son(5,3) 
     son.sum()  //8  son.subStruct()  //2


​      

## super关键字为何必须在this之前 ·
    super其实是调用了父类的构造函数构造了一个父类实例
    并且把子类的this绑定在这个实例对象上，super调用之前this上还没绑定东西就不可以使用



## 前端页面由哪三层构成
   结构层、表示层、行为层


## 本地存储与 cookie 的区别
    localstorage和sessionStorage的区别
    LoaclStorage 存储是永久的，除非手动清除；然而 SessionStorage 如果浏览器关闭了就消失了。
    本地存储与 cookie 的区别  
    存储大小，本地存储官方说法是 5M 的大小，然而 Cookies 的存储大小更小并且和浏览器本身有关（大概在 4000个字节左右）
Cookie 存储的内容会保留在 HTTP 请求的 Header 中，并且每次请求都会带上；然而本地存储只保留在浏览器，不会发送到服务器

## 浏览器标准模式和怪异模式的区别
  标准模式就是按照w3c标准解析执行代码
  怪异模式就是浏览器根据自己的方式解析执行代码 不同浏览器行为不同所以叫怪异模式



## 区分微任务和宏任务的标准是什么
   是由谁发起的 以及是否会触发新一轮的tick
   宏任务 是由宿主发起的(浏览器或node)
   微任务js引擎发起的

--------------------------------------------10月2日---------------------------------

 ## 手写foreach  reduce map  find  every  filter

function map(arr,mapper){
  var res =[]
  for(var i=0;i<arr.length;i++){
    res.push(mapper(arr[i]))
  }
  return res
}
function reduce(arr,f,initial){
 var startIdx= 0
 if(argumnets.length==2){
   initial =arr[0]
   startIdx =1
 }
 for(var i=startIdx;i<arr.length;i++){
   initial =f(initial,arr[i])
 }
 return initial
}
function foreach(arr,action){
  for(var i=0;i<arr.length;i++){
    action(arr[i])
  }
}

function filter(arr,test){
  var res=[]
  for(var i=0;i<arr.length;i++){
    if(test(arr[i])){
      res.push(arr[i])
    }
  }
  return res
}

function every(arr,test){
    for(var i=0;i<arr.length;i++){
      if(!test(arr[i])){
        return false
      }
    }
    return true
}

## 洗牌算法
function shuffle(arr,size=arr.length){
  var lastIndex =arr.length-1
  for(var i=0;i<size,i++){
    var rand =Math.floor(Math.random()*(lastIndex-i+1))+i
    swap(arr[i],arr[rand])
  }
  arr.length=size
  return arr
}



## 事件冒泡和事件捕捉有什么区别
   都是为了解决dom事件发生顺序的问题
   事件捕获是从祖先元素捕获到目标元素
   事件冒泡是从目标元素冒泡到祖先元素
   顺序是先捕获再冒泡
## mouseover和mouseenter的区别 
   mouseover绑定的事件经过自身的时候会触发一次 经过子盒子也会触发一次，对应mouseout

   mouseenter                     会触发一次            不会触发       mouseleave  
                  (无事件冒泡阶段但有事件捕获阶段)

## 什么是跨域 
   浏览器从一个域名的网页去请求另一个域名的资源时，域名、端口、协议任一不同，都叫做跨域

## 如何实现跨域
   cors
   jsonp  但是缺点在于无法发送post请求所以需要后端配合返回指定格式的数据
   服务器代理  主要原理就是服务器请求服务器无跨域限制  
   iframe    postMessage或者#路由  onmessage事件可以外层窗口和内层窗口信息通讯实现跨域请求



## jsonp
    ajax不允许跨域请求，而 script 标签 src 属性可以跨域的 js 脚本，利用这个特性，服务端不再返回 JSON 格式的数据，而是返回一段调用某个函数的 js 代码，在 src 中进行了调用，这样实现了跨域。
    
    代码实现原理
    function getJson(url,callback){
      var script =document.createElement('script')
      var varName ='jsonp_'+Date.now()+Math.random().toString(16).slice(2)
      window[varName] =callback
      script.src=url+'&callback'+varName
      document.body.append(script)
    }

-----------------------------------------------10月5日-----------------------------------------

## 手写call apply  bind
    Function.prototype.mybind=function(thisObj,...fixedArgs){
      var fn =this
      if(typeof fn !== 'Function'){
        return new TypeError('It is not a function')
      }
      if(!thisObj){
        thisObj=window
      }
      return function(...RestArgs){
        return fn.apply(thisObj,[...fixedArgs,...RestArgs])
      }
    }
    
    Function.prototype.myCall = function(context) {
      // 判断调用对象
      if (typeof this !== "function") {
        console.error("type error");
      }
      // 获取参数
      let args = [...arguments].slice(1),
        result = null;
      // 判断 context 是否传入，如果未传入则设置为 window
      context = context || window;
      // 将调用函数设为对象的方法
      context.fn = this;
      // 调用函数
      result = context.fn(...args);
      // 将属性删除
      delete context.fn;
      return result;
    };


    Function.prototype.myApply = function(context) {
      // 判断调用对象是否为函数
      if (typeof this !== "function") {
        throw new TypeError("Error");
      }
      let result = null;
      // 判断 context 是否存在，如果未传入则为 window
      context = context || window;
      // 将函数设为对象的方法
      context.fn = this;
      // 调用方法
      if (arguments[1]) {
        result = context.fn(...arguments[1]);
      } else {
        result = context.fn();
      }
      // 将属性删除
      delete context.fn;
      return result;
    };








## 数据类型检测的方式有哪些
    typeof
    instanceof
    constructor
    Object.prototype.toString.call()

## typeof和instanceof的区别 
    typeof只能判断原始类型  其他都判断为object
    intanceof只能正确判断引用数据类型
        其原理是测试该对象在其原型链中是否存在一个构造函数的prototype属性

## null和undefined的区别
   undefined 代表的含义是未定义  ，null 代表的含义是空对象
   一般变量声明了但未定义的时候会返回 undefined，null主要用于赋值给一些可能会返回对象的变量，作为初始化。

## 手写instanceof和typeof
      function MyInstanceof(obj,constructor){
         if(typeof obj !=='object') return false
         if(obj.__proto__ ===constructor.prototype){
           return true
         }else{
           return   MyInstanceof(obj.__proto__,constructor)
         }
      }
    
      function MyTypeOf(val){
        if(val ==='null'){
          return 'object'
        }
        if(val ==='undefined'){
          return 'undefined'
        }
        return val.__proto__.constructor.name.toLowerCase()
      }

## 为什么0.1+0.2 ! == 0.3，如何让其相等  
    因为计算机是通过二进制方式存数据的  而有的小数的二进制数是无限循环的数
    而在js里只有一种数字类型 它使用64位固定长度表示  double双精度浮点数
    而在二进制科学计数法里双精度浮点数的小数部分最多只能保留52位 再加上前面的1其实就是保留53位有效数字
    剩余的舍去  遵循0舍1入的规则
       符号位  +  指数位 +  小数位
    这里如果指数是负数 IEEE标准规定了一个偏移量对于指数部分每次都加上这个偏移量这样就可以处理负数的情况了  js是双精度  指数范围是11位  能表示的范围就是0-2047 IEEE固定偏移量为1023  
    
    js提供一个属性设定为标准误差范围 Number.EPSILON属性 它的值是2的-52次方  
    所以只要判断0.1+0.2-0.3<Number.EPSILON就可以判断 0.1+0.2===0.3
##  为什么0.2+0.3===0.5

## 为什么typeof NaN 为number
NaN 指不是一个数字，用于指数字类型中的错误情况，即执行数学运算失败的结果。
##  isNaN 和 Number.isNaN 函数的区别？
    isNaN会将参数转换为数值，不能被转换为数值的都返回 true，非数值传入也会返回 true ，会影响 NaN 的判断。
    Number.isNaN 会先判断传入参数是否为数字，不转换数据类型，如果是继续判断是否为 NaN ，这种方法更为准确。




## 如何判断一个对象是空对象
     Object.keys(Obj).length < 0

## symbol
    代表独一无二且不可变的数据类型  主要解决全局变量冲突的问题

--------------------------------------------10月6日--------------------------------------------


## 箭头函数和普通函数的this指向问题

      箭头函数比普通函数更简洁 
      call()、apply()、bind()等方法不能改变箭头函数中this的指向
      箭头函数不会创建自己的this，它所谓的this是捕获其所在上下⽂的 this 值，作为⾃⼰的 this 值，所以箭头函数中this指向在它在定义时就确定了，并且之后也不会被改变。

## 展开运算符和object.assign
   展开运算符是利用原有的对象上的属性创建出来一个具有相同属性的对象(副本)
   不复制继承的属性或者类的属性但是会复制symbol属性

   Object.assign其实是修改了传入的第一个参数(空对象，旧的对象)  因此它会触发setter方法
   如果事先修改了空对象里的setter方法（Object.defineProperty）那这时候再调用 Object.assign报错


##  对对象与数组的解构的理解
      解构是 ES6 提供的一种新的提取数据的模式，这种模式能够从对象或数组里有针对性地拿到指定的数据。

## 如何提取高度嵌套的对象里的指定属性？
     解构：const school = {
              classes: {
                    stu: {
                      name: 'Bob',
                      age: 24,
                    }
              }
            }
            const { classes: { stu: { name } }} = school


## 对 rest 参数的理解
    扩展运算符被用在函数形参上时，可以把多个的参数序列合成一个数组



## 什么是this
    是执行上下文中的一个属性   指向最后一次调用这个方法的对象

## 什么是执行栈，什么是执行上下文?
   执行上下文就是当前 JavaScript 代码被解析和执行时所在环境
   全局执行上下文   函数执行上下文
   当函数多了就有了多个执行上下文，就需要用栈的结构来管理这些执行上下文 这就是执行栈


## 什么是作用域 
   规定变量和函数的可使用范围称作作用域
   全局作用域  函数作用域  块级作用域


##  什么叫做作用域链
 每个函数都有一个作用域链，查找变量或者函数时，需要从局部作用域到全局作用域依次查找，这些作用域集合称作作用域链

## 面向对象  对象创建的方式有哪些？

     基本模式
        var person = new Object();
          person.name = "孙悟空";
          person.weapon = "棒子";
          person.run = function () { return this.name + "武器是" + person.weapon;
        }
      工厂模式
        function creatPerson(name, weapon) { var person = new Object();
          person.name = "孙悟空";
          person.weapon = "棒子";
          person.run = function () { return this.name + "武器是" + person.weapon;
          } return person;
        }
     构造函数模式
    
     原型模式
    
     原型构造函数组合模式
    
          function personObj(name,weapon) { this.name = name; this.weapon = weapon;}
            personObj.prototype = {
              run: function () { return this.name + "武器是" + this.weapon;}
            } //创建对象
            var wukou = new personObj("孙悟空", "棒子");
     动态原型模式
         function personObj(name, weapon) { this.name = name this.weapon = weapon if (typeof this.run != "function"){
            personObj.prototype = {
              run: function () { return this.name + "武器是" + this.weapon;}
              }
            }
          } 
          //创建对象
          var wukou = new personObj("孙悟空", "棒子");


## js常用的设计模式
    单例，工厂，代理，装饰，观察者模式等
        1) 单例：　任意对象都是单例，无须特别处理
    var obj = {name: 'michaelqin', age: 30};
    
    2) 工厂: 就是同样形式参数返回不同的实例
    function Person() { this.name = 'Person1'; }
    function Animal() { this.name = 'Animal1'; }
    
    function Factory() {}
    Factory.prototype.getInstance = function(className) {
        return eval('new ' + className + '()');
    }
    
    var factory = new Factory();
    var obj1 = factory.getInstance('Person');
    var obj2 = factory.getInstance('Animal');
    console.log(obj1.name); // Person1
    console.log(obj2.name); // Animal1
    
    3) 代理: 就是新建个类调用老类的接口,包一下
    function Person() { }
    Person.prototype.sayName = function() { console.log('michaelqin'); }
    Person.prototype.sayAge = function() { console.log(30); }
    
    function PersonProxy() {
        this.person = new Person();
        var that = this;
        this.callMethod = function(functionName) {
            console.log('before proxy:', functionName);
            that.person[functionName](); // 代理
            console.log('after proxy:', functionName);
        }
    }
    
    var pp = new PersonProxy();
    pp.callMethod('sayName'); // 代理调用Person的方法sayName()
    pp.callMethod('sayAge'); // 代理调用Person的方法sayAge()
    
    4) 观察者: 就是事件模式，比如按钮的onclick这样的应用.
    function Publisher() {
        this.listeners = [];
    }
    Publisher.prototype = {
        'addListener': function(listener) {
            this.listeners.push(listener);
        },
    
        'removeListener': function(listener) {
            delete this.listeners[listener];
        },
    
        'notify': function(obj) {
            for(var i = 0; i < this.listeners.length; i++) {
                var listener = this.listeners[i];
                if (typeof listener !== 'undefined') {
                    listener.process(obj);
                }
            }
        }
    }; // 发布者
    
    function Subscriber() {
    
    }
    Subscriber.prototype = {
        'process': function(obj) {
            console.log(obj);
        }
    };　// 订阅者
    
    var publisher = new Publisher();
    publisher.addListener(new Subscriber());
    publisher.addListener(new Subscriber());
    publisher.notify({name: 'michaelqin', ageo: 30}); // 发布一个对象到所有订阅者
    publisher.notify('2 subscribers will both perform process'); // 发布一个字符串到所有订阅者


​     
## 什么是内存泄漏？ 为什么会导致的内存泄漏 
    内存泄漏指js 无法访问某个对象，而垃圾回收机制却认为该对象还在被引
    用，因此不释放该对象，导致该内存无法释放，积少成多，系统会崩溃

## 垃圾回收
  Js代码运行时，需要内存空间来储存变量和值。当变量不参与运行时，系统要收回被占用的内存空间，这就是垃圾回收。
## 垃圾回收的方法    
    标记清除法
   垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后。垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。
    引用计数法
   另外一种垃圾回收机制就是引用计数，这个用的相对较少。引用计数就是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减1。当这个引用次数变为0时，说明这个变量已经没有价值，因此，在在机回收期下次再运行时，这个变量所占有的内存空间就会被释放出来。

## 浏览器内存泄漏场景
   闭包使用不当
   意外创建的全局变量
   被遗忘的计时器和回调函数
   脱离dom的引用


## 内存泄漏解决方案
   数组的优化  尽量使用arr.length=0
   对象尽量复用 不用的设置为null 
   循环中的函数表达式可以复用的话尽量放在函数的外面

-----------------------------------------------10月7日-----------------------------------------





##  map和objec的区别
    map的键可以是任意值  object必须是string或者symbol
    map的key是有序的 object是无序的
    map的键的个数可以直接size拿到 object只能手动计算
    map可以直接被迭代  object不行


## weakmap
   也是键值对的集合  键是弱引用且必须是对象不可以是原始类型  值可以是任意类型
   weakmap的作用: 当你想在普通对象上存放数据但是会形成对这个对象的引用一旦不用这个对象
   就必须手动删除否则垃圾回收不会释放该内存
   由于weakmap键名都是弱引用 一旦不需要里面键名对象和键值会自动消失不用手动删除


## 什么是json
  是基于文本的轻量级数据交换格式 它可以被任何语言读取传递
  前端通过将一个符合json格式的数据结构序列化为json字符串  后端将该json格式的字符串反序列化解析后生成
  对应的数据结构 以此实现前后端的数据传递

## js脚本延迟加载的方式有哪些
   defer属性 会让脚本加载和文同步解析 然后在文档解析完再执行脚本
   async属性  会使脚本异步加载
   动态创建script标签
   setTimeout延迟
   将js脚本放在文档底部

## 什么是类数组对象  如何转化为数组
一个拥有 length 属性和若干索引属性的对象就可以被称为类数组对象
Array.from(arrayLike)
Array.prototype.slice.call(arrayLike);
## Unicode
   unicode 万国码 为每种语言的每个字符设定了唯一的二进制编码
   它的实现(编码)方式有很多种 UTF-8、UTF-16、UTF-32



##   为什么函数的 arguments 参数是类数组而不是数组？如何遍历类数组?
   它是一个对象  与数组类似但没有数组常见的方法和属性
  Array.prototype.forEach.call(arguments, a => console.log(a))
  Array.from(arguments) 
  展开运算符


##  什么是dom和bom
    dom文档对象模型  定义处理网页内容的方法和接口
    bom  浏览器对象模型 定义了与浏览器交互的方法和接口

## 什么是变量提升
   变量提升的表现是，无论在函数中何处位置声明的变量，都被提升到了函数的首部
   可以提高性能  容错性更好

## 什么是尾调用
   尾调用指的是函数的最后一步调用另一个函数
   作用 在最后一步调用的话可以不用保留当前的执行上下文


## 严格模式作用和特点
   是es5新加的一种js运行模式
   作用  消除js不合理之处  减少怪异行为
         保证代码安全
         提高编译效率
    特点
    禁止使用with
    禁止this指向全局对象window
    对象不能重名




## ajax和fetch和axios的区别
ajax是一种在无需重新加载整个网页的情况下，更新部分网页的技术。通过与服务器进行数据交换，它能使网页异步更新。
fetch是基于promise没有使用xmlhttprequest对象的ajax的替代品
axios是一种基于promise封装的一种http客户端


## js如何实现异步编程  
     回调函数  promise generator  async


-------------------------------------------10月8日---------------------------------------------



## 什么是sourcemap(暂时只有chrome支持)
   是前端独有的独立的map文件与源码在同一目录下
   生产环境中大部分源码需要经过转换打包
      过程可能会将代码压缩或者多文件合并以及用别的语言编译成js
    这样debugger会很困难
    sourcemap是信息文件  里面是json数据  存储代码位置及命名等各种信息
    这样一旦运行出错编译器可以直接显示源代码出错的位置



## 并发与并行
  并发是宏观概念   多个任务在一段时间内通过任务间的切换被完成了  叫做并发
  并行是微观概念   同时完成多个任务的情况叫做并行

##  回调地狱  
   多个嵌套函数之间存在高度依赖和耦合性不利于阅读和维护


## requestAnimation
    var step = 0;
    //回调函数
    function render() {  
      step += 1; //修改图像的位置  
      if (step < 100) {  //在动画没有结束前，递归渲染    
       requestAnimationFrame(render); 
      }
    }
    //第一帧渲染
    requestAnimationFrame(render);

## 继承过来的getter
   getter是可被枚举且是自由属性可以被hasOwnproperty访问到
   但是如果是继承过来的getter并不是自由属性 就访问不到
    

  

## js实现一个sleep函数
function sleep(delay) {
  var start = (new Date()).getTime();
  while ((new Date()).getTime() - start < delay) {
    continue;
  }
}



## 函数柯里化原理

    简化代码结构，提高维护性，一个方法，只有一个参数，强制了功能的单一性
    降低代码的重复


    // curry函数的实现
    function curry(f, n = f.length) {
      return function curried(...args) {
        if (args.length < n) {
          return curry(f.bind(null, ...args), n - args.length)
        } else {
          return f(...args)
        }
      }
    }




## 实现一个斐波那契数列  递归 循环
    function fibb(n){
        if(n < 0) throw new Error('输入的数字不能小于0');
        if(n==1 || n==2){
            return 1;
        }else{
            return fibb(n-1) + fibb (n-2);
        }
    }
    
    function fibb(n){
        var a = [0,1,1];
        if(n < 0) throw new Error('输入的数字不能小于0');
        if(n >= 3){
            for(var i=3;i<=n;i++){
                a[i] = a[i-1]+a[i-2];
            }
        }
        return a[n];
    }
    
    function fibb(n){
        var pre = 0;//表示前一个值
        var cur = 1;//表示后一个值
        var data;//表示当前值
        if(n < 0) throw new Error('请输入大于0的值！');
        if(n == 0) return 0;
        if(n == 1) return 1;
        if(n > 2){
            for(var i=2;i<=n;i++){
                data = pre + cur;
                pre = cur;
                cur = data;
            }
        }
        return data;
    }





## 模块化
是一种将js代码程序员拆分为可按需导入的模块机制，它可以使每个文件有私有命名空间，避免全局变量污染提高代码可复用性

commonjs                                es6 module
支持node                                支持web和node
运行时加载                               编译时输出加载接口
导出的是值的拷贝且可修改                  导出的值是引用地址且只读不可修改
require是同步加载模块                    import为异步加载 有一个独立的模块依赖解析阶段
会缓存导入的模块                         不缓存




## es6 module

可以有多个导出但只能有一个默认导出
(默认导出没那么复杂 简单理解就是引入的时候可以引入的名称可以和导出的名称不一样，但是不是默认导出的东西引入时必须和导出时候一致)
自动采用严格模式

import存在提升行为  会提升到整个模块顶部首先执行  导入的所有东西都是只读的  编译时导入


if(true){
    import  xxx  from  "xxx"
}  报错  因为提升  所以不会去分析执行if

const  path =  "a"  +  pathName
import   fn  from  path   
编译时导入   所以编译时import  的path无效

import()语法与import区别是前者为动态导入且返回一个promise后者为静态导入

import("xxx.js")
.then(({a,fn})=>{}).catch(err=>{})

也支持await  和  promise

const  aaa  =   await  import("xxx.js")
const {a,fn}  =   await  import("xxx.js")

const  [a,b,fn] = promise.all([
        import("xxx.js")
        import("xxx.js")
        import("xxx.js")
])



## CommonJS   
会将导入过的模块缓存起来 node端使用

## CommonJS加载原理
一个模块对应一个脚本文件  require每加载一个模块会执行整个脚本生成一个对象  此后每次执行相同require就会去对象缓存中取值  所以CommonJs模块无论require多少次都只会在第一次加载时运行一次  此后都是加载第一次返回的结果  除非手动清除缓存

exports.a ="hello"

const  a ="hello"
module.exports ={
     a,
}

require("fs")
require("xxx.js")


模块的分类
内置模块(核心模块)
nodejs源码编译时被编译成二进制，nodejs启动时直接被加载进内存


文件模块(用户自己写的模块)
分为以路径导入模块和自定义模块

路径导入模块
         由于提供了确切路径，引入时require会把其变成硬盘上真实路径，并将这个路径作为索引将编译后的结果缓存起来，所以速度＞自定义模块


自定义模块
路径分析
        查找node_module目录
        查找父级目录node_module
        查找根目录下的node_module
路径分析后若无扩展名则利用特定规则进行文件定位  按照.js  .node  .json

规则
        先在命中的文件夹目录下寻找package.json  并进行JSON.parse得到mian属性的值作为命中文件
若无   则找这个目录下的index文件  后缀名依旧是.js  .node  .json的顺序
若仍无   以同样规则去文件定位上一级目录






import加载CommonJS模块
正常CommonJS输出和export default是等同的，但是CommonJS是运行时加载所以在用import加载CommonJS模块只能用整体输入*
import  * as xxx from  "xxx"

require加载es6 module
用require加载模块时  所有加载进来的内容都变成输入对对象的属性

export default let  foo = {a:"xxx"}

const needFoo = require("xxx")
console.log(needFoo.default)
//   {a:"xxx"}



前端工程化



前端如何实现数据加密   https://juejin.cn/post/7011306453373812744