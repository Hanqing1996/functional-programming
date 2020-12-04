#### compose


redux 和 koa 的 middleware 都有借鉴 compose 的地方

* 用法

```js
var toUpperCase = function(x) { return x.toUpperCase(); };
var exclaim = function(x) { return x + '!'; };
var shout = compose(exclaim, toUpperCase);

shout("send in the clowns");
//=> "SEND IN THE CLOWNS!"
```

* 特点

1. `compose(f1,f2,f3,f4)`执行顺序是 f4->f3->f2->f1，即从右向左（符合数学上的表达式求值顺序）
2. 上一个函数的执行结果作为下一个函数的入参

* 实现

```js
// https://github.com/reduxjs/redux/blob/master/src/compose.js
function compose(...funcs) {
    if (funcs.length === 0) {
      return arg => arg
    }
  
    if (funcs.length === 1) {
      return funcs[0]
    }
  
    return funcs.reduce((a, b) => (...args) => a(b(...args)))
  }
```

```js
combin=compose(head, reverse)
res=combin("jack"); // HELLO , JACK

// reduce 中 a,b 变化如下

a:funcs[0] // 最左边的函数
b:funcs[1]
     
a:(...args)=>0(1(...args))
b:funcs[2]


a:(...args)=> 0(1(2(...args)))
b:funcs[3]

a:(...args)=> 0(1(2(3(...args))))
b:funcs[4]
...
```

---

#### 
