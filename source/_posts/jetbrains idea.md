---
title: jetbrains
date: 2016-12-16 23:10:30
tags: tools
categories: jetbrains
---

# IDEA

## 插件

- 参照[一大坨插件](https://blog.csdn.net/q547550831/article/details/79129311)

- 现在已安装的插件：
  
  - Translation(翻译工具)
  - CheckStyle-IDEA（代码规范）
  - FindBugs-IDEA(静态分析工具)
  - BashSupport（Linux Bash支持，对bash语法的高亮支持）
  - CodeGlance（类似SublimeText的Mini Map插件 ）
  - Key promoter（提示功能快捷键）
  - GsonFormat（暂未安装，其功能为把json格式的内容转成Object）
  - jRebel（暂未安装，热部署插件）

- **scratches草稿功能**
  
  - 参照[强大的草稿功能](https://blog.csdn.net/theape/article/details/70194185)
  
  - scratch中文乱码解决办法
    
    需要在`C:\Users\Administrator\.IntelliJIdea2019.1\config`中修改文件`idea64.exe.vmoptions`,在后面加上设置`-Dfile.encoding=UTF-8`

- **Lombok **插件
  
  提高编码效率

## 中文编码配置

- 有关Properties文件中的中文unicode转码问题
  
  在IDEA中创建了properties文件，发现默认中文不会自动进行unicode转码，需要配置
  
  ![](https://i.imgur.com/W2a0nMR.jpg)

## grep console

- 去掉指定日志级别显示
  
  ![]()

# keymap

### 常用

- ctrl + P
  列出参数列表（使用比较多 ）
- ctrl + shift + enter
  当在括号里输入完最后一个参数时候他会直接光标跳到最后并且添加分号，不需要自己按向左键移动光标。（使用比较多 ）
  ctrl + enter 向下插入一行，光标位置不变
- shift + enter
  向下插入一行。光标位置进入下一行（可以在一行的任何位置换行，而不必将光标移动到行尾，使用比较多）
- ctrl+left/right
  在单词间跳动光标，基本在所有的编辑器中都是适用 （使用比较多 ）
- ctr+shitf+left/right
  快速选中，基本在所有的编辑器中都是适用（shift选中，ctrl+left/right在单词中快速跳动，使用比较多 ）
- Alt+left/right，切换代码视图（使用比较多 ）
- Alt+Up/Down，在方法间快速移动定位（使用比较多 ）
- F2 或 Shift+F2，高亮错误或警告快速定位（使用比较多）
- Ctrl+Alt+T
  可以把代码包在一个块内，例如：try/catch（使用比较多 ）
- Ctrl+F，处于查找状态下按down向下查找，up向上查找（使用比较多）
- Ctrl+F12，可以显示当前文件的结构（快速查找当前类中某个方法，使用比较多）
- Ctrl+Shift+W，取消选择光标所在词（使用比较多）
- Ctrl+[ OR ]，可以跑到大括号的开头与结尾
- Ctrl+Shift +[ OR ]，选择大括号中的内容
- alt + 1 同上（在工程目录和编辑页面间切换，使用比较多）
- Ctrl+Alt+O，优化导入的类和包(去掉没有使用的import，使用比较多 )
- Shift+F6，重构 - 重命名（使用比较多 ）
- Ctrl+G，定位行
- Ctrl+H，显示类结构图（类的继承层次）
- Ctrl+U，转到父类
- Ctrl+”+/-“，当前方法展开、折叠
- Ctrl+Shift+”+/-“，全部展开、折叠
- Ctrl+Shift+J，整合两行
- ctrl + shift + space
  当new 一个变量的时候，已经写了变量类型，打了等号之后按这个可以快速完成。



---

## Compile and Run

[![nRX0JI.jpg](https://s2.ax1x.com/2019/09/16/nRX0JI.jpg)](https://imgchr.com/i/nRX0JI)

## Debugging

[![nRXhYn.jpg](https://s2.ax1x.com/2019/09/16/nRXhYn.jpg)](https://imgchr.com/i/nRXhYn)

## Editing

[![nRXwFA.jpg](https://s2.ax1x.com/2019/09/16/nRXwFA.jpg)](https://imgchr.com/i/nRXwFA)

## General

[![nRX2wQ.jpg](https://s2.ax1x.com/2019/09/16/nRX2wQ.jpg)](https://imgchr.com/i/nRX2wQ)

## Live Templates

[![nRXRoj.jpg](https://s2.ax1x.com/2019/09/16/nRXRoj.jpg)](https://imgchr.com/i/nRXRoj)

## Navigation

[![nRXBWt.jpg](https://s2.ax1x.com/2019/09/16/nRXBWt.jpg)](https://imgchr.com/i/nRXBWt)

## Refactoring

[![nRXsQf.jpg](https://s2.ax1x.com/2019/09/16/nRXsQf.jpg)](https://imgchr.com/i/nRXsQf)

## Search/Replace

[![nRXrSP.jpg](https://s2.ax1x.com/2019/09/16/nRXrSP.jpg)](https://imgchr.com/i/nRXrSP)

## Usage Search

[![nRXyy8.jpg](https://s2.ax1x.com/2019/09/16/nRXyy8.jpg)](https://imgchr.com/i/nRXyy8)

## VCS/Local History

[![nRX6OS.jpg](https://s2.ax1x.com/2019/09/16/nRX6OS.jpg)](https://imgchr.com/i/nRX6OS)

