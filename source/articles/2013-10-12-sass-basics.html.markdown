---
title: Sass 筆記
date: 2013-10-12 15:46
tags: css,sass
---

![sass](/images/sass.gif)

[Sass](http://sass-lang.com/)是CSS3的擴展，包含了**Variables**、**Nesting**、**Mixins**、**Selector Inheritance**，藉由撰寫Sass來產生出CSS，如此一來CSS變得好維護而且模組化。

#### 安裝
系統須先安裝Ruby，安裝sass 這個gem就行了

```
gem install sass
```

將sass編譯輸出為css

```
sass --watch style.scss:style.css
```

Sass有兩種語法一種是常見的**SCSS**，另一種是**SASS**
，SCSS的語法相容於CSS3(是CSS3的superset)，SASS少掉括弧和分號，利用**縮排**來完成css rule間的階層關係。

READMORE

#### CSS 輸出格式
輸出的css有4種格式`nested`(此為預設)、`expanded`、`compact`、`compressed`

**nested style**好處是css的巢狀結構一目了然

例如:

```
#main {
  color: #fff;
  background-color: #000; }
  #main p {
    width: 10em; }
```

**expanded style** 像我們平常寫的css一樣，右大括號單獨一行，對齊首字元

```
#main {
  color: #fff;
  background-color: #000;
}
#main p {
  width: 10em;
}
```

**compact style** 移除不必要的空白，每條css rule只佔一行(即使多個properties)

```
#main { color: #fff; background-color: #000; }
#main p { width: 10em; }
```

最後是檔案最少，適合production的**compressed style**

```
#main{color:#fff;background-color:#000}#main p{width:10em}
```

在command line 中可由 `--style` 指定style

```
sass --style expanded style.scss
```

#### 註解
有兩種，和C/C++一樣的方式
適用多行的`/* */` 和 單行的`//`，差別就是多行的註解會保留在產生的css中，單行的不會。
所以下面的sass


```
/* This comment is
 * several lines long.
 * since it uses the CSS comment syntax,
 * it will appear in the CSS output. */
body { color: black; }

// These comments are only one line long each.
// They won't appear in the CSS output,
// since they use the single-line comment syntax.
a { color: green; }
```

產生出的css如下

```
/* This comment is
 * several lines long.
 * since it uses the CSS comment syntax,
 * it will appear in the CSS output. */
body {
  color: black; }

a {
  color: green; }
```

#### Variables

syntax: `$變數名稱: 變數值`

ex:

```
$blue: #3bbfce;
$font: Helvetica, sans-serif;
```

引用變數

```
// .scss
.content-navigation {
  font-family: $font-stack;
  border-color: $blue;
  color:
    darken($blue, 9%);
}

// .sass
.content-navigation
  font-family: $font-stack
  border-color: $blue
  color: darken($blue, 9%)
```

#### Nesting

```
// .scss
table.hl {
  margin: 2em 0;
  td.ln {
    text-align: right;
  }
}

li {
  font: {
    family: serif;
    weight: bold;
    size: 1.2em;
  }
}

// .sass
table.hl
  margin: 2em 0
  td.ln
    text-align: right

li
  font:
    family: serif
    weight: bold
    size: 1.2em
```

#### Import
將大的CSS file切割為數個最小的CSS files，再使用`@import`引入
例如:

```
/* _reset.scss */

html,
body,
ul,
ol {
  margin:  0;
  padding: 0;
}
```

```
/* _base.scss */

@import 'reset';

body {
  font-size: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

引用

```
/* main.scss */

@import "reset";
@import "base";
```

#### Mixins
像巨集一樣，經常可重複使用的CSS可寫成Mixin，例如常常需要指定`box-sizing` property，而且須針對不同瀏覽器的實作去設定

```
* {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
}
```

變可以寫成Maxin

```
@mixin bs($bs-type) {
    -webkit-box-sizing: $bs-type;
    -moz-box-sizing: $bs-type;
    box-sizing: $bs-type;
}
```

當某元素需要設定box-sizing時，只需要`@include`即可

```
*{
    @include bs(border-box);
}
```

Mixin傳入的引數設定預設值

```
@mixin bs($bs-type: border-box) {
    -webkit-box-sizing: $bs-type;
    -moz-box-sizing: $bs-type;
    box-sizing: $bs-type;
}
```

#### Extend / Inheritance
使用`@extend`繼承其他已定義好的style rule

```
$color1: #faa732;
$color4: #5bb75b;
$color7: #49afcd;

.btn {
    display: inline-block;
    padding: 4px 12px;
    background-color: #f5f5f5;
}

// Warning Box
.btn-warning {
  @extend .btn;
  border: 2px dotted $color1;
}

// Success Box
.btn-success {
  @extend .btn;
  border: 2px dotted $color4;
}

// Information Box
.btn-info {
  @extend .btn;
  border: 2px dotted $color7;
}
```

輸出css

```
.btn, .btn-warning, .btn-success, .btn-info {
  display: inline-block;
  padding: 4px 12px;
  background-color: #f5f5f5; }

.btn-warning {
  border: 2px dotted #faa732; }

.btn-success {
  border: 2px dotted #5bb75b; }

.btn-info {
  border: 2px dotted #49afcd; }
```

#### Placeholder selector

placeholder selector 可以讓要被繼承的base rule只是用來被繼承，本身並不會在產出的css中

注意到btn前面的符號變成`%`了

```
%btn {
  display: inline-block;
  padding: 4px 12px;
  background-color: #f5f5f5;
}

// Warning Box
.btn-warning {
  @extend %btn;
  border: 2px dotted $color1;
}

// Success Box
.btn-success {
  @extend %btn;
  border: 2px dotted $color4;
}

// Information Box
.btn-info {
  @extend %btn;
  border: 2px dotted $color7;
}
```

產生出來的css也少了`.btn`了，

```
.btn-warning, .btn-success, .btn-info {
  display: inline-block;
  padding: 4px 12px;
  background-color: #f5f5f5; }

.btn-warning {
  border: 2px dotted #faa732; }

.btn-success {
  border: 2px dotted #5bb75b; }

.btn-info {
  border: 2px dotted #49afcd; }
```
