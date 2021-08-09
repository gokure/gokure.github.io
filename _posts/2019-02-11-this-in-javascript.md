---
layout: post
title: JavaScript 中的 this 是什么？
date: 2019-02-11 00:00:00 +08:00
category: blog
tags:  [javascript]
---

- 如果这个函数是 `function#bind` 调用的结果，那么 `this` 指向的是传入 `bind` 的参数
- 如果函数是以 `foo.func()` 形式调用的，那么 `this` 值为 `foo`
- 如果是在严格模式下，`this` 将为 `undefined`
- 否则，`this` 将是全局对象（浏览器环境里为 `window`）

```js
class Foo {
  x = 3;
  print() {
    console.log('x is ' + this.x);
  }
}

var f = new Foo();
f.print(); // Prints 'x is 3' as expected

// Use the class method in an object literal
var z = { x: 10, p: f.print };
z.p(); // Prints 'x is 10'

var p = z.p;
p(); // Prints 'x is undefined'
```

_来自 [TypeScript Handbook](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/wiki/this-in-TypeScript.html)_
