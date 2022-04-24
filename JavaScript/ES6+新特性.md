### let、var、const的区别

- var可重复声明，let和const不行
- 变量提升，var会存在变量提升，let和const会有临时性死区，在声明之前访问变量报错
- 块级作用域，let 和 const 只在代码块内有效，var声明变量绑定到window 上

### Symbol

Symbol用来表示独一无二的值，它的引入是为了解决对象属性命名冲突的问题。

使用注意点：

1. Symbol不会被自动转换为字符串

   ```javascript
   let id = Symbol("id");
   alert(id); // 类型错误：无法将 Symbol 值转换为字符串。
   alert(id.toString()); // Symbol(id)，现在它有效了
   ```

2. 使用`Symbol()`创建一个Symbol值，不能使用 new 命令（因为它是一个原始值，且从ES6开始不支持使用原始数据类型创建一个显式包装器对象）；

3. Symbol值作为对象属性名，不能用点运算符，必须放在方括号内，且该属性是公开属性（点表示属性键名为字符串类型，而不是Symbol值）；

4. Symbol 作为属性名，遍历对象的时候

   - 该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。
   - 可以通过`Object.getOwnPropertySymbols()`方法获取指定对象的所有 Symbol 属性名，该方法返回一个数组。
   - 或者使用`Reflect.ownKeys()`方法返回所有类型的键名，包括常规键名和 Symbol 键名。

5. 如果要使Symbol值全局共享（可跨域），可以使用`Symbol.for()`和`Symbol.keyFor()`

   - `Symbol.for()`接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局。
   - `Symbol.for()`与`Symbol()`这两种写法的区别是，前者会被登记在全局环境中供搜索，后者不会，因为`Symbol()`写法没有登记机制，所以每次调用都会返回一个不同的值。
   - `Symbol.keyFor()`方法返回一个已登记的 Symbol 类型值的`key`，也就是说对Symbol.for()创建的Symbol才有用。

**内置的Symbol值**

> 除了定义自己使用的Symbol值以外，ES6还提供的11个内置的Symbol值，指向语言内部使用的方法（都是对象的属性，可以自定义一些方法的行为）

1. **Symbol.hasInstance**：当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法。比如，`foo instanceof Foo`在语言内部，实际调用的是`Foo[Symbol.hasInstance](foo)`。可以用它自定义instanceof操作符在某个类上的行为。

   ```javascript
   class MyClass {
     //等同于 [Symbol.hasInstance]: function (foo) {
     [Symbol.hasInstance](foo) {
       return foo instanceof Array;
     }
   }
   [1, 2, 3] instanceof new MyClass() // true
   ```

   该实例的`Symbol.hasInstance`方法，会在进行`instanceof`运算时自动调用，判断左侧的运算值是否为`Array`的实例。

2. **Symbol.isConcatSpreadable**：对象的该属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开（为true可以展开，为false不能）。数组的默认行为是可以展开的，而类似数组的对象正好相反，默认不展开。

   ```javascript
   let arr1 = ['c', 'd'];
   ['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
   arr1[Symbol.isConcatSpreadable] // undefined
   let arr2 = ['c', 'd'];
   arr2[Symbol.isConcatSpreadable] = false;
   ['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
   
   let obj = {length: 2, 0: 'c', 1: 'd'};
   ['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
   obj[Symbol.isConcatSpreadable] = true;
   ['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
   ```

3. **Symbol.species**

4. **Symbol.match**

5. **Symbol.replace**

6. **Symbol.search**

7. **Symbol.split**

8. **Symbol.iterator**：一个返回一个对象默认迭代器的方法。被 `for...of` 使用。对象进行`for...of`循环时，会调用`Symbol.iterator`方法，返回该对象的默认遍历器。

9. **Symbol.toPrimitive**：指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。`Symbol.toPrimitive`被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式。

   - Number：该场合需要转成数值
   - String：该场合需要转成字符串
   - Default：该场合可以转成数值，也可以转成字符串

10. **Symbol.toStringTag**

11. **Symbol.unscopables**

**应用**

- 由于以 Symbol 值作为键名，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。
- `Symbol.for()`的这个全局登记特性，可以用在不同的 iframe 或 service worker 中取到同一个值。

### Map

> 由于没有格式限制，可以做数据存储。

Map类似于对象，也是键值对的集合，但是Map的键可以为任何类型的值（包括对象）。

**Map和对象的区别**：

