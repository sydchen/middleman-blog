---
title: Tmux 1.9 'default-path' option 被移除了
date: 2014-03-05 23:41
tags: tmux
---

Tmux 1.9之前的版本中，當開新視窗時，預設的開啟目錄是**當前視窗裡游標所在Pane裡**的`$PWD`，在1.9之後變成是**執行tmux之前**中的`$PWD`，這改變一點都不貼心阿。

解決辦法就是開啟新視窗時加上`-c 指定目錄`

`.tmux.conf`

```
bind c new-window -c "#{pane_current_path}"

bind - split-window -v -c "#{pane_current_path}"

bind - split-window -h -c "#{pane_current_path}"
```
