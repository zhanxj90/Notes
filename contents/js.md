# JavaScript

## es6
  > es6通常是指es2017，但是2018-2022新增内容没有划分出es新版本，所以这些也归类至es6里
### *es6数组方法*
  1. push
  2. pop
  3. shift
  4. unshift
  5. splice 
  6. split
  7. map:更新数组
  8. forEach() -----循环
  9. filter()、
  10. includes()、
  11. find()、
  12. findIndex()— —筛选（删除）数组
  13. some()、
  14. every()— —判断数组
  15. reduce()— —叠加数组
### *rest 参数*
  1. ES6中引入rest参数(形式为'...变量名')，用于获取函数的多余参数
  2. rest参数是真正意义上的数组，可以使用数组的任何方法
  3. 对于函数的length属性，不包含rest
  4. rest参数之后不能再有其他参数(即只能是最后一个参数)。在它之后不能存在任何其他的参数，否则会报错
  ```js
    function fn(a,b,...rest) {
        console.log(rest) //[ 3, 4, 5 ]
    }
    fn(1,2,3,4,5)
    console.log(fn.length)  //2
  ```

-----

## promise
### *promise释义*
  > Promise 是异步编程的一种解决方案，其实是一个构造函数，自己身上有all、reject、resolve这几个方法，原型上有then、catch等方法。
  1. Promise对象的状态不受外界影响,有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
  2. Promise一旦状态改变，就不会再变，任何时候都可以得到这个结果。
  3. 解决了回调地狱（异步方法中callback函数嵌套其他异步，一层一层的）的问题
### *成功回调（resolve,then）*
  1. 
### *失败回调（reject,catch）*
  1. 
### *all的使用*
  1. all方法是在Promise类上的，通过Promise.all使用。all接收一个数组参数(参数不一定是要promise，有返回就有值),数组全执行完才返回，有一个错了就走catch，全对才走then
### *race的使用*
  1. race方法是在Promise类上的，通过Promise.race使用。也是接收数组（同all），数组有个执行完了立马返回，对就then，错就catch。
