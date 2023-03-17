# TypeScript

## TypeScript应用tips

### 函数重载
  不同参数个数或者不同参数类型，对应的返回值类型也不同是，需要使用函数重载，而不是联合类型
  ``` ts
    // 参数是A类型时返回M类型，B类型时返回N类型
    function foo(arg:A):M;
    function foo(arg:B):N;
    function foo(arg:A|B){
      // todoing...
    }
  ```