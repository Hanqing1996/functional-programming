

参考：有哪些函数式编程在前端的实践经验？ - 月影的回答 - 知乎 https://www.zhihu.com/question/33652103/answer/60485395



#### 函数纯度

对于常规的函数来说，每一次传参确定，返回值是确定的，但可惜的是，这个规则并不完全成立，比如Math.random()、Date.now()这些函数每次调用的参数相同，返回值仍然不同，或者说，这些函数的“纯度”比较低。

#### **函数等价范式**

```js
function equal(func){
    return function(){
        var ret = func.apply(this, arguments);
        return ret;
    }
}
```

上面这个我把它叫做**函数等价范式**，这个范式是一个恒等范式。
也就是说，对于

```text
someObj.someMethod = equal(someObj.someMethod);
```

不论someObj和someMethod如何实现的，上面的赋值语句都完全不会让代码运行结果有一丝一毫改变。

#### AOP

```
function safeTransform(func){
    return function(){
        做一些事情，但不去改变 arguments
        var ret = func.apply(this, arguments);
        做一些事情，但不去改变 ret
        return ret;
    }
}
```

* 示例

```js
var fn = {
  watch: function(func, before, after){
    return function(){
      var args = [].slice.call(arguments);
      before &&  before.apply(this, args);
      var ret = func.apply(this, arguments);
      after && after.apply(this, [ret].concat(args));
      return ret;
    }
  }
}

var swipable = new Swipable({
    element: '.swipable-wrap',
    dir: 'horizontal'
  });

swipable._scroll = fn.watch(swipable._scroll, function(pos){
    var p = pos / (swipable.min - swipable.max);
    $('#progress-bar .current').css('width', Math.min(p, 1.0) * 240 + 'px');
});
```

> 基本思路就是上面的代码，首先定义一个watch的高阶函数，它可以"watch"任意一个方法，在它被调用之前（before）和被调用之后（after）拦截进去。
>
> 然后我就watch了swipable组件中的_scroll方法，在它被调用前拿到了它的参数pos，进行计算，同步更新伪滚动条。
>
> 这样的话我就在没有对swipable组件本身改动一行代码的情况下得到了我要的功能。









