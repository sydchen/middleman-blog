---
title: 輕巧的程式碼Syntax Highlighter - Prism
date: 2014-03-03 21:39
tags: javascript
---

最近在幫自己的Blog找Syntax Highligher，因為看了[Smashing Magazine](http://www.smashingmagazine.com/)用了[Prism](http://prismjs.com/)，看起來還不錯。最後就決定是他了。

###使用方式

加入`prism.css` 和 `prism.js`

```language-markup
<!DOCTYPE html>
<html>
<head>
	...
	<link href="themes/prism.css" rel="stylesheet" />
</head>
<body>
	...
	<script src="prism.js"></script>
</body>
</html>
```

然後用`pre`將要高亮度顯示的區塊包覆，加入適當的css class，例如下面的`language-markup`

```language-markup
<pre><code class="language-css">p { color: red }</code></pre>
```

當頁面載入時就會自動轉換囉。

### 多語言支持
支援很多種語言是必須的，例如Ruby

```language-ruby
# The Greeter class
class Greeter
  def initialize(name)
    @name = name.capitalize
  end

  def salute
    puts "Hello #{@name}!"
  end
end

# Create a new object
g = Greeter.new("world")

# Output "Hello World!"
g.salute
```

### Plugins
* [Line Highlight](http://prismjs.com/plugins/line-highlight)
* [Line Numbers](http://prismjs.com/plugins/line-numbers)
* [Show Invisibles](http://prismjs.com/plugins/show-invisibles)
* [Autolinker](http://prismjs.com/plugins/autolinker)
* [WebPlatform Docs](http://prismjs.com/plugins/wpd)
* [File Highlight](http://prismjs.com/plugins/file-highlight)

### Themes
除了預設的theme外還有6組themes，可在[這頁面](http://prismjs.com/extending.html)點選不同theme觀看。
