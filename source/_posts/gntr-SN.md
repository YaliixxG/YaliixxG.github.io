---
title: Generator知识点
date: 2018-07-17 11:12:05
tags: Generator 
categories: web
---

# Generator 
Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。  
* Generator 函数有两个特征：  
  * `function`关键字与函数名之间有一个`星号`
  * 函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）  
  <!--more-->
  
        function* helloWorldGenerator() {
        yield 'hello';
        yield 'world';
        return 'ending';
        }

        var hw = helloWorldGenerator(); 
        hw.next()
        // { value: 'hello', done: false } value属性就是当前yield表达式的值，done属性为false，表示遍历还没有结束。

        hw.next()
        // { value: 'world', done: false }

        hw.next()
        // { value: 'ending', done: true }

        hw.next()
        // { value: undefined, done: true } done属性为true，表示遍历已经结束。   

上面代码定义了一个 Generator 函数`helloWorldGenerator`，它内部有两个`yield`表达式（`hello`和`world`），即该函数有三个状态：hello，world 和 return 语句（结束执行）。   
调用方法与普通函数一样，但是调用，函数并不执行，返回一个指向内部状态的指针对象，也就是遍历器对象。
必须调用遍历器对象的`next`方法，使得指针移向下一个状态。内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。  
换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。  
## yield 表达式  
由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。   
`yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。
## next 方法的参数  
`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。  

        function* foo(x) {
        var y = 2 * (yield (x + 1));
        var z = yield (y / 3);
        return (x + y + z);
        }

        var a = foo(5);
        a.next() // Object{value:6, done:false}
        a.next() // Object{value:NaN, done:false}
        a.next() // Object{value:NaN, done:true}

        var b = foo(5);
        b.next() // { value:6, done:false }
        b.next(12) // { value:8, done:false }
        b.next(13) // { value:42, done:true }  
  
上面代码中，第二次运行`next`方法的时候不带参数，导致 `y` 的值等于`2 * undefined`（即`NaN`），除以 `3` 以后还是`NaN`，因此返回对象的`value`属性也等于`NaN`。第三次运行`Next`方法的时候不带参数，所以`z`等于`undefined`，返回对象的`value`属性等于`5 + NaN + undefined`，即`NaN`。

如果向`next`方法提供参数，返回结果就完全不一样了。上面代码第一次调用b的`next`方法时，返回`x+1`的值`6`；第二次调用`next`方法，将上一次`yield`表达式的值设为`12`，因此`y`等于`24`，返回`y / 3`的值`8`；第三次调用`next`方法，将上一次`yield`表达式的值设为`13`，因此z等于`13`，这时`x`等于`5`，`y`等于`24`，所以`return`语句的值等于`42`。

注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。V8 引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。从语义上讲，第一个`next`方法用来启动遍历器对象，所以不用带有参数。  

`next`方法的参数，也可以向Generator 函数内部输入值  

        function* dataConsumer() {
        console.log('Started');
        console.log(`1. ${yield}`);
        console.log(`2. ${yield}`);
        return 'result';
        }

        let genObj = dataConsumer();
        genObj.next();
        // Started
        genObj.next('a')
        // 1. a
        genObj.next('b')
        // 2. b  

## for...of 循环       
`for...of`循环可以自动遍历 Generator 函数时生成的`Iterator`对象，且此时不再需要调用`next`方法。

        function* foo() {
        yield 1;
        yield 2;
        yield 3;
        yield 4;
        yield 5;
        return 6; //return语句返回的，不包括在for...of循环之中
        }

        for (let v of foo()) {
        console.log(v);
        }
        // 1 2 3 4 5  

除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是遍历器接口。这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。 

        function* numbers () {
        yield 1
        yield 2
        return 3
        yield 4
        }

        // 扩展运算符
        [...numbers()] // [1, 2]

        // Array.from 方法
        Array.from(numbers()) // [1, 2]

        // 解构赋值
        let [x, y] = numbers();
        x // 1
        y // 2

        // for...of 循环
        for (let n of numbers()) {
        console.log(n)
        }
        // 1
        // 2  

## Generator.prototype.throw()  
Generator 函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。 

        var g = function* () {
        try {
            yield;
        } catch (e) {
            console.log('内部捕获', e);
        }
        };

        var i = g();
        i.next();

        try {
        i.throw('a');
        i.throw('b');
        } catch (e) {
        console.log('外部捕获', e);
        }
        // 内部捕获 a
        // 外部捕获 b  

一旦执行了`catch`，捕捉了错误，Generator 函数就已经结束了，不再执行下去了。  

## Generator.prototype.return()  
Generator 函数返回的遍历器对象，还有一个`return`方法，可以返回给定的值，并且终结遍历 Generator 函数。  

