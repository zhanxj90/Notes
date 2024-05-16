# TypeScript

## ts的优缺点
  1. 优点
      - 强类型，支持静态和动态类型
      - 程序更容易理解
      - 效率更高:增强代码的可维护性，尤其是在大型项目中
      - 更少的错误:友好的编辑器错误提示，在编译阶段就能检查类型发现大部分错误
      - 更好的包容性:能够编译成任意版本的JS,且支持最新的JS新特性
      - 周边生态繁荣，Vue3已全面支持TS
  2. 缺点
      - 前期开发成本较大（后期维护容易）
      - 不能被浏览器理解，需要编译成JS

-----

## TS类型
### *联合类型*
  1. 标识符：|（可以解读为位运算中的’或‘）
  2. 定义：type c=a|b
  3. 定义联合类型的变量时，变量必须包含联合类型子集中的某一种
      ```ts
        type one = {
          a:number,
          b:number,
          c:number,
        };
        type two = {
          b:number,
          d:number,
          f:number,
        };
        type thr = one | two;

        // 定义的时候，data类型必须是one或者two中的一个，所以定义的属性必须包含one或者two其中之一的所有必填属性，可以多不能少
        const data:thr={
          a:1,
          b:2,
          c:3,
          //属性可以加上 d f
        }
        //调用时只能使用one或two类型中共有的属性，因为不确定定义的时候，定的是one的所有属性还是two的
        console.log(data.b)
        // 下面的会提示报错，不确定有没有a
        // console.log(data.a)
      ```
### *交叉类型*
  1. 标识符：&（可以解读为位运算中的’且‘）
  2. 定义：type c=a&b
  3. 定义交叉类型的变量时，变量必须符合交叉类型所有子集的条件
      ```ts
        type one = {
          a:number,
          b:number,
          c:number,
        };
        type two = {
          b:number,
          d:number,
          f:number,
        };
        type thr = one & two;

        // 定义的时候，data类型必须包含one和two中的所有属性，一个都不能少
        const data:thr={
          a:1,
          b:2,
          c:3,
          d:4,
          f:5
        }
        //调用时可以使用one或two类型中所有的属性，因为定义的时候全写了
        console.log(data.b)
        // 下面的正常
        // console.log(data.a)

        //ccc是个错误的定义，没有报错，但是定义不了符合ccc类型的变量，因为一个变量没有办法是字符又是对象
        type ccc='a'|'b'&{c:string}
      ```

-----

## TypeScript应用tips
### *函数重载*
  1. 不同参数个数或者不同参数类型，对应的返回值类型也不同时，需要使用函数重载，而不是联合类型
      ``` ts
      // 参数是A类型时返回M类型，B类型时返回N类型
      function foo(arg:A):M;
      function foo(arg:B):N;
      function foo(arg:A|B){
        // todoing...
      }
      ```
### *vue获取组件类型封装*
  1. vue+ts使用ref获取组件，调用组件参数时没有智能提示，ref需要传组件泛型
  2. InstanceType<typeof Comp>:typeof获取组件类型，InstanceType获取类型实例
  3. 封装方法1：需要传入组件类型
      ```ts
        // useCompRef.ts
        import {ref} from 'vue'
        // 因为这里不知道T是什么，而需要的必须是个抽象类，所以需要extends继承一下判断一下
        export function useCompRef<T extends abstract new (...args:any) => any>(){
          return ref<InstanceType<T>>()
        }

        // 页面中调用
        import {useCompRef} from './useCompRef'
        import Child from './myChild.vue'
        const childRef=useCompRef<typeof Child>()
      ```
  4. 封装方法2：直接传入组件，不需要typeof，
      ```ts
        // useCompRef.ts
        import {ref} from 'vue'
        // 因为这里不知道T是什么，而需要的必须是个抽象类，所以需要extends继承一下判断一下
        // 这里使用的是类型推导，通过传入的组件实例参数倒推出抽象类
        // _comp:命名规范，参数添加下划线可以解决参数未使用的报错问题
        export function useCompRef<T extends abstract new (...args:any) => any>(_comp:T){
          return ref<InstanceType<T>>()
        }

        // 页面中调用
        import {useCompRef} from './useCompRef'
        import Child from './myChild.vue'
        //这里使用的时候就可以不定义类型了
        const childRef=useCompRef(Child)
      ```
### *联合类型转交叉类型*
  1. 实现
      ```ts
        type UnionToIntersection<T>=
          (T extends any ? (x:T)=>any : never)
            extends (x:infer R)=>any ? 
              R : never

        // 实际应用
        // type B = {a:number}|{b:string}>
        // type A=UnionToIntersection<B>
        // 结果为 A = {a:number}&{b:string}
      ```
  2. 解析
      - T：传入的Ts联合类型
      - extends any：返回的一定是ture
      - 联合类型三目运算：会把联合类型拆开来进行三目运行
          ```ts
            string|number extends any ? xxx : never
            /*
            解析的结果是:
            string extends any ? xxx : never
            |
            number extends any ? xxx : never
            */
          ```
      - 联合函数参数自动推导（逆变）：同一类参数不同的函数组成的联合类型，推导出来的联合类型的参数就是所有参数的交叉
        图示：![](/assets/ts1.png)
          ```ts
            //示例：
            type UnionTest = 
              | ((x: {a:string}) => any)
              | ((x: {b:number}) => any)
              | ((x: {c:boolean}) => any)

            function fn(cb:UnionTest){
              // 这里推导出来的cb的参数就是交叉类型
              // 也就是： { a: string; } & { b: number; } & { c: boolean; }
              cb({a:'1',b:2,c:true})
            }
          ```

-----

## type和interface
  1. 相同点
      - 都是用来定义对象或函数的形状。
      - 它俩也支持继承,并且可以互相继承
  2. 不同点
      - interface是通过extends实现继承的，type是通过&实现继承的。
      - type可以定义 基本类型的别名
      - type可以通过 typeof 操作符来定义
      - type可以申明 联合类型
      - type可以申明 元组类型
      - interface可以声明合并,如果是type的话，就会报重复定义的警告
      - interface可以实现函数重载

-----
