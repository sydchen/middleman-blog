---
title: Middleman 介紹
date: 2013-10-05 12:16
tags: web development,middleman
---

![Middleman](/images/mm-blue-w-text.svg)

[Middleman](http://middlemanapp.com/)是建立靜態網頁的command-line tool，類似的工具還有[Yeoman](http://yeoman.io/)，試用過發現生態系比較適合Node.js的開發，轉而接觸Middleman，發現很適合自己的開發，畢竟Middleman是採用Ruby和Sinatra為基礎架構，很適合有經驗的Ruby Developer。

READMORE

Middleman整合了不少工具，讓Prototyping更為快速而簡潔。
例如:

* **Project Templates** - 提供了[HTML5 Boilerplate](http://html5boilerplate.com/), [SMACSS](http://smacss.com/), and [Mobile Boilerplate](http://html5boilerplate.com/mobile/) 等基本的template，不用再copy and paste 一堆file，甚至可以客製化專屬的extension，例如有人就開發了[Middleman-blog](http://middlemanapp.com/blogging/)，直接就用Middleman來當blog system。
* **Template**
    * 支援非常多的Templating languages，例如Markdown、Haml、Slim、erb 等
    * Layouts - 把頁面基本的骨架抽取出來成為獨立layout
    * Partials - 特化的網頁區塊可透過Partial讓不同頁面引入共享，例如: sidebar, header, footer
* **Template Helpers** - 不少產生頁面基本元素的Helpers ex: `Link Helpers`，`Asset Helpers`，`Form Helpers`，`Format Helpers`，`Lorem Ipsum Helpers`
* **LiveReload** - 自動重載瀏覽器的頁面
* **Asset Pipeline** - Dependency Management for Assets files like JavaScript and CSS

下一篇將介紹使用Middleman建置Project。
