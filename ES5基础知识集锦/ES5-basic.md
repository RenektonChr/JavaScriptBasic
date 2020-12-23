# ES5基础知识集锦（持续更新，如有错误，欢迎指正。。。）

## 变量声明提升与函数声明提升
```javascript
if(false) {
  var a = 0
}
console.log(a)
```
思考一个问题：控制台会输出什么？  
答案揭晓：会输出  undefined
  
以上这段代码会被JS解释为如下形式：
```javascript
var a;
if(false) {
  a = 0
}
console.log(a)
```
形如这样无论变量在何处定义都会先在代码解释初期先声明，后赋值，这样的现象就叫做变量声明提升。  
变量声明提升的定义（出自JavaScript高级程序设计）：**变量的声明都将被提升到函数的最顶部。**  
  
```javascript
func()
function func() {
  console.log('hello world')
}
```  
思考一个问题：func()会不会成功被执行？  
答案是肯定的：会！！！  
这是怎么回事儿？原因是函数和变量一样会被声明提升，所以函数声明提升的定义和变量声明提升的定义是相同的：**函数的声明都将被提升到函数的最顶部**  
那么以上的代码就会被解释成如下：  
 
```javascript
function func() {
  console.log('hello world')
}

func()
```  
* 声明提升：**变量和函数的声明都将被提升到函数的最顶部**
  
解释清楚了变量和函数的声明提升，我们开始进入正题，请看下面的一个经典面试题  
```javascript
console.log(a);
a();
var a = 3;
function a() {
  console.log(10)
}
```
 
仔细看这段代码你会发现一个好玩的事儿，这段代码中同事出现了变量声明提升和函数声明提升，那么现在问题来了，JS解释器会怎样解释这段代码呢？同时存在函数声明提升和变量声明提升，哪一个提升会更靠前？两种提升同名会怎么处理？  
 
下面我们来看上面这段代码的等价代码：  
 
```javascript
function a() {
  console.log(10)
}
var a;
console.log(a);
a();
a = 3;
```
 
看到这里，我们可以得出一个结论：**当代码中同时出现变量声明提升和函数声明提升时，函数声明提升比变量声明提升更加靠前！**  
 
现在我们就回答了前面提出的问题。现在我们来看等价代码会发现第二个问题：被提升的变量名和函数名竟然同名，两种提升同名会怎么处理？那么我们只需要看控制台输出了什么就OK了。  
 
答案揭晓：function a() { console.log(10) }  
 
这里得出了一个和我们思维方式不同的结果，按照代码的执行逻辑，两种声明都提升了，并且同名的情况下，变量声明又在函数声明之后，那么函数a的声明应该被变量a的声明覆盖，控制台应该输出undefined才对，为什么会打印出函数a的函数体？
 
这里我们解释一下出现这种怪异情况的原因：**当代码中同时出现变量声明提升和函数声明提升时，并且两种声明提升同名，在变量没有被赋值的情况下，函数声明优先级更高，会优先于变量声明生效！**  
 
那么我们会有这么一个疑问，如果变量声明提升之后再被赋值呢？  
我们手动改一下等价代码，来求证一下这个问题
```javascript
function a() {
  console.log(10)
}
var a;
a = 'renekton'
console.log(a);
a();
a = 3;
```  
 
控制台会输出：'renekton'  
并且a()会报错，这个报错就很好理解了，因为a现在是一个字符串，并不是一个函数，自然会报错Uncaught TypeError: a is not a function  
 
这里要特别声明一下，在日常我们编程的时候声明提升之后手动赋值的情况并不会出现，我们这里只是为了求证当下提出的问题，毕竟谁都不会写出形如上面我们手动修改的代码，这段代码实在太别扭！
 
再次阅读上面声明提升之后的等价代码，我们会发现一个好玩儿的事儿，那就是变量声明提升和函数声明提升有一个本质的不同，那就是变量声明提升只是提升了变量的声明，变量的赋值操作并没有被提升，而是赋值位置不变，但是函数声明提升则是整个都提升了，这是怎么回事儿？？我们下面就来解答这个问题。  
 
上面这个问题是因为JS遵从此法作用域，通俗的讲就是不会改变每一行的语义，所以这里的变量声明虽然提升，但是赋值语句会被留在原来的位置。至于函数声明，函数的声明就只是声明，而且这里使用的是函数的声明式语法来声明函数，所以会全部都提升。
 
现在看一下这个面试题的加强版  
 
```javascript
console.log(a);
a();
var a = 3;
function a() {
  console.log(10)
}
console.log(a)
a = 6
a()
```
 
这次加强版的题目比普通版的多了几行代码，还是老办法，我们先给这个代码进行变形，看看JS是如何解释他的：  
 
```javascript
function a() {
  console.log(10)
}
var a;
console.log(a);
a();
a = 3
console.log(a)
a = 6
a()
```
  
分析一下变形之后的代码：由于变量和函数的声明提升且a没有被赋值，所以console.log(a)打印的是函数a，现在a是一个函数，执行它会在控制台打印出10，然后a被赋值为3，现在的a是数字3，console.log(a)会在控制台打印出3，随后a被赋值为6，此时a仍然为数字类型，此时执行a，会报错Uncaught TypeError: a is not a function  
 
我们现在需要把变形之前的原题在浏览器中跑一下，验证我们对声明提升和刚才的分析。  
 
毫无疑问我们是对的~~
 
  
接下来还没完，我们对于这个题进行一个扩展，先看一个扩展题：  
 
```javascript
var foo = function func(num) {
  func = num;
  console.log(typeof func)
  return 1;
}
foo(1)
console.log(typeof func())
```
 
对于这段代码，实际上可以提出两个比较明显的问题：  
+ foo函数中打印的结果是什么？
+ foo函数外部的打印结果是什么？
 
答案：foo函数内部打印的结果是'function'，foo函数外部的打印语句会报错 Uncaught ReferenceError: func is not defined  
 
下面分析并解释一下，为什么会是这个结果：  
首先foo函数中的打印结果为什么会是'function',因为foo是一个具名函数表达式，但是func这个函数名只能在函数体内部才能访问的到，在外部这个函数表达式实际上和匿名函数表达式没有什么不同，并且在函数内部，func是**只读**的，所以对func进行赋值是无效的，这就解释了为什么typeof func得到的是'function'。  
第二个问题，为什么函数外部的打印会报错，解释第一个问题的时候我们就提到了，func只能在函数内部访问到，所以在函数外部执行它，肯定是会报错的。



## this的指向问题（重中之重）
废话不多说，先看一段代码（也还是一个经典的面试题）  
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
};
test.init();
```
+ 普遍规律this谁调用指向谁
 
分析：根据以上的普遍规律我们可以出执行test.init();此时的a为40。  
 
现在我们将上面的代码做一次扩展  
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
};

var fn = test.init;
fn();
```
 
分析一下上面的代码：创建一个变量fn，并为之赋值test.init，并执行fn()。在全局作用域下执行fn()，等价于执行window.fn()，this.a等价于window.a，由此可得出this.a为20。这也印证了this谁调用指向谁的普遍规律。  
 
对代码再一次做扩展  
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    function go() {
      console.log(this.a)
    }
    go()
  }
};

test.init();
```  
 
上面的代码，打印出来的this.a为20，也就是说this指向window。说明同样验证了上文说到的普遍规律。  
 
对以上代码再次进行变形：  
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    function go() {
      console.log(this.a)
    }
    return go;
  }
};

var s = test.init();
s();
```
 
上面的代码执行结果this.a仍然是20，也就是说this仍然指向window，还是那句话this的指向是谁调用指向谁。
 
说完了普遍情况，我们还要说一些特殊的情况。。。  
 
 ```javascript
 function test(a) {
   this.a = a;
 }
 test.prototype.a = 20;
 test.prototype.init = function() {
   console.log(this.a)
 }
 var s = new test(30);
 s.init()
 ```
  
先揭晓答案：控制台会打印30  
 
分析：new test(30)得到一个实例化对象，我们都知道一旦给构造函数加上new操作符，函数内部的this就指向构造函数返回的实例，又构造函数返回的实例会覆盖原型链上的值，所以打印this.a会得到30。那么我们思考一个问题，如果我们不给构造函数传值，this.a会是多少？  
 
```javascript
 function test(a) {
   this.a = a;
 }
 test.prototype.a = 20;
 test.prototype.init = function() {
   console.log(this.a)
 }
 var s = new test();
 console.log(s)
 s.init()
```  
 
形如上面的代码我们就没给构造函数传值，但是this的指向并没有变，仍然指向实例，由于没有给构造函数传值，那么构造函数中实际执行的语句就会变为this.a = undefined;，所有控制台会打印undefined！   
 
+ 总结：其实说白了this的指向到这里还是谁调用指向谁，关键是在遇到new操作符的时候，this的指向会硬性的绑定在实例上，所以当this遇到new操作符的时候就应该引起我们的重视。可能会有坑！  
 
接下来我们要来看一个神奇的问题，一个分号引起的血案！废话不多说，我们上代码：  
 
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
}
(function() {
  var fn = test.init;
  fn();
})()
```
 
相信很多前端人在这里都会采坑，这段代码实际上会报错。。。  
 
我们来还原一下上面这段代码的实际解释结果就会明白为什么报错了：  
 
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
}(function() {
  var fn = test.init;
  fn();
})()
```
 
JS会把最后一个括号之前的所有代码当做函数体，所以显然他不是一个函数，报错也在情理之中（这里作者本人在看到的时候也是心里MMP）。  
 
那么怎么解决这个错误呢？方法很简单在test对象后面加分号。
  
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
};
(function() {
  var fn = test.init;
  fn();
})()
```  
 
那么问题来了，这段代码this.a会是多少？？  
 
答案很明显：this.a为20  
 
接下来，我们看一下箭头函数对于this的影响：  
 
```javascript
this.a = 20;
var test = {
  a: 40,
  init: () => {
    console.log(this.a)
  }
}
test.init()
```
 
这道题如果按照ES5的解析方式，那么毫无疑问this的指向是谁调用指向谁。所以this.a为40。但是，这道题的答案this.a却为20。  
 
分析：我们对比一下代码就会知道，上面的代码和之前的代码有一个明显的区别，就是init函数是一个ES6的箭头函数，箭头函数和ES5的函数一个最大的不同就是箭头函数会绑定父级作用域！所以当执行test.init()的时候init函数内的this指向的是全局作用域也就是window，那么init函数内的this.a自然就为20。  
 
思考一个问题，如果用ES5的写法怎么实现ES6箭头函数的效果呢？  
答案是显然的：使用bind改变原有的this指向，使其指向父级  
 
```javascript
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a)
  }
}
var s = test.init.bind(this)
s()
```  
 
现在我们知道了bind的作用，可以看一个面试题测试一下：  
 
```javascript
var s = {
  a: function() {
    console.log(this);
  },
  b() {
    console.log(this);
  },
  c: () => {
    console.log(this);
  }
}
s.a();
s.b();
s.c();
```
