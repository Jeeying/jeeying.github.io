---
title: YDKJS - 3_值
date: 2021-01-14 11:20:01
tags:
- Javascript
- YDKJS
categories:
- Javascript
description: 你所不知道的JS 導讀，型別與文法 - chapter 5
---

# 陣列

array是以數值來索引的物件
如果key可以被強制轉型為number，js就會假設你想要把他當作一個number來索引，而非作為一個string鍵值

```js
a = []

a[0] = 1
a.foobar = 2

a.length // 1
a.foobar // 2

a['5'] = 6
a.length // 6
a // [1, empty × 4, 6, foobar: 2]
```

在一個array值上使用delete會從那個array移除指定的那個插槽（slot），不過即使你移除的是最後一個元素，他也不會更新length特性

```js
a = [1,2,3]
delete a[1]

a // [1, empty, 3]
a.length // 3
```

```js
a = []

a[0] = 1
a[2] = 3

a // [1, empty, 3]
a.length // 3
```

## 類陣列（Array-Likes）

以數值索引的值所成的群集

將類陣列轉換為真正的array，就可以使用array的工具函式，如indexOf, concat, forEach

轉換方式

```vbscript
' hidden code
```

{% codeblock lang:js mark:2 %}
function foo() {
  var arr = Array.prototype.slice.call(arguments);
  arr.push('bam');
  console.log(arr) // ["a", "b", "c", "bam"]
}
{% endcodeblock %}

{% codeblock lang:js mark:2 %}
function bar() {
  var arr = Array.from(arguments);
  arr.push('bam');
  console.log(arr) // ["a", "b", "c", "bam"]
}
{% endcodeblock %}

# 字串

雖然string與陣列相似

```js
a = 'foo'
b = ['f', 'o', 'o']

a.length // 3
b.length // 3

a.indexOf('o') // 1
b.indexOf('o') // 1

c = a.concat('bar') // foobar
d = b.concat(['b', 'a', 'r']) // ["f", "o", "o", "b", "a", "r"]

a === c // false
b === d // false

a // foo
b // ['f', 'o', 'o']
```

但<b class="color-success">js的string實際上並不等同於字元所成的陣列</b>

```js
a[1] = 'x'
b[1] = 'x'

a // foo
b // ["f", "x", "o"]
```

<b class="color-info">js的string是不可變的（immutable），而array則是相當可變的（mutable）</b>

可以借用array的那些不會直接改變內容的方法

```js
a = 'abcde'

a.join // undefined
a.map // undefined

Array.prototype.join.call(a) // "a,b,c,d,e"

Array.prototype.map.call(a, (v) => v += '!').join('') // "a!b!c!d!e!"
```

如果使用一些會改變內容的方法，會報錯

```js
b = [1, 2, 3]
b.reverse() // [3, 2, 1]
b // [3, 2, 1]

Array.prototype.reverse.call(a)
// Cannot assign to read only property '0' of object '[object String]'
```

![string use Array mutable method cause error](/images/javascript/YDKJS-3/1.png "string use Array mutable method cause error")

將字串反轉
只適用於無複雜（unicode）字元，如astral符號、多位元組字元等。

```js
a = 'abcde'
a.split('').reverse().join('') // "edcba"
```

