[curry 的实现](https://github.com/Hanqing1996/JavaScript-advance/tree/master/%E4%BD%A0%E7%9C%9F%E7%9A%84%E6%87%82%E5%87%BD%E6%95%B0%E5%90%97#%E6%9F%AF%E9%87%8C%E5%8C%96%E5%87%BD%E6%95%B0)
```js
function curry(func , fixedParams){
    if ( !Array.isArray(fixedParams) ) { fixedParams = [ ] } // 初始化
    return function(){
        let newParams = Array.prototype.slice.call(arguments); // 新传的所有参数
        if ( (fixedParams.length+newParams.length) < func.length ) {
            return curry(func , fixedParams.concat(newParams));
        }else{
            return func.apply(undefined, fixedParams.concat(newParams));
        }
    };
}
```

只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

没吃饱（参数不足），就一直饿着（返回函数）


```js
var match = curry(function(what, str) {
  return str.match(what);
});
```

```js
match(/\s+/g, "hello world");// [ ' ' ]

// 只传递一个参数给 match,而传入 curry 的函数需要两个参数,所以 match(/\s+/g) 返回的仍是一个函数
match(/\s+/g)("hello world");// [ ' ' ]
```
---

curry 的局部调用特性，常配合 compose 一起使用，以实现数据在一系列纯函数间传递。具体来说，通常是将一个多参数的函数 curry 后放入 compose 中。
```
var add=curry(function(a,b){
  return a+b
})

var multiply=curry(function(a,b){
  return a*b
})

var addAndmultiply=compose(add(10),multiply(20))

addAndmultiply(3) // 3*20+10
```
