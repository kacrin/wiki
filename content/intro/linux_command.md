---
title: "quick linux"
layout: page
date: 2017-5-14 23:00
---

# command


## 挂起 恢复

### 挂起

作业控制允许将进程挂起并可以在需要时恢复进程的运行，被挂起的作业恢复后将从中止处开始继续运行。只要在键盘上按<ctrl+z>，即可挂起当前的前台作业。

```
$ cat > text.file
<ctrl+z>
［1］ + stopped cat > text.file
```

查看挂起进程
```
$ jobs
［1］+ stopped cat > text.file
```
使用jobs命令可以显示shell的作业清单，包括具体的作业、作业号以及作业当前所处的状态。

### 恢复

fg 命令将挂起的作业放回到前台执行
```
$ fg 1
cat > text.file
```

bg 命令将挂起的作业放到后台执行。
```
$ fg 1
cat > text.file
```

例子:
［例16］ 用户正在使用Emacs，突然需要查看系统进程情况。就首先使用<Ctrl+z>组合键将Emacs进程挂起，然后使用bg命令将其在后台启动，这样就得到了前台的操作控制权，接着键入“ps –x”查看进程情况。查看完毕后，使用fg命令将Emacs带回前台运行即可。其命令格式为：

```
<Ctrl+z>
$ bg emacs
$ ps –x
$ fg emacs
```

默认情况下，fg和bg命令对最近停止的作业进行操作。如果希望恢复其他作业的运行，可以在命令中指定要恢复作业的作业号来恢复该作业。例如：


