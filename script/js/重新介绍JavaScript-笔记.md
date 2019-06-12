### 概览

* JavaScript 是一种**多范式的动态语言**，它包含类型、运算符、标准内置（ built-in）对象和方法。
* JavaScript **通过原型链而不是类来支持面向对象编程**
* JavaScript同样**支持函数编程**--因为它们也是对象，函数也可以被保存在变量中，并且像其他对象一样被传递。

### 内置函数
* **parseInt** 将字符串转换为整型。该函数的第二个可选参数表示字符串所表示数字的基（进制）：
    ```
    parseInt("123", 10); // 123
    parseInt("010", 10); // 10

    parseInt("11", 2); // 3
    ```
    如果给定的字符串不存在数值形式，函数会返回一个特殊的值 NaN（Not a Number 的缩写）：
    ``` 
    parseInt("hello", 10); // NaN
    ```

* **parseFloat** 用以解析浮点数字符串，**与parseInt()不同的地方是，parseFloat()只应用于解析十进制数字**。

* **单元运算符 +** 也可以把数字字符串转换成数值：
    ```
    + "42";   // 42
    + "010";  // 10
    + "0x10"; // 16
    ```
    注意：**parseInt() 和 parseFloat() 函数会尝试逐个解析字符串中的字符，直到遇上一个无法被解析成数字的字符，然后返回该字符前所有数字字符组成的数字。然而如果使用运算符 "+"， 只要字符串中含有无法被解析成数字的字符，该字符串都将被转换成 NaN**。可分别使用这两种方法解析“10.2abc”这一字符串，并比较得到的结果，来理解这两种方法的区别。
    ```
    +"10.2abc"
    // NaN
    
    parseInt("10.2abc")
    // 10
    
    parseFloat("10.2abc")
    // 10.2
    ```
* **isNaN()** 来判断一个变量是否为 NaN
    ```
    isNaN(NaN); // true
    ```

* **isFinite()** 来判断一个变量是否是一个有穷数， 如果类型为Infinity, -Infinity 或 NaN则返回false
*  **typeof()**
*  **instanceof()**
### 类型

1. Number（数字）
2. String（字符串）
3. Boolean（布尔）
4. Symbol（符号）（ES2015 新增）
5. Object（对象）
    1. Function（函数）
    2. Array（数组）
    3. Date（日期）
    4. RegExp（正则表达式）
6. null（空）
7. undefined（未定义）
8. Error（错误）

#### 1. 数字
##### 1.1 整数和浮点数
JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。所以，1与1.0是相同的，是同一个数。
```
1 === 1.0 // true
```
这就是说，**JavaScript 语言的底层根本没有整数，所有数字都是小数（64位浮点数）**。容易造成混淆的是，某些运算只有整数才能完成，此时 JavaScript 会自动把64位浮点数，转成32位整数，然后再进行运算，参见《运算符》一节的”位运算“部分。由于浮点数不是精确的值，所以涉及小数的比较和运算要特别小心。
```
0.1 + 0.2 === 0.3   // false
0.3 / 0.1   // 2.9999999999999996
(0.3 - 0.2) === (0.2 - 0.1)     // false
```
##### 1.2 数值精度
根据国际标准 IEEE 754，JavaScript 浮点数的64个二进制位，从最左边开始，是这样组成的。
* **第1位：符号位，0表示正数，1表示负数；**
* **第2位到第12位：指数部分；**
* **第13位到第64位：小数部分（即有效数字）**

符号位决定了一个数的正负，指数部分决定了数值的大小，小数部分决定了数值的精度。IEEE 754 规定，有效数字第一位默认总是1，不保存在64位浮点数之中。也就是说，有效数字总是1.xx...xx的形式，其中xx..xx的部分保存在64位浮点数之中，最长可能为52位。因此，JavaScript 提供的有效数字最长为53个二进制位。
**`(-1)^符号位 * 1.xx...xx * 2^指数位`**

