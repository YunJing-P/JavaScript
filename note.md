# JavaScript 高级程序设计（第四版） 笔记

+ 如果这里看到`[toc]`，请下载到本地查看目录  

[toc]

## 变量

[目录](#javascript-高级程序设计第四版-笔记)

### let & var

+ `let` 声明的范围是**块** 作用域，而`var` 声明的范围是**函数** 作用域。
+ 在使用`var` 声明变量时，由于声明会被**提升** ，JavaScript引擎会自动将多余的声明在作用域顶部合并为一个声明。提升只会**存在声明，不会赋值。**
  
```javascript
function foo() {
    console.log(age);
    var age = 26;
}
foo();  // undefined 而不是报错，因为age 已经被声明了
```

相当于

```javascript
function foo() {
    var age;
    console.log(age);
    age = 26;
}
foo();  // undefined
```

+ 因为`let` 的作用域是块，所以不可能检查前面是否已经使用 `let` 声明过同名变量，同时也就不可能在没有声明的情况下声明它。

```javascript
if(typeof var1 === undefined) {
    let var1 = 1    // 无效，let 声明不能带出块外
}
```

### const

+ `const` 的行为与`let` 基本相同，唯一一个重要的区别是用它声明变量时**必须同时初始化变量** ，且尝试**修改** `const` 声明的变量会导致运行时错误。

```javascript
const num = 1;    // 声明时必须初始化
num = 2;    // 报错，const 不能修改
```

+ `const` 声明的限制只适用于它指向的变量的引用。换句话说，如果const 变量引用的是一个**对象（Array，Object）** ，那么修改这个对象内部的属性并不违反`const` 的限制。

```javascript
const person = {};
person.name = 'Jack';
console.log(person)    // {name: 'Jack'}

const list = [1, 2, 3, [4, 5]];
list[2] = 7;
list[3].push(6);
console.log(list)    // [1, 2, 7, [4, 5, 6]]
```

## 数据类型

[目录](#javascript-高级程序设计第四版-笔记)

### Undefined 类型

+ `undefined` 与**未声明** 有区别

```javascript
let message;    // message 被声明了，只是值为undefined

console.log(message)    // "undefined"
console.log(age)    // 报错
```

### Null 类型

+ Null 类型同样只有一个值，即特殊值null 。逻辑上讲，null 值表示一个空对象指针。

```javascript
let car = null;
console.log(typeof car);  // "object"
```

### Boolean 类型

+ 只有`true/false`

#### Boolean()

+ `Boolean()` 转型函数可以在任意类型的数据上调用，而且始终返回一个布尔值。

数据类型|转换为true 的值|转换为false 的值
--|:--:|--:
Boolean|true|false
String|非空字符串|"" 空字符串
Number|非零数值（包括无穷值）|0 、NaN
Object|任意对象|null
Undefined|N/A （不存在）|undefined

### Number 类型

+ **不要直接进行小数运算** ，因为使用了IEEE 754数值，存在这种舍入错误；因此**永远不要测试某个特定的浮点值**。小数运算可以`(num1*1000 + num2*1000)/1000`

```javascript
console.log(0.1 + 0.2)    // 0.30000000000000004
```

+ 如果某个计算得到的数值结果超出了JavaScript可以表示的范围，那么这个数值会被自动转换为一个特殊的`Infinity` （无穷）值。任何无法表示的负数以`-Infinity` （负无穷大）表示，任何无法表示的正数以`Infinity` （正无穷大）表示。
+ `NaN` 表示 **不是数值（Not a Number）**

#### Number()

+ `Number()` 函数转换对象先调用`valueOf()` 方法，如果是`NaN`则调用`toString()` 方法，再按照转换字符串的规则转换。

转换前|转换后
--|:--:
true|1
false|2
数值|直接返回
null|0
undefined|NaN
"000011" 去除0|11
"-5" 保留符号|-5
-1.1|-1.1
"0xf" 进制转换|15
"" 空字符串|0
其他字符串|NaN

```javascript
console.log(Number(true))    // 1
console.log(Number(false))     // 0
console.log(Number(-21))     // -21
console.log(Number(null) )    // 0
console.log(Number(undefined) )    // NaN
console.log(Number("000011") )    // 11
console.log(Number("-5") )    // -5
console.log(Number("-1.1") )    // -1.1
console.log(Number("0xf") )    // 15
console.log(Number("") )    // 0
console.log(Number("Hello -5") )    // NaN
```

#### parseInt()

+ `parseInt("")` 为 `NaN`，`Number("")` 为0
+ 通常在需要得到**整数** 时可以**优先使用** `parseInt()` 函数。
+ `parseInt()` 函数转换，从第一个**非空格字符**开始转换。如果第一个字符**不是** 数值字符、加号或减号，`parseInt()` **立即返回** `NaN` 。忽略小数点和字符串。第二个参数指定进制，**建议始终传给它第二个参数。**

```javascript
console.log(parseInt("1234blue"));  // 1234
console.log(parseInt(""));          // NaN
console.log(parseInt("0xA"));       // 10，解释为十六进制整数
console.log(parseInt(22.5));        // 22 无视小数点
console.log(parseInt("70"));        // 70，解释为十进制值
console.log(parseInt("0xf"));       // 15，解释为十六进制整数
console.log(parseInt(' 0x11.2and2'))    // 17
console.log(parseInt(' 11.2and2', 16))   // 17，指定16可以省了'0x'
```

#### parseFloat()

+ `parseFloat()` 函数的工作方式跟`parseInt()` 函数类似，它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。**只解析十进制值，因此不能指定底数。**
+ 如果字符串表示整数（没有小数点或者小数点后面只有一个零），则`parseFloat()` 返回整数。

```javascript
console.log(parseFloat("22.34.5"))   // 22.34
console.log(parseFloat("1234.0blue"))    // 1234，按整数解析
```

### String 类型

+ 字符串可以使用双引号（"）、单引号（'）或反引号（\`）标示

```javascript
let hi
hi = "hi"
hi = 'hi'
hi = `hi`
```

#### toString()

+ 转换为字符串：`toString()` 方法可见于数值、布尔值、对象和字符串值（字符串副本）。

```javascript
let age = 11;
console.log(age.toString());      // 字符串"11"

let found = true;
console.log(found.toString());  // 字符串"true"

let foo = {}
console.log(foo.toString());  // 字符串"[object Object]"

foo.toString = function(){
    return "Hi"
}
console.log(foo.toString());  // 字符串"Hi"
```

+ `null` 和`undefined` 值**没有**`toString()` 方法。
+ 数值类型接受进制参数；

```javascript
let num = 10;
console.log(num.toString());     // "10"
console.log(num.toString(2));    // "1010"
console.log(num.toString(16));   // "a"
```

#### String()

+ `String()` 函数：如果值有`toString()` 方法，则**调用该方法**（不传参数）并返回结果。如果值是`null` ，返回`"null"` 。如果值是`undefined` ，返回"`undefined"` 。

#### 模板字面量

+ 模板字面量，模板字面量会**保持** 反引号内部的空格，因此在使用时要格外注意。**格式正确的模板字符串可能会看起来缩进不当**。

```javascript
// 这个模板字面量在换行符之后有25个空格符
let myTemplateLiteral = `first line
                        second line`;
console.log(myTemplateLiteral.length);  // 47

// 这个模板字面量以一个换行符开头
let secondTemplateLiteral = `
first line
second line`;
console.log(secondTemplateLiteral[0] === '\n'); // true

// 这个模板字面量没有意料之外的字符
let thirdTemplateLiteral = `first line
second line`;
console.log(thirdTemplateLiteral);
// first line
// second line
```

+ 模板字面量最常用的一个特性是支持**字符串插值** ，也就是可以在一个连续定义中插入一个或多个值。

```javascript
let value = 5;
let exponent = 'second';
// 以前，字符串插值是这样实现的：
let interpolatedString = value + ' to the ' + exponent + ' power is ' + (value * value);

// 现在，可以用模板字面量这样实现：
let interpolatedTemplateLiteral = `${ value } to the ${ exponent } power is ${ value * value }`;
```

+ 所有插入的值都会使用`toString()` **强制转型为字符串**，而且**任何JavaScript表达式** 都可以用于插值。嵌套的模板字符串无须转义：

```javascript
console.log(`Hello, ${ `World` }!`);  // Hello, World!

let foo = { 
    toString: () => 'World'
};
console.log(`Hello, ${ foo }!`);      // Hello, World!
```

+ 模板字面量也支持定义标签函数 （tag function），而通过标签函数可以自定义插值行为。标签函数会接收被插值记号**分隔后的模板** 和**对每个表达式求值的结果**。
+ 因为表达式参数的数量是可变的，所以通常应该使用剩余操作符（rest operator）`fun(arg1, ...args)`，将它们收集到一个数组中

```javascript
let a = 6;
let b = 9;

function simpleTag(strings, ...expressions) {
    console.log(strings);
    console.log(expressions);

    return 'foobar';
}

let untaggedResult = `${ a } + ${ b } = ${ a + b }`;
let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
// ["", " + ", " = ", ""] 分隔后的模板
// [6, 9, 15]

console.log(untaggedResult);   // "6 + 9 = 15"
console.log(taggedResult);     // "foobar"
```

+ `String.raw` 可以直接获取原始的模板字面量内容

```javascript
console.log(`\u00A9`);            // ©
console.log(String.raw`\u00A9`);  // \u00A9
console.log(String.raw`first line\nsecond line`); //"first line\nsecond line" 只能转\n，不能转实际换行
```

### Symbol 类型

+ Symbol() 符号是原始值，且符号实例是唯一、不可变的。符号的用途是**确保对象属性使用唯一标识符**，不会发生属性冲突的危险。
+ 可以传入一个字符串参数作为对符号的描述（description），将来可以通过这个字符串来调试代码。但是，这个字符串参数与符号定义或标识**完全无关**：

```javascript
let genericSymbol = Symbol();    // 初始化
let otherGenericSymbol = Symbol();

let fooSymbol = Symbol('foo');
let otherFooSymbol = Symbol('foo');

console.log(genericSymbol == otherGenericSymbol);  // false
console.log(fooSymbol == otherFooSymbol);          // false
```

+ `Symbol()` 函数**不能用作**构造函数，与`new` 关键字一起使用。这样做是为了避免创建符号包装对象，像使用`Boolean` 、`String` 或`Number` 那样，它们都支持构造函数且可用于初始化包含原始值的包装对象：
+ 如果运行时的不同部分需要**共享**和**重用**符号实例，那么可以用一个字符串作为键，在全局符号注册表中创建并重用符号。需要使用`Symbol.for()` 方法：

```javascript
let fooGlobalSymbol = Symbol.for('foo');       // 创建新符号
let otherFooGlobalSymbol = Symbol.for('foo');  // 重用已有符号

console.log(fooGlobalSymbol === otherFooGlobalSymbol);  // true
```

+ 可以使用`Symbol.keyFor()` 来查询全局注册表，这个方法接收符号，返回该全局符号对应的字符串键。如果查询的**不是全局符号**，则返回`undefined` 。如果传给`Symbol.keyFor()` 的不是符号，则该方法抛出**TypeError** ：

```javascript
let s = Symbol.for('foo');
console.log(Symbol.keyFor(s));   // foo

// 创建普通符号
let s2 = Symbol('bar');
console.log(Symbol.keyFor(s2));  // undefined

Symbol.keyFor(123); // TypeError: 123 is not a symbol
```

+ 凡是可以使用字符串或数值作为属性的地方，都可以使用符号。这就包括了对象字面量属性和`Object.defineProperty()`、`Object.defineProperties()` 定义的属性。对象字面量只能在计算属性语法中使用符号作为属性。

```javascript
let s1 = Symbol('foo'),
    s2 = Symbol('bar'),
    s3 = Symbol('baz'),
    s4 = Symbol('qux');

let o = {
    [s1]: 'foo val'    // 这样也可以：o[s1] = 'foo val';
};

console.log(o);    // {Symbol(foo): foo val}

Object.defineProperty(o, s2, {value: 'bar val'});

console.log(o);    // {Symbol(foo): foo val, Symbol(bar): bar val}

Object.defineProperties(o, {
    [s3]: {value: 'baz val'},
    [s4]: {value: 'qux val'}
});

console.log(o);
// {Symbol(foo): foo val, Symbol(bar): bar val, Symbol(baz): baz val, Symbol(qux): qux val}
```

+ 类似于`Object.getOwnPropertyNames()` 返回对象实例的**常规属性数组**，`Object.getOwnPropertySymbols()` 返回对象实例的**符号属性数组**。这两个方法的返回值**彼此互斥**。`Object.getOwnPropertyDescriptors()` 会返回**同时包含常规和符号属性描述符**的对象。`Reflect.ownKeys()`会返回两种类型的**键**：

```javascript
let s1 = Symbol('foo'), s2 = Symbol('bar');

let o = {
    [s1]: 'foo val',
    [s2]: 'bar val',
    baz: 'baz val',
    qux: 'qux val'
};

console.log(Object.getOwnPropertySymbols(o));
// [Symbol(foo), Symbol(bar)]

console.log(Object.getOwnPropertyNames(o));
// ["baz", "qux"]

console.log(Object.getOwnPropertyDescriptors(o));
// {baz: {...}, qux: {...}, Symbol(foo): {...}, Symbol(bar): {...}}

console.log(Reflect.ownKeys(o));
// ["baz", "qux", Symbol(foo), Symbol(bar)]
```

+ `Symbol`**剩下的以后再看**

### Object 类型

+ 开发者可以通过创建Object 类型的实例来创建自己的对象，然后再给对象添加属性和方法：

```javascript
let o = new Object();
```

+ 每个Object 实例都有如下属性和方法。
+ `constructor` ：用于创建当前对象的函数。在前面的例子中，这个属性的值就是`Object()` 函数。
+ `hasOwnProperty(propertyName )` ：用于判断当前对象实例（不是原型）上是否存在给定的属性。要检查的属性名必须是字符串（如`o.hasOwnProperty("name")` ）或**符号**。
+ `isPrototypeOf(object )` ：用于判断**当前对象是否为另一个对象的原型**。
+ `propertyIsEnumerable(propertyName )` ：用于判断给定的属性是否可以使用`for-in` 语句枚举。与`hasOwnProperty()` 一样，属性名必须是字符串。
+ `toLocaleString()` ：返回对象的字符串表示，该字符串反映对象所在的本地化执行环境。
+ `toString()` ：返回对象的字符串表示。
+ `valueOf()` ：返回对象对应的**字符串、数值或布尔值表示**。通常与`toString()` 的返回值相同。

## 操作符

[目录](#javascript-高级程序设计第四版-笔记)

### 一元操作符

+ ~

### 位操作符

+ 负值以一种称为二补数 （或补码）的二进制编码存储。一个数值的二补数通过如下3个步骤计算得到：
  + 确定绝对值的二进制表示（如，对于-18，先确定18的二进制表示）；
  + 找到数值的一补数（或反码），换句话说，就是每个**0都变成1**，**每个1都变成0**；
  + 给结果加1。

```javascript
0000  0000  0000  0000  0000  0000  0001  0010 // 二进制表示，此处为18
1111  1111  1111  1111  1111  1111  1110  1101 // 找到反码
1111  1111  1111  1111  1111  1111  1110  1110 // 加1
```

+ 如果将位操作符应用到**非数值**，那么首先会使用`Number()` 函数将该值**转换为数值**（这个过程是自动的），然后再应用位操作。最终结果是数值。

#### 按位非 `~`

+ `按位非`操作符用波浪符（`~` ）表示，它的作用是返回**数值的一补数**。
+ `按位非`的最终效果是对**数值取反并减1**

```javascript
let num1 = 25;      // 二进制00000000000000000000000000011001
let num2 = ~num1;   // 二进制11111111111111111111111111100110
console.log(num2);  // -26
```

#### 按位与 `&`

+ `按位与`：`按位与`操作在两个位**都是1时返回1，在任何一位是0时返回0**。

```javascript
let result = 25 & 3; // 11001 & 00011 = 00001
console.log(result); // 1
```

#### 按位或 `|`

+ `按位或`：`按位或`操作在**至少一位是1时返回1，两位都是0时返回0**。

```javascript
let result = 25 | 3; // 11001 | 00011 = 11011
console.log(result); // 27
```

#### 按位异或 `^`

+ `按位异或`：它只在一位上是**1的时候返回1（两位都是1或0，则返回0）**。

```javascript
let result = 25 ^ 3; // 11001 ^ 00011 = 11010
console.log(result); // 26
```

#### 左移 `<<`

+ `左移`：`左移`操作符用两个小于号（`<<`）表示，会按照指定的**位数**将数值的所有位向左移动。
+ 注意，`左移`会**保留它所操作数值的符号**。比如，如果-2左移5位，将得到-64，而不是正64。

```javascript
let oldValue = 2;              // 二进制10
let newValue = oldValue << 5;  // 等于二进制1000000，即十进制64
```

#### 有符号右移 `>>`

+ `有符号右移`,由两个大于号（`>>` ）表示，会将数值的所有32位都向右移，同时**保留符号**（正或负）。
+ 移位后就会出现空位。不过，右移后空位会出现在左侧，且在**符号位之后**

```javascript
let oldValue = 64;             // 等于二进制1000000
let newValue = oldValue >> 5;  // 等于二进制10，即十进制2
```

#### 无符号右移 `>>>`

+ `无符号右移` 用3个大于号表示（`>>>` ），会将数值的所有32位都向右移。
+ 与`有符号右移` **不同**，无符号右移会给空位补0，而**不管符号位是什么**。

```javascript
let oldValue = -64;              // 等于二进制11111111111111111111111111000000 二进制64取反码加1
let newValue = oldValue >>> 5;   // 等于十进制134217726
```

### 布尔操作符

#### 逻辑非 `!`

+ 无论应用到的是什么数据类型，`逻辑非`操作符首先将操作数转换为`布尔值`（[布尔值转换规则](#Boolean)），然后再对其**取反**。
+ 同时使用两个叹号（`!!` ），相当于调用了转型函数`Boolean()` 。

#### 逻辑与 `&&`

+ 逻辑与操作符（`&&`）可用于任何类型的操作数，不限于布尔值。如果有操作数不是布尔值，**则逻辑与并不一定会返回布尔值**：

第一个操作数|第二个操作数|返回
--|:--:|:--:
Object|any|any
Object1|Object2|Object2
true|Object|Object
false|Object|false
null/any|any/null|null
any/NaN|NaN/any|NaN
any/undefined|undefined/any|undefined

#### 逻辑或 `||`

+ 逻辑或操作符（`||`），**如果有一个操作数不是布尔值，那么逻辑或操作符也不一定返回布尔值**。

第一个操作数|第二个操作数|返回
--|:--:|:--:
Object|any|Object
false|any|any
Object1|Object2|Object1
null|null|null
NaN|NaN|NaN
undefined|undefined|undefined

+ 逻辑与操作符是一种**短路操作符**，意思就是**如果第一个操作数决定了结果，那么永远不会对第二个操作数求值**：

```javascript
let found = false;
let result = (found && someUndeclaredVariable);  // 不会出错
console.log(result);  // 会执行
```

### 乘性操作符 `*`

+ 如果`乘性操作符`有**不是数值**的操作数，则该操作数会在后台被使用`Number()` （[转换规则](#Number)）转型函数转换为数值。这意味着空字符串会被当成`0``，而布尔值true` 会被当成`1`。
+ 如果操作数都是**数值**，则执行常规的乘法运算，即两个正值相乘是正值，两个负值相乘也是正值，正负符号不同的值相乘得到负值。如果ECMAScript不能表示乘积，则返回`Infinity` 或`-Infinity` 。

```javascript
2*NaN             // NaN
Infinity*0        // NaN
Infinity*2        //Infinity
Infinity*-2       // -Infinity
Infinity*Infinity //Infinity
```

### 除性操作符 `/`

+ 如果操作数**都是数值**，则执行常规的除法运算，即两个正值相除是正值，两个负值相除也是正值，符号不同的值相除得到负值。如果ECMAScript不能表示商，则返回`Infinity` 或`-Infinity` 。

```javascript
2/NaN             // NaN
NaN/2             // NaN
Infinity/Infinity // NaN
0/0               // NaN
2/0               // Infinity
-2/0              // -Infinity
Infinity/2        // Infinity
Infinity/-2       // -Infinity
```

### 取模操作符 `%`

```javascript
2%Infinity // 2
0%Infinity // 0
2%0 // NaN
Infinity%Infinity // NaN
Infinity%2 // NaN
0%2 // 0
```

### 指数操作符 `Math.pow()`、`**`

```javascript
console.log(Math.pow(3, 2);    // 9
console.log(3 ** 2);           // 9
```

### （略）加性操作符、减法操作符 `+`、`-`

### 关系操作符 `<`、`>`、`<=`、`>=`

+ 关系操作符执行比较两个值的操作，包括小于（`<` ）、大于（`>` ）、小于等于（`<=` ）和大于等于（`>=` ），这几个操作符都返回**布尔值**
  + 如果操作数都是`数值`，则执行`数值`比较。
  + 如果操作数都是`字符串`，则逐个比较字符串中对应**字符的编码**。**大写字母的编码都小于小写字母的编码**
  + 如果有任一操作数是`数值`，则将另一个操作数**转换为**`数值`，执行数值比较。
  + 如果有任一操作数是`对象`，则调用其`valueOf()` 方法，取得结果后再根据前面的规则执行比较。如果没有`valueOf()` 操作符，则调用`toString()` 方法，取得结果后再根据前面的规则执行比较。
  + 如果有任一操作数是`布尔值`，则将其转换为`数值`再执行比较。
  + 在比较`NaN` 时，**无论是小于还是大于等于，比较的结果都会返回`false`** 。

### 相等操作符 `=`、`!=`、`===`、`!==`

+ ECMAScript中的等于操作符用两个等于号（`==` ）表示，如果操作数相等，则会返回`true` 。不等于操作符用叹号和等于号（`!=` ）表示，如果两个操作数不相等，则会返回`true` 。这两个操作符都会**先进行类型转换**（通常称为强制类型转换 ）再确定操作数是否相等。
  + 如果**任一操作数**是`布尔值`，则将其转换为`数值`再比较是否相等。`false` 转换为`0`，`true` 转换为`1`。
  + 如果一个操作数是`字符串`，另一个操作数是`数值`，则尝试将`字符串`转换为`数值`，再比较是否相等。
  + 如果一个操作数是`对象`，另一个操作数**不是**，则调用对象的`valueOf()` 方法取得其原始值，再根据前面的规则进行比较。
  + `null` 和`undefined` 相等。
  + `null` 和`undefined` **不能转换为其他类型**的值再进行比较。
  + 如果有任一操作数是`NaN` ，则相等操作符返回`false` ，不相等操作符返回`true` 。记住：即使两个操作数都是`NaN` `，相等操作符也返回false` ，因为按照规则，`NaN` **不等于**`NaN` 。
  + 如果两个操作数都是`对象`，则比较它们是不是同一个`对象`。如果两个操作数都**指向同一个**`对象`，则相等操作符返回`true` ，**否则两者不相等**。

表达式|结果
--|:--:
null == undefined|true
"NaN" == NaN|false
5 == NaN|false
NaN == NaN|false
NaN != NaN|true
false == 0|true
true == 1|true
true == 2|false
undefined == 0|false
null == 0|false
"5" == 5|true

+ 全等和不全等
  + `全等` 和`不全等` 操作符与`相等` 和`不相等` 操作符类似，只不过它们在比较相等时**不转换操作数**。

```javascript
let result1 = ("55" != 55);  // false，转换后相等
let result2 = ("55" !== 55); // true，不相等，因为数据类型不同

null == undefined // true
null === undefined // false，类型不想等
```

### 条件操作符 `? :`

+ 即根据条件表达式`boolean_expression` 的值决定将哪个值赋给变量`variable` 。如果`boolean_expression` 是`true` ，则赋值`true_value` ；如果`boolean_expression` 是`false` ，则赋值`false_value` 。

```javascript
variable = boolean_expression ? true_value : false_value;
```

### 赋值操作符 `=`

+ **赋值与拷贝（复制）不是同一种东西**
+ **赋值与拷贝（复制）不是同一种东西**
+ **赋值与拷贝（复制）不是同一种东西**
+ `+=`、`*=`... 等等，这些操作符**仅仅是简写语法，使用它们不会提升性能。**

### 逗号操作符 `,`

```javascript
let num1 = 1, num2 = 2, num3 = 3;

let num = (5, 1, 4, 8, 0); // num的值为0，不常见
```

## （略）语句

## 变量、作用域与内存

[目录](#javascript-高级程序设计第四版-笔记)

### 原始值与引用值

+ 原始值指的是**原始类型**的值，也叫 `基本类型`，例如 `Number`、`Stirng`、`Boolean`、`Null`、`Underfined`、`Symbol` 。保存原始值的变量是按值 （by value）访问的，因为我们操作的就是**存储在变量中的实际值**。
+ 引用值指的是**引用类型**的值，例如 `Object`、`Function`、`Array`、`Date`、`RegExp` 。**实际上操作的是对该对象的引用 （reference）而非实际的对象本身**。类似Python中的`可变变量`与`不可变变量`。

### 动态属性

+ 原始类型的初始化可以只使用原始`字面量`形式。如果使用的是`new` 关键字，则JavaScript会创建一个`Object` 类型的实例，但其行为**类似**原始值。下面来看看这两种初始化方式的差异：

```javascript
let name1 = "Nicholas";
let name2 = new String("Matt");
name1.age = 27;
name2.age = 26;
console.log(name1);        // "Nicholas"
console.log(name2);        // String {"Nicholas"}
console.log(name1.age);    // undefined
console.log(name2.age);    // 26
console.log(typeof name1); // string
console.log(typeof name2); // object
```

### 复制值

+ 除了[存储方式](#原始值与引用值)不同，`原始值`和`引用值`在通过变量复制时也有所不同。在通过变量把一个原始值赋值到另一个变量时，原始值会**被复制到新变量的位置**。

```javascript
let num1 = 5;
let num2 = num1;
```

var|value
--|:--:
num1|5
num2|5

+ 在把`引用值`从一个变量赋给另一个变量时，存储在变量中的值也会被复制到新变量所在的位置。区别在于，这里复制的值实际上是一个`指针`，它指向存储在堆内存中的对象。操作完成后，两个变量**实际上指向同一个对象**，因此一个对象上面的变化会在另一个对象上反映出来。

```javascript
let obj1 = new Object();
let obj2 = obj1;
obj1.name = "Nicholas";
console.log(obj2.name); // "Nicholas"
```

var|value
--|:--:
obj1|-> Object1
obj2|-> Object1

### 传递参数

+ **ECMAScript中所有函数的参数都是按值传递的。**
+ **ECMAScript中所有函数的参数都是按值传递的。**
+ **ECMAScript中所有函数的参数都是按值传递的。**
+ 数外的值会被`复制`到函数内部的参数中，就像从一个变量`复制`到另一个变量一样。（**改变引用值会影响函数外部**）

```javascript
function test(num, obj) {
    num += 10;
    obj.name = 'jack'
    return num;
}

let num1 = 10
let obj1 = {}
console.log(test(num1, obj1)) // 20
console.log(num1)             // 10
console.log(obj1)             // {name: "jack"}
```

+ 很多开发者**错误地认为**，当在局部作用域中修改对象而变化反映到全局时，就意味着参数是按引用传递的 **X**。

```javascript
function setName(obj) {
    obj.name = "Nicholas";  // obj与 person的值相同，指向同一个地址，可以影响person
    obj = new Object(); // obj的值改变，指向不同的内存地址值，影响不到 person
    obj.name = "Greg";
}

let person = new Object();
setName(person); // 把person 的值复制给 obj
console.log(person.name);  // "Nicholas"
```

### 确定类型

#### 判断类型 `typeof`、`instanceof`

+ `typeof` 操作符最适合用来判断一个变量是否为`原始类型`。它是判断一个`变量`是否为`字符串`、`数值`、`布尔值`或`undefined` 的最好方式。如果值是对象或`null` ，那么`typeof` 返回`"object"`，我们可以使用`instanceof` 判断引用类型的类型：

```javascript
result = variable instanceof constructor
```

#### 执行上下文与作用域

+ 垃圾回收，用到过两种主要的标记策略：标记清理和引用计数（基本不用）。标记清理垃圾回收程序运行的时候，会标记内存中存储的所有变量（记住，标记方法有很多种）。然后，它会将所有在上下文中的变量，以及被在上下文中的变量引用的变量的标记去掉。在此之后再被加上标记的变量就是待删除的了；引用计数思路是对每个值都记录它被引用的次数。声明变量并给它赋一个引用值时，这个值的引用数为1。如果同一个值又被赋给另一个变量，那么引用数加1。类似地，如果保存对该值引用的变量被其他值给覆盖了，那么引用数减1。当一个值的引用数为0时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。
+ **TODO！！！！！！！！！**
+ 隐藏类和删除操作 - 以后再过一下
+ 内存泄漏 - 以后再过一下
+ RegExp 正则先跳过
+ eval()函数先放放，可以解析内部脚本
+ ECMA-262没有规定直接访问Global 对象的方式，但浏览器将window 对象实现为Global 对象的代理。因此，所有全局作用域中声明的变量和函数都变成了window 的属性。不过window 对象在JavaScript中远不止实现了ECMAScript 的Global 对象那么简单。
+ 这段代码创建一个立即调用的函数表达式，返回了this 的值。如前所述，当一个函数在没有明确（通过成为某个对象的方法，或者通过call() /apply() ）指定this 值的情况下执行时，this 值等于Global 对象。因此，调用一个简单返回this 的函数是在任何执行上下文中获取Global 对象的通用方式。
  
```javascript
let global = function() {
    return this;    
}();
```

+ min() 和max() 方法用于确定一组数值中的最小值和最大值。
  
```javascript
let max = Math.max(3, 54, 32, 16);
console.log(max);  // 54

let values = [1, 2, 3, 4, 5, 6, 7, 8];
let max = Math.max(...val); // 8
```

+ Math.ceil() 方法始终向上舍入为最接近的整数。
+ Math.floor() 方法始终向下舍入为最接近的整数。
+ Math.round() 方法执行四舍五入。
+ Math.fround() 方法返回数值最接近的单精度（32位）浮点值表示。
+ 显式地创建Object 的实例有两种方式。第一种是使用new 操作符和Object 构造函数；另一种是用对象字面量。

```javascript
let person = new Object();
person.name = "Nicholas";
person.age = 29;
// 全等 下面使用对象字面量
let person = {
    name: "Nicholas",
    age: 29
};
```

+ ES6之前的方法则会忽略数组空位，但具体的行为也会因方法而异：由于行为不一致和存在性能隐患，因此实践中要避免使用数组空位。如果确实需要空位，则可以显式地用undefined 值代替。

```javascript
const array_test = [,,,]; // 长度为3 相当于 [empty, empty, empty,]
const options = [1,,,,5];
// map()会跳过空位置
console.log(options.map(() => 6));  // [6, undefined, undefined, undefined, 6]

// join()视空位置为空字符串
console.log(options.join('-'));     // "1----5"
```

+ 数组length 属性的独特之处在于，它不是只读的。通过修改length 属性，可以从数组末尾删除或添加元素。  

```javascript
let colors = ["red", "blue", "green"]; // 创建一个包含3个字符串的数组
colors.length = 2;
alert(colors);  // ["red", "blue"]
```

+ 在ES6中，Array 的原型上暴露了3个用于检索数组内容的方法：keys() 、values() 和entries() 。keys() 返回数组索引的迭代器，values() 返回数组元素的迭代器，而entries() 返回索引/值对的迭代器：

```javascript
const a = ["foo", "bar", "baz", "qux"];

for (const [idx, element] of a.entries()) {
    alert(idx);
    alert(element);
}
```

+ 填充数组方法fill( value, begin, end)，包含开始索引，不包含结束索引。fill() 静默忽略超出数组边界、零长度及方向相反的索引范围。

```javascript
// 用7填充索引大于等于1且小于3的元素
zeroes.fill(7, 1, 3);
console.log(zeroes);  // [0, 7, 7, 0, 0];
zeroes.fill(0);       // 重置

// 用8填充索引大于等于1且小于4的元素
// (-4 + zeroes.length = 1)
// (-1 + zeroes.length = 4)
zeroes.fill(8, -4, -1);
console.log(zeroes);  // [0, 8, 8, 8, 0];
```

+ 数组有两个方法可以用来对元素重新排序：reverse() 和sort() 。顾名思义，reverse() 方法就是将数组元素反向排列。sort() 会按照升序重新排列数组元素，即最小的值在前面，最大的值在后面。sort() 会在每一项上调用String() 转型函数，然后比较字符串来决定顺序，即使数组的元素都是数值，也会先把数组转换为字符串再比较、排序。

```javascript
let values = [0, 1, 5, 10, 15];
values.sort();
alert(values);  // 0,1,10,15,5 字符串1在5前面
```

+ 删除 。需要给splice() 传2个参数：要删除的第一个元素的位置和要删除的元素数量。可以从数组中删除任意多个元素，比如splice(0, 2) 会删除前两个元素。
+ 插入 。需要给splice() 传3个参数：开始位置、0（要删除的元素数量）和要插入的元素，可以在数组中指定的位置插入元素。第三个参数之后还可以传第四个、第五个参数，乃至任意多个要插入的元素。比如，splice(2, 0, "red", "green") 会从数组位置2开始插入字符串"red" 和"green" 。
+ 替换 。splice() 在删除元素的同时可以在指定位置插入新元素，同样要传入3个参数：开始位置、要删除元素的数量和要插入的任意多个元素。要插入的元素数量不一定跟删除的元素数量一致。比如，splice(2, 1, "red", "green") 会在位置2删除一个元素，然后从该位置开始向数组中插入"red" 和"green" 。
+ splice() 返回被删除数组
  
```javascript
let colors = ["red", "green", "blue"];
let removed = colors.splice(0,1);  // 删除第一项
alert(colors);                     // green,blue
alert(removed);                    // red，只有一个元素的数组

removed = colors.splice(1, 0, "yellow", "orange");   // 在位置1插入两个元素
alert(colors);                                       // green,yellow,orange,blue
alert(removed);                                      // 空数组

removed = colors.splice(1, 1, "red", "purple");  // 插入两个值，删除一个元素
alert(colors);                                   // green,red,purple,orange,blue
alert(removed);                                  // yellow，只有一个元素的数组
```

+ 简写方法名

```javascript
let person = {
    sayName: function(name) {
        console.log(`My name is ${name}`);
    }
};

person.sayName('Matt'); // My name is Matt

// 全等于

let person = {
    sayName(name) {
        console.log(`My name is ${name}`);
    }
};

person.sayName('Matt'); // My name is Matt

// 简写方法名与可计算属性键相互兼容：
const methodKey = 'sayName';

let person = {
[methodKey](name) {
    console.log(`My name is ${name}`);
}
}

person.sayName('Matt'); // My name is Matt
```

+ 对象解构
  
```javascript
// 不使用对象解构
let person = {
    name: 'Matt',
    age: 27
};

let personName = person.name,
    personAge = person.age;

console.log(personName); // Matt
console.log(personAge);  // 27

// 使用对象解构
let person = {
    name: 'Matt',
    age: 27
};

let { name: personName, age: personAge } = person;

console.log(personName);  // Matt
console.log(personAge);   // 27

let person = {
name: 'Matt',
age: 27
};

let { name, job='Software engineer', fu } = person;

console.log(name); // Matt
console.log(job);  // Software engineer
console.log(fu);  // undefined
```

+ 构造函数模式

### 对象、类与面向对象编程

#### 对象创建

```javascript
let person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";
person.sayName = function() {
    console.log(this.name);
};

// 流行
let person = {
    name: "Nicholas",
    age: 29,
    job: "Software Engineer",
    sayName() {
        console.log(this.name);
    }
};
```

#### 属性的类型

+ 属性分两种：**数据属性**和**访问器属性**。
+ **数据属性**包含一个保存数据值的位置。值会从这个位置读取，也会写入到这个位置。**数据属性**有4个特性描述它们的行为：
+ `Configurable` ：表示属性是否可以通过`delete` 删除并重新定义，是否可以**修改它的特性**，以及是否可以把它改为**访问器属性**。默认情况下，所有直接定义在对象上的属性的这个特性都是`true` 。
+ `Enumerable` ：表示属性是否可以通过`for-in` 循环返回。默认情况下，所有直接定义在对象上的属性的这个特性都是`true` 。
+ `Writable` ：表示属性的**值**是否可以被修改。默认情况下，所有直接定义在对象上的属性的这个特性都是`true` 。
+ `Value` ：包含属性实际的**值**。这就是前面提到的那个读取和写入属性值的位置。这个特性的默认值为`undefined` 。
+ 在严格模式下，违反特性行为会**抛出错误**，非严格模式下会**被忽略**

```javascript
// 将属性显式添加到对象
let person = {
    name: "Nicholas"
};

// 修改属性的默认特性
let person = {};
Object.defineProperty(person, "name", {
    configurable: true,
    enumerable: true,
    writable: true,
    value: "Nicholas"
});
```

+ **访问器属性**不包含数据值。相反，它们包含一个获取（`getter`）函数和一个设置（`setter`）函数，不过这两个函数**不是必需**的。
+ `Configurable` ：表示属性是否可以通过`delete` 删除并重新定义，是否可以**修改它的特性**，以及是否可以把它改为**数据属性**。默认情况下，所有直接定义在对象上的属性的这个特性都是`true` 。
+ `Enumerable` ：表示属性是否可以通过`for-in` 循环返回。默认情况下，所有直接定义在对象上的属性的这个特性都是`true` 。
+ `Get` ：获取函数，在读取属性时调用。默认值为`undefined` 。
+ `Set` ：设置函数，在写入属性时调用。默认值为`undefined` 。
+ **访问器属性是不能直接定义的**，必须使用`Object.defineProperty()` 。
+ 只定义获取函数意味着属性是**只读**的，尝试修改属性会**被忽略**。在严格模式下，尝试写入只定义了获取函数的属性会**抛出错误**。类似地，只有一个设置函数的属性是**不能读取**的，非严格模式下读取会返回`undefined` ，严格模式下会**抛出错误**。

```javascript
// 定义一个对象，包含伪私有成员year_和公共成员edition
let book = {
  year_: 2017,
  edition: 1
};

Object.defineProperty(book, "year", {
    get() {
        return this.year_;
    },
    set(newValue) {
        if (newValue > 2017) {
            this.year_ = newValue;
            this.edition += newValue - 2017;
        }
    }
});
book.year = 2018;
console.log(book.edition); // 2
```

#### 定义多个属性的特性

+ `Object.defineProperties()` 方法。这个方法可以通过多个描述符一次性定义多个属性。它接收两个参数：**要为之添加或修改属性的对象和另一个描述符对象**，其属性与要添加或修改的属性一一对应。
+ 这段代码在`book` 对象上定义了两个数据属性`year_` 和`edition` ，还有一个访问器属性`year` 。最终的对象跟上面例中的一样。唯一的区别是**所有属性都是同时定义的**，并且数据属性的`configurable` 、`enumerable` 和`writable` 特性值**都是false** 。

```javascript
let book = {};
Object.defineProperties(book, {
    year_: {
        // writable: true,
        value: 2017,
    },

    edition: {
        // writable: true,
        value: 1,
    },

    year: {
        get() {
            return this.year_;
        },

        set(newValue) {
            if (newValue > 2017) {
            this.year_ = newValue;
            this.edition += newValue - 2017;
            }
        }
    }
});
console.log(book.year)
console.log(book.edition)
book.year = 2019 // 修改不会成功，因为year_ 和edition 的writable 属性为false
console.log(book.year) // 把year_ 和edition 的writable 设置为true ，看看效果
console.log(book.edition)
```

#### 读取属性的特性

+ `Object.getOwnPropertyDescriptor()` 方法可以取得指定属性的属性描述符。

```javascript
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017
  },

  edition: {
    value: 1
  },

  year: {
    get: function() {
      return this.year_;
    },

    set: function(newValue){
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    }
  }
});

