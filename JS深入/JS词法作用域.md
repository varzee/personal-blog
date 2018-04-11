<font size=3>**javascript采用词法作用域，又称静态作用域。**<br>
> **静态作用域**：可以理解为函数定义完成以后，就已经决定了作用域的范围，不会因为调用位置的改变而改变。<br>

通过两个例子了解一下：
```
例一：
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();  //结果会打印出什么？
```

<br>修改代码如下：
```
例二：
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    value = 2;
    foo();
}

bar();  //又会打印出什么？
```
> ### 解析
> 1：例一会打印1，是因为foo函数在定义的时候，作用域已经确定（此例中是window，foo函数内的value即是window.value），即使在bar函数作用域内调用foo函数，依然会从foo定义时所在的作用域中寻找value的值。
> 
> 2：例二会打印2，因为在执行foo函数之前，已经改变了foo函数原有作用域的value值（即window.value=2）