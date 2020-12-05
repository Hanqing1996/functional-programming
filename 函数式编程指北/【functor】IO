```js
var IO = function(f) {
  this.__value = f;
}

IO.of = function(x) {
  return new IO(function() {
    return x;
  });
}

// 这里的映射是把原先 container 的 _value 函数与传入的 f 组合起来
IO.prototype.map = function(f) {
  return new IO(_.compose(f, this.__value));
}
```

**IO** 跟前面那几个 **Functor** 不同的地方在于，它的 __value 是一个函数。它把不纯的操作（比如 IO、网络请求、DOM）包裹到一个函数内，从而延迟这个操作的执行。所以我们认为，**IO 包含的是被包裹的操作的返回值**。

```js
// 肮脏的非纯函数 _ => window.document 被 IO 包裹起来了
var io_document = new IO(_ => window.document);

io_document.map(function(doc){ return doc.title });
//=> IO(document.title)
```

* useage

> 编写一个函数，从当前 url 中解析出对应的参数。

【注意】url 是一个 IO 类型的 functor；`url.map` 是对 IO 进行 map，具体定义见上面

```js
import _ from 'lodash';

// 先来几个基础函数：
// 字符串
var split = _.curry((char, str) => str.split(char));
// 数组
var first = arr => arr[0];
var last = arr => arr[arr.length - 1];
var filter = _.curry((f, arr) => arr.filter(f));
//注意这里的 x 既可以是数组，也可以是 functor
var map = _.curry((f, x) => x.map(f)); 
// 判断
var eq = _.curry((x, y) => x == y);
// 结合
var compose = _.flowRight;


var toPairs = compose(map(split('=')), split('&'));
// toPairs('a=1&b=2')
//=> [['a', '1'], ['b', '2']]

var params = compose(toPairs, last, split('?'));
// params('http://xxx.com?a=1&b=2')
//=> [['a', '1'], ['b', '2']]

// 这里会有些难懂=。= 慢慢看
// 1.首先，getParam是一个接受IO(url)，返回一个新的接受 key 的函数；
// 2.我们先对 url 调用 params 函数，得到类似[['a', '1'], ['b', '2']]
//   这样的数组；
// 3.然后调用 filter(compose(eq(key), first))，这是一个过滤器，过滤的
//   条件是 compose(eq(key), first) 为真，它的意思就是只留下首项为 key
//   的数组；
// 4.最后调用 Maybe.of，把它包装起来。
// 5.这一系列的调用是针对 IO 的，所以我们用 map 把这些调用封装起来。
var getParam = url => key => url.map(compose(Maybe.of, filter(compose(eq(key), first)), params));

// 创建充满了洪荒之力的 IO！！！
var url = new IO(_ => window.location.href);
// 最终的调用函数！！！
var findParam = getParam(url);

// 上面的代码都是很干净的纯函数，下面我们来对它求值，求值的过程是非纯的。
// 假设现在的 url 是 http://xxx.com?a=1&b=2
// 调用 __value() 来运行它！
findParam("a").__value();
//=> Maybe(['a', '1'])
```

我可以看懂，但是我实际开发可能不会这么去写吧。
