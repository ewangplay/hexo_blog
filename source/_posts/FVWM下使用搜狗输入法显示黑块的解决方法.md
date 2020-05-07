title: FVWM下使用搜狗输入法显示黑块的解决方法
date: 2014-06-29 21:44:06
tags: [FVWM]
categories: 技术记录
---
## 问题
前几天在浏览[UbuntuKylin](http://www.ubuntukylin.com/)时发现搜狗输入法发布了for Linux版本，刚好我Ubuntu 14.04上使用的ibus sunpinyin输入法有些问题，就决定换成搜狗输入法。<br>
在我的Ubuntu 14.04上安装后一切OK，使用起来很是舒服。于是切换到我FVWM的桌面环境下，使用下面的命令加载：

    +I Exec exec fcitx-qimpanel &

但是问题来了： 在FVWM下加载了搜狗输入法后，桌面上竟然显示一个小黑方块，而且跟随光标的移动而移动。<br>
我一开始以为是搜狗输入法的配置上可以调整，翻遍了所有的配置项，也没能把这个小黑方块给去掉，郁闷死了！<br>

## 解决方案
最后实在没有办法了，就到[Ubuntu中文论坛](http://forum.ubuntu.com.cn)上发了一个帖子，咨询一下。<br>
很快就有网友回复，说是**需要支持混成（compositing）的窗口管理器，否则不支持透明效果，就出那黑块了**，按照网友的提示安装了compton 或 xcompmgr，使用下面的指令启动加载：

    + I Exec exec compton --config /dev/null &

或者

    + I Exec exec xcompmgr -Ss -n -Cc -fF -I-10 -O-10 -D1 -t-3 -l-4 -r4 &

果然有效，烦人的小黑块再也不出现啦。<br>