let descriptor = Object.getOwnPropertyDescriptor(book, "year_");
console.log(descriptor.value);          // 2017
console.log(descriptor.configurable);   // false
console.log(typeof descriptor.get);     // "undefined"
let descriptor = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value);          // undefined
console.log(descriptor.enumerable);     // false
console.log(typeof descriptor.get);     // "function"
```

+ `Object.getOwnPropertyDescriptors()` 静态方法。这个方法实际上会在每个自有属性上调用`Object.getOwnPropertyDescriptor()` 并在一个新对象中返回它们。

```javascript
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017
  },

  edition: {
    value: 1
  },

  year: {
    get: function() {
      return this.year_;
    },

    set: function(newValue){
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    }
  }
});

console.log(Object.getOwnPropertyDescriptors(book));
// {
//   edition: {
//     configurable: false,
//     enumerable: false,
//     value: 1,
//     writable: false
//   },
//   year: {
//     configurable: false,
//     enumerable: false,
//     get: f(),
//     set: f(newValue),
//   },
//   year_: {
//     configurable: false,
//     enumerable: false,
//     value: 2017,
//     writable: false
//   }
// }
```

#### 合并对象

+ `Object.assign()` 方法。这个方法接收一个`目标对象`和一个或多个`源对象`作为参数，然后将每个源对象中可枚举（`Object.propertyIsEnumerable()` 返回`true` ）和自有（`Object.hasOwnProperty()` 返回`true` ）属性复制到目标对象。以**字符串和符号为键的属性会被复制**。对每个符合条件的属性，这个方法会使用源对象上的`Get` 取得属性的值，然后使用目标对象上的`Set` 设置属性的值。此外，从源对象访问器属性取得的值，比如获取函数，会作为一个**静态值**赋给目标对象。换句话说，**不能在两个对象间转移获取函数和设置函数**。

```javascript
let dest, src, result;

