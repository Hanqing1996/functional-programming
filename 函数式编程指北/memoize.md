#### memoize

```js
var squareNumber  = memoize(function(x){ return x*x; });

squareNumber(4);
//=> 16

squareNumber(4); // 从缓存中读取输入值为 4 的结果
//=> 16

squareNumber(5);
//=> 25

squareNumber(5); // 从缓存中读取输入值为 5 的结果
//=> 25
```

* 实现

```js
var memoize = function(f) {
  var cache = {};

  return function() {
    var arg_str = JSON.stringify(arguments);
    cache[arg_str] = cache[arg_str] || f.apply(f, arguments);
    return cache[arg_str];
  };
};
```

#### 把不纯的函数转换为纯函数

```js
var pureHttpCall = memoize(function(url, params){
  return function() { return $.getJSON(url, params); }
});
```

* 不纯的函数

```js
// 函数的结果不应受非输入值的外部变量影响
function() { 
    return $.getJSON(url, params); 
}
```

* 纯函数

```js
// 相同的输入保证能返回相同的输出
function(url, params){
  return function() { return $.getJSON(url, params); }
}
```
