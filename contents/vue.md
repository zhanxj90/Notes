# vue2
## MVVM（Model-View-ViewModel ）
  - MVVM 是一种设计思想。
  - Model 层代表数据模型，也可定义数据修改和操作的业务逻辑。
  - View 代表 UI 组件，它负责将数据模型转化成 UI 展现出来。
  - ViewModel 是一个同步 View 和 Model 的对象，View 和 Model 是通过此进行双向交互的
  - View 和 Model 之间的同步工作完全是自动的，复杂的数据状态维护完全由 MVVM 来统一管理。

## new Vue的流程
  - 调用_init()：合并配置，初始化生命周期，初始化事件中心，初始化渲染，初始化 data、props、computed、watcher 等等
  - 调用$mount：进行编译转换（compile），vue格式内容转成render函数；执行mountComponent函数。
  - mountComponent :执行beforeMount钩子；实例化Watcher（传入updateComponent）；最后执行mounted钩子。
  - updateComponent：执行_render()生成Node，Node传入_update生成DOM。vm._update(vm._render(), hydrating)。
  - _render：执行$mount中生成的render函数，render函数的传参$createElement就是createElement回调（createElement是_createElement的封装），最终是通过createElement生成Node。 
  - _update：不同环境执行不同的__patch__ 方法，web浏览器端__patch__是createPatchFunction的返回。
  附上流程图:[/assets/new-vue.png]

## diff
### 新旧节点不同
  - 创建新节点
  - 更新父级占位符节点
  - 删除旧节点