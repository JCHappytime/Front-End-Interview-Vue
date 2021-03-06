## 前言
这是几乎每场面试时面试官都会问到的问题，所以重要性可以毫无疑问的排第一。同时也是需要牢记在心的一个问题，它是我们在前端开发过程中几乎每天都会用到的基础点。

## 参考文章
1. [Javascript的基本数据类型分析和判断](https://juejin.cn/post/6844903623231537159)
2. [判断JS数据类型的四种方法(非常经典，需要牢记)](https://www.cnblogs.com/onepixel/p/5126046.html)

    js可以分为两种类型的值，一种是基本数据类型，一种复杂数据类型。
    两种类型的主要区别是它们的存储位置不同，基本数据类型的值直接保存在栈中，复杂数据类型的值保存在堆中，
    通过使用在栈中保存对应的指针来获取堆中的值。

- 栈：基本数据类型（Undefined, Null, Boolean, Number, String, Symbol（ES6新增）, BigInt（ES10新增））
  （1）Symbol 代表创建后独一无二且不可变的数据类型，它的出现我认为主要是为了解决可能出现的全局变量冲突的问题。
   （2）BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这
   个数已经超出了 Number 能够表示的安全整数范围。
- 堆：引用数据类型（Object, Array，Function）
以上两种类型的区别是：存储位置不同。
1. 基本数据类型直接存储在栈（Stack）中的简单数据段，占据空间小，大小固定且是频繁使用的数据。
2. 引用数据类型存储在堆（Heap）中的对象，占据空间大，大小不固定。但是如果存储在栈中，将会影响程序运行的性能；
    引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器找到引用值时，首先检索其在栈中的地址，
    取得内存地址后从堆中获得实体。

## undefined和null又有什么区别呢？
- 相同点
首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。
- 不同点
1. undefined 代表的含义是未定义，null 代表的含义是空对象。一般变量声明了但还没有定义的时候会返回 undefined，
null主要用于赋值给一些可能会返回对象的变量，作为初始化。
2. undefined 在 js 中不是一个保留字，这意味着我们可以使用 undefined 来作为一个变量名，但这样的做法是非常危险
    的，它会影响我们对 undefined 值的判断。但是我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。
3. 当我们对两种类型使用 typeof 进行判断的时候，Null 类型化会返回 "object"，这是一个历史遗留的问题。当我们使用
    双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

## 数据类型的判断
主要有以下四种方法：
### 1. typeof
typeof 是一个操作符，其右侧跟一个一元表达式，并返回这个表达式的数据类型。返回的结果用该类型的字符串(全小写字母)形式表示，包括以下 7 种：number、boolean、symbol、string、object、undefined、function 等。
```
typeof '';// string 有效
typeof 1;// number 有效
typeof Symbol();// symbol 有效
typeof true;//boolean 有效
typeof undefined;//undefined 有效
typeof null;//object 无效
typeof [] ;//object 无效
```
有的时候，typeof会返回一些令人不解但技术上却正确的结果：
- 对于基本类型，除了null以外，均可以返回正确的结果；
- 对于引用类型。除了function，均返回object类型；
- 对于null，返回object类型；
- 对于function，返回function类型。
其中，null有属于自己的类型Null，引用类型中的数组、日期、正则也都有属于自己的具体类型，而typeof对于这些类型的处理，只返回了处于其原型链最顶端的Object类型。技术上没有错，但不是我们想要的结果。
### 2. instanceof
instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B，如果 A 是 B 的实例，则返回 true,否则返回 false。 在这里需要特别注意的是：instanceof 检测的是原型，我们用一段伪代码来模拟其内部执行过程：
```
instanceof (A, B) = {
    var L = A.__proto__;
    var R = B.prototype;
    if(L === R) {
        // A的内部属性 __proto__ 指向 B 的原型对象
        return true;
    }
    return false;
}
```
从上述过程可以看出，当 A 的 __proto__ 指向 B 的 prototype 时，就认为 A 就是 B 的实例，我们再来看几个例子：
```
[ ] instanceof Array;       // true
{1: 'test', 2: 'test'} instanceof Object;     // true
new Date() instanceof Date;      // true
 
function Person(){};
new Person() instanceof Person;      // true
 
[ ] instanceof Object;// true
new Date() instanceof Object;          // true
new Person instanceof Object;        // true
```
可以看出，instanceof可以判断出[ ]是Array的实例，但它也认为[ ]是Object的实例，这是为什呢？ [ ]、Array、Object 三者之间存在怎样的关系呢？
- 从 instanceof 能够判断出 [ ].__proto__  指向 Array.prototype，而 Array.prototype.__proto__ 又指向了Object.prototype，最终 Object.prototype.__proto__ 指向了null，标志着原型链的结束。因此，[]、Array、Object 就在内部形成了一条原型链：
![捕获](https://user-images.githubusercontent.com/10249805/108807308-02cbce80-75df-11eb-914c-d7ff4c2158ab.PNG)
- 从原型链可以看出，[] 的 __proto__  直接指向Array.prototype，间接指向 Object.prototype，所以按照 instanceof 的判断规则，[] 就是Object的实例。依次类推，类似的 new Date()、new Person() 也会形成一条对应的原型链 。因此，instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。
- instanceof 操作符的问题在于，它假定只有一个全局执行环境。如果网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。
```
var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
xArray = window.frames[0].Array;
var arr =new xArray(1,2,3);   // [1,2,3]
arr instanceof Array;   // false
```
针对数组的这个问题，ES5 提供了 Array.isArray() 方法 。该方法用以确认某个对象本身是否为 Array 类型，而不区分该对象在哪个环境中创建。
Array.isArray() 本质上检测的是对象的 [[Class]] 值，[[Class]] 是对象的一个内部属性，里面包含了对象的类型信息，其格式为 [object Xxx] ，Xxx 就是对应的具体类型 。对于数组而言，[[Class]] 的值就是 [object Array] 。
### 3. constructor
当一个函数 F被定义时，JS引擎会为F添加 prototype 原型，然后再在 prototype上添加一个 constructor 属性，并让其指向 F 的引用。当执行 var f = new F() 时，F 被当成了构造函数，f 是F的实例对象，此时 F 原型上的 constructor 传递到了 f 上，因此 f.constructor == F
可以看出，F 利用原型对象上的 constructor 引用了自身，当 F 作为构造函数来创建对象时，原型上的 constructor 就被遗传到了新创建的对象上， 从原型链角度讲，构造函数 F 就是新对象的类型。这样做的意义是，让新对象在诞生以后，就具有可追溯的数据类型。
```
''.constructor  == String; // true
new Number(1).constructor  == Number; // true
new Error().constructor == Error; // true
```
细节点：
- null和undefined是无效对象，因此是不会有constructor存在的，这两种类型的数据需要通过其他方式来判断；
- 函数的constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写prototype后，原来的constructor 引用就会丢失，constructor会默认为Object。因此，为了规范开发，在重写对象原型时一般都需要重新给 constructor 赋值，以保证对象实例的类型不被篡改。
### 4. toString()
toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。
对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。
```
Object.prototype.toString.call('') ;      // [object String]
Object.prototype.toString.call(1) ;      // [object Number]
Object.prototype.toString.call(true) ;      // [object Boolean]
Object.prototype.toString.call(Symbol());      //[object Symbol]
Object.prototype.toString.call(undefined) ;      // [object Undefined]
Object.prototype.toString.call(null) ;      // [object Null]
Object.prototype.toString.call(new Function()) ;      // [object Function]
```