## next()、throw()、return() 的共同点  
本质上是同一件事，可以放在一起理解。它们的作用都是让 Generator 函数恢复执行，并且使用不同的语句替换`yield`表达式。  
`next()`是将`yield`表达式替换成一个值。  

        const g = function* (x, y) {
        let result = yield x + y;
        return result;
        };

        const gen = g(1, 2);
        gen.next(); // Object {value: 3, done: false}

        gen.next(1); // Object {value: 1, done: true}
        // 相当于将 let result = yield x + y
        // 替换成 let result = 1;  

`throw()`是将`yield`表达式替换成一个`throw`语句。  

        gen.throw(new Error('出错了')); // Uncaught Error: 出错了
        // 相当于将 let result = yield x + y
        // 替换成 let result = throw(new Error('出错了'));  

`return()`是将`yield`表达式替换成一个`return`语句。  

        gen.return(2); // Object {value: 2, done: true}
        // 相当于将 let result = yield x + y
        // 替换成 let result = return 2;  

## yield* 表达式  
如果在 Generator 函数内部，调用另一个 Generator 函数，默认情况下是没有效果的。`yield*`表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数。

        function* foo() {
        yield 'a';
        yield 'b';
        }
        //普通方法调用foo() ==========================
        function* bar() {
        yield 'x';
        foo(); 
        yield 'y';
        }
        for (let v of bar()){
        console.log(v);
        }
        // "x"
        // "y"  
        //上面foo()的调用是没有效果的

        //yield*表达式调用 =================================
        function* bar() {
        yield 'x';
        yield* foo();
        yield 'y';
        }
        // 等同于
        function* bar() {
        yield 'x';
        yield 'a';
        yield 'b';
        yield 'y';
        }

        // 等同于
        function* bar() {
        yield 'x';
        for (let v of foo()) {
            yield v;
        }
        yield 'y';
        }

        for (let v of bar()){
        console.log(v);
        }
        // "x"
        // "a"
        // "b"
        // "y"
从语法角度看，如果`yield`表达式后面跟的是一个遍历器对象，需要在yield表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为`yield*`表达式。  


`yield*`后面的 Generator 函数（没有return语句时），等同于在 Generator 函数内部，部署一个`for...of`循环。

        function* concat(iter1, iter2) {
        yield* iter1;
        yield* iter2;
        }

        // 等同于

        function* concat(iter1, iter2) {
        for (var value of iter1) {
            yield value;
        }
        for (var value of iter2) {
            yield value;
        }
        }  
上面代码说明，`yield*`后面的 Generator 函数（没有`return`语句时），不过是`for...of`的一种简写形式，完全可以用后者替代前者。反之，在有`return`语句时，则需要用`var value = yield* iterator`的形式获取`return`语句的值。

#### 实际上，任何数据结构只要有 Iterator 接口，就可以被yield*遍历。  
`yield*`命令可以很方便地取出嵌套数组的所有成员。

        function* iterTree(tree) {
        if (Array.isArray(tree)) {
            for(let i=0; i < tree.length; i++) {
            yield* iterTree(tree[i]);
            }
        } else {
            yield tree;
        }
        }

        const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];

        for(let x of iterTree(tree)) {
        console.log(x);
        }
        // a
        // b
        // c
        // d
        // e 

## Generator 函数的this   
Generator 函数g返回的遍历器`obj`，是g的实例，而且继承了`g.prototype`。但是，如果把g当作普通的构造函数，并不会生效，因为g返回的总是遍历器对象，而不是`this`对象,也不能跟new命令一起用，会报错。  

下面是一个变通方法。首先，生成一个空对象，使用`call`方法绑定 Generator 函数内部的this。这样，构造函数调用以后，这个空对象就是 Generator 函数的实例对象了。  

        function* F() {
        this.a = 1;
        yield this.b = 2;
        yield this.c = 3;
        }
        var obj = {};
        var f = F.call(obj);

        f.next();  // Object {value: 2, done: false}
        f.next();  // Object {value: 3, done: false}
        f.next();  // Object {value: undefined, done: true}

        obj.a // 1
        obj.b // 2
        obj.c // 3  

还有一个办法就是将obj换成`F.prototype`。

        function* F() {
        this.a = 1;
        yield this.b = 2;
        yield this.c = 3;
        }
        var f = F.call(F.prototype);

        f.next();  // Object {value: 2, done: false}
        f.next();  // Object {value: 3, done: false}
        f.next();  // Object {value: undefined, done: true}

        f.a // 1
        f.b // 2
        f.c // 3  

再将F改成构造函数，就可以对它执行`new`命令了。

        function* gen() {
        this.a = 1;
        yield this.b = 2;
        yield this.c = 3;
        }

        function F() {
        return gen.call(gen.prototype);
        }

        var f = new F();

        f.next();  // Object {value: 2, done: false}
        f.next();  // Object {value: 3, done: false}
        f.next();  // Object {value: undefined, done: true}

        f.a // 1
        f.b // 2
        f.c // 3
