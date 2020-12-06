

#### 用函数式实现 cat 命令

【注】由于 fs.readFileSync 和 console.log 是不纯的动作（前者依赖外部变量 fs，后者与外界有交互）。所以必须用 IO 包裹

```js
import fs from 'fs';
import _ from 'lodash';

var map = _.curry((f, x) => x.map(f));
var compose = _.flowRight;

// filename 在这里作为闭包变量,被 readFile 返回的函数所引用
var readFile = function(filename) {
    return new IO(_ => fs.readFileSync(filename, 'utf-8'));
};

var print = function(x) {
    return new IO(_ => {
        console.log(x);
        return x;
    });
}

var cat = compose(map(print), readFile);

// 先得到一个 _value 为 _ => fs.readFileSync('file', 'utf-8') 的 IO
// 再变成一个 _value 为 compose(print,_ => fs.readFileSync('file', 'utf-8')) 的 IO。
// 注意这里实际上有执行 IO.map(print)
step1=cat("file") // step1 是个 IO,其 _value 为 compose(print,_ => fs.readFileSync('file', 'utf-8'))

// 执行 compose(print,_ => fs.readFileSync('file', 'utf-8')),
// 假设 fs.readFileSync('file', 'utf-8') 结果为 ‘fileContent’
// 得到一个 _value 为 _ => { console.log('fileContent');return 'fileContent';} 的 IO 
step2=step1._value() // step2 是个 IO

// 执行 _value,即执行 _ => { console.log('fileContent');return 'fileContent';},打印 'fileContent',得到字符串 'fileContent' 
step3=step2._value() // step2 是个字符串
```

注意到了吗，我们为了得到`cat('file')`的结果，要两次调用`_value()`，如果我们涉及到 100 个 **IO** 操作，那么难道要连续写 100 个 **__value()** 吗？

---

当然不能这样不优雅，我们来实现一个 **join** 方法，它的作用就是剥开一层 Functor，把里面的东西暴露给我们：

#### join

```js
var join = x => x.join();
IO.prototype.join = function() {
  return this.__value ? this.__value():IO.of(null);
}
```

那么对于上面那个 print 和 readfile 的需求，我们可以这么拿到结果。

```js
// join 相当于 一次 _value()
var cat = compose(join, map(print), readFile);
cat("file").__value()
```

我们不可能总是在 **map** 之后手动调用 **join** 来剥离多余的包装，否则代码会长得像这样：

```js
var doSomething = compose(join, map(f), join, map(g), join, map(h));
```

所以我们需要一个叫 **chain** 的方法来实现我们期望的链式调用，它会在调用 **map** 之后自动调用 **join** 来去除多余的包装，这也是 Monad 的一大特性：

#### chain

```js
var chain = _.curry((f, functor) => functor.chain(f));

// 注意这里的 map 是 IO 类型的 map,即 compose(f,this._value)
IO.prototype.chain = function(f) {
  return this.map(f).join();
}

// 现在可以这样调用了
var doSomething = compose(chain(f), chain(g), chain(h));

// 当然，也可以这样，这里的 someMonad 是 IO 类型的
someMonad.chain(f).chain(g).chain(h)

// 写成这样是不是很熟悉呢？
readFile('file')
    .chain(x => new IO(_ => {
        console.log(x);
        return x;
    }))
    .chain(x => new IO(_ => {
        // 对x做一些事情，然后返回
    }))
```

哈哈，你可能看出来了，**chain** 不就类似 Promise 中的 **then** 吗？是的，它们行为上确实是一致的（**then** 会稍微多一些逻辑，它会记录嵌套的层数以及区别 Promise 和普通返回值），Promise 也确实是一种函数式的思想。



总之就是，Monad 让我们避开了嵌套地狱，可以轻松地进行深度嵌套的函数式编程，比如IO和其它异步任务。
