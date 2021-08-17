---
title: YDKJS - 4_Natives
date: 2021-01-18 19:10:53
tags:
- Javascript
- YDKJS
categories:
- Javascript
description: 你所不知道的JS 導讀，型別與文法 - chapter 6
---

# Natives

內建的函式（built-ins functions），常被稱作「natives（原生功能）」，常用到的幾個：
- String()
- Number()
- Boolean()
- Array()
- Object()
- Function()
- RegExp()
- Date()
- Error()
- Symbol()

```js
a = new String('Hello World!')
b = 'Hello World!!'

a.toString() // "Hello World!"
b.toString() // "Hello World!!"

typeof a // "object"
typeof b // "string"

a instanceof String // true
b instanceof String // false
```

建構器形式的值建立動作（new String）之結果是一個包裹了基礎值（primitive）的物件包裹器（object wrapper）
typeof a 是object的子型別（subtypes）

![字串包裹器在chrome上的顯示方式](/images/javascript/YDKJS-4/1.png "字串包裹器在chrome上的顯示方式")

new String，是建立了一個字串包裹器物件，包裹了字串基型值（primitive）本身

## Internal [[Class]]

typeof為"object"的那些值（例如一個陣列）會額外標示有一個內部的[[Class]]特性
可以把這想成是一種內部的分類（classification），而非傳統類別導向編成方法中的類別（class）
這種特性無法被直接取用，但可借取預設的Object.prototype.toString(...)的方法，以目標值呼叫它來間接顯露此特性

```js
typeof [1, 2, 3]    // "object"
Object.prototype.toString.call([1, 2, 3])   // "[object Array]"

typeof {}           // "object"
Object.prototype.toString.call({})          // "[object Object]"

typeof (()=>{})     // "function"
Object.prototype.toString.call((()=>{}))    // "[object Function]"

typeof /regex/      // "object"
Object.prototype.toString.call(/regex/)     // "[object RegExp]"

typeof new Date()   // "object"
Object.prototype.toString.call(new Date())  // "[object Date]"

typeof Symbol('a') // "symbol"
Object.prototype.toString.call(Symbol('a')) // "[object Symbol]"
```

基型值會被「封裝（boxing)」

```js
typeof 'abc'        // "string"
Object.prototype.toString.call('abc')       // "[object String]"

typeof 123          // "number"
Object.prototype.toString.call(123)         // "[object Number]"

typeof true         // "boolean"
Object.prototype.toString.call(true)        // "[object Boolean]"
```

雖然Null()或是Undefined()原生建構器都不存在，但顯露出來的內部[[Class]]值就是"Null"和"Undefined"

```js
typeof null        // "object"
Object.prototype.toString.call(null)        // "[object Null]"

typeof undefined   // "undefined"
Object.prototype.toString.call(undefined)   // "[object Undefined]"
```

# 封裝用的包裹器

基型別值（primitive values）沒有特性（properties）或方法（methods）
所以要存取.length或.toString()會需要包裹基型值的物件包裹器

js會自動封裝基本型別值

<b class="color-success">如果直接使用物件形式試著預先最佳化（preoptimize），實際上會執行的更慢</b>
不需要直接使用封裝的物件包裹器，最好是讓封裝隱含地發生

## 物件包裹器的陷阱

建立了一個false值周圍的物件包裹器，但物件本身卻是truthy
所以只用這個物件與使用其底層的那個false值本身比起來，所產生的行為不同

```js
var a = new Boolean(false)
a // Boolean {false}

!!a // true
```

![boxed primitive values: false](/images/javascript/YDKJS-4/1.png "boxed primitive values: false")

你想要手動封裝一個基本型別值（primitive value），你可以使用Object(...)函式（不帶new關鍵字）

```js
a = 'abc'
b = new String(a)
c = Object(a)

typeof a // "string"
typeof b // "object"
typeof c // "object"

b instanceof String // true
c instanceof String // true

Object.prototype.toString.call(b) // "[object String]"
Object.prototype.toString.call(c) // "[object String]"
```

# 解封裝

<b class="color-info">如果你有一個物件包裹器，想要取出其底層的基本型別值（underlying primitive value），可以使用valueOf方法</b>

```js
a = new String('abc')
b = new Number(42)
c = new Boolean(true)

a.valueOf() // "abc"
b.valueOf() // 42
c.valueOf() // true
```

隱含的解封裝，用該物件包裹器其基本型別值的方法來使用

```js
a = new String('abc')
b = a + '!'

a // String {"abc"}
b // "abc!"

typeof a // "object"
typeof b // "string"

c = new Number(42)
d = c + 3

c // Number {42}
d // 45

typeof c // "object"
typeof d // "number"
```

# 作為建構器的Natives

對於 array、object、function 及正規表達式這些值而言，是使用字面形式（literal form）來建立，會與建構器形式所創建的那些物件相同，不會有未經包裹的值。

## Array(...)

Array(...)建構器前不一定要使用new，省略new的行為，還是會跟你有使用時相同

```js
var a = new Array(1, 2, 3)
a // [1, 2, 3]

var b = Array(1, 2, 3)
b // [1, 2, 3]

c = [1, 2, 3]
c // [1, 2, 3]
```