### *手写Promise*
  ```js
    // 先定义三个常量表示状态
    const PENDING = "pending";
    const FULFILLED = "fulfilled";
    const REJECTED = "rejected";

    // 新建 MyPromise 类
    class MyPromise {
      constructor(executor) {
        // executor 是一个执行器，进入会立即执行
        // 并传入resolve和reject方法
        try {
          // 要绑定this，防止函数调用时this指向改变
          executor(this.resolve.bind(this), this.reject.bind(this));
        } catch (error) {
          this.reject(error);
        }
      }

      // 储存状态的变量，初始值是 pending
      status = PENDING;
      // 成功之后的值
      value = null;
      // 失败之后的原因
      reason = null;

      // 存储成功回调函数
      onFulfilledCallbacks = [];
      // 存储失败回调函数
      onRejectedCallbacks = [];

      // 更改成功后的状态
      resolve = (value) => {
        // 只有状态是等待，才执行状态修改
        if (this.status === PENDING) {
          // 状态修改为成功
          this.status = FULFILLED;
          // 保存成功之后的值
          this.value = value;
          // resolve里面将所有成功的回调拿出来执行
          while (this.onFulfilledCallbacks.length) {
            // Array.shift() 取出数组第一个元素，然后（）调用，shift不是纯函数，取出后，数组将失去该元素，直到数组为空
            this.onFulfilledCallbacks.shift()(value);
          }
        }
      };

      // 更改失败后的状态
      reject = (reason) => {
        // 只有状态是等待，才执行状态修改
        if (this.status === PENDING) {
          // 状态成功为失败
          this.status = REJECTED;
          // 保存失败后的原因
          this.reason = reason;
          // resolve里面将所有失败的回调拿出来执行
          while (this.onRejectedCallbacks.length) {
            this.onRejectedCallbacks.shift()(reason);
          }
        }
      };

      then(onFulfilled, onRejected) {
        const realOnFulfilled =
          typeof onFulfilled === "function" ? onFulfilled : (value) => value;
        const realOnRejected =
          typeof onRejected === "function"
            ? onRejected
            : (reason) => {
                throw reason;
              };

        // 为了链式调用这里直接创建一个 MyPromise，并在后面 return 出去
        const promise2 = new MyPromise((resolve, reject) => {
          const fulfilledMicrotask = () => {
            // 创建一个微任务等待 promise2 完成初始化
            queueMicrotask(() => {
              try {
                // 获取成功回调函数的执行结果
                const x = realOnFulfilled(this.value);
                // 传入 resolvePromise 集中处理
                resolvePromise(promise2, x, resolve, reject);
              } catch (error) {
                reject(error);
              }
            });
          };

          const rejectedMicrotask = () => {
            // 创建一个微任务等待 promise2 完成初始化
            queueMicrotask(() => {
              try {
                // 调用失败回调，并且把原因返回
                const x = realOnRejected(this.reason);
                // 传入 resolvePromise 集中处理
                resolvePromise(promise2, x, resolve, reject);
              } catch (error) {
                reject(error);
              }
            });
          };
          // 判断状态
          if (this.status === FULFILLED) {
            fulfilledMicrotask();
          } else if (this.status === REJECTED) {
            rejectedMicrotask();
          } else if (this.status === PENDING) {
            // 等待
            // 因为不知道后面状态的变化情况，所以将成功回调和失败回调存储起来
            // 等到执行成功失败函数的时候再传递
            this.onFulfilledCallbacks.push(fulfilledMicrotask);
            this.onRejectedCallbacks.push(rejectedMicrotask);
          }
        });

        return promise2;
      }

      // resolve 静态方法(直接调用Promise.resolve)
      static resolve(parameter) {
        // 如果传入 MyPromise 就直接返回
        if (parameter instanceof MyPromise) {
          return parameter;
        }

        // 转成常规方式
        return new MyPromise((resolve) => {
          resolve(parameter);
        });
      }

      // reject 静态方法(直接调用Promise.reject)
      static reject(reason) {
        return new MyPromise((resolve, reject) => {
          reject(reason);
        });
      }
    }

    function resolvePromise(promise2, x, resolve, reject) {
      // 如果相等了，说明return的是自己，抛出类型错误并返回
      if (promise2 === x) {
        return reject(
          new TypeError("Chaining cycle detected for promise #<Promise>")
        );
      }
      // 判断x是不是 MyPromise 实例对象
      if (x instanceof MyPromise) {
        // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
        // x.then(value => resolve(value), reason => reject(reason))
        // 简化之后
        x.then(resolve, reject);
      } else {
        // 普通值
        resolve(x);
      }
    }

    module.exports = MyPromise;
  ```

-----

## 隐式转换
### *数字运算*
  1. +：将表达式的值转换为String（仅当有一个是String类型）,否则转换成Number
  2. －：将表达式的值转换为Number
  3. *：将表达式的值转换为Number
  4. /：将表达式的值转换为Number
### *比较运算符*
  1. 全等和不全等——仅比较而不转换（===）;类型不同即返回false
  2. 相等和不相等——先转换再比较（==）
      - 如果操作数有运算符处理（比如自增，加减，取反等），可能要根据运算符优先级，先把操作数的前置运算都做完先（如：a==!b,要先!b取反）
      - 特殊比较：不能将null 和 undefined 转换成其他任何值(null==0和null==1都是false),null 和undefined 是相等的
      - 如果有一个操作数是NaN，则相等操作符返回 false ，而不相等操作符返回 true。即使两个操作数都是NaN，相等操作符也返回 false；因为按照规则， NaN 不等于 NaN
      - 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false转换为0，而true转换为1
      - 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值
      - 如果一个操作数是对象，另一个操作数是基本类型，则调用toPrimitive把对象转换成基本类型，用得到的基本类型值按照前面的规则进行比较
      - 如果两个操作数都是对象，则比较它们是不是同一个对象，如果两个操作数都指向同一个对象，则相等操作符返回 true；否则， 返回false
      - 如果是字符串，就通过 unicode 字符索引来比较 
      - 比较运算图示：![](/assets/比较运算符.png)