上面公式是一个数在 JavaScript 内部实际的表示形式。精度最多只能到53个二进制位，这意味着，**绝对值小于2的53次方的整数**，即`-(2^53-1)`到`2^53-1`，**都可以精确表示**。
```
Math.pow(2, 53)
// 9007199254740992

Math.pow(2, 53) + 1
// 9007199254740992

Math.pow(2, 53) + 2
// 9007199254740994

Math.pow(2, 53) + 3
// 9007199254740996

Math.pow(2, 53) + 4
// 9007199254740996
```
从上面示例可以看到，大于2的53次方以后，整数运算的结果开始出现错误。所以，大于等于2的53次方的数值，都无法保持精度。
```
Math.pow(2, 53)
// 9007199254740992

Number.MAX_SAFE_INTEGER
// 9007199254740991

Number.MIN_SAFE_INTEGER
// -9007199254740991

// 多出的三个有效数字，将无法保存
9007199254740992111
// 9007199254740992000
```

上面示例表明，大于2的53次方以后，多出来的有效数字（最后三位的111）都会无法保存，变成0。
##### 1.3  数值范围

根据标准，**64位浮点数的指数部分的长度是11个二进制位**，意味着指数部分的最大值是2047（2的11次方减1）。也就是说，64位浮点数的指数部分的值最大为2047，分出一半表示负数，则 **JavaScript 能够表示的数值范围为2^1024到2^-1023（开区间），超出这个范围的数无法表示**。如果指数部分等于或超过最大正值1024，JavaScript 会返回Infinity（关于Infinity的介绍参见下文），这称为“正向溢出”；如果等于或超过最小负值-1023（即非常接近0），JavaScript 会直接把这个数转为0，这称为“负向溢出”。
```
var x = 0.5;

for(var i = 0; i < 25; i++) {
    x = x * x;
}
x   // 0
```
上面代码对0.5连续做25次平方，由于最后结果太接近0，超出了可表示的范围，JavaScript 就直接将其转为0。至于具体的最大值和最小值，**JavaScript 提供Number对象的MAX_VALUE和MIN_VALUE属性表示**（参见《Number 对象》一节）。
```
Number.MAX_VALUE // 1.7976931348623157e+308

Number.MIN_VALUE // 5e-324
```
##### 1.4 JavaScript 还有两个特殊值：Infinity（正无穷）和 -Infinity（负无穷）：
```
1 / 0; //  Infinity
-1 / 0; // -Infinity
```
#### 2. 字符串

JavaScript 中的字符串是一串Unicode 字符序列。更准确地说，**它们是一串UTF-16编码单元的序列，每一个编码单元由一个 16 位二进制数表示。每一个Unicode字符由一个或两个编码单元来表示。**

#### 3. null
null 表示一个空值（non-value），必须使用 null 关键字才能访问


#### 4. undefined

* undefined 是一个“undefined（未定义）”类型的对象，表示一个未初始化的值，也就是还没有被分配的值。
* undefined 实际上是一个不允许修改的常量。

#### 5. Boolean
JavaScript 包含布尔类型，这个类型的变量有两个可能的值，分别是 true 和 false（两者都是关键字）。根据具体需要，JavaScript 按照如下规则将变量转换成布尔类型：
* **false、0、空字符串（""）、NaN、null 和 undefined 被转换为 false**
* **所有其他值被转换为 true**

#### 6. Object
* JavaScript 中的对象，**Object，可以简单理解成“名称-值”对（而不是键值对：现在，ES 2015 的映射表（Map），比对象更接近键值对）**。“名称”部分是一个 JavaScript 字符串，“值”部分可以是任何 JavaScript 的数据类型——包括对象。
* 正因为 **JavaScript 中的一切（除了核心类型，core object）都是对象**，所以 JavaScript 程序必然与大量的散列表查找操作有着千丝万缕的联系，而散列表擅长的正是高速查找。
* 注意：
    * 从 EcmaScript 5 开始，预留关键字可以作为对象的属性名（reserved words may be used as object property names "in the buff"）。 这意味着当定义对象字面量时不需要用双引号了。
    * **从 EcmaScript 6 开始，对象键可以在创建时使用括号表示法由变量定义。`{ [phoneType]: 12345}` 可以用来替换 var userPhone = {}; userPhone[phoneType] = 12345 .**

