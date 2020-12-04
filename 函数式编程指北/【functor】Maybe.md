#### Container

```js
var Container = function(x) {
  this.__value = x;
}

Container.of = function(x) { return new Container(x); };
```

```js
Container.of(3)
//=> Container(3)


Container.of("hotdogs")
//=> Container("hotdogs")


Container.of(Container.of({name: "yoda"}))
//=> Container(Container({name: "yoda" }))
```

#### map

> 数组的 map 用于描述数组和数组间的映射关系。而这里表示 container 和 container 之间的映射关系。

```js
// (a -> b) -> Container a -> Container b
Container.prototype.map = function(f){
  return Container.of(f(this.__value))
}
```

```js
Container.of(2).map(function(two){ return two + 2 })
//=> Container(4)


Container.of("flamethrowers").map(function(s){ return s.toUpperCase() })
//=> Container("FLAMETHROWERS")


Container.of("bombs").map(concat(' away')).map(_.prop('length'))
//=> Container(10)
```

#### functor 

functor 是实现了 `map` 函数并遵守一些特定规则的容器类型。

* Maybe

> 可用于容错处理

```js
var Maybe = function(x) {
  this.__value = x;
}

Maybe.of = function(x) {
  return new Maybe(x);
}

Maybe.prototype.isNothing = function() {
  return (this.__value === null || this.__value === undefined);
}

Maybe.prototype.map = function(f) {
  return this.isNothing() ? Maybe.of(null) : Maybe.of(f(this.__value));
}
```

`Maybe` 看起来跟 `Container` 非常类似，但是有一点不同：`Maybe` 会先检查自己的值是否为空，然后才调用传进来的函数。

```js
Maybe.of("Malkovich Malkovich").map(match(/a/ig));
//=> Maybe(['a', 'a'])

Maybe.of(null).map(match(/a/ig));
//=> Maybe(null)

Maybe.of({name: "Boris"}).map(_.prop("age")).map(add(10));
//=> Maybe(null)

Maybe.of({name: "Dinah", age: 14}).map(_.prop("age")).map(add(10));
//=> Maybe(24)
```

注意看，当传给 `map` 的值是 `null` 时，代码并没有报错。这是因为每一次 `Maybe` 要调用函数的时候，都会先检查它自己的值是否为空。

