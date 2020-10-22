---
title: JavaScript中let、const与var的区别
date: 2020-10-18 16:48:21
tags: JavaScript
category: 文章
header-img: https://pic.tnli.xyz/header-img/03.jpg
---

ES6 推出之前在 `js` 中只能用 `var` 来声明一个变量，在 ES6 中新增了 `let` 与 `const` 两个关键字，分别用来声明变量与常量。

---

## let

### 块级作用域

与 `var` 一样，`let` 也是用于声明一个变量，但是 `var` 所声明的变量作用于函数作用域，而 `let` 所声明的变量作用于块作用域，在 ES6 之前 js 中是不存在块级作用域的，可以看看下面的例子

```js
if (true) {
  var foo = "foo";
  let bar = "bar";
}

console.log(foo); // foo'
console.log(bar); // Uncaught ReferenceError: bar is not defined
```

上面的代码块中 `if` 语句形成了一个块级作用域，在这块级作用域中用 `let` 所声明变量无法在外部被访问，但是用 `var` 所声明的变量在外部却可以成功访问，因为用 `var` 声明的变量作用于函数作用域，所以 `foo` 被隐式绑定在了外层的函数作用域在此也为全局作用域，而 `let` 被绑定在了 `if` 代码块中的块级作用域中，所以在 `if` 语句外部无法访问到变量 `bar`

### 暂时性死区（Temporal dead zone）

js 中存在变量提升的机制，简单来说就是变量的声明与赋值是分开进行的，例如

```js
console.log(foo); // undefined
var foo = "foo";
```

上面的代码块并不会报错，输的为 `undefined`, 这正是因为变量提升机制将变量的声明提升到了当前函数作用域的最顶端，然而若是使用 `let` 进行变量声明则又不同

```js
{
  console.log(foo); // Uncaught ReferenceError: Cannot access 'foo' before initialization
  let foo = "foo";
}
```

上面代码块中在 `let` 声明变量之前尝试对变量进行输出便会报错，当使用 `let` 在块级作用域内声明变量时，这个变量便会被绑定在当前的块级作用域内，在此作用域顶部到变量声明之前的区域该变量都不可被使用，一旦使用到该变量就会报错，这便是 **暂时性死区（TDZ）**

### 重复声明

```js
{
  var a = "a";
  var a = "b";

  console.log(a); // b
}
```

上面代码块中尝试对变量 `a` 进行查询，从输出结果来看后来的声明覆盖了之前的声明，这表明当只有 `var` 进行变量声明时是可以重复声明的，并且后者会覆盖前者

```js
{
  let a = "a";
  var a = "b";
}
// SyntaxError: Identifier 'a' has already been declared
```

当前作用域中出现 `let` 声明并对该变量进行重复声明时便会报错，报错信息是 标识符 `a` 已经被声明，

### 全局变量声明

```js
var foo = "foo";
let bar = "bar";

console.log(foo); // foo
console.log(bar); // undefined
```

从上面的代码可以看出在全局作用域中使用 `var` 进行变量的声明，被声明的变量会被全局对象 `window` 的属性，而使用 `let` 则不会，但在全局作用域中使用 `let` 声明的变量依然会存在于全局作用域中而不会被销毁

## const

`const` 用来声明一个常量，所声明的常量在声明过后不可修改，`const` 在使用中与 `let` 有很多相似的地方，其中有一个重要的区别便是使用 `const` 声明时必须同时初始化所声明的变量，如下面的代码块中，使用 `const` 声明了 `foo` 却没有对其进行初始化，所以出现了语法报错。

```js
const foo; //SyntaxError: Missing initializer in const declaration
```

而 const 与 let 相似的地方有以下几点

1. 在块状作用域中使用 const 声明常量会被绑定在当前块状作用域中，外部不可访问
2. 存在 **暂时性死区（TDZ）** 声明不会被提升
3. 不能进行重复声明
4. 在全局作用域中声明所声明的常量不会成为全局对象的属性

---

```js
const foo = "constant";
foo = "foo"; // TypeError: Assignment to constant variable

const bar = [];

console.log(bar); // []
bar.push("bar");
console.log(bar); // ["bar"]
```

`const foo = "constant"` 声明一个名为 `foo` 的常量并赋值为 `constant`，此时标识符 `foo` 指向的实际上是一个内存地址，该内存地址保存的值为 `constant`，当声明一个常量并将简单类型的数据（string、number、booelan）赋值给该常量时，该常量指向的内存地址储存的就是简单类型的数据本身，而当将一个复杂类型的数据赋值给常量时，实际上常量本身指向的内存地址中保存的为指向该复杂数据本身的指针。所谓的使用 `const` 声明常量后值不可修改，不可修改的实际上是所声明标识符指向的内存地址的值不可修改。例如上面代码块中，将一个空数组赋值给常量 `bar`，之后对该数组进行操作是可行的，因为 `bar` 指向的内存地址保存的只是一个指向该数组的一个指针，只要这个不被修改就不会报错。