##### 6.1 Array

**JavaScript 中的数组是一种特殊的对象**。它的工作原理与普通对象类似（以数字为属性名，但只能通过[] 来访问），但数组还有一个特殊的属性——length（长度）属性。这个属性的值通常比数组最大索引大 1。
###### 遍历数组：
* **for...of**：**遍历数组元素**，ES2015 引入了更加简洁的 for...of 循环，可以用它来遍历可迭代对象，例如数组：
```
for (const currentValue of a) {
  // Do something with currentValue
}
```
* **for...in**：**遍历数组的索引**。注意，**如果哪个家伙直接向 Array.prototype 添加了新的属性，使用这样的循环这些属性也同样会被遍历。所以并不推荐使用这种方法遍历数组**：
```
for (var i in a) {
  // Do something with a[i]
}
```
* **forEach()**：ECMAScript 5 增加的另一个遍历数组的方法
```
["dog", "cat", "hen"].forEach(function(currentValue, index, array) {
  // Do something with currentValue or array[index]
});
```
###### 易忘方法：
* a.**slice**(start, end)：返回子数组，以 a[start] 开头，以 a[end] 前一个元素结尾。该方法并**不会修改原数组**a，而是返回一个子数组。如果想删除数组中的一段元素，应该使用方法 Array.splice()
* a.**concat**(item1[, item2[, ...[, itemN]]])：返回一个数组，这个数组包含原先 a 和 item1、item2、……、itemN 中的所有元素。**不会改变原数组**a。
* a.**splice**(start, delcount[, item1[, ...[, itemN]]])：**从 start 开始，删除 delcount 个元素，然后插入所有的 item**。

#### 6.2 Function

如果没有使用 return 语句，或者一个没有值的 return 语句，JavaScript 会返回 undefined。
函数实际上是访问了函数体中一个名为 arguments 的内部对象，这个对象就如同一个类似于数组的对象一样，包括了所有被传入的参数。
```
function avg(...args) {
  var sum = 0;
  for (let value of args) {
    sum += value;
  }
  return sum / args.length;
}

avg(2, 3, 4, 5); // 3.5 --传入逗号分隔的参数列表
avg.apply(null, [2, 3, 4, 5]); // 3.5 --传入一个数组作为参数列表
```
##### 匿名函数
```
var avg = function() {
    var sum = 0;
    for (var i = 0, j = arguments.length; i < j; i++) {
        sum += arguments[i];
    }
    return sum / arguments.length;
};
```
##### 块级作用域
```
var a = 1;
var b = 2;
(function() {
    var b = 3;  //隐藏了局部变量
    a += b;
})();

a; // 4
b; // 2
```
##### 立即执行函数
你可以命名立即调用的函数表达式（IIFE——Immediately Invoked Function Expression），如下所示：
```
var charsInBody = (function counter(elm) {
    if (elm.nodeType == 3) { // 文本节点
        return elm.nodeValue.length;
    }
    var count = 0;
    for (var i = 0, child; child = elm.childNodes[i]; i++) {
        count += counter(child);
    }
    return count;
})(document.body);
```
**如上所提供的函数表达式的名称的作用域仅仅是该函数自身。**？？？这允许引擎去做更多的优化，并且这种实现更可读、友好。该名称也显示在调试器和一些堆栈跟踪中，节省了调试时的时间。

需要注意的是 JavaScript 函数是它们本身的对象——就和 JavaScript 其他一切一样——你可以给它们添加属性或者更改它们的属性，这与前面的对象部分一样。

#### 6.3 自定义对象

在经典的面向对象语言中，对象是指数据和在这些数据上进行的操作的集合。JavaScript 是一种基于原型的编程语言，并没有 class 语句，而是把函数用作类。
##### this

