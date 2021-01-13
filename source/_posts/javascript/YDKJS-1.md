---
title: YDKJS - 1_概述
date: 2021-01-09 15:24:41
tags:
- Javascript
- YDKJS
categories:
- Javascript
description: 你所不知道的JS 導讀，型別與文法 - chapter 1, 2, 3
---

# 概述

通常會說js是直譯式，但實際上js引擎會即時編譯那些程式，然後立即執行經過編譯的程式碼。

## 輸出

```js console
console.log(...)
```

## 輸入

利用像是alert的談窗，接收使用者輸入的資訊
```js prompt
let a = prompt('please tell me your age:');
console.log(a);
```
![prompt request](/images/javascript/YDKJS-1/1.png "prompt request")

![prompt result](/images/javascript/YDKJS-1/2.png "prompt result")

## 名詞

強制轉型 coercion, coerce
隱含的 implicitly, implicit
寬鬆地相等 loosely equal
編譯過程 compilation
變動 vary
靜態型別 static typing
強制型別 type enforcement
弱型別 weak typing
動態型別 dynamic typing
範疇 scope
<span class="tab">一群變數以及這些變數如何透過名稱來存取的規則所成的一個集合，只有在函式內的程式碼能夠存取該函式範疇內的變數</span>
語彙範疇 lexical scope
<span class="tab">一個範疇中的程式碼能夠存取該範疇以及其外層任何範疇內的變數</span>
控制反轉 Inversion of Control, IoC
線性推理能力 linear reason-ability
[Web Workers](https://developer.mozilla.org/zh-TW/docs/Web/API/Web_Workers_API/Using_web_workers)
單指令流多資料流Single Instruction Multiple Data, SIMD
基準測試 benchmarking

<br>

# JavaScript入門

## 值與型別

js擁有<b class="color-success">具有型別的值（typed values）</b>，而非具有型別的變數（typed variables）

- string
- number
- boolean
- null與undefined
- object
- symbol

```js js型別
typeof a  // "undefined"

var a
typeof a // "undefined"

a = 42
typeof a // "number"

a = true
typeof a // "boolean"

a = null
typeof a // "object"

a = undefined
typeof a // "undefined"

a = { b: 'c'}
typeof a // "object"

a = [1, 2]
typeof a // "object"

a = Symbol('a')
typeof a // "symbol"

function a() { }
typeof a // "function"

a = () => { }
typeof a // "function"
```

typeof並不是在詢問「a的型別是什麼」，而是在詢問「目前在a中的值的型別是什麼」
在js中，<b class="color-success">只有值擁有型別，變數單純只是那些值的簡單容器</b>

### 物件

- 點記號法
```js 點記號法
obj.a
```

- 方括號記號法
```js 方括號記號法
obj['a']
```

### 陣列
持有值（任意型別）的一個object，以數值化的索引位置來儲存
會自動更新length

### 函式

函式是object的子型別

### 內建方法

物件包裹器在其原型之上定義了某些方法
一個string值會被一個String物件所包裹，一個number會被一個Number物件所包裹，一個boolean值則是被一個Boolean物件所包裹

### 比較值

兩種主要的值比較動作
- 相等性（equality）
- 不相等性（inequality）

### 強制轉型（Coercion）

強制轉型有兩種形式
- 明確的
```js
var a = '42'
var b = Number(a)

a // "42"
b // 42
```

- 隱含的
```js
var a = '42'
var b = a * 1

a // "42"
b // 42
```

### Truthy & Falsy

Falsy
- "", `''`
- 0, -0, NaN
- null, undefined
- false

不在這個清單上被轉換成boolean時，都是truthy

### 相等性

== vs ===
<b class="color-info">== 檢查值的相等性，並允許強制轉型，而===檢查值的相等性，但不允許強制轉型</b>

```js
a = [1, 2]
b = [1,2]
c = "1,2"
d = "1, 2"

a == c // true
a == d // false

b == c // true
b == d // false

a == b // false

a.toString() // "1,2"
b.toString() // "1,2"
```

```js
'a' > 'b' // false
'a' < 'b' // true

42 > 'foo' // false
42 < 'foo' // false
42 == 'foo' // false

Number('foo') // NaN

42 > NaN // false
42 < NaN // false
42 == NaN // false
```

## 變數

a-z, A-Z, $或_為開頭

### 函式範疇

#### 拉升

每當一個範疇內有一個var宣告出現，那個宣告就會屬於整個範疇，可從該範疇的任何位置取用他
這種行為被比喻為所謂的拉升（hoisting）動作

#### 巢狀範疇

當你宣告一個變數，他在該範疇中的任何位置都可取用，而較低層或叫內層的範疇也能夠取用他

## 條件式

```js
if (a == 42) {
  // do somethings
} else if (a == 41) {
  // do somethings
} else {
  // do somethings
}

switch (a) {
  case 42:
    // do somethings
    break;

  default:
    // do somethings
    break;
}
```

## Strict模式

ES5為該語言引進了一種「嚴格模式（strict mode）」，他會以更嚴謹的方法來規範某些行為。
<b class="color-success">讓程式碼遵循一組較為安全且適當的指導原則，程式碼更容易被引擎最佳化</b>

嚴格模式，不允許省略var宣告而隱含且自動建立全域變數的情形發生

## 作為值的函式

```js
function foo() {
  // ..
}
```

foo基本上就是位於外層包圍範疇中的一個變數，他被賦予了所宣告的function的一個參考
因此這個function本身是一個值，就是42或[1, 2]那樣
可以將一個值傳給一個函式，也能將函式指定給變數，或傳入或回傳自其他函式的值

### 即刻調用的函式運算式（IIFE）

```js
(function IIFE() {
  console.log('hello')
})()
// hello
```

### Closure 閉包

在函式執行完畢之後，讓你記住並持續存取一個函式範疇（其變數）的一種方法
```js
function makeAdder(x) {
  function add(y) {
    return y + x
  }
  return add
}

var plusOne = makeAdder(1)
var plusTen = makeAdder(10)

plusOne(3) // 4
plusTen(5) // 15
```

### 模組

js中closures最常見的用法式所謂的模組模式（module pattern）
模組能讓擬定意思有的實作細節（變數、函式），將他們隱藏起來，不讓外部世界看到，並提供一個可從外部取用的公開API

## this識別字

this的指向取決於函式被呼叫的方式
this關鍵字彙依據函式的執行方式動態地被繫結（bound）
```js
function foo() {
  console.log(this.bar)
}

var bar = 'global'

var obj1 = {
  bar: 'obj1',
  foo: foo,
}

var obj2 = {
  bar: 'obj2'
}

foo() // global
obj1.foo() // obj1
foo.call(obj2) // obj2
new foo() // undefined
```

非嚴格模式，foo()最後會將this設定為全域物件，而在嚴格模式中，this會是undefined，因此你會在存取bar特性時得到錯誤
obj1.foo()將this設為了obj1物件
foo.call(obj2)將this設為了obj2物件
new foo()將this設為了一個全新的空物件

## 原型

當你參考一個物件上的某個特性（property），如果該特性並不存在，Javascript就會自動使用該物件內部的原型參考（prototype reference）在另外的物件上尋找那個特性，

最簡單的原型參考範例
```js
var foo = {
  a: 42
}

var bar = Object.create(foo)

bar.b = 'hello'

bar.b // "hello"
bar.a // 42
```

## 舊功能與新特色

### Polyfilling

依據一個較新功能的定義，產生具有相同行為，但能在舊的JS環境中執行的程式碼
```js
if (!Number.isNaN) {
  Number.isNaN = function isNaN(x) {
    return x !== x
  }
}
```

能夠信任的polyfill
- [ES5-shim](https://github.com/es-shims/es5-shim)
- [ES6-shim](https://github.com/es-shims/es6-shim)

### Transpiling

用新語法來撰寫，部屬到瀏覽器是經過transpile的程式碼

優點
- 易讀、易維護
- 瀏覽器對新語法的最佳化

預設參數
```js
function foo(a = 2) {
  console.log(a)
}

function foo() {
  var a = arguments[0] !== (void 0) ? arguments[0] : 2;
  console.log(a)
}

void 0 // undefined
```

轉換語法的工具
[babel](https://github.com/babel/babel)

## 非Javascript的部分

不是JavaScript的JavaScript是DOM API
```js
var el = document.getElementById('foo')
```

在瀏覽器中執行，會有document這個全域變數，不是由js引擎所提供，也沒有特別受到js語言規格的管制，被稱為host物件
alert是由瀏覽器提供給你的js程式，不是由js引擎本身所提供的
console.log也是相同的，你的瀏覽器提供這機制，將他們掛接到開發人員工具


---
參考閱讀
- [你懂 JavaScript 嗎？#26 程式效能（Program Performance）](https://cythilya.github.io/2018/11/02/program-performance/)
- [Ecma Standards](https://www.ecma-international.org/publications/standards/Standard.htm)
- [計算機組織架構](https://sites.google.com/site/jigaizigongdaolun/ji-suan-ji-zu-zhi/5-6liangzhongbutongdejiagou)