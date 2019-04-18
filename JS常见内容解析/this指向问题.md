#### 根据ECMAScript规范分析函数内this指向问题：
<font size=3>首先，需要明白以下概念<br>


ECMAScript规范中的类型体系分为**语言类型**和**规范类型**。<br>

**语言类型** 包括
- Undefined
- Null
- Boolean
- String
- Symbol
- Number
- Object

**规范类型** 包括
- Reference(引用)
- List(列表)
- Completion(完结)
- Property Descriptro(属性描述)
- Property Identifier(属性标识)
- Lexical Environment(词法环境)
- Environment Record(环境记录)
<br>
> **Reference**是属于规范类型中的一类，用来解释 delete 、 typeof 、赋值运算符、 super 关键字等语言特性的行为，由三部分组成：
> - **base(基值)**   可以是undefined, object, boolean, string, number, environment record中的任意一个
> - **referenced name(引用名称)**   字符串
> - **strict reference(严格模式引用标志)**<br>

> **Lexical Environment**是一个用于定义特定变量和函数标识符在 ECMAScript 代码的词法嵌套结构上关联关系的规范类型，包括两部分：
> - **Environment Record(环境记录)**：记录相应环境中的形参，函数声明，变量声明（可参考变量对象中的函数上下文）
> - **Out Reference(外部词法环境引用)**：对外部环境的引用（可理解为父级环境）



**左值表达式**：运算符号左边的表达式。（此处可理解为表达式中 位于最右边'()'左边的一串表达式）

```
例：
var val = 'window';
var foo = {
    val : 'foo',
    bar : function() {
        console.log(this.val)
    }
};
var fun = foo.bar;
fun();      //此处的左值表达式为fun
foo.bar();  //此处的左值表达式为foo.bar
```




判断this的步骤：
1. 先找到左值表达式，令其计算结果为ref
2. 通过[ECMAScript规范](http://yanhaijing.com/es5/#115)判断ref是否为reference类型
3. 如果ref不是reference类型，this的值为undefined，非严格模式下指向window
4. 如果ref是reference类型:
    - base的值是对象或者boolean,number或者string 则this指向ref的base
    - base的值是environment record，则this值为undefined，非严格模式下指向window

常见左值表达式的reference类型判断：
1. 左值表达式为函数名，例如foo(),返回的一个reference类型，base值为environment record，this值为undefined
2. 左值表达式为对象，例如foo.bar(),返回的一个reference类型，base值为foo，this值为base，也就是foo
3. 左值表达式为构造函数实例，例如new foo.bar()，会先执行new foo.bar,返回一个实例对象，此时foo.bar函数内的this会指向该实例对象
4. 左值表达式为匿名函数，例如(function(){console.log(this)})，返回的不是一个reference类型，this值为undefined