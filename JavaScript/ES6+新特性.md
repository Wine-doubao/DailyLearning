### let、var、const的区别





### Symbol



### Map和Set



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