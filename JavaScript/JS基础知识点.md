## 数据类型

### JS中的数据类型



### undefined与void 0

undefined代表的是未定义，undefined是由null派生来的，因此将它们定义为相等，但不全等。增加这个特殊值的目的就是为了**正式明确空对象指针（null）和未初始化变量的区别**。

undefined 在JavaScript中不是一个保留字，这意味着可以使用undefined作为一个变量来使用赋值。

undefined 在ES5中已经是全局对象的一个只读属性了，它不能被重写，**但是在局部作用域中还是可以被重写的**。它会影响对undefined值的判断，我们可以用`void 0`作为undefined的替代，因为void运算符能对给定表达式进行求值，然后返回undefined。也就是说void后面可以随便给定表达式，但 void 0 是表达式中最短的，且能节省字节。

### null是Object吗？

null 代表的含义是空对象，且typeof null==="object"，但这只是一个JS历史遗留的bug。

在JS的最初版本中使用的是32位系统，为了性能考虑**使用低位存储变量的类型信息**，其中 000 开头代表是对象，然而 null 表示为全零，所以被错误的判定为object。虽然现在的内部类型判断代码已经改变，但是这个bug一直存在。

### 关于NaN

typeof NaN === "number"

**isNaN和Number.isNaN函数的区别**

- 函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，因此非数字值传入也会返回 true ，会影响 NaN 的判断。
- 函数 Number.isNaN 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 NaN ，不会进行数据类型的转换，这种方法对于 NaN 的判断更为准确。

### typeof与instanceof

typeof 对于原始类型来说，除了null会显示成object，其他都可正常显示；对于对象来说，除了函数都会显示object。所以说typeof并不能准确判断变量到底是什么类型的。

如果想判断一个对象的正确类型，可以考虑使用 instanceof，其内部机制是通过判断对象的原型链中是不是能找到类型的 prototype。自定义实现：

```javascript
function myInstanceof(left, right) {
  // 原始类型直接返回false
  if(left === null || (typeof left!= "object" && typeof left!= "function")) return false;
  // 获取对象的原型
  let proto = Object.getPrototypeOf(left);
  // 获取构造函数的 prototype 对象
  let prototype = right.prototype; 
 
  // 判断构造函数的 prototype 对象是否在对象的原型链上
  while (true) {
    if (!proto) return false;
    if (proto === prototype) return true;
    // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
    proto = Object.getPrototypeOf(proto);
  }
}
```

对于原始类型来说，想直接通过 instanceof 来判断类型是不行的，但改一下instanceof还是可以判断原始类型的：

```javascript
class PrimitiveString {
  static [Symbol.hasInstance](x) {
    return typeof x === 'string'
  }
}
console.log('hello world' instanceof PrimitiveString) // true
```

`Symbol.hasInstance` 是一个能让我们自定义 instanceof 行为的东西，以上代码等同于 `typeof 'hello world' === 'string'`，所以结果自然是 true 了。

这其实也侧面反映了一个问题， instanceof 也不是百分之百可信的。

### 为什么会有**BigInt**的提案？ 

JavaScript中Number.MAX_SAFE_INTEGER表示最大安全数字，计算结果是9007199254740991(2^53-1)，即在这个数范围内不会出现精度丢失（小数除外）。但是⼀旦超过这个范围，js就会出现计算不准确的情况，这在大数计算的时候不得不依靠⼀些第三方库进行解决，因此官方提出了BigInt来解决此问题。 

### 类型转换

**转Boolean**：在条件判断时，除了 `undefined`， `null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 `true`，包括所有对象。

**"=="**运算符:

当两个操作数的类型不相同时

- 如果一个值是null，另一个值是undefined，则它们相等
- 如果一个值是数字，另一个值是字符串，先将字符串转换为数学，然后使用转换后的值进行比较。
- 如果其中一个值是true，则将其转换为1再进行比较。如果其中的一个值是false，则将其转换为0再进行比较。
- 如果一个值是对象，另一个值是数字或字符串，则将对象转换为原始值，再进行比较。

这里有三道题：

```javascript
[]==![] // true
// 首先先执行的是![]，它会得到false，然后[]==false，返回true。

