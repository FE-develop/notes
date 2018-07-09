# 关于闭包

**闭包（closure）是函数和声明该函数的词法环境的组合**。昨天有个前端小伙伴分享了个前端面试题，我在这里记录下自己理解的过程，加深一些对闭包的理解哈。看了些很多人写的博客，主要总结了一下几个定义结论，这里我们直接来看题目吧。

1. JavaScript 变量作用域有两个范围：全局和局部。
2. JavaScript 不支持块范围（通过一组大括号 {...} 定义新范围），但块范围变量的特殊情况除外(如`let` `const`)。
3. 用`var`申明的变量实际上是有作用域的。如果一个变量在函数体内部声明，则该变量的作用域为整个函数体，在函数体外不可引用该变量。
4. 不同函数内部的同名变量互相独立，互不影响。
5. JavaScript的函数可以嵌套，此时，内部函数可以访问外部函数定义的变量，反过来则不行。

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

既然看到这个问题必然会追加提问: **如何改写上述代码，使运行输出结果为1, 2, 3**

通过上面的分析可以看出，输出结果时执行的代码`console.log(i)`在运行时在作用域中能访问到的`i`只有全局作用域的`i`的值，所以我们如果只是改变全局的`i`的值他们执行的结果肯定都是一样的输出。所以就必须每个函数fn必须有自己的作用域，并且在自己的作用域中有自己可访问到的`i`的值。解决这个问题可以通过下面两种方式：

+ 闭包: 为返回的函数创建一个自有的运行环境

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

+ `let`: 改变变量作用域的范围

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
3. [变量作用域 (JavaScript)](https://msdn.microsoft.com/zh-cn/library/bzt2dkta(v=vs.94).aspx)
4. [变量作用域与解构赋值](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014344993159773a464f34e1724700a6d5dd9e235ceb7c000)