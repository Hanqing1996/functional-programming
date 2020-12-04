[curry 的实现](https://github.com/Hanqing1996/JavaScript-advance/tree/master/%E4%BD%A0%E7%9C%9F%E7%9A%84%E6%87%82%E5%87%BD%E6%95%B0%E5%90%97#%E6%9F%AF%E9%87%8C%E5%8C%96%E5%87%BD%E6%95%B0)

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