/**
 * 覆盖属性
 */
dest = { id: 'dest' };

result = Object.assign(dest, { id: 'src1', a: 'foo' }, { id: 'src2', b: 'bar' });

// Object.assign会覆盖重复的属性
console.log(result); // { id: src2, a: foo, b: bar }

// 可以通过目标对象上的设置函数观察到覆盖的过程：
dest = {
  set id(x) {
    console.log(x);
  }
};

Object.assign(dest, { id: 'first' }, { id: 'second' }, { id: 'third' });
// first
// second
// third


/**
 * 对象引用
 */

dest = {};
src = { a: {} };
src.a.name = 'pyj'
Object.assign(dest, src);

// 浅复制意味着只会复制对象的引用
console.log(dest);              // { a :{ name: "pyj"} }
console.log(dest.a === src.a);  // true
```

#### 对象标识及相等判定

+ 有些特殊情况即使是=== 操作符也无能为力，为改善这类情况，ECMAScript 6规范新增了`Object.is()` ，这个方法与`===` 很像，但同时也考虑到了上述边界情形。这个方法必须接收两个参数：

```javascript
// 这些是===符合预期的情况
console.log(true === 1);  // false
console.log({} === {});   // false
console.log("2" === 2);   // false

// 这些情况在不同JavaScript引擎中表现不同，但仍被认为相等
console.log(+0 === -0);   // true
console.log(+0 === 0);    // true
console.log(-0 === 0);    // true

// 要确定NaN的相等性，必须使用极为讨厌的isNaN()
console.log(NaN === NaN); // false
console.log(isNaN(NaN));  // true

// Object.is()
console.log(Object.is(true, 1));  // false
console.log(Object.is({}, {}));   // false
console.log(Object.is("2", 2));   // false

// 正确的0、-0、+0相等/不等判定
console.log(Object.is(+0, -0));   // false
console.log(Object.is(+0, 0));    // true
console.log(Object.is(-0, 0));    // false

// 正确的NaN相等判定
console.log(Object.is(NaN, NaN)); // true
```

+ 要检查超过两个值，递归地利用相等性传递即可：

```javascript
function recursivelyCheckEqual(x, ...rest) {
  return Object.is(x, rest[0]) && (rest.length < 2 || recursivelyCheckEqual(...rest));
}
```

#### 增强的对象语法

+ **简写属性名**只要使用变量名（不用再写冒号）就会自动被解释为**同名的属性键**。

```javascript
let name = 'Matt';

let person = {
  name: name
};

// 等价

let person = {
  name
};
```

+ **可计算属性**

```javascript
const nameKey = 'name';
const ageKey = 'age';
const jobKey = 'job';

let person1 = {};
person1[nameKey] = 'Matt';
person1[ageKey] = 27;
person1[jobKey] = 'Software engineer';

