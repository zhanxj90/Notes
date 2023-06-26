# 前端体系
  > 前端已经不是简单的’切图仔‘了，随着互联网的急速发展，前端已形成了自己的工程体系。相关的工程化、模块化、设计模式、开发框架等一系列内容成了前端工程师必会的技能

## 模块化规范
### *commonJS*
  ```js
  // a.js
  var a=2
  function printName(){  
    console.log(name)
  }
  module.exports = {
    a,
    b,
    printName: printName
  }
  ```
  ```js
  // b.js
  // 只能.或者[key]添加属性，无法给exports重新赋值
  exports.b=3
  ```
  ```js
  // c.js
  var aModule = require('./a.js')
  var bModule = require('./b.js')
  console.log(aModule.a) // 2
  console.log(bModule.b) // 3
  aModule.printName();
  ```
  1. 服务器端规范,主要实现者--nodeJs
  2. 定义：一个单独的文件是一个模块，每一个模块都是一个单独的作用域；即模块内部定义的数据都是私有的，对其他文件不可见，无法被其他模块读取，除非为global对象的属性。
  3. 输入：
      - 加载模块用require方法，该方法读取一个文件并且执行，返回文件内部的module.exports对象。
      - require的查找规则:
          * 优先核心模块
          * 有路径，则找文件，没有再找目录
          * 无路径，则找npm包
          * 上述步骤有缓存的情况下优先使用缓存
      - require的加载顺序:深度优先搜索（DFS, depth first search）
  4. 输出：
      - CommonJS中是没有module.exports的概念的；
      - 但是为了实现模块的导出，Node中使用的是Module的类(提供了一个Module构造函数)，每一个模块都是Module的一个实例，也就是module；
      - module才是导出的真正实现者；
      - 所以在Node中真正用于导出的其实根本不是exports，而是module.exports。只是为了实现CommonJS的规范，也为了使用方便，Node为每个模块提供了一个exports对象，让其对module.exports有一个引用而已。
      - 相当于在每个模块头部，有这样一行命令：var exports = module.exports;所以export只能添加属性，无法重新赋值
      - 所以不能直接给exports、module.exports赋值，这样等于切断了exports和module.exports的联系。最终输出的结果只会是module.exports的值
### *AMD*
  1. AMD：Asynchronous Module Definition(异步模块定义)，浏览器端规范，主要实现者--RequireJS
  2. requireJS主要解决两个问题：
      - 多个js文件可能有依赖关系，被依赖的文件需要早于依赖它的文件加载到浏览器。
      - js加载的时候浏览器会停止页面渲染，加载文件愈多，页面失去响应的时间愈长。
  3. requireJS定义模块：全局变量，define函数，define(id,dependencies,factory)
      |参数名|是否必填|描述|
      |-|-|-|
      |id|否|模块的标识，默认值为脚本文件名（去掉拓展名）|
      |dependencies|否|所依赖的模块，数组类型|
      |factory|是|要执行的函数或对象，函数只执行一次，对象则是模块的输出值|
      ```js
      // a.js
      define(['jquery'],function($){
        const name=$('.name')
        return {
          name:name.text()
        }
      })
      ```
  4. requireJS加载模块：require函数，require(dependencies, factory)
      |参数名|是否必填|描述|
      |-|-|-|
      |dependencies|是|所依赖的模块，数组类型|
      |factory|是|回调函数，参数为依赖的模块，依赖模块都加载成功后调用函数|
      ```js
      // main.js
      require(['./a.js'],function(a){
        console.log(a.name)
      })
      ```
  5. AMD推崇的是依赖前置,被提前罗列出来并会被提前下载并执行（requireJS2.0开始也修改成延迟执行了）
### *CMD*
  1. CMD：common module definition(通用模块定义)，浏览器端规范，主要实现者--sea.js
  2. CMD引入模块、定义模块都在全局变量define函数中进行，define(function(require, exports, module){})
      |参数名|描述|
      |-|-|
      |require|引入模块函数，接受 模块标识 作为唯一参数|
      |exports|暴露模块，用来向外提供模块接口，相当于module.exports的引用|
      |module|当前模块相关联的一些属性和方法|
      ```js
      define(function(require, exports, module){
        const  $ = require('jquery.js')
        const name = $('.name')
        // 等同 module.exports.name=name.text()
        exports.name=name.text()
      })
      ```
  3. CMD推崇的是依赖就近,用到某模块的时候再去require
### *umd*
  1. 严格上说，umd不能算是一种模块规范
  2. 是AMD和CommonJS的结合体，保证模块可以被amd和commonjs调用
      ```js
      (function (root, factory) {
        if (typeof define === 'function' && define.amd) {
            define(['b'], factory);
        } else if (typeof module === 'object' && module.exports) {
            module.exports = factory(require('b'));
        } else {
            root.returnExports = factory(root.b);
        }
      }(this, function(b){ return {} }));
      ```
### *ES6*
  1. js语言的模块功能，浏览器和服务器通用
  2. 编译时加载，效率要比CommonJS模块高，使得静态分析成为可能
  3. ES6的模块自动采用严格模式,顶层的this指向undefined
  4. 浏览器中加载ES6:`<script type="module" src="./foo.js" />`
  5. export/export.default命令用于规定模块的对外接口
  6. import命令用于输入其他模块提供的功能。

-----

## 模块化对比
  |名称|CommonJs|AMD|CMD|ES6|
  |-|-|-|-|-|
  |同步/异步|同步|异步|异步|同步/异步都可，取决于使用的loader API|
  |实现实例|nodeJS|RequireJS|sea.js|JavaScript|
  |运行环境|服务器|浏览器|浏览器|前后端|
  |依赖处理|-|依赖前置|依赖就近|-|
  |导入|require()（全量）|require([],callback)|require()|import（可部分）|
  |导出|exports（值拷贝）|define([],callback)|exports|export、export.default（值引用）|
### *AMD与CMD区别*
  1. 在模块定义时对依赖的处理不同：
      - AMD推崇依赖前置 在定义模块的时候就有声明其依赖的模块
      - CMD推崇就近依赖 只有在用到某模块的时候再去require
  2. 依赖模块的执行时机处理不同
      - AMD在加载模块完成后就会执行该模块，都执行完才执行主逻辑；依赖模块的执行顺序和书写顺序不一定一致，但一定先于主逻辑
      - CMD加载完某个依赖模块后并不执行，进入主逻辑后遇到require才执行；依赖模块的执行顺序和书写顺序一定一致，但不一定先于主逻辑
### *ES6和CommonJS的区别*
  1. 语法上
      - CommonJS使用的是module.exports = {}导出一个模块对象，require(‘file_path’)引入模块对象
      - ES6使用的是export导出指定数据，import引入具体数据。
  2. 加载内容
      - CommonJS全量加载整个模块
      - ES6可以选择性加载模块的部分内容
  3. 导出类型
      - CommonJS模块输出的是浅拷贝，模块内部常用类型的变化就影响不到这个值
      - ES6模块输出的是值的引用；JS遇到import，就会生成一个只读引用，到被加载的模块里取值；原始值变了，import加载的值也会跟着变。