- Map默认情况下不包含任何键，而Object有原型
- Map的键可以是任意值，而Object必须是一个String或Symbol
- Map的键值对个数可以通过size属性获取，而Object只能手动计算
- Map可以直接被迭代，而Object需要先获取键才能迭代

**WeakMap与Map的区别**

1. `WeakMap`只接受对象作为键名（`null`除外）

2. `WeakMap`的键名所指向的对象，不计入垃圾回收机制（有助于防止内存泄漏）。

   ```javascript
   const e1 = document.getElementById('foo');
   const e2 = document.getElementById('bar');
   const arr = [
     [e1, 'foo 元素'],
     [e2, 'bar 元素'],
   ];
   // 不需要 e1 和 e2 的时候
   // 必须手动删除引用
   arr [0] = null;
   arr [1] = null;
   ```

如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。比如获取 DOM 节点，每当发生`click`事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是这个节点对象。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。

### Set

> 可用于数组去重。

Set本身是一个构造函数，用来生成Set数据结构。它类似于数组，但其成员的值都是唯一的，没有重复的值，且值可以为任何类型。

- Set内部判断判断两个值是否不同类似于"==="，除了NaN
- Set的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。
- 扩展运算符+Set、Array.from+Set可以用于数组去重

**WeakSet**

> [WeakSet](https://www.bookstack.cn/read/es6-3rd/spilt.2.docs-set-map.md)结构与Set类似，也是不重复值的集合，但是它和Set有两个区别。

1. WeakSet的成员只能是对象，不能是其他类型的值。
2. WeakSet中的对象都是弱引用，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。因此ES6规定WeakSet不能遍历。

**应用场景**：

- 适合临时存放一组对象，以及存放跟对象绑定的信息。
- 储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

### 函数



### 数组API



### 迭代器Iterator



### 生成器Generator



### Yield函数



### Proxy和Reflect

#### Proxy

`Proxy构造函数`用于创建一个对象的的代理，从而**实现基本操作的拦截和自定义**（如属性查找、赋值、枚举、函数调用等）。通俗来讲，它在目标对象之前架设一道“拦截”，外界对该对象的访问，都必须先通过这层拦截，也就是说`proxy可以对外界的访问进行过滤和改写`，劫持整个对象并返回一个新对象。

基本用法：

```javascript
const handler = {
  get: function(target,prop) {
    console.log('我被获取啦！');
    return prop in target ? target[prop] : '我没有这个属性';
  }
}
const obj = {
  a: 1
}
const p = new Proxy(obj,handler);
console.log(p.a); 
// 我被获取啦! 
// 1
```

在上例中，代理 p 会将所有应用到它上面的操作转发到这个对象上，如赋值。

在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。有些原生对象的内部属性，只有通过正确的`this`才能拿到，所以 Proxy 也无法代理这些原生对象的属性。这时，`this`绑定原始对象，就可以解决这个问题。

#### Reflect

`Reflect对象`是一个内置对象，而不是构造函数。Reflect中的所有属性和方法都是**静态**的，就像Math对象。

ES6 中将 Object 的一些明显属于语言内部的方法移植到了 Reflect 对象上（如[[Get]]、[[Set]]，当前某些方法会同时存在于 Object 和 Reflect 对象上），未来的新方法会只部署在 Reflect 对象上。Reflect 对象对某些方法的返回结果进行了修改，使其更合理。

**对于每个可被 `Proxy` 捕获的内部方法，`Reflect` 都有一个对应的方法，其名称和参数与 `Proxy` 钩子相同。**

一般 Proxy 和 Reflect 会一起使用，如果 Proxy 想把调用转发给对象，只需要使用相同的参数调用 `Reflect.<method>`就可以了。

```javascript
let user = {
  name: "John",
};

user = new Proxy(user, {
  get(target, prop, receiver) {
    console.log(`GET ${prop}`);
    return Reflect.get(target, prop, receiver); // (1)
  }
});

let name = user.name; // "GET name"
console.log(name); // "John"
```

在该例子中，Proxy 拦截了对象user的get操作，调用相同的Reflect.get可以完成原有的get操作，Proxy可以在此基础上进行其他操作。这里也可以使用`target[prop]`得到user的name属性值，大部分情况使用这种情况都没问题，但是使用Reflect有另一个好处：它可以接受receiver参数，可以将prop绑定在最外层的实例对象上，即让this正确指向最外层实例。

为什么不用call/apply这类函数绑定this值呢？这是因为这是一个getter，它不是被“调用”的，而是被访问的。

可参考[Proxy 和 Reflect](https://juejin.cn/post/6844904090116292616#heading-6)。

### Promise、async、await