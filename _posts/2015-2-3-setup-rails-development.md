---
layout: post
title: Set up Rails development with tmux, tmuxinator, macvim
---
本文介绍了安装tmux，tmuxinator，macivim，以及使用[thought bot dotfile](https://github.com/dotfiles)设置环境等
<!--more-->

本文适用于Mac， Linux（部分安装不适用）

安装
===

### Mac下安装tmux：
` brew install tmux`

### Ubuntu下安装：
`sudo apt-get install tmux`

### [Tmuxinator](https://github.com/tmuxinator/tmuxinator)：
```
   gem install tmuxinator
```
在Mac下可以安装MacVim，也是用brew安装，命令略。
我用的zsh
所以在~/.aliases文件里设定了
` alias vm="mvim -v"`
**注意**: =两边无空格

要真正的利用vim显然这样是不够的
所以下一步是安装配置文件，注意这个适用于zsh的

选来选去，最后还是用了[thoughtbot](https://github.com/thoughtbot/dotfiles)的dotfiles文件
具体安装可以参照说明，非常详细了

不过我喜欢railscasts的配色，所以在网上下载了railscasts.vim文件，复制到~/.vim/colors/railscasts.vim
然后记得在~/.vimrc.local中加上一句：
` colorscheme railscasts`

**注意：**如果发现配色不对的话，注意检查terminal的配色：
打开vim，输入：
` :echo &t_Co`
如果得到的是8，就在~/.vimrc.local中再加一句
`set t_Co=265`

最后，祝大家使用愉快 ：》


