## 1

```js
function foo() {
  console.log(this.a)
}

function doFoo() {
  foo()
}

var obj = {
  a: 1,
  doFoo: doFoo,
}

var a = 2
obj.doFoo()
```

```
2
```

## 2

```js
var a = 10
var obj = {
  a: 20,
  say: () => {
    console.log(this.a)
  },
}
obj.say()

var anotherObj = { a: 30 }
obj.say.apply(anotherObj)
```

```
10
```

## 3

```js
function a() {
  console.log(this)
}
a.call(null)
```

```
window 对象
```

## 4

```js
var obj = {
  name: "cuggz",
  fun: function () {
    console.log(this.name)
  },
}
obj.fun()
new obj.fun()
```

```
cuggz
undefined
```

## 5

```js
var obj = {
  say: function () {
    var f1 = () => {
      console.log("1111", this)
    }
    f1()
  },
  pro: {
    getPro: () => {
      console.log(this)
    },
  },
}
var o = obj.say
o()
obj.say()
obj.pro.getPro()
```

```
1111 window 对象
1111 obj 对象
window 对象
```

## 6

```js
var myObject = {
  foo: "bar",
  func: function () {
    var self = this
    console.log(this.foo)
    console.log(self.foo)
    ;(function () {
      console.log(this.foo)
      console.log(self.foo)
    })()
  },
}
myObject.func()
```

```
bar
bar
undefined
bar
```

## 7

```js
window.number = 2
var obj = {
  number: 3,
  db1: (function () {
    console.log(this)
    this.number *= 4
    return function () {
      console.log(this)
      this.number *= 5
    }
  })(),
}
var db1 = obj.db1
db1()
obj.db1()
console.log(obj.number)
console.log(window.number)
```

```
window 对象
window 对象
obj 对象
15
40
```

## 8

```js
var length = 10
function fn() {
  console.log(this.length)
}

var obj = {
  length: 5,
  method: function (fn) {
    fn()
    arguments[0]()
  },
}

obj.method(fn, 1)
```

```
10
2
```

## 9

```js
var a = 1
function printA() {
  console.log(this.a)
}
var obj = {
  a: 2,
  foo: printA,
  bar: function () {
    printA()
  },
}

obj.foo()
obj.bar()
var foo = obj.foo
foo()
```

```
2
1
1
```

## 10

```js
var x = 3
var y = 4
var obj = {
  x: 1,
  y: 6,
  getX: function () {
    var x = 5
    return (function () {
      return this.x
    })()
  },
  getY: function () {
    var y = 7
    return this.y
  },
}
console.log(obj.getX())
console.log(obj.getY())
```

```
3
6
```

## 11

```js
var a = 10
var obt = {
  a: 20,
  fn: function () {
    var a = 30
    console.log(this.a)
  },
}
obt.fn()
obt.fn.call()
obt.fn()
```

```
20
10
20
```

## 12

```js
function a(xx) {
  this.x = xx
  return this
}
var x = a(5)
var y = a(6)

console.log(x.x)
console.log(y.x)
```

```
undefined
6
```

## 13

```js
function foo(something) {
  this.a = something
}

var obj1 = {
  foo: foo,
}

var obj2 = {}

obj1.foo(2)
console.log(obj1.a)

obj1.foo.call(obj2, 3)
console.log(obj2.a)

var bar = new obj1.foo(4)
console.log(obj1.a)
console.log(bar.a)
```

```
2
3
2
4
```

## 14

```js
function foo(something) {
  this.a = something
}

var obj1 = {}

var bar = foo.bind(obj1)
bar(2)
console.log(obj1.a)

var baz = new bar(3)
console.log(obj1.a)
console.log(baz.a)
```

```
2
2
3
```