### *toPrimitive*
  > toPrimitive(input,preferedType?),内部函数,将对象转换成原始值;input是输入的值，preferedType是期望转换的类型(字符串或数字)
  1. preferedType可省略，日期会被认为是字符串，而其他的值会被当做Number
  2. 如果转换的类型是number:
      - input是原始值，直接返回这个值
      - 否则，如果input是对象，调用input.valueOf()，如果结果是原始值，返回结果
      - 否则，调用input.toString()。如果结果是原始值，返回结果
      - 否则，抛出错误
  3. 如果转换的类型是String:上述步骤2，3交换；先执行toString()，后valueOf()

-----

## 作用域
### *全局作用域*
  1. 所有 window 对象的属性拥有全局作用域，全局作用域会污染全局命名空间，引起起名冲突所以 jquery 等库会把所有代码放在自执行函数中，防止变量泄漏污染
### *函数作用域*
  1. 函数作用域是指声明在函数内部的变量，和全局作用域相反，局部作用域一般只在固定的代码片段内部可访问

-----

## 求余和求模
  > 求余和求模都是计算机编程中的一种获取余数的方法，差别在于具体取整的不同（主要是负数），js中只有求余
### *求余(rem)*
  1. js中使用 % 求余
  2. x对y求余：x rem y = x - fix(x/y) * y
  3. 例：5 % 2 = 5 - fix(5/2) * 2 = 5 - 2 * 2 = 1
  4. fix：是向0取整，除法运算的得数，大于0的去除小数，小于0的加一去除小数；如：fix(5/2)=fix(2.5)=2，fix(-5/2)=fix(-2.5)=-2
### *求模(mod)*
  1. js中没有自带的求模操作，需要则要自己编写函数
  2. x对y求模：x mod y = x - floor(x/y) * y
  3. 例：5 % 2 = 5 - floor(5/2) * 2 = 5 - 2 * 2 = 1
  4. floor：是向下取整，除法运算的得数，大于0的去除小数，小于0的减一去除小数；如：floor(5/2)=floor(2.5)=2，floor(-5/2)=floor(-2.5)=-3

-----

## 进制转换
### *十进制转二进制*
  1. 整数转换
      - 十进制整数转换为二进制整数采用"除2取余，逆序排列"法
      - 具体操作：用2整除十进制整数,得余数和商，接着继续用2整除商，直到余数小于2;把先得到的余数作为个位，依次排列
  2. 小数转换
      - 十进制小数转换成二进制小数采用"乘2取整，顺序排列"法
      - 具体操作：用2乘十进制小数，得积，积的整数取出,小数部分继续乘以2，直到积中的小数部分为零或者达到所要求的精度为止；把先取出的整数部分作为最高位，其他按顺序排列起来。

-----

## DOM API
### *queueMicrotask*
  ```js
    // 手写Promise中有使用这个，不知道源码是不是也是用的这个
    queueMicrotask(() => {/* ... */});
  ```
  1. Window 或 Worker 接口的 queueMicrotask() 方法，将微任务加入队列以在控制返回浏览器的事件循环之前的安全时间执行
  2. 参数：回调函数,当浏览器引擎确定可以安全调用你的代码时执行的 function
  3. 微任务（microtask）的执行顺序在所有进行中的任务（pending task）完成之后，在对浏览器的事件循环产生控制（yielding control to the browser's event loop）之前
  4. 返回值:无
### *IntersectionObserver*
  ```js
    // 建立观察者
    let observer = new IntersectionObserver(
      (entries) => {
          entries.forEach((entry) => {
              observer.unobserve(target)
          })
      },
      // 观察监听配置
      { 
        // root: null, // 观察对象参照物，默认null，窗口视图
        threshold: 1 // 和参照物的重叠范围，0-1
      }
    )
    // 观察-可观察多个对象
    observer.observe(dom)
    // observer.observe(dom1)
  ```
  1. 异步观察目标元素与其祖先元素或顶级文档视口（viewport）交叉状态的方法。
  2. entries:观察的对象中符合重叠条件的对象数组
  3. observer.observe(dom):观察者添加观察对象，可以同时观察多个
  4. 可用于替代列表中加载更多操作，消耗会比监听滚动事件小
