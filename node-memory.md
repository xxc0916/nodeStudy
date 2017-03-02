## 内存释放

> Javascript 中不同类型以及不同环境下变量的内存都是何时释放?

引用类型是在没有引用之后, 通过 v8 的 GC 自动回收, 值类型如果是处于闭包的情况下, 要等闭包没有引用才会被 GC 回收, 非闭包的情况下等待 v8 的新生代 \(new space\) 切换的时候回收.

与前端 Js 不同, 2年以上经验的 Node.js 一定要开始注意内存了, 不说对 v8 的 GC 有多了解, 基础的内存释放一定有概念了, 并且要开始注意内存泄漏的问题了.

你需要了解哪些操作一定会导致内存泄漏, 或者可以崩掉内存. 比如如下代码能否爆掉 V8 的内存?

```
let
 arr 
=
 [];

while
(
true
)

arr
.
push
(
1
);
```

然后上述代码与下方的情况有什么区别?

```
let
 arr 
=
 [];

while
(
true
)

arr
.
push
();
```

如果 push 的是`Buffer`情况又会有什么区别?

```
let
 arr 
=
 [];

while
(
true
)

arr
.
push
(
new
Buffer
(
1000
));
```

思考完之后可以尝试找找别的情况如何爆掉 V8 的内存. 以及来聊聊内存泄漏?

```
var
 theThing 
=
null
var
replaceThing
=
function
 () {

var
 originalThing 
=
 theThing

var
unused
=
function
 () {

if
 (originalThing)

console
.
log
(
"
hi
"
)
  }
  theThing 
=
 {
    longStr
:
new
Array
(
1000000
).
join
(
'
*
'
),

someMethod
:
function
 () {

console
.
log
(someMessage)
    }
  };
};

setInterval
(replaceThing, 
1000
)
```

比如上述情况中`unused`的函数中持有了`originalThing`的引用, 使得每次旧的对象不会释放从而导致内存泄漏 \(例子出自[《Node.js 垃圾回收》](https://eggggger.xyz/2016/10/22/node-gc/\)\)

当然对于一些高水平的同学, 要求能清楚的了解 v8 内存 GC 的机制, 懂得内存快照等 \(之后会在`调试/优化`的小结中讨论\) 了. 比如 V8 中不同类型的数据存储的位置, 在内存释放的时候不同区域的不同策略等等.

[https://eggggger.xyz/2016/10/22/node-gc/](https://eggggger.xyz/2016/10/22/node-gc/)

[http://www.jianshu.com/p/996671d4dcc4?hmsr=toutiao.io&utm\_medium=toutiao.io&utm\_source=toutiao.io](http://www.jianshu.com/p/996671d4dcc4?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

[http://developer.51cto.com/art/201605/511624.htm](http://developer.51cto.com/art/201605/511624.htm)

