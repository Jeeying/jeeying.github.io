---
title: YDKJS - 2_型別
date: 2021-01-12 13:40:51
tags:
- Javascript
- YDKJS
categories:
- Javascript
description: 你所不知道的JS 導讀，型別與文法 - chapter 4
---

# 型別

<b class="color-success">演算法中所操作的每個值（values）都有一個關連的型別。</b>

# 內建型別

定義了七個內建型別（built-in types）：
- null
- undefined
- boolean
- number
- string
- object
- symbol

除了object外，所有的這些型別都叫做「primitive」基本型別值，簡稱「基別值」

```js
typeof undefined // "undefined"
typeof true // "boolean"
typeof 42 // "number"
typeof "42" // "string"
typeof { life: 42 } // "object"
typeof Symbol() // "symbol"
```

<br>

```js
typeof null // "object"
```
<b class="color-info">檢測一個值是否為null</b>
```js
let var = null
!val && typeof val === 'object' // true
```

<br>

```js
typeof function () { } // "function"
```

<b>function是object裡面的一個子型別（subtype）</b>
更確切的說，一個函式被稱作是一個「可呼叫的物件（callable object）」，一個擁有[[call]]內部特性，讓他能夠被調用（invoke）的物件
function擁有特性（properties）
比如函式物件具有一個length特性，length被設為他所宣告的形式參數之數目

```js
function a(a, b, c) {
  this.d = 5
 //...
}
a.length // 3

function b() {
  this.d = 5
 //...
}
b.length // 0
```

<br>

```js
typeof [ 1, 2, 3 ] // "object"
```
array只是物件，也是object的一個子型別，具有「能以數值化的方式來索引」及維護了一個會自動更新的 .length特性

# 作為型別的值

<b class="color-success">在JavaScript中，變數（variables）沒有型別，值（values）才有型別</b>
typeof是在詢問在該變數中的那個值是什麼型別

## 未定義（undefined）vs. 未宣告（undeclared）

目前沒有值的變數實際上擁有undefined這個值
一個undefined變數
<span class="tab">是已經在可取用的範疇（assessible scope）中被宣告了變數，只不過當下沒有其他的值<span>

一個undeclared變數
<span class="tab">尚未在可取用的範疇中，正式被宣告的變數<span>

![undefined vs undeclared](/images/javascript/YDKJS-2/1.png "undefined vs undeclared")

<b class="sub-title">一級函式（First-class Function）</b>
<span class="tab">當函式在那個語言中可以被視為跟其他的變數一樣時，我們稱那樣的程式語言擁有一級函式。</span>

<b class="sub-title">為何typoef Undeclared 是 "undefined"</b>
用來檢查變數是否存在，如若是尚未宣告，會有ReferenceError擲出

```js
// 會丟出一個錯誤
if (val) {
  console.log(val)
}

// 檢查存在與否的一個安全檢查
if (typeof val !== "undefined") {
  console.log(val)
}
```
![typeof undeclared](/images/javascript/YDKJS-2/2.png "typeof undeclared")

<b class="sub-title">host 物件</b>
瀏覽器中的全域內建變數，比如HTMLElement或Node.js

如果是對全域變數進行檢查，可以改使用window.__，因為所有的全域變數都是全域物件window，本身就是物件

```js
if (window.val) {
  // ...
}
```

或是可以使用「依存性注入（dependency injection）」
```js
function foo(featureFoo) {
  const func = featureFoo || () => { }
  func() // ...
}
```