### *visibilitychange*
  1. 当其选项卡的内容变得可见或被隐藏时，会在文档上触发(切换标签页会触发)

-----

## new操作符
## *具体流程*
  1. 新生成了一个对象；
  2. 链接到原型；
  3. 绑定 this；
  4. 返回新对象
## *重写new方法*
  ```js
    function myNew(){
      // 创建一个空的对象
      let obj = new Object()
      // 获得构造函数
      let Con =[].shift.call(arguments)
      // 链接到原型
      obj.__proto__ = Con.prototype
      // 绑定 this，执行构造函数
      let result = Con.apply(obj, arguments)
      // 确保 new 出来的是个对象
      return typeof result ==='object'? result : obj
    }
  ```

## 原型和原型链
  1. 所有对象都是通过```nwe 函数```创建的，字面量对象也是通过内部调用new实现的
      ```js
      var obj = {} // obj = new Object()
      var arr = [] // arr = new Array()
      ```
  2. 所有函数也都是对象，函数中可以有属性
  3. 所有对象都是引用类型
  4. 实例没有prototype属性，只有__proto__。构造函数两者都有
### *原型 prototype*
  1. 所有函数都有一个属性：prototype，称之为函数原型（只有函数有，对象字面量及数组字面量没有）
  2. 默认情况下，prototype是一个普通的object对象，
  3. 默认情况下，prototype有个属性constructor，也是个对象，指向构造函数本身
      ```js
      var obj = {}
      console.log(obj.prototype) // undefined
      var fun = function(){}
      console.log(fun.prototype) // {constructor: ƒ}
      console.log(fun.prototype.constructor === fun) // true
      ```
### *隐式原型*
  1. 所有对象都有一个属性：`__proto__`，称之为隐式原型（函数、数组也是对象，所以他们也有）
  2. 隐式原型是系统内部变量，不要轻易访问（__开头的都是系统变量）
  3. 默认情况下，隐式原型指向创建该对象的函数的原型（构造函数如果返回了对象，则指向的是该返回的原型，跟new原理有关）
      ```js
      var obj = {}
      // true 字面量是调用new Object
      console.log(obj.__proto__ === Object.prototype)
      
      var fun1 = function(){}
      var obj1 = new fun1()
      // true 构造函数fun未设置返回，默认返回其实是fun本身
      console.log(obj1.__proto__ === fun1.prototype)

      var fun2 = function(){
        return {} // ==> return new Object()
      }
      var obj2 = new fun2()
      // 因fun2有返回，所以obj2其实是返回对象的实例，不是fun2本身；var obj2 = new fun2() ==> var obj2 = new Object()
      // false
      console.log(obj2.__proto__ === fun2.prototype)
      // true
      console.log(obj2.__proto__ === Object.prototype)
      ```
## *原型链*
  1. 对象属性成员的查找顺序：
      - 看该对象自身是否拥有该成员，如果有直接使用
      - 否则看该对象的隐式原型中是否拥有，如果有则使用
      - 否则继续向上层隐式原型中查找，直到找到null
  2. 这种逐级向上查询的链式调用就称之为原型链,原型链图示![](/assets/1684748150729.jpg)
  3. 特殊点：
      - Function的__proto__指向自身的prototype
        ```js
        // Function是系统内置函数对象
        Function.__proto__===Function.prototype
        ```
      - Object的__proto__指向Function.prototype（和Object.prototype不是同一个）
        ```js
        // Object是函数对象，Object.__proto__指向的是创建Object的函数的原型，创建Object的函数是Function
        Object.__proto__===Function.prototype
        ```
      - Object的prototype的__proto__指向null

-----


git命令操作

vue路由模式

路由跳转底层原理

不同路由模式的区别

