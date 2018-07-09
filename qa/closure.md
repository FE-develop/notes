# 关于闭包

闭包（closure）是函数和声明该函数的词法环境的组合。昨天有个前端小伙伴分享了个前端面试题，我在这里记录下自己理解的过程，加深一些对闭包的理解哈。

## 题1：问下面的代码运行结果如何？

```javascript
var data = [];
for (var i = 0; i < 3; i++) {
  data[i] = function() {
    console.log(i);
  }
}
data[0]();
data[1]();
data[2]();
```

执行过程分析：

```javascript
// 保证代码等价，简单改写主体代码
var i = 0;  // 这里全局定义一个了 i，后面的 i++ 操作都是这个 i
for (; i < 3; i++) {
  data[i] = function() {
    console.log(i);
  }
}

/** 代码运行到这里(for循环执行完毕), 此时值的情况如下:
=> 全局作用域中定义的 i 此时已经变成了 3
=> data = [fn, fn, fn];  fn是个方法指向了一个内存地址，注意只是定义，但没有执行哦

fn的值为
fn = function() {
  console.log(i);  // 这里的 i 会找到能访问到的离自己最近的作用域中的 i
}

data[0](); // 执行fn函数, 此时fn中函数体中能访问到的 i 只有全局定义的，此时全局定义的 i 已经变成了 3, 所以输出的结果为 3.
data[1](); // 同理 这里的输出也为 3
data[2]();

结果: 3, 3, 3
*/
```

**追加提问: 如何改写上述代码，使运行输出结果为1, 2, 3**

通过上面的分析可以看出，输出结果时执行的代码`console.log(i)`在运行时在作用域中能访问到的`i`只有全局作用域的`i`的值，所以我们如果只是改变全局的`i`的值他们执行的结果肯定都是一样的输出。所以就必须每个函数fn必须有自己的作用域，并且在自己的作用域中有自己的`i`的值。解决这个问题可以通过下面两种方式：

+ 闭包

  在为`data[i]`赋值的时, 他是一个自执行函数运行的结果(一个匿名函数), 这个函数执行时依赖一个值i。 在每轮循环时候 i 的值不一样，所依赖访问到的 i 也不同。

  ```javascript
  var data = [];
  for (let i = 0; i < 3; i++) {
    data[i] = (function(n) {
      return function() {
        console.log(n);
      }
    })(i)
  }
  data[0](); // 0
  data[1](); // 1
  data[2](); // 2
  ```

+ let

  ```javascript
  var data = [];
  for (let i = 0; i < 3; i++) {
    data[i] = function() {
      console.log(i);
    }
  }
  data[0]();  // 0
  data[1]();  // 1
  data[2]();  // 2
  ```

学习参考

1. [闭包-MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)。
2. [学习Javascript闭包Closure](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)。