// 使用可计算属性
let person2 = {
  [nameKey]: 'Matt',
  [ageKey]: 27,
  [jobKey]: 'Software engineer'
};

let uniqueToken = 0;

function getUniqueKey(key) {
  return `${key}_${uniqueToken++}`;
}

// 也可以是复杂的表达式，在实例化时再求值
let person3 = {
  [getUniqueKey(nameKey)]: 'Matt',
  [getUniqueKey(ageKey)]: 27,
  [getUniqueKey(jobKey)]: 'Software engineer'
};

```

+ **简写方法名**

```javascript
let person = {
  sayName: function(name) {
    console.log(`My name is ${name}`);
  }
};

// 等价
let person = {
  sayName(name) {
    console.log(`My name is ${name}`);
  }
};
```

#### 对象解构

+ 常规用法

```javascript
// 使用对象解构
let person = {
  name: 'Matt',
  age: 27
};

let { name: personName, age, job, body='good'} = person;

console.log(personName);  // Matt, personName = person.name, personname 是别名
console.log(age);   // 27, age = person.age, 省略即默认同名
console.log(job);   // undeifned ,引用的属性不存在
console.log(body);   // good ,引用的属性不存在,但有默认值
```

+ 解构并**不要求**变量必须在解构表达式中**声明**。不过，如果是给事先声明的变量赋值，则赋值表达式必须**包含在一对括号中**。

```javascript
let personName, personAge;

let person = {
  name: 'Matt',
  age: 27
};

({name: personName, age: personAge} = person);
```

+ 需要注意的是，涉及多个属性的解构赋值是一个输出无关的顺序化操作。如果一个解构表达式涉及多个赋值，开始的赋值成功而**后面的赋值出错**，则整个解构赋值**只会完成一部分**

```javascript
let person = {
  name: 'Matt',
  age: 27
};

let personName, personBar, personAge;

try {
  // person.foo是undefined，因此会抛出错误
  ({name: personName, foo: { bar: personBar }, age: personAge} = person);
} catch(e) {}

console.log(personName, personBar, personAge);
// Matt, undefined, undefined
```

### 创建对象

#### new的时候发生了什么

```javascript
function Person () {
    this.name = name;
    this.age = age;
    this.sex = sex

    this.sayName = function () {
        return this.name;
    };
}

var person = new Person("tom", 21, "famle");

console.log(person.name);
```

+ 创建一个新对象
+ 将新对象的`_proto_`指向构造函数的`prototype`对象
+ 将构造函数的**作用域**赋值给新对象 （也就是`this`指向新对象）
+ 执行构造函数中的代码（为这个新对象添加属性）
+ 返回新的对象
+ [call函数](#call)

```javascript
var Obj = {};

