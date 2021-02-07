# JavaScript 笔记

## 变量

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

+ 所有插入的值都会使用`toString()` **强制转型为字符串**，而且任何JavaScript表达式都可以用于插值。嵌套的模板字符串无须转义：

```javascript
    console.log(`Hello, ${ `World` }!`);  // Hello, World!

    let foo = { 
        toString: () => 'World'
    };
    console.log(`Hello, ${ foo }!`);      // Hello, World!
```

+ String.raw() 可以直接获取原始的模板字面量内容

```javascript
    console.log(`\u00A9`);            // ©
    console.log(String.raw`\u00A9`);  // \u00A9
    console.log(String.raw`first line\nsecond line`); //"first line\nsecond line" 只能转\n，不能转实际换行
```

+ Symbol() 符号是原始值，且符号实例是唯一、不可变的。

```javascript
    let genericSymbol = Symbol();
    let otherGenericSymbol = Symbol();

    let fooSymbol = Symbol('foo');
    let otherFooSymbol = Symbol('foo');

    console.log(genericSymbol == otherGenericSymbol);  // false
    console.log(fooSymbol == otherFooSymbol);          // false
```

+ Symbol.for() 对每个字符串键都执行幂等操作。

```javascript
    let fooGlobalSymbol = Symbol.for('foo');       // 创建新符号
    let otherFooGlobalSymbol = Symbol.for('foo');  // 重用已有符号

    console.log(fooGlobalSymbol === otherFooGlobalSymbol);  // true
```

+ 逻辑与操作符（&&）可用于任何类型的操作数，不限于布尔值。如果有操作数不是布尔值，**则逻辑与并不一定会返回布尔值**，而是遵循如下规则；如果第一个操作数是对象，则返回第二个操作数；如果第二个操作数是对象，则只有第一个操作数求值为true 才会返回该对象；如果两个操作数都是对象，则返回第二个操作数；如果有一个操作数是null ，则返回null；如果有一个操作数是NaN，则返回NaN；如果有一个操作数是undefined ，则返回undefined。
+ 逻辑或操作符（||），**如果有一个操作数不是布尔值，那么逻辑或操作符也不一定返回布尔值**。它遵循如下规则。如果第一个操作数是对象，则返回第一个操作数。如果第一个操作数求值为false ，则返回第二个操作数。如果两个操作数都是对象，则返回第一个操作数。如果两个操作数都是null ，则返回null 。如果两个操作数都是NaN ，则返回NaN 。如果两个操作数都是undefined ，则返回undefined 。
+ 原始值指的是**原始类型**的值，也叫 基本类型，例如 Number、Stirng、Boolean、Null、Underfined。引用值指的是**引用类型**的值，例如 Object、Function、Array、Date、RegExp 。类似Python可变变量与不可变变量。
+ 原始类型的初始化可以只使用原始字面量形式。如果使用的是new 关键字，则JavaScript会创建一个Object 类型的实例，但其行为**类似**原始值。下面来看看这两种初始化方式的差异：

```javascript
    let name1 = "Nicholas";
    let name2 = new String("Matt");
    name1.age = 27;
    name2.age = 26;
    console.log(name1.age);    // undefined
    console.log(name2.age);    // 26
    console.log(typeof name1); // string
    console.log(typeof name2); // object
```

+ **ECMAScript中所有函数的参数都是按值传递的。**
+ **ECMAScript中所有函数的参数都是按值传递的。**
+ **ECMAScript中所有函数的参数都是按值传递的。**

```javascript
    function setName(obj) {
    obj.name = "Nicholas";  // obj与 person是同一个地址值，指向同一个地址，影响不到 person
    obj = new Object(); // obj地址值改变，影响不到 person
    obj.name = "Greg";
    }

    let person = new Object();
    setName(person); // 把 person的地址值传给 obj
    console.log(person.name);  // "Nicholas"
```

+ 垃圾回收，用到过两种主要的标记策略：标记清理和引用计数（基本不用）。标记清理垃圾回收程序运行的时候，会标记内存中存储的所有变量（记住，标记方法有很多种）。然后，它会将所有在上下文中的变量，以及被在上下文中的变量引用的变量的标记去掉。在此之后再被加上标记的变量就是待删除的了；引用计数思路是对每个值都记录它被引用的次数。声明变量并给它赋一个引用值时，这个值的引用数为1。如果同一个值又被赋给另一个变量，那么引用数加1。类似地，如果保存对该值引用的变量被其他值给覆盖了，那么引用数减1。当一个值的引用数为0时，就说明没办法再访问到这个值了，因此可以安全地收回其内存了。

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

+ 对象结构
  
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
