---
layout: post
title : Python 速记
author: 赵何宇
---

#### 一些基本语法的速记,简单的不要不要的

##### Matplotlib;

##### pyqt5 (python3.6.3 & PyQt5-5.9.2-5.9.3-cp35.cp36.cp37-none-win_amd64) [ref](http://www.cnblogs.com/archisama/p/5442071.html);

###### 信号和槽机制
```
QPushButton(string text, QWidget parent = None)
```

在PyQt5中，事件处理系统由信号&槽机制建立。如果我们点击了按钮，信号clicked被发送。槽可以是Qt内置的槽或Python 的一个方法调用。QCoreApplication类包含了主事件循环；它处理和转发所有事件。instance()方法给我们返回一个实例化对象。注意QCoreAppli类由QApplication创建。点击信号连接到quit()方法，将结束应用。事件通信在两个对象之间进行：发送者和接受者。发送者是按钮，接受者是应用对象。

##### opencv & Python