Obj._proto_ =  Person.prototype();

Person.call(Obj);
```

### 函数

#### this

+ 箭头函数没有`arguments` todo
+ `this` 在**标准函数**和**箭头函数**中有**不同**的行为。
+ 在标准函数中，`this` 引用的是把函数当成方法调用的**上下文对象**，这时候通常称其为`this` 值（在网页的**全局上下文**中调用函数时，**`this` 指向`window`对象**，如果作为**某个对象的方法调用**，则`this` 等于这个对象。）。
+ 在**严格模式**下，如果在全局函数中调用`this` ，`this`等于`undefined` 。
+ 箭头函数中的`this` 会**保留定义该函数时的上下文**。

```javascript
window.color = 'red';
let o = {
    color: 'blue'
};

// 标准函数
function sayColor1() {
    console.log(this.color);
}

sayColor1();    // 'red' ，this 指向window

o.sayColor = sayColor1;
o.sayColor();  // 'blue'，对象.标准函数，this 指向对象o

// 箭头函数
sayColor2 = () => {
    console.log(this.color);
}

sayColor2();    // 'red' ，this 指向window

o.sayColor = sayColor2;
o.sayColor();  // 'red'，对象.箭头函数，声明的时候固定了this，this 指向window
```

#### call

+ **重点！！！！！！！**
+ **重点！！！！！！！**
+ **重点！！！！！！！**
+ `apply()` 、`call()` 、`bind()` ，这三个方法都会以指定的`this` 值来调用函数，即会**设置调用函数时函数体内this 对象的值**。

```javascript
function sum(num1, num2) {
    return num1 + num2;
}