[]==[] // false
{}=={} // false
// 类型一致，它们是引用类型，地址是不一样的，所以为false!
```

**包装类型**：在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，再访问其属性。

**隐式类型转换**：`ToPrimitive(需要转换的对象，期望的结果类型)`方法是 JavaScript 中每个值隐含的自带的方法，用来将值 （无论是基本类型值还是对象）转换为基本类型值。如果值为基本类型，则直接返回值本身；如果值为对象，就进行对象转原始类型。

JavaScript 中的隐式类型转换主要发生在`+、-、*、/`以及`==、>、<`这些运算符之间。而这些运算符只能操作基本类型值，所以在进行这些运算前的第一步就是将两边的值用`ToPrimitive`转换成基本类型，再进行操作。

**对象转原始类型**：

对象在转换类型的时候，会调用内置的 `[[ToPrimitive]]` 函数，对于该函数来说，算法逻辑一般来说如下：

- 如果已经是原始类型了，那就不需要转换了
- 如果需要转字符串类型就先调用 `x.toString()`，结果不是基础类型的话再调用`valueOf`，转换为基础类型的话就返回转换的值。
- 如果需要转数值类型的话就先调用 `valueOf`，结果不是基础类型的话再调用 `toString`。
- 如果都没有返回原始类型，就会抛 TypeError 异常。
- 如果对象为 Date 对象，则`type`默认为`string`；其他情况下，`type`默认为`number`。

当然可以重写 `Symbol.toPrimitive` ，该方法在转原始类型时调用优先级最高。

```javascript
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```

**加减乘除运算符**：

加法运算符不同于其他几个运算符，它有以下几个特点：

- 运算中其中一方为字符串，那么就会把另一方也转换为字符串
- 如果一方不是字符串或者数字，那么会将它转换为数字或者字符串

```javascript
1 + '1' // '11'
true + true // 2
4 + [1,2,3] // "41,2,3"
```

如果你对于答案有疑问的话，请看解析：

- 对于第一行代码来说，触发特点一，所以将数字 `1` 转换为字符串，得到结果 `'11'`
- 对于第二行代码来说，触发特点二，所以将 `true` 转为数字 `1`
- 对于第三行代码来说，触发特点二，所以将数组通过 `toString` 转为字符串 `1,2,3`，得到结果 `41,2,3`

另外对于加法还需要注意这个表达式 `'a' + + 'b'`

```javascript
'a' + + 'b' // -> "aNaN"
```

因为 `+ 'b'` 等于 `NaN`，所以结果为 `"aNaN"`。

对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字

```javascript
4 * '3' // 12
4 * [] // 0
4 * [1, 2] // NaN
```

**比较运算符**：

1. 如果是对象，就通过 `toPrimitive` 转换对象
2. 如果是字符串，就通过 `unicode` 字符索引来比较

```javascript
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  }
}
a > -1 // true
```

在以上代码中，因为 `a` 是对象，所以会通过 `valueOf` 转换为原始类型再比较值。

### 判断数组

```javascript
var arr = []
console.log(arr instanceof Array) // true
console.log(Array.prototype.isPrototypeOf(arr)) // true
console.log(arr.constructor === Array) // true
console.log(Object.prototype.toString.call(arr) === "[object Array]") // true
console.log(Array.isArray(arr)) // true
console.log(Object.getPrototypeOf(arr) === Array.prototype) // true
```

### 数组去重

### for in、for of、forEach、map

- map：数组方法，不改变原数组，返回新数组，可以使用break中断，可以return到外层函数
- forEach：数组方法，不可以使用break中断，不可以return到外层函数
- for-in：用于遍历数组和对象，遍历对象键值(key)，或者数组下标，不推荐循环一个数组；如果要迭代的变量是null或undefined，则不执行循环体。
- for-of：遍历Arrays（数组）,Strings（字符串）,Maps（映射）,Sets（集合）等可迭代的数据结构，按照可迭代对象的 next() 方法产生值的顺序迭代元素。在 ES6 中引入的 for-of 循环，以替代 for-in 和 forEach() ，并支持新的迭代协议。

**for-in和for-of比较**：

- 对于数组的遍历，for-in循环出的是key，for-of循环出的是value。for-in 会返回数组中所有可枚举的属性(包括原型链上可枚举的属性)，for-of 只返回数组的下标对应的属性值。
- 对于对象的遍历，for-in 和 Object.keys(myObject) 都用于枚举对象属性，但for-in遍历是会包括原型方法和属性（性能非常差不推荐使用），Object.keys()不包括。

### 0.1+0.2!=0.3?