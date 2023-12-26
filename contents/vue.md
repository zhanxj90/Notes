# vue2

## MVVM（Model-View-ViewModel ）
  1. MVVM 是一种设计思想。
  2. Model 层代表数据模型，也可定义数据修改和操作的业务逻辑。
  3. View 代表 UI 组件，它负责将数据模型转化成 UI 展现出来。
  4. ViewModel 是一个同步 View 和 Model 的对象，View 和 Model 是通过此进行双向交互的
  5. View 和 Model 之间的同步工作完全是自动的，复杂的数据状态维护完全由 MVVM 来统一管理。

-----

## Vue双向绑定原理
  1. 采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty() 来劫持各个属性的 setter、getter，在数据变动时发布消息给订阅者，触发相应的监听回调。
  2. 需要 observe 的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter 和 getter 这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化
  3. compile 解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图
  4. Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁，主要做的事情是:在自身实例化时往属性订阅器(dep)里面添加自己自身;必须有一个 update() 方法待属性变动 dep.notice() 通知时，能调用自身的 update() 方法，并触发 Compile 中绑定的回调，则功成身退。
  5. MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 数据变化，通过 Compile 来解析编译模板指令，最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化(input) ->数据 model 变更的双向绑定效果。

-----

## new Vue的流程
  1. 调用_init()：合并配置，初始化生命周期，初始化事件中心，初始化渲染，初始化 data、props、computed、watcher 等等
  2. 调用$mount：进行编译转换（compile），vue格式内容转成render函数；执行mountComponent函数。
  3. mountComponent :执行beforeMount钩子；实例化Watcher（传入updateComponent）；最后执行mounted钩子。
  4. updateComponent：执行_render()生成Node，Node传入_update生成DOM。vm._update(vm._render(), hydrating)。
  5. _render：执行$mount中生成的render函数，render函数的传参$createElement就是createElement回调（createElement是_createElement的封装），最终是通过createElement生成Node。 
  6. _update：不同环境执行不同的__patch__ 方法，web浏览器端__patch__是createPatchFunction的返回。  
  附上流程图:![](/assets/new-vue.png)

-----

## diff
### *新旧节点不同*
  1. 创建新节点
  2. 更新父级占位符节点
  3. 删除旧节点

-----

## 组件传值
### *父子组件传值*
### *$attrs/$listeners*
  1. 可用于非父子组件传值
  2. $attrs:包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)
  3. $listeners:包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器
  4. inheritAttrs:组件选项，默认值true;是否在子组件顶层元素上显示props中未定义的属性（即$attrs内容）

-----

## Vue脚手架集合
> vue搭配的脚手架有两个：Vue-cli和Vite。 Vue-cli是 Vue 2 默认官方脚手架工具;Vite 是Vue3之后发布的（Vue-cli也可用于Vue3）
### *Vue-cli*
  1. Vue-cli由来
      - Vue-cli是 Vue 2 默认官方脚手架工具，Vue-cli 基于 Webpack 开发，是 Webpack 的超集
  2. Vue-cli的特点
      - Vue-cli基于 Webpack 构建，配置好了打包规则
      - 内置热模块重载的开发服务器
      - 有丰富的官方插件合集，站在 webpack 庞大的社区资源上
      - 友好的图形化创建和管理 Vue 项目界面
      - Vue-cli在服务启动之前，要把所有代码打包成 Bundle 再启动服务。这就是为什么启动一些大型项目时，特别慢的原因
  3. Vue-cli获取env环境变量
      - 配置.env.[mode]文件来配置环境变量(例：.env.develop 文件)
      - 属性名必须以 VUE_APP_ 开头，如：VUE_APP_XXX
      - 在js文件中用process.env来获取环境配置
        ```js
        const OLDURL = process.env.VUE_APP_OLD_URL
        ```
