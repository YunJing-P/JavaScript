+ 在使用var 声明变量时，由于声明会被提升，JavaScript引擎会自动将多余的声明在作用域顶部合并为一个声明。
  
```javascript
    if(typeof var1 === undefined) {
        var var1 = 1    // 达不到效果，会自动提升
    }
```

+ 因为let 的作用域是块，所以不可能检查前面是否已经使用let 声明过同名变量，同时也就不可能在没有声明的情况下声明它。

```javascript
    if(typeof var2 === undefined) {
        let var2 = 1    // 无效，let声明不能带出块外
    }
```

+ const 的行为与let 基本相同，唯一一个重要的区别是用它声明变量时必须同时初始化变量，且尝试修改const 声明的变量会导致运行时错误。
+ undefined与未声明有区别

```javascript
    let message;    // 这个变量被声明了，只是值为undefined

    // 确保没有声明过这个变量
    // let age

    console.log(message)    // "undefined"
    console.log(age)    // 报错
```

+ 不要直接进行小数运算，因为使用了IEEE 754数值，因此永远不要测试某个特定的浮点值。小数运算可以先*1000再/1000
+ Number() 函数转换对象先调用valueOf() 方法，如果是NaN则调用toString() 方法，再按照转换字符串的规则转换。

```javascript
    Number("Hello world!")    // NaN
    Number("000011")    // 11
```

+ parseInt() 函数转换，从第一个非空格字符开始转换。如果第一个字符不是数值字符、加号或减号，parseInt() 立即返回NaN 。忽略小数点和字符串。第二个参数指定进制，建议始终传给它第二个参数。

```javascript
    parseInt(' 0x11.2and2')    // 17
    parseInt(' 11.2and2', 16)   // 17，指定16可以省了'0x'
```

+ parseFloat() 函数的工作方式跟parseInt() 函数类似，它也是解析到字符串末尾或者解析到一个无效的浮点数值字符为止。只解析十进制值，因此不能指定底数。

```javascript
    parseFloat("22.34.5")   // 22.34
    parseFloat("1234blue")    // 1234，按整数解析
```

+ 转换为字符串:toString() 方法可见于数值、布尔值、对象和字符串值（字符串副本）。null 和undefined 值没有toString() 方法。数值类型接受进制参数；String() 函数：如果值有toString() 方法，则调用该方法（不传参数）并返回结果。如果值是null ，返回"null" 。如果值是undefined ，返回"undefined" 。
+ 模板字面量，模板字面量会保持反引号内部的空格，因此在使用时要格外注意。格式正确的模板字符串可能会看起来缩进不当。

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

    let value = 5;
    let exponent = 'second';
    // 以前，字符串插值是这样实现的：
    let interpolatedString = value + ' to the ' + exponent + ' power is ' + (value * value);

    // 现在，可以用模板字面量这样实现：
    let interpolatedTemplateLiteral = `${ value } to the ${ exponent } power is ${ value * value }`;
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
