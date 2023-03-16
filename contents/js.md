# JavaScript

## es6
  > es6通常是指es2017，但是2018-2022新增内容没有划分出es新版本，所以这些也归类至es6里

### es6数组方法
  - push
  - pop
  - shift
  - unshift
  - splice 
  - split
  - map:更新数组
  - forEach() -----循环
  - filter()、
  - includes()、
  - find()、
  - findIndex()— —筛选（删除）数组
  - some()、
  - every()— —判断数组
  - reduce()— —叠加数组

## promise

### promise释义
  > Promise 是异步编程的一种解决方案，其实是一个构造函数，自己身上有all、reject、resolve这几个方法，原型上有then、catch等方法。
  - Promise对象的状态不受外界影响,有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
  - Promise一旦状态改变，就不会再变，任何时候都可以得到这个结果。
  - 解决了回调地狱（异步方法中callback函数嵌套其他异步，一层一层的）的问题

### 成功回调（resolve,then)
  - 

### 失败回调（reject,catch)
  - 

### all的使用
  - all方法是在Promise类上的，通过Promise.all使用。all接收一个数组参数(参数不一定是要promise，有返回就有值),数组全执行完才返回，有一个错了就走catch，全对才走then

### race的使用
  - race方法是在Promise类上的，通过Promise.race使用。也是接收数组（同all），数组有个执行完了立马返回，对就then，错就catch。

## git命令
  - git clone
  - git branch
  - git remote prune origin
  - git add
  - git status
  - git commit
  - git push
  - git pull

  实例没有prototype属性，只有__proto__。构造函数两者都有


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