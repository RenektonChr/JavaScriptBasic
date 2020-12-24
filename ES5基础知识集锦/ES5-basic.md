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
 
稍微的解释一下这段代码：函数a是标准ES5的写法，执行s.a()，this指向s这是毫无疑问的，函数b的写法可以暂时看做ES5写法的简便写法（注意这里只是暂时可以这么认为，不同点我们以后会说），执行s.b()，this指向s，这和ES5的标准写法相同，唯一不同的是函数c，函数c是箭头函数，箭头函数的this会被硬性绑定到父级作用域上，执行s.c，this指向window。  
 
弄清楚了这一点我们在对于上面的代码进行扩展：  
 
```javascript
this.test = 11;
var s = {
  a: function() {
    console.log(this.test + 1);
  },
  b() {
    console.log(this.test);
  },
  c: () => {
    console.log(this.test);
  }
};

var f = s.a.bind(this);
new f();

var p = s.b.bind(this);
new p();

var q = s.c.bind(this);
new q();
```
 
大家猜一下，上面代码的执行结果是什么？  
 
答案揭晓：执行new f();，控制台会打印 NaN。执行new p();控制台会报错，Uncaught TypeError: p is not a constructor。  
 
分析：  
+ 首先看函数a和函数b的不同，一个是ES5的写法，一个是简便写法（注意这里我没有说是‘ES5的简便写法’）。接下来都给函数绑定了this，然后都对于函数f和函数p使用了new操作符。接下来我们先分析函数a，注意这里给函数a绑定了this，但是又new了函数a，那么函数a中的this指向会发生变化，this的最终指向的是实例（这一点要切记）！所以在实例上是没有test属性的，所以函数a中的this.test为undefined，undefined + 1为NaN。
+ 我们在说一下new p()为什么报错，一句话解释清楚就是ES6的这种函数的简便写法不支持new操作符，所以会报错Uncaught TypeError: p is not a constructor。  
 
说到这里，有个疑问还没有解决：我们明明在new f()之前给s.a绑定了this，为什么a函数中的this没有指向window呢？  
 
一句话解释清楚这个问题：因为有new操作符的存在，new操作符会把this强行指向实例，有bind也没用，new操作符的作用要比bind强！  
 
到现在为止，上面的那个面试题我们就完全解释完了。下面我们来总结一下：  
+ ES6函数的写法（包括箭头函数和简便写法两种）都不支持new操作符。
+ new操作符的作用要强于bind，new操作符会硬性的改变this的指向，使其指向实例，有没有bind都一样。
+ 至此，可以改变this指向的方法有三种：
  + 箭头函数
  + bind函数
  + new操作符
+ ES6函数的简便写法不仅仅是简单，要注意Uncaught TypeError: xxx is not a constructor  
 
现在我们再来看一个扩展题：  
 
```javascript
function C1(name) {
  if (name) {
    this.name = name;
  }
}

function C2(name) {
  this.name = name;
}

function C3(name) {
  this.name = name || 'Naruto';
}

C1.prototype.name = "renekton";
C2.prototype.name = "cui";
C3.prototype.name = "haoran";
console.log((new C1().name) + (new C2().name) + (new C3().name))
```
 
答案揭晓：控制台会打印 renektonundefinedNaruto  
 
分析：  
+ new C1().name：我们先看函数C1，如果name为真值，则执行this.name=name; 由于new C1()并没有传参，这就造成了new C1()之后，得到的实例上并没有name属性，然后JS的解释规则就会去构造函数的原型上去找name属性，C1的原型上name属性为“renekton”。
+ new C2()：C2与C1的不同之处在于没有参数为真的判断，不管参数有没有都会给this.name赋值，没有传参数的情况下name为undefined，所以new C2()返回的实例上是有name属性的，只不过name属性值为undefined。
+ new C3()：C3函数的不同在于，如果不传参数name，会有一个默认值“Naruto”赋值给this.name，所以new C3()得到的实例上是有name属性的并且name属性值为“Naruto”。
+ 综上所述：控制台会打印 renektonundefinedNaruto
 
再来看一个面试题：  
```javascript
var a = 20;
{
  a = 50;
  let a = 40;
}
```
 
答案揭晓：上面的代码会报错Uncaught ReferenceError: Cannot access 'a' before initialization  
  