Array建構器有一種特殊的使用方式，如果只有一個number引數，此建構器不會把該值當作陣列內容，而是將他當成一個長度，來預設陣列的大小

```js
var a = new Array(1, 2, 3)
a // [1, 2, 3]

var b = Array(3)
b // [empty × 3]

var c = []
c.length = 3
c // [empty × 3]
```

其中有一個空插槽（empty slot）存在的陣列稱作為「稀疏陣列（empty array）」

在ES5中，串列（例如陣列值・特性串列）中的尾隨逗號（trailing commas）是被允許的，因此會被忽略並丟棄，所以打了[ , , , ]值，實際上所得到的底層值會像是[empty, empty, empty]，帶有三個空插槽的陣列

```js
var a = [ , , , ]
a // [empty × 3]
```

a.map之所以會失敗是因為那些插槽並不是真的存在，所以沒有map(..)可以迭迨

```js
a = new Array(3)
b = [undefined, undefined, undefined]

a.join('-') // "--"
b.join('-') // "--"

a.map((v, i) => i) // [empty × 3]
b.map((v, i) => i) // [0, 1, 2]
```

<b class="color-info">如果想要真正建立出帶有實際undefined值（而不只是「空補值」的陣列）</b>

```js
var a = Array.apply(null, { length: 3 })
a // [undefined, undefined, undefined]

var b = Array.apply(null, Array(3))
b // [undefined, undefined, undefined]
```

Array.apply會呼叫Array(...)函式，並把{ length: 3 }這個物件值的值展開為引數

## Object(...)、Function(...)、RegExp(...)

Object(...)、Function(...)、RegExp(...)這些建構器通常也是非必須的，除非特殊需求，不然應該避免使用

```js
var c = new Object();
c.foo = 'bar'
c // {foo: "bar"}

var d = { foo: 'bar' }
d // {foo: "bar"}

var e = new Function('a', 'return a * 2;')
var f = function(a) { return a * 2 }
function g(a) { return a * 2 }

var h = new RegExp('^a*b+', 'g')
var i = /^a*b+/g
```

之所以會建議優先選用字面值形式定義，除語法較簡單外，也因為效能因素
<b class="color-success">js引擎會在程式碼執行之前預先編譯（precompiles），並快取（caches）他們</b>
不同於我們目前所見過的其他建構器形式，RegExp(...)具有某些合理的功用：動態地為一個正規表達式定義範式（pattern）
這種情境你會需要使用這個`new RegExp('pattern', 'flags')`形式

```js
var userName = 'Kyle'
var namePattern = new RegExp('\\b(?:' + userName + ')+\\b', 'ig') // /\b(?:Kyle)+\b/gi
var matches = 'Kyle'.match(namePattern) // ["Kyle"]
```

## Date(...)、Error(...)

Date(...)、Error(...)無字面值形式（literal form）可用

獲取當前時間，ES5有定義了一個靜態輔助函式（static helper function）： `Date.now()`

```js
if (!Date.now) {
  Date.now = function () {
    return (new Date()).getTime()
  }
}
```

如果不帶new來呼叫`Date()`，會得到該瞬間日期時間的一個字串表示值（string representation），這個表示值的確定形式在語言規格中並沒有指定，不過各家瀏覽器都傾向於使用類似這樣的方式：`Wed Feb 03 2021 07:10:53 GMT+0800 (Taipei Standard Time)`

直接使用Date，是將Date作為一個函式，他會返回當前時間（UTC）的字符串
new Date是一個構造函式，他會初始化新創建的對象

```js
a = new Date('2020/2/3 13:01:22') // Mon Feb 03 2020 13:01:22 GMT+0800 (Taipei Standard Time)
b = Date('2020/2/3 13:01:22') // "Wed Feb 03 2021 07:12:23 GMT+0800 (Taipei Standard Time)"

typeof a // "object"
typeof b // "string"
```

Error(...)建構器，不管有沒有使用new關鍵字，其行為都相同

```js
a = new Error('hi')
// Error: hi
//     at <anonymous>:1:5
b = Error('hi')
// Error: hi
//     at <anonymous>:2:5

a.stack
// "Error: hi
//     at <anonymous>:1:5"
a.message
// "hi"
a.name
// "Error"
a.toString()
// "Error: hi"
```

![new Error vs Error](/images/javascript/YDKJS-4/2.png "new Error vs Error")


<b class="color-info">建立一個錯誤物件，會將目前的執行堆疊情境（execution stack context）捕捉到該物件中</b>（在多數js引擎中，建構之後，會以一個唯讀的.stack特性對外提供）
在這堆疊情境包括了函式呼叫堆疊（function call stack）及錯誤物件建立處的行號（line number），這使得那個錯誤的除錯工作容易許多

![error contain execution stack context](/images/javascript/YDKJS-4/3.png "error contain execution stack context")

錯誤的物件實體（instances）一般至少會有一個message特性，有時也會有其他的特性（應該將他們視為唯讀）

其他的特定錯誤型別的natives
- EvalError(...)
- RangeError(...)
- ReferenceError(...) 未宣告變數
- SyntaxError(...)
- TypeError(...)
- URIError(...)

## Symbol(...)
## 原生的原型（Native Prototypes）
### 作為預設值的原型

# 參考文章