当使用在函数中时，this 指代当前的对象，也就是调用了函数的对象。如果在一个对象上使用点或者方括号来访问属性或方法，这个对象就成了 this。如果并没有使用“点”运算符调用某个对象，那么 this 将指向全局对象（global object）。
```
function makePerson(first, last) {
    return {
        first: first,
        last: last,
        fullName: function() {
            return this.first + ' ' + this.last;
        },
        fullNameReversed: function() {
            return this.last + ', ' + this.first;
        }
    }}
s = makePerson("Simon", "Willison");
s.fullName(); // Simon Willison
s.fullNameReversed(); // Willison, Simon


var fullName = s.fullName;
fullName(); // undefined undefined
```
当我们调用 fullName() 时，this 实际上是指向全局对象的，并没有名为 first 或 last 的全局变量，所以它们两个的返回值都会是 undefined。
##### new

它和 this 密切相关。**它的作用是创建一个崭新的空对象，然后使用指向那个对象的 this 调用特定的函数**。注意，含有 this 的特定函数不会返回任何值，只会修改 this 对象本身。new 关键字将生成的 this 对象返回给调用方，而被 new 调用的函数称为构造函数。习惯的做法是将这些函数的首字母大写，这样用 new 调用他们的时候就容易识别了。
##### Object.prototype
```
function Person(first, last) {
    this.first = first;
    this.last = last;
}

Person.prototype.fullName = function() {
    return this.first + ' ' + this.last;
}
    
Person.prototype.fullNameReversed = function() {
    return this.last + ', ' + this.first;
}
```
它是一个名叫**原型链（prototype chain）**的查询链的一部分：当你试图访问一个 Person 没有定义的属性时，解释器会首先检查这个 Person.prototype 来判断是否存在这样一个属性。所以，任何分配给 Person.prototype 的东西对通过 this 对象构造的实例都是可用的。

这个特性功能十分强大，**JavaScript 允许你在程序中的任何时候修改原型（prototype）中的一些东西**，也就是说你可以在运行时(runtime)给已存在的对象添加额外的方法：
```
s = new Person("Simon", "Willison");
s.firstNameCaps();  // TypeError on line 1: s.firstNameCaps is not a function

Person.prototype.firstNameCaps = function() {
    return this.first.toUpperCase()
}
s.firstNameCaps(); // SIMON
```
有趣的是，你还可以给 JavaScript 的内置函数原型（prototype）添加东西。让我们给 String添加一个方法用来返回逆序的字符串：
```
var s = "Simon";
s.reversed(); // TypeError on line 1: s.reversed is not a function

String.prototype.reversed = function() {
    var r = "";
    for (var i = this.length - 1; i >= 0; i--) {
        r += this[i];
    }
    return r;
}
s.reversed(); // nomiS
```
##### apply

