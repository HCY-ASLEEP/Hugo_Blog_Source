---
title: "Tmux 使用"
date: 2024-01-24T11:40:20+08:00
draft: false
---
### (Neo)VIM 在 tmux 里面配色不对
在 `.bashrc` 里面加上一句
```bash
alias tmux='tmux -2'
```
最后 `source .bashrc` 立即生效

### (Neo)VIM 在 tmux 里面特殊字符以及中文显示不正常
在 `.bashrc` 里面加上一句
```bash
export LANG=en_US.UTF-8
```
最后 `source .bashrc` 立即生效

### (Neo)VIM 在配饰 OSCYank 之后，在 tmux 中依旧无法剪切到系统剪切板
首先创建 tmux 配置文件
```bash
touch ~/.tmux.conf
```
在 tmux 配置文件里面写入
```bash
set-option -g set-clipboard on
```
重启 tmux 生效

### 在 tmux session 里面切换 session
`Ctrl+b` `s`    (means [s]ession)

### 在 tmux 里面进入滚动模式
`Ctrl+b` `[`
`q` 退出
