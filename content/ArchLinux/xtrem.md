---
title: "xterm"
layout: page
date: 2016-10-8 01:00
---

# xterm #

cat ~/.Xdefaults 
vi .Xdefaults

use  fc-match -all   look all font

```
#XTerm*font: -misc-fixed-medium-r-normal--20-200-75-75-c-100-iso8859-1
XTerm*font:  -misc-fixed-medium-r-normal--18-120-100-100-c-90-iso10646-1
XTerm*wideFont: -misc-fixed-medium-r-normal-*-18-120-100-100-c-180-iso10646-1
XTerm*saveLines: 4096
XTerm*scrollbar: yes

xterm.geometry: 80x36
xterm*scrollBar: false
xterm*rightScrollBar: true
xterm*loginshell: true
xterm*cursorBlink: true
xterm*background: black
xterm*foreground: gray
xterm.borderLess: true
xterm.cursorBlink: true
xterm*colorUL: yellow
xterm*colorBD: white
!mouse selecting to copy, ctrl-v to paste
!Ctrl p to print screen content to file
XTerm*VT100.Translations: #override /
    Ctrl <KeyPress> V: insert-selection(CLIPBOARD,PRIMARY,CUT_BUFFER0) /n/
    <BtnUp>: select-end(CLIPBOARD,PRIMARY,CUT_BUFFER0) /n/
    Ctrl <KeyPress> P: print() /n
xterm*locale: true
xterm.utf8:     true
xterm*utf8Title: true
xterm*fontMenu*fontdefault*Label: Default
xterm*xftAntialias: true
xterm*cjkWidth:false  
```

my .Xdefaults
```
xterm*background:black
xterm*foreground:gray

!设置开启256颜色显示，默认显示颜色代码为8
xterm.termName: xterm-256color

xterm*font:9*15
```




此方法无效
=========================
1、修改.bashrc文件

~/.bashrc文件添加
if [ "$TERM" == "xterm" ]; then
export TERM=xterm-256color
fi
=========================

重新引导配置文件
xrdb -load ~/.Xresources


显示中文，首先在local.gen下启用中文zh_CN.UTF-8
安装中文字体，
```
xterm*faceName: Consolas: antialias=True: pixelsize=18
xterm*faceNameDoublesize: Microsoft YaHei: antialias=True: pixelsize=16
```