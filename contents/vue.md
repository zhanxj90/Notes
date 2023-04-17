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
  - 调用_init()：合并配置，初始化生命周期，初始化事件中心，初始化渲染，初始化 data、props、computed、watcher 等等
  - 调用$mount：进行编译转换（compile），vue格式内容转成render函数；执行mountComponent函数。
  - mountComponent :执行beforeMount钩子；实例化Watcher（传入updateComponent）；最后执行mounted钩子。
  - updateComponent：执行_render()生成Node，Node传入_update生成DOM。vm._update(vm._render(), hydrating)。
  - _render：执行$mount中生成的render函数，render函数的传参$createElement就是createElement回调（createElement是_createElement的封装），最终是通过createElement生成Node。 
  - _update：不同环境执行不同的__patch__ 方法，web浏览器端__patch__是createPatchFunction的返回。
  附上流程图:[/assets/new-vue.png]

-----

## diff
### *新旧节点不同*
  - 创建新节点
  - 更新父级占位符节点
  - 删除旧节点

-----

## 组件传值
### *父子组件传值*
### *$attrs/$listeners*
  1. 可用于非父子组件传值
  2. $attrs:包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)
  3. $listeners:包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器
  4. inheritAttrs:组件选项，默认值true;是否在子组件顶层元素上显示props中未定义的属性（即$attrs内容）

-----

##
### **

-----

##
### **

-----

##
### **

-----

##
### **

-----