说说做过最难的一个项目

vue hash路由跳转的h5 api

聊聊你在公司的角色定位

基础建设的推广方案，为什么要这么做

接下来技术方面的职业规划

了解的前沿最新的技术是什么

微前端 https://baijiahao.baidu.com/s?id=1700072018226731987&wfr=spider&for=pc


聊一聊pc端桌面应用开发

webpack5新特性了解那些，问的是你项目中使用的
https://www.jianshu.com/p/dfd794119922
https://zhuanlan.zhihu.com/p/348612482

webpack做过哪些优化
https://blog.csdn.net/qq_44722915/article/details/109101979
https://blog.csdn.net/weixin_33709590/article/details/91377763

项目做过那些优化
https://blog.csdn.net/LuckXinXin/article/details/106853981

前端存储有哪些，区别
https://blog.csdn.net/Meraln/article/details/114025508
https://blog.csdn.net/LuckyWinty/article/details/103462293

gulp跟webpack的区别
https://www.cnblogs.com/lovesong/p/6413546.html

webpack做过那些配置
https://www.cnblogs.com/jinzhou/p/9269854.html
https://cli.vuejs.org/zh/config/#pages

说一说错误监控
我们公司只有阿里监控，知道用户操作流程的就自己跑一遍看看，不知道就看下阿里监控有没有接口报错,然后通过神策埋点看看用户操作流程
微信端线上环境添加vconsole入口

写过什么nginx代码

公司部署的流程

讲一讲你服务端渲染的项目，项目实现流程

对你的两家公司的经历仔细说说

自我介绍

html2canvas都遇到过什么问题，如果解决跨域问题
服务器地址的图片跨域问题，转成base64；
有滚动有白屏，设置顶部滚动为0

错误日志跨域问题如何解决
用gif传输日志？

你进行后端开发用的什么方技术

公司日志埋点是如何实现的

为什么要离职

vue源码你有了解那些
nextstick,watch,Keep-alive

Keep-alive的实现原理
https://www.jianshu.com/p/9523bb439950
    - abstract属性会使Keep-alive标签被忽略
    - 再次活跃时vnode.componentInstance的值和keepAlive的值是true，会调用insert(parentElm, vnode.elm, refElm)，把缓存放入父元素里
    - mount中判断keepAlive的值是true就不会在执行下次了

响应式原理
Object.defineProperty
vue3里面是改用ES6里面的特性proxy来实现

nextstick实现
    - 能监听到DOM改动的API好像只有MutationObserver
    - 队列控制的最佳选择是microtask(Promsie,MutationObserver),不行就降级为macrotask：setImmediate、MessageChannel、setTimeout.

vue3有那些新功能
https://blog.csdn.net/tanglitong/article/details/102902940

vue3对composition Api的支持
https://www.jianshu.com/p/cd7e9a549d47

说一说对vite的理解
https://zhuanlan.zhihu.com/p/310670435
https://zhuanlan.zhihu.com/p/467325485

webpack打包的原理是什么
https://blog.csdn.net/weixin_42098339/article/details/103286444
https://www.jianshu.com/p/e24ed38d89fd
- 出口文件打包成立即执行函数，里面定义了require方法
- 其他文件打包成方法，相关连依赖也用require方法调用的

你写过webpack插件么
https://zhuanlan.zhihu.com/p/94577244
    - 插件就是个class类
    - 定义了apply方法，会在引入时触发
    - apply方法会在compiler暴露出来的hook钩子上执行
    ``` js
    apply(compiler){
      compiler.hooks.beforeRun.tap(
        'testPlugin',//插件名
        (comp) => {   
            // ... 
        }
      );
    }
    ```

那loader插件呢
  - 每个loader都是向外暴露一个函数
  - 解析时函数会接收到webpack传入的文件文本 字符串code
  - 函数内部可以自定义处理字符串的过程
  - this.callback(错误信息，处理后的字符串，source-map)，如果不需要其他信息只传回处理结果可以用return code
  ``` js
  // 只是简单的处理，打包成npm包还要其他操作吧
  module.exports = function(code) {
    // 获取options配置项
    const options = loaderUtils.getOptions(this)
    // 自己要做的处理
    code = code.replace(/log/g, 'console.log')
    let error = null
    this.callback(error, code)
  }
  ```