### *Vite*
  1. vite由来
      - Vite 是 Vue 团队开发的新一代前端开发与构建工具，Vite 不是基于 Webpack 开发的，他为了解决服务启动慢的问题，Vite 通过一开始将应用中的模块区分为依赖和源码两类，改进了开发服务器启动时间。
  2. Vite模块化的划分
      - 依赖：大多为在开发时不会变动的纯 JavaScript。一些较大的依赖（例如有上百个模块的组件库）处理的代价也很高。依赖也通常会存在多种模块化格式。Vite 会使用 esbuild 预构建依赖。esbuild 使用 Go 编写，并且比以 JavaScript 编写的打包器预构建依赖快 10-100 倍。
      - 源码：通常包含一些并非直接是 JavaScript 的文件，需要转换，时常会被编辑。同时，并不是所有的源码都需要同时被加载（例如基于路由拆分的代码模块）。
  3. Vite的特点
      - 在开发过程中，Vite 是一个开发服务器，根据浏览器的要求编译源文件。无需捆绑，编译后真正做到按需使用。未修改的文件会返回304，所以浏览器根本就不会要求。这就是它启动快、保持快的原因。
      - Vite 支持热模块替换，这和 "简单的重载页面 "有本质的区别，在DX（开发者体验）方面是天壤之别。Vue组件和CSS HMR是开箱即用的支持，第三方框架可以利用HMR API。
      - Vite支持一些webpack启发的功能，比如从js中导入’.css’文件（a la css-loader），基于fs的相对路径引用资产（a la file-loader m在构建过程中只需指定’-base’就能自动处理最终的公共部署路径）。
      - Vite通过esbuild支持.(t|j)sx?文件，开箱即用，速度快得惊人，所以即使是TS转码，HMR也是字面上的即时性。
      - Vite使用Rollup进行生产构建，内部配置与开发服务器功能一致。生产构建的输出是一个传统的静态资产目录，可以部署在任何地方（并且可以被polyfilled以支持旧的borwsers）。
      - Vite的核心也是可扩展的（配置/插件格式待定）–你可以通过添加Koa中间件（用于开发）+Rollup aplugin（用于构建）来添加对自定义文件转换的支持。
  4. Vite获取env环境变量
      - 配置.env.[mode]文件来配置环境变量(例：.env.develop 文件)
      - 只有以 VITE_ 为前缀的变量才会暴露给经过 vite 处理的代码，如：VITE_APP_XXX
      - 在js文件中用import.meta.env来获取环境配置
        ```js
        const OLDURL = import.meta.env.VITE_APP_OLD_URL
        ```
      - 默认的内建变量
        * import.meta.env.MODE: {string} 应用运行的模式。
        * import.meta.env.BASE_URL: {string} 部署应用时的基本 URL。他由base 配置项决定。
        * import.meta.env.PROD: {boolean} 应用是否运行在生产环境。
        * import.meta.env.DEV: {boolean} 应用是否运行在开发环境 (永远与import.meta.env.PROD相反)。
### *Vite 和 Vue Cli 区别*
  1. Vite 是基于原生 ES6 Modules，在生产环境下打包使用的是 Rollup
  2. vue-cli 基于 webpack 封装，生产环境和开发环境都是基于 Webpack 打包。所以两者在生产环境下都是基于源代码文件打包
  3. 但在开发环境中，两者有所不同。Vite 在开发环境下，基于原生 ES6 ，无需对代码进行打包，浏览器可以直接调用。所以 Vite 因为基于浏览器的原生功能，省掉了打包过程，在开发环境中体验及其愉快。
  4. vite 与 webpack 两者定位不同。webpack 是一个纯打包工具，vite 是更上层的工具链解决方案，类似（webpack + web 常用配置 + webpack-dev-server

-----

## 指令
### *常用内部指令*
  1. v-model:双向绑定数据
  2. v-bind:缩写（:），绑定动态数据
  3. v-on:缩写（@），绑定事件
  4. v-if、v-else:条件判断
  5. v-for:循环遍历
  6. v-text、v-html:绑定文本节点，v-htm会解析成一段dom节点
  7. v-clock:配合css（[v-clock]{display:none;}）使用；后续步骤未完成时，用于隐藏未渲染的节点,解决网速慢出现{{xx}}的问题
### *自定义指令*
  1. 指令类型
    - 全局自定义指令:Vue.directive('color',{bind(el,obj,vnode,oldVnode){ } })
    - 局部自定义指令:组件中定义；directives:{color:{bind(el,obj,vnode,oldVnode){} } }；与全局指令重名时只会运行局部指令。
  2. 指令参数
    - 第一个参数为指令名，使用时以v-开头（v-color)
    - 第二个参数为对象或函数，为对象时其中可以包含多个钩子函数
### *指令钩子函数*
  1. 生命周期
    - bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
    - inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
    - update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新（绑定在此函数中指令会跟随数据变化而动态变化）。
    - componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
    - unbind：只调用一次，指令与元素解绑时调用（通常如果指令中监听了事件，需要在这个钩子中取消监听）。
  2. 函数参数
    - el：指令所绑定的元素，可以用来直接操作 DOM。
    - binding：一个对象，包含以下 property：
      * name：指令名，不包括 v- 前缀。
      * value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
      * oldValue：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
      * expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
      * arg：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
      * modifiers：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
    - vnode：Vue 编译生成的虚拟节点，可以通过vnode.context拿到当前组件的实例（全局组件则拿到vue实例对象）。
    - oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

-----

##
### **

-----

##
### **

-----