如若想精密處理可參考[Esrever](https://github.com/mathiasbynens/esrever)

# 數字

<b class="color-success">js的number實作是基於「IEEE 754」的雙精準度格式（64位元的二進位數字）</b>

## IEEE 754

定義單精度（32 bit）、雙精度（64 bit）跟特殊值（無窮大、NaN）等的表示方式

<b class="sub-title">單精度浮點數（single precision）</b>（32 bit）
組成：
- sign（1 bit）正負號
- exponent（8 bit）正規化後的次方數
- fraction（23 bit）小數

3.5
　= 3 + 0.5
　= 2<sup>1</sup> + 2<sup>0</sup>  + 1/2<sup>1</sup>
　= 11.1（二進制）
　= 1.11 x 2<sup>1</sup>（正規化後）
　= +1.11 x 2^1
　　sign + 1 bit
　　exponent 1 + 127 = 1000 0000（二進制）
　　fraction .11 = 1100 0000 0000 0000 0000 0000

因為3.5可以轉換成2的次方數，所以不需要犧牲任何精準度
但如果是3.7，因為沒辦法換成2的次方數，會產生誤差

```js
(3.5).toString(2) // "11.1"
(3.7).toString(2) // "11.10110011001100110011001100110011001100110011001101"
```

<b class="sub-title">雙精度浮點數（double precision）</b>（64 bit）
組成：
- sign（1 bit）正負號
- exponent（11 bit）正規化後的次方數
- fraction（52 bit）小數

雖然可以更精準，但是到了小數下16位還是會出現誤差

```js
a = 0.9999999999999999 // 小數點16位
a // 0.9999999999999999

b = 0.99999999999999999 // 小數點17位
b // 1
```

## 數值語法

number值能夠取用內建於Number.prototype中的方法

```js
42.0                    // 42
42.                     // 42
42.00000                // 42

.42                     // 0.42

5e3                     // 5000
(-100).toExponential()  // "-1e+2"
(0.001).toExponential() // "1e-3"

```

```js
a = 23.45

a.toFixed(0)  // "23"
a.toFixed(1)  // "23.4"
a.toFixed(2)  // "23.45"
a.toFixed(3)  // "23.450"
a.toFixed(4)  // "23.4500"
a.toFixed(5)  // "23.45000"

a.toPrecision(0) // RangeError: toPrecision() argument must be between 1 and 100

a.toPrecision(1) // "2e+1"
a.toPrecision(2) // "23"
a.toPrecision(3) // "23.4"
a.toPrecision(4) // "23.45"
a.toPrecision(5) // "23.450"
a.toPrecision(6) // "23.4500"
a.toPrecision(7) // "23.45000"
```

注意 `.` 運算子，因為他也是一個有效的數值字元，<b>會先被解讀為number字面值的一部份</b>，而非解讀唯一個特性存取器（property accessor）

```js
42.toFixed(2) // SyntaxError: Invalid or unexpected token
42..toFixed(2) // "42.00"
(42).toFixed(2) // "42.00"
0.42.toFixed(2) // "0.42"
```

![. is number literals first then property accessor](/images/javascript/YDKJS-3/2.png ". is number literals first then property accessor")

```js 二進位（binary）
0b101001101 // 333
```

```js 八進位（octal
0o515 // 333
```

```js 十六進位（hexadecimal）
0x14d // 333
```

## 小的十進位值

<b class="sub-title">約整誤差（rounding error）</b>
　值作為比較的容許值（tolerance），這個很小的值常被叫做「machine epsilon」，在js中通常為 2^-52

```js
Math.pow(2, -52) // 2.220446049250313e-16
Number.EPSILON   // 2.220446049250313e-16

Number.EPSILON === Math.pow(2, -52) // true
```

用epsilon來比較兩個值的差值，是否在約整誤差的容許範圍內

```js
a = 0.1 + 0.2
b = 0.3
Math.abs(a - b) < Number.EPSILON // true
```

能被表示的最大浮點數值約是1.7976931348623157e+308，即2^971 * (2^53 - 1)
最小浮點數5e-324，即2^-1074，其不是負數，但非常接近0

```js
Number.MAX_VALUE                          // 1.7976931348623157e+308
Math.pow(2, 971) * (Math.pow(2, 53) - 1)  // 1.7976931348623157e+308
Math.pow(2, 971) * (Math.pow(2, 53) - 1) === Number.MAX_VALUE // true

Number.MIN_VALUE   // 5e-324
Math.pow(2, -1074) //5e-324
Math.pow(2, -1074) === Number.MIN_VALUE // true
```

## 安全的整數範圍

能安全表示的最大整數2^53 - 1
最小整數

```js
Number.MAX_SAFE_INTEGER // 9007199254740991
Math.pow(2, 53) - 1      // 9007199254740991
Math.pow(2, 53) - 1 === Number.MAX_SAFE_INTEGER // true

Number.MIN_SAFE_INTEGER // -9007199254740991
Math.pow(-2, 53) + 1     // -9007199254740991
Math.pow(-2, 53) + 1 === Number.MIN_SAFE_INTEGER // true
```

## 測試整數

```js 試測某值是否為整數
Number.isInteger(42.)  // true
Number.isInteger(42.0) // true
Number.isInteger(42.3) // false
```

```js isInteger的polyfill
if (!Number.isInteger) {
  Number.isInteger = function (n) {
    return typeof num === "number" && n % 1 === 0;
  }
}
```

```js 測試一個值是否為安全整數
Number.isSafeInteger(Number.MAX_VALUE) // false
Number.isSafeInteger(Number.MAX_SAFE_INTEGER) // true
Number.isSafeInteger(Math.pow(2, 53)) // false
Number.isSafeInteger(Math.pow(2, 53) - 1) // true
```

```js isSafeInteger的polyfill
if (!Number.isSafeInteger) {
  Number.isSafeInteger = function (n) {
    return Number.isInteger(n) && (Math.abs(num) <= Number.MAX_SAFE_INTEGER)
  }
}
```

## 32位元（有號）整數

雖然安全的整數可以到2^53 - 1，但有些位元運算子（bitwise operators）只定義給32位元的number使用
要強制將一個number轉為32位元的有號整數值（signed integer value），就用 | 0，因為 | 運算子只適用於32位元的整數值（與0進行or運算，基本上就是逐位元（bitwise）的no-op（no operation，無動作）運算）

# 特殊值

## 非值（Nonvalue）的值

<b class="sub-title">null</b>

- 是一個空值
- 代表曾經有過一個值，但不再有了
- 一個特殊關鍵字（不是一個識別字，不能將他當作一個變數）

<b class="sub-title">undefined</b>

- 一個缺少的值
- 代表尚未有值
- 是一個識別字

### undefined

在strict模式中，不能指定一個值給全域的undefined識別字
在strict或非strict模式中，都可以建立一個名為undefined的區域變數

```js
null = 2 // SyntaxError: Invalid left-hand side in assignment
undefined = 2 // 2
undefined // undefined

function a() {
  let undefined = 2
  console.log(undefined) // 2
}
```

### void operator

void __ 會讓任何值變得「空無所有」，永遠都會得到undefined

```js
void 0    // undefined
void 1    // undefined
void true // undefined
```

## 特殊數字

### 非數字的數字

<b class="sub-title">NaN</b>

一種「哨符值」（sentinel value，被指定了特殊意義的普通值），代表在number集合中的某種特殊錯誤情況
<b class="color-warning">NaN是一個「無效的數字（invalid number）」</b>，而非「not a number」

```js
typeof NaN // "number"
```

NaN唯一一個非反身（not reflexive）值

```vbscript
' hidden code
```
{% codeblock lang:js mark:3 %}
NaN == NaN  // false
NaN === NaN // false
NaN !== NaN // true
{% endcodeblock %}

記得使用Number.isNaN去判斷該值是否為NaN

```js
window.isNaN(NaN) // true
window.isNaN('dd') // true
Number.isNaN('dd') // false
```

```js isNaN的polyfill
if (!Number.isNaN) {
  Number.isNaN = function (n) {
    return typeof n === "number" && window.isNaN(n)
  }
}

if (!isNaN) {
  isNaN = function (n) {
    return n !== n
  }
}
```

<b class="sub-title">無限</b>

```js
1 / 0 // Infinity
Number.POSITIVE_INFINITY // Infinity

-1 / 0 // -Infinity
Number.NEGATIVE_INFINITY // -Infinity
```

IEEE 754的約整至最接近值（round-to-nearest）模式

```js
Number.MAX_VALUE + Math.pow(2, 969) // 1.7976931348623157e+308
Number.MAX_VALUE + Math.pow(2, 970) // Infinity
```

```js
Infinity / Infinity // NaN
-Infinity / Infinity // NaN


5 / Infinity // 0
-5 / Infinity // -0
```

<b class="sub-title">零</b>

```js
0  // 0
-0 // -0

a = 0 / -3
a // -0

a.toString()      // "0"
a + ''            // "0"
String(a)         // "0"
JSON.stringify(a) // "0"

+ '-0'            // -0
Number(-0)        // -0
JSON.parse("-0")  // -0

-0 == 0   // true
- 0 === 0 // true
0 > -0    // false
```

在某些特定應用中，會使用一個值的大小來表示某項資訊（像是動畫每影格的移動速率），正負號表示另一項資訊（像是移動的方向）

```js 是否為-0
function isNegZero(n) {
  n = Number(n)
  return n === 0 && (1 / n === -Infinity)
}

isNegZero(0) // false
isNegZero(-0) // true
```

## 特殊相等性

測試兩個值是某絕對相等
Object.is
![Sameness Comparisons](/images/javascript/YDKJS-3/3.png "Sameness Comparisons")

```js Object.is的polyfill
if (!Object.is) {
  Object.is = function (v1, v2) {
    // 測試-0
    if (v1 === 0 && v2 === 0) {
      return 1 / v1 === 1 / v2 // Infinity !== -Infinity
    }
    // 測試NaN
    if (v1 !== v2) {
      return v2 !== v2
    }
    // 其他所有情況
    return v1 === v2
  }
}
```

# 值 vs 參考

值可藉由值的拷貝（value-copy）或參考的拷貝（reference-copy）來指定或傳遞
無法從某個js變數指向另一個變數的一個參考
<b class="color-success">js的參考指向一個（共有）值</b>，<b>所以如果你有不同的參考，他們一定都是指向單一個共有值（shared value）的個別參考，他們不會是指向彼此的參考或指標</b>

<b class="color-alert">值的型別完全控制了該值是藉由值的拷貝（by value-copy）或是藉由參考的拷貝（by reference-copy）</b>

```js
a = 2
b = a // b一定是a中的值的一份拷貝

b += 1
b // 3
a // 2

c = [1, 2, 3]
d = c // d是指向共有值[1, 2, 3]的一個參考
// c跟d都是對一個共有值[1, 2, 3]的個別參考，任一對參考來修改，兩個參考都會參考到新的值

d.push(4)
d // [1, 2, 3, 4]
c // [1, 2, 3, 4]
```

<b class="color-success">簡單的值（即純量的基型值，scalar primitives）永遠都是藉由<span class="color-alert">值的拷貝（value-copy）</span>來指定或傳遞的</b>，如null, undefined, string, number, boolean以及symbol

<b class="color-success">複合值object（包括array以及所有封裝用的物件包裹器，boxed object wrappers）和function，永遠都會在指定或傳遞時，建立<span class="color-alert">參考的拷貝（reference-copy）</span></b>

<b class="color-warning">參考指向的是值本身，而非變數</b>，無法使用一個參考來變更另一個參考所指向的東西

```js
a = [1, 2, 3]
b = a

b = [4, 5, 6]

b // [4, 5, 6]
a // [1, 2, 3]
```

```js
function foo(x) {
  x.push(4)
  console.log(x) // [1, 2, 3, 4]

  x = [4, 5, 6]
  x.push(7)
  console.log(x) // [4, 5, 6, 7]
}

a = [1, 2, 3]
foo(a)

a // [1, 2, 3, 4]
```

{% codeblock lang:js mark:5 讓傳進來的array被賦予新的內容 %}
function foo(x) {
  x.push(4)
  console.log(x) // [1, 2, 3, 4]

  x.length = 0
  console.log(x) // []

  x.push(4, 5, 6, 7)
  console.log(x) // [4, 5, 6, 7]
}

a = [1, 2, 3]
foo(a)

a // [4, 5, 6, 7]
{% endcodeblock %}

<b class="color-info">無法直接控制或覆寫值拷貝或參考的行為，那些語意完全是由底層的值之型別來控制的</b>

想讓純量的基型值可被更新，包裹在object裡

```js
function foo(wrapper) {
  wrapper.a = 42
}

obj = {
  a: 2
}

foo(obj)

obj // { a: 42 }
```

如果是封裝在Number的物件包裹器，值不會被改變
因為底層的純量基型值是不可變的（string和boolean也會有相同結果）

```js
function foo(x) {
  x += 1
  console.log(x) // 3
}

a = 2
b = new Number(a)

foo(b)
b // Number {2}
a // 2
```

如果一個Number物件持有純量基型值2會被解封（unboxed），自動從那個Number物件中被擷取（extracted）出來，所以x += 1會以非常細微的方式，將x從指向那個Number物件的一個共有參考，改為單純持有純量基型值3

可以在Number物件上新增特性，只是不能修改他內部的基型值，所以能藉由那些額外的特性間接的交換資訊

# 參考文章

[從 IEEE 754 標準來看為什麼浮點誤差是無法避免的](https://medium.com/starbugs/see-why-floating-point-error-can-not-be-avoided-from-ieee-754-809720b32175)
[Number.MAX_SAFE_INTEGER与Number.MAX_VALUE引发的思考](https://zhuanlan.zhihu.com/p/67470550)
[Equality comparisons and sameness](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)