https://blog.csdn.net/weixin_30839881/article/details/100090500
https://www.jianshu.com/p/3a9b2e42a142

你对webpack做那些优化

讲一讲前端的工程化
https://www.jianshu.com/p/88ed70476adb
    - 规范化:样式规范，代码规范，命名规范
    - 模块化：文件拆分
    - 组件化：ui拆分
    - 自动化

如何实现前端监控
    https://blog.csdn.net/weixin_34337265/article/details/91366694
    - window.onerror监听错误,跨域js监听不到，加跨域头
    - Vue.config.errorHandler处理框架报错
    - window.performance 有浏览器相关时间的api
    - 监听hash change 变化上报pv

    - 页面加载时间
    - 统计用户使用设备
    - 错误量的统计

如何实现一个通用的组件呢

你有推动过一些技术落地么

你在项目有使用nestJs,讲一下这个框架吧

你对koa和express这两个框架的实现原理有了解么
https://zhuanlan.zhihu.com/p/342504064
https://www.jianshu.com/p/feff9c4b6327
node中间件洋葱圈模式，先顺序执行next前的，后倒序执行next后的
koa，使用async/await，严格洋葱圈
express，用callback，不严格，异步的后执行

讲一下你之前对redis如何做的用户信息的存储的

mongose的一些操作

node的核心模块有哪些
    - http：搭建服务，监听端口
    - fs:文件浏览，文件处理
    - url:地址处理
    - path：路径处理

你在项目中遇到了那些难点
护眼功能标签页之间的数据互通，

node在实际中运用的场景有哪些
云函数，接口中间数据处理

看过那些书
红宝石

你写的博客怎么实现自动化部署的？

promise原理
vue优化
  - 代码模块化
  - for循环设置key值
  - 使用keep-alive
  - Vue路由设置成懒加载
  - 按需引入
  - 减少图片的使用
  - 不生成 map 文件
  - 不常改变的库使用 cdn 引入

webpack用了什么插件
  - terser-webpack-plugin：压缩js的，删除注释、debugger、console，多进程编译，删除map映射
  - CompressionPlugin：压缩文件

loder是怎么加载的
  chainWebpack中链式调用：config.module.rule.use().loader()

封装的公共组件有哪些
  - 基础组件：用户头像，老师头像，音频按钮，loading
  - 进阶组件：svg，弹窗，toast

弹窗的实现 怎么挂载
  - 定义公共组件：
    插件方式注入（直接组件名使用，类似el-radio这种）：vue.use(install(Vue.component(component.name, component)))
    子组件引入：import引入，这种一般是弹窗有些个性化设置，不是全局的
  - 绑定到原型上（Vue.prototype），类似我们项目的loading：定义展示，卸载方法；new Vue.extend(loading)编译一个vue组件子类；$mount()挂载，挂载方法带参会替换原dom，不带参则挂在内存；document.body.appendChild(instance.$el)绑定到元素上


vue2 3的传值，有什么区别
    - vu2:props,$emit,event bus
        emit/on
        vuex
        attrs/listeners(会获取到没有在props里接收的)
        provide/inject
        parent/children 与 ref
    - vue3:props通过setup(props, context)中的props接收，$emit==context.emit
        event bus:const vueEvent = mitt()
        https://www.cnblogs.com/daifuchao/p/15029995.html

loder的实现
react的生命周期
1、挂载过程
constructor()
componentWillMount()
componentDidMount()

2、更新过程
componentWillReceiveProps(nextProps)
shouldComponentUpdate(nextProps,nextState)
componentWillUpdate (nextProps,nextState)
render()
componentDidUpdate(prevProps,prevState)

3、卸载过程
componentWillUnmount()

17版的：
- 新增：getDerivedStateFromProps，getSnapshotBeforeUpdate
- 删除：componentWillReceiveProps,componentWillMount,componentWillUpdate

