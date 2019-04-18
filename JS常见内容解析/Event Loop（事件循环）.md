[详细的事件循环解释](https://yangbo5207.github.io/wutongluo/ji-chu-jin-jie-xi-lie/shi-er-3001-shi-jian-xun-huan-ji-zhi.html)


> **宏任务(Task)**：**script**(整体代码)， **setTimeout**， **setInterval**， **setImmediate**,，**I/O**， **UI rendering**;
>
>**微任务(jobs)**： **process.nextTick**， **Promise**（Promise内的函数如果没有微任务则会直接以宏任务的形式执行，而then内的函数会直接进入微任务）， **MutationObserver**，Object.observe(已废弃) ;
>
>**特别注意：++chrome环境下是执行一个宏任务，再去执行该宏任务下产生的微任务；而最近版本的node环境中是执行完所有的同源宏任务再去执行他们产生的微任务++**（见例1）
>
>**注**：
>1. 不同任务源的任务会进入到不同的任务队列，其中setTimeout与setInterval是同源的<br>
>2. setTimeout中的函数是作为第二轮宏任务执行,Promise内的函数是作为第一轮宏任务执行,then内的函数作为Promise队列内的微任务执行

**事件循环执行顺序**：
```
graph TD
A(执行第一轮宏任务)-->|setTimeout , setInterval , setImmediate|C(跳过函数执行 , 并将该函数加入下一轮宏对应任务源的任务队列中)
A-->|微任务|D(加入对应任务源的微任务队列中)
D-->E(第一轮宏任务执行完毕)
C-->E
E-->F(按任务源的顺序执行微任务队列中的微任务)
F-->|执行完所有微任务|G(执行第二轮的宏任务)
G-->|按照上面的顺序执行|H(...)
H-->J(执行完所有的宏任务和微任务)
J-->K(出栈)

```

例1
```
console.log('1');

setTimeout(function() {
    console.log('2');
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
});

//chrome环境下会打印 1 7 8 2 4 5 9 11 12
//node环境下会打印 1 7 8 2 4 9 11 5 12
```
