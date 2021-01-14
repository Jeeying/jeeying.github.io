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

# 數字

# 特殊值
# 值 vs 參考