https://blog.csdn.net/u011748319/article/details/107081264/

数组方法
new 的实现
源码知道哪些 newvue的流程
神策埋点有定义那些方法吗
继承方法
https://www.cnblogs.com/ranyonsue/p/11201730.html
    ps：父类Per,子类Son
- 原型链继承:
    ` Son.prototype=new Per()`
- 借用构造函数继承:
    `Son(){Per.call(this,...args)}`
- 组合继承：原型链继承+构造函数继承+构造函数修改（原型链继承会把构造函数改成父类的，所以这里要改回来）
    ```js
    Son(){
        Per.call(this,...args)
    }
    Son.prototype=new Per()
    Son.prototype.constructor = Son
    ```
- 原型式继承:感觉和原型链继承一个样。(object.create()是这么实现的)
    ``` js
    content(c=new Per()){ 
        function S(){} 
        S.prototype=c
        retun new S()
    }
    ```
- 寄生式继承:就是原型式继承加个包裹函数（这也算新的？）
    ```js
    sub(obj){
        var n=content(obj)
        n.name='cc'
        return n
    }
- 寄生组合式继承:原型式继承+组合继承。
    ```js
    Son(){
        Per.call(this,...args)
    }
    // 有些文章content替换成了object.create()，应该是等价的，不过object.create是现有的更方便
    // new Per()也替换成了Per.prototype,可以减少一次父类构造函数的调用
    Son.prototype=content(new Per())
    Son.prototype.constructor = Son
    ```
- class+extends继承:es6语法。和寄生组合式继承区别在于不需要手动修改Son.prototype.constructor，并且Son._proto_指向的是父类（寄生组合式继承因手动修改了所以指向的是Function.prototype)


有使用过混入（mixin），和组件的区别
### mixin释义
  - 混合 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。
  - 混合对象可以包含任意组件选项。
  - 当组件使用混合对象时，所有混合对象的选项将被混入该组件本身的选项。

### mixin和组件的区别
  - 组件相当于在父组件内开辟了一块单独的空间，来根据父组件props过来的值进行相应的操作，单本质上两者还是泾渭分明，相对独立。
  - mixin则是将父组件里的内容和mixin的合并，扩充父组件内容，mixin就相当于基础数据


动态路由权限添加
  ```js
  router.beforeEach((to, from, next) => {
    if (!store.state.UserToken) {
        ...
    } else {
        /* 现在有token了,permissionList判断路由是否添加完成了 */
        if (!store.state.permission.permissionList) {
            /* 如果没有permissionList，真正的工作开始了 */
            store.dispatch('permission/FETCH_PERMISSION').then(() => {
                next({ path: to.path })
            })
        } else {
            if (to.path !== '/login') {
                next()
            } else {
                next(from.fullPath)
            }
        }
    }
  })
  async FETCH_PERMISSION({ commit, state }) {
        /*  获取后台给的权限数组 */
        let permissionList = await fetchPermission()
        /**
          * 根据后台权限跟我们定义好的权限对比，筛选出对应的路由并加入到path=''的children
          * recursionRouter:路由循环遍历匹配方法
          * DynamicRoutes：动态基础路由，包含home页，*重定向404（这个放这是防止刷新时跳到404）
          */
        let routes = recursionRouter(permissionList, dynamicRouter)
        let MainContainer = DynamicRoutes.find(v => v.path === '')
        let children = MainContainer.children
        children.push(...routes)
        /* 生成左侧导航菜单 */
        commit('SET_MENU', children)
        setDefaultRoute([MainContainer])
        /*  初始路由 */
        let initialRoutes = router.options.routes
        /*  动态添加路由,主要方法，在app created里触发，接口获取防止刷新失效 */
        router.addRoutes(DynamicRoutes)
        /* 完整的路由表 */
        commit('SET_PERMISSION', [...initialRoutes, ...DynamicRoutes])
    }
  ```
  https://refined-x.com/2017/09/01/%E7%94%A8addRoutes%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/