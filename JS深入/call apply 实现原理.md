## 模拟实现call apply

###### <font size=4>语法：
call：
> ```
> fun.call(thisArg, arg1, arg2, ...)
> thisArg =>  fun函数运行时的this值，
>             非严格模式下可为null,undefined,此时this值会自动指向全局对象,
>             值为原始值(数字，字符串，布尔值)的this会指向该原始值的自动包装对象。
> arg1 arg2... => fun函数运行时的 参数列表
>```
apply：
> ```
> fun.apply(thisArg,[arg1, arg2, ...])
> thisArg =>  参照call
> [arg1 arg2...] => fun函数运行时的 参数数组
>```

###### <font size=4>举个栗子：
```
fun1= function(a,b){
    console.log(a,b,this.value);
    return a;
};
obj = {
    value : 3
};
fun1.call(obj,1,2)  //结果会打印'1','2','3' 说明call方法为obj新增了fun1的方法并且运行了fun1方法
console.log(obj)    //{valu:3}  obj中没有fun1方法，说明运行完该方法后删除了该方法
```

###### <font size=4>解析：
```
fun1.call(obj,1,2) 执行过程可看作以下四个步骤
1. 将fun1方法绑定到obj内 obj.fn = fun1
    obj = {
        value : 3,
        fn : function(a,b){
            console.log(a,b,this.value);
            return a;
        }
    }
2. 执行obj内的fun1方法 obj.fn(1,2)
3. 删除obj.fn
4. 如果fun1内有return值返回 此处也需要将fun1执行的结果返回
```
###### <font size=4>模拟实现call：
```
Function.prototype.call_copy = function(context,arg1,arg2) {
    context = context || window;        //因为context可能为null或者undefined 此时的this应指向window
    let args = [];          
    for(let i=1;i<arguments.length;i++){
        args.push(arguments[i])
    };                                  //获取函数内的参数数组
    context.fn = this;                  //为当前上下文绑定函数，此处的this代表调用call方法的函数
    let result = context.fn(...args);   //运行绑定的方法，且改变this指向到context
    delete context.fn;                  //删除该方法的属性
    return result;                      
}
```
###### <font size=4>模拟实现apply：
```
Function.prototype.apply_copy = function(context,arr) {
    context = context || window;        //因为context可能为null或者undefined 此时的this应指向window
    let args = arr || ［］;             //获取函数内的参数数组
    context.fn = this;                  //为当前上下文绑定函数，此处的this代表调用apply方法的函数
    let result = context.fn(...args);   //运行绑定的方法，且改变this指向到context
    delete context.fn;                  //删除该方法的属性
    return result;                      
}
```
###### <font size=3>[参考来源：JavaScript深入之call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)