function callSum1(num1, num2) {
    return sum.call(this, num1, num2); // call() 传入多个参数
}

function callSum2(num1, num2) {
    return sum.apply(this, [num1, num2]); // apply() 传入数组
}

console.log(callSum1(10, 10)); // 20
console.log(callSum2(10, 10)); // 20
```

+ `apply()` 和`call()` 真正强大的地方并不是给函数传参，而是**控制函数调用上下文**即函数体内`this` 值的能力。

```javascript
window.color = 'red';
let o = {
    color: 'blue'
};

function sayColor() {
    console.log(this.color);
}

sayColor();             // 'red' ，this 指向window
sayColor.call(this);    // 'red' ，this 指向window
sayColor.call(window);  // 'red' ，this 指向window
sayColor.call(o);       // 'blue' ， this 指向对象o
```

#### 闭包

+ `匿名函数`经常被人**误认为**是`闭包（closure）`。
+ `闭包` 指的是那些**引用了另一个函数作用域中变量的函数**，通常是在嵌套函数中实现的。

```javascript
function createComparisonFunction(propertyName) {
    return function(object1, object2) {
        let value1 = object1[propertyName];
        let value2 = object2[propertyName];

        if (value1 < value2) {
            return -1;
        } else if (value1 > value2) {
            return 1;
        } else {
            return 0;
        }
    };
}
```

+ 这里定义的`compare()` 函数是在全局上下文中调用的。第一次调用`compare()` 时，会为它创建一个包含`arguments` 、`value1` 和`value2` 的活动对象，这个对象是其作用域链上的第一个对象。而全局上下文的变量对象则是`compare()` 作用域链上的第二个对象，其中包含`this` 、`result` 和`compare` 。

```javascript
function compare(value1, value2) {
    if (value1 < value2) {
        return -1;
    } else if (value1 > value2) {
        return 1;
    } else {
        return 0;
    }
}

let result = compare(5, 10);
```

![avatar](http://elaiza.cc/img/js_note/Image00020.png)

+ 函数执行时，每个执行上下文中都会有一个包含其中变量的对象。全局上下文中的叫**变量对象**，它会在代码执行期间始终存在。而函数局部上下文中的叫**活动对象**，只在函数执行期间存在。
+ 在调用一个函数时，会为这个函数调用创建一个执行上下文，并创建一个作用域链。然后用`arguments` 和`其他命名参数`来初始化这个函数的活动对象。外部函数的活动对象是内部函数作用域链上的第二个对象。这个作用域链**一直向外串起**了所有包含函数的活动对象，直到**全局执行上下文**才终止。
+ 一个函数内部定义的函数会把**其包含函数的活动对象添加到自己的作用域链中**。因此，在`createComparisonFunction()` 函数中，匿名函数的作用域链中实际上包含`createComparisonFunction()` 的活动对象。

```javascript
let compare = createComparisonFunction('name');
let result = compare({ name: 'Nicholas' }, { name: 'Matt' });
```

![avatar](http://elaiza.cc/img/js_note/Image00021.png)

+ `createComparisonFunction()` 的活动对象**并不能**在它执行完毕后销毁，因为匿名函数的作用域链中**仍然有对它的引用**。在`createComparisonFunction()` 执行完毕后，其执行上下文的作用域链会销毁，但它的活动对象仍然会保留在内存中，**直到匿名函数被销毁后才会被销毁**。
+ TODO