apply() **它接收一个数组**，第一个参数应该是一个被当作 this 来看待的对象。下面是一个 new 方法的简单实现：
```
function trivialNew(constructor, ...args) {
    var o = {}; // 创建一个对象
    constructor.apply(o, args);
    return o;
}
```
**这并不是 new 的完整实现，因为它没有创建原型（prototype）链**。想举例说明 new 的实现有些困难，因为你不会经常用到这个，但是适当了解一下还是很有用的。在这一小段代码里，...args（包括省略号）叫作剩余参数（rest arguments）。如名所示，这个东西包含了剩下的参数。
```
var bill = trivialNew(Person, "William", "Orange");
和
var bill = new Person("William", "Orange");
等效
```
##### call
它也可以允许你设置 this，但**它接收一个扩展的参数列表**而不是一个数组。
```
function lastNameCaps() {
    return this.last.toUpperCase();
}
var s = new Person("Simon", "Willison");
lastNameCaps.call(s);
// 和以下方式等价
s.lastNameCaps = lastNameCaps;
s.lastNameCaps();
```
##### 内部函数
关于 JavaScript 中的**嵌套函数**，一个很重要的细节是，它们**可以访问父函数作用域中的变量**：
```
function parentFunc() {
  var a = 1;

  function nestedFunc() {
    var b = 4; // parentFunc 无法访问 b
    return a + b;
  }
  return nestedFunc(); // 5
}
```
如果某个函数依赖于其他的一两个函数，而这一两个函数对你其余的代码没有用处，你可以将它们嵌套在会被调用的那个函数内部，这样做可以减少全局作用域下的函数的数量，这有利于编写易于维护的代码。
##### 闭包
```
function makeAdder(a) {
  return function(b) {
    return a + b;
  }}var add5 = makeAdder(5);
  var add20 = makeAdder(20);
  add5(6); // 11
  add20(7); // 27
```
这里发生的事情和前面介绍过的内嵌函数十分相似：一个函数被定义在了另外一个函数的内部，内部函数可以访问外部函数的变量。唯一的不同是，内部函数被返回了，那么常识告诉我们局部变量“应该”不再存在。但是它们却仍然存在——否则 adder 函数将不能工作。也就是说，这里存在 makeAdder 的局部变量的两个不同的“副本”——一个是 a 等于 5，另一个是 a 等于 20。
##### 作用域
**每当 JavaScript 执行一个函数时，都会创建一个作用域对象（scope object），用来保存在这个函数中创建的局部变量**。它使用一切被传入函数的变量进行初始化（初始化后，它包含一切被传入函数的变量）。这与那些保存的所有全局变量和函数的全局对象（global object）相类似，但仍有一些很重要的区别：
* **每次函数被执行的时候，就会创建一个新的，特定的作用域对象；**
* **与全局对象（如浏览器的 window 对象）不同的是，你不能从 JavaScript 代码中直接访问作用域对象，也没有 可以遍历当前作用域对象中的属性 的方法。**

所以，当调用 makeAdder 时，解释器创建了一个作用域对象，它带有一个属性：a，这个属性被当作参数传入 makeAdder 函数。然后 makeAdder 返回一个新创建的函数（暂记为 adder）。通常，JavaScript 的垃圾回收器会在这时回收 makeAdder 创建的作用域对象，但是，makeAdder 的返回值--新函数 adder，拥有一个指向作用域对象的引用。最终，作用域对象不会被垃圾回收器回收，直到没有任何引用指向新函数 adder。
**作用域对象组成了一个名为作用域链（scope chain）的（调用）链**。它和 JavaScript 的对象系统使用的原型（prototype）链相类似。
##### 闭包与作用域
**一个闭包，就是 一个函数 与其 被创建时所带有的作用域对象 的组合。闭包允许你保存状态——所以，它们可以用来代替对象**。
### 变量
#### let 
声明一个**块级作用域**的本地变量

#### const
声明一个不可变的常量。这个常量**在定义域内总是可见的**。
#### var
使用 var 声明的变量在它所声明的**整个函数都是可见的**。

#### 作用域
JavaScript 与其他语言的（如 Java）的重要区别是**在 JavaScript 中语句块（blocks）是没有作用域的，只有函数有作用域。** 因此如果在一个复合语句中（如 if 控制结构中）使用 var 声明一个变量，那么它的作用域是整个函数（复合语句在函数中）。 
但是从 ECMAScript Edition 6 开始将有所不同的， **let 和 const 关键字允许你创建块作用域的变量。**

### 运算符
#### +操作符
如果你用一个字符串加上一个数字（或其他值），那么操作数都会被首先转换为字符串。如下所示：
```
"3" + 4 + 5;    // 345
3 + 4 + "5";    // 75
```
#### 位操作符
// Todo

### 控制结构
#### for...of
```
for (let value of array) {
  // do something with value
}
```
#### for...in
```
for (let property in object) {
  // do something with object property
 }
```
#### switch 
**在 switch 的表达式和 case 的表达式是使用 === 严格相等运算符进行比较的：**

### 对象和原型
// Todo
### 继承与原型链
// Todo