分析：  
+ 这是由于暂时性死区的原因报错，在ES6中一个花括号就是一个代码块，也就是块级作用域，在块级作用域中用let声明一个变量a，那么let操作符就会劫持整个代码块，在a声明之前如果使用a，则会报错，这时候虽然代码块之外已经var a了也是不行的。  
+ 关于暂时性死区，可以看一下阮一峰的[ES6入门](https://es6.ruanyifeng.com/#docs/function)  
 
来看一道经典的面试题：  
```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>6</li>
</ul>

<script>
// 如何将这段代码改写，点击每一个li元素时，输出li元素中的数字？
var list_li = document.getElementsByTagName("li");
for(var i = 0; i < list_li.length; i++) {
  list_li[i].onclick = function() {
    console.log(i)
  }
}
</script>
```  
 
分析：  
+ 这个面试题相当经典了，他的问题出在了当你点击的时候，脚本中的代码已经执行完了，i已经变成了6，所以不论你点击哪个li，打印出来的都是6。
+ 出现这种问题的原因就是JS没有块级作用域，所以i是个全局变量，有且只有一个i，最后递增到了6，所以程序只能读到i为6。
+ 解决这个问题的一个思路就是，给JS添加上局部作用域，保存每一次循环i的值！
+ 很自然的我们能想到两个办法闭包和es6的let声明。
 
答案已经出来了，其实这并不是全部的解题方法，可以想想还有没有别的？  
 
答案是肯定的，还有一种方法就是利用this
 
下面我们就用这三种方法分别实现：  
 
 ```html
 <!-- JS 闭包 -->
 <ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
  </ul>
  <script>
    var list_li = document.getElementsByTagName("li");
    for(var i = 0; i < list_li.length; i++) {
      list_li[i].onclick = (function(i) {
        return function () {
          console.log(i + 1)
        }
      })(i)
    }
  </script>
 ```  
  
```html
<!-- JS this -->
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>6</li>
</ul>

<script>
var list_li = document.getElementsByTagName("li");
for(var i = 0; i < list_li.length; i++) {
  list_li[i].onclick = function() {
    console.log(this.innerText)
  }
}
</script>
```  
 
```html
<!-- JS let -->
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>6</li>
</ul>

<script>
var list_li = document.getElementsByTagName("li");
for(let i = 0; i < list_li.length; i++) {
  list_li[i].onclick = function() {
    console.log(i + 1)
  }
}
</script>
```  
 
上面这道面试题实际上是老生常谈，放在这里就是要再次说明一下this的指向问题。还是那句话this谁调用指向谁。this的这种作用域引用有的时候能够化腐朽为神奇。  
 
再来看一个面试题：  
 
```javascript
function test(m) {
  m = {
    v: 5
  }
}
var m = {
  k: 30
};

test(m)
console.log(m.v)
```  
 
答案揭晓：m.v是undefined。  

分析：  
+ 首先我们先明确一个问题，函数的传参是按值传递，并不是按引用传递！
+ 需要澄清的第二个问题传进去的是栈内存中的值！
+ 所以函数外面的m和函数的参数m，并没有任何关系，只是指向了同一个对象，但是在函数内部m重新赋值了m = { v: 5 }，所以外部的m.v是肯定访问不到的。
 
现在我们对于上面的面试题做一下扩展：  
 
```javascript
function test(m) {
  m.v = 5
}
var m = {
  k: 30
};

test(m)
console.log(m.v)
```  
 
分析：
+ 与上一题不同的是，函数内部m并没有重新赋值，只是增加了一个属性，m中存储的地址还是和外部的m指向同一个地址。
+ 所以外部的m.v为5。
+ 但是我们要清楚一个概念，函数外部的m和函数参数的m并没有关系！！他们只是都指向同一个堆地址，也就是指向同一个对象。
+ 参数m和外部的m，变量名一不一样都不影响最终的结果，还是那句话函数的参数是按值传递。  
 
下面我们来看一个非常牛逼的面试题，可以说这道面试题可以帮你干翻面试官：  

```javascript
function func() {
  console.log(1);
}
(function () {
  if(false) {
    function func() {
      console.log(2);
    }
  }
  func();
})();
```
 
揭晓答案：这段代码在不同的浏览器中的执行结果不一样！有三种情况:
+ 1
+ 2
+ 报错
 
分析：  
+ IE6、IE7，if中的func会提升到函数作用域上，打印为2
+ FireFox的少数版本浏览器认为函数提升不合理，这题打印为1
+ 现代浏览器会报错Uncaught TypeError: func is not a function
 
这里写一下现代浏览器是怎么解释这段代码的：  
 
```javascript
function func() {
  console.log(1);
}
(function () {
  var func;
  if(false) {
    function func() {
      console.log(2);
    }
  }
  func();
})();
```  
 
现代浏览器居然是这么解释的，函数声明提升值提升了一个变量，这时候func是undefined，所以会报错Uncaught TypeError: func is not a function。  
 
还有一个和浏览器相关的是[, ,].length，这是因为浏览器对于逗号的解析不一样。  
 
再来看一个面试题：
 
把'abc'，转换成数组，不能使用split。  
解决方法：  
+ Array.from()
+ [...new Set('abc')]
 
 
  
 
## JS的继承（面试必问）
 

