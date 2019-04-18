声明一个父类构造函数


```
function Parent(name, age) {
    this.name = name;
    this.info = {
        age: age,
        blood: 'B'
    };
};
Parent.prototype.say = function() {
    console.log(`age:${this.info.age}, blood:${this.info.blood}`)
}

```

#### 原型链继承
优点：父类原型的方法可以复用，所有子类都可以调用父类原型的方法  
缺点：  
1.父类的引用属性会被所有子类实例共享，修改一个子类实例的引用属性，其他子类属性都会被修改  
2.子类实例化时无法向父类构造函数传递参数

```

function Child(sex) {
    this.sex = sex
}

Child.prototype = new Parent('child', 15); //此处会修改Child.prototype.constructor的指向 将其指向Parent
Child.prototype.constructor = Child; //修正Child.prototype.constructor的指向 将其指向Child

var child = new Child('boy');
child.info.blood = 'A';  //修改child的blood child2的blood属性也会发生变化
var child2 = new Child('boy');

console.log(child.info.blood, child2.info.blood) // A A
console.log(child.say()) //age: 15 blood: A
```
#### 构造函数继承
优点：  
1.父类的引用属性不会被所有子类实例共享  
2.可以向父类构造函数传递参数  
缺点：  
父类原型的方法子类实例无法调用，因为call,apply 方法只针对父类的构造函数
```
function Child(sex, args) {
    this.sex = sex;
    Parent.apply(this, args)
}
var child = new Child('boy',['child', 13]); //可以向父类构造函数传参
child.info.blood = 'A';                     //child2的blood属性不会发生变化
var child2 = new Child('boy',['child2', 15]);

console.log(child.info.blood, child2.info.blood) // A B
console.log(child.say()) // child.say is not a function
```
#### 组合继承（将上述两种方法结合）
优点：解决了上述两种方法的缺点  
缺点：调用了两次Parent方法
```
function Child(sex) {
    this.sex = sex;
    Parent.apply(this, args)
};

Child.prototype = new Parent(); //此处会修改Child.prototype.constructor的指向 将其指向Parent
Child.prototype.constructor = Child; //修正Child.prototype.constructor的指向 将其指向Child

var child = new Child('boy',['child', 13]); //可以向父类构造函数传参
child.info.blood = 'A';                     //child2的blood属性不会发生变化
var child2 = new Child('boy',['child2', 15]);
console.log(child.info.blood, child2.info.blood) // A B
console.log(child.say()) //age: 13 blood: A

```
#### 寄生组合继承
原理:  
1.浅复制一个父类(Parent)的原型(childPrototype)  
2.将子类构造函数的prototype指向该原型(childPrototype)  
3.将该原型(childPrototype)的constructor指向子类构造函数(Child)  
4.实例化Child  

优点：解决上述方法所有缺点
```
function Child(sex) {
    this.sex = sex;
    Parent.apply(this, args)
};

var childPrototype = Object.assign( {}, Parent.prototype ); //浅复制父类原型
Child.prototype = childPrototype;
childPrototype.constructor = Child;

var child = new Child('boy',['child', 13]);
child.info.blood = 'A';                    
var child2 = new Child('boy',['child2', 15]);
console.log(child.info.blood, child2.info.blood) // A B
console.log(child.say()) //age: 13 blood: A


```
