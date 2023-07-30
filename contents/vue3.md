# vue3

## wacth和watchEffier
### *wacth*
  1. 格式：watch(WatcherSource, Callback, [WatchOptions])
      | 参数 | 类型 | 说明 |
      | -- | -- | -- |
      | WatcherSource | 对象、数组、函数 | 要监听的源数据，响应对象或其组成的数组或返回响应对象属性的回调 |
      | Callback | 函数 | 执行的回调函数，入参（newValue,oldValue） |
      | WatchOptions | 对象 | 可选配置项，deep、immediate、flush可选 |
  2. 监听ref时
      - ref是基本类型时：直接写该ref
      - ref是引用类型时：监听整个对象则写ref.value,或者写ref然后增加deep配置项；如果是要监听其中某个属性，则要使用getting函数
  3. 监听reactive时
      - 监听整个对象：无法正确获得oldValue,且强制开启了深度监视,深度监视不生
      - 监听其中某个属性：要使用getting函数,属性是引用类型时还要开启深度监听（deep）
  4. 取消监听：监听返回一个函数，执行的回调函数中调用该函数便可取消
### *watchEffier*
  1. 只有一个参数，就是回调
  2. 回调中用到的数据只要发生变化，就会重新执行回调
  3. 取消监听：监听返回一个函数，执行的回调函数中调用该函数便可取消