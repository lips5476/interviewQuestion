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
##  yield 实现一个斐波那契数列
     
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



## jsonp
    function getJson(url,callback){
      var script =document.createElement('script')
      var varName ='jsonp_'+Date.now()+Math.random().toString(16).slice(2)
      window[varName] =callback
      script.src=url+'&callback'+varName
      document.body.append(script)
    }
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
