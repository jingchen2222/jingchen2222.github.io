---
layout: "post"
title: "Geetnote的安装和使用"
date: "2018-09-20 10:23"
tag:
- Tool
- evernote
categories:
- Tool
copyright: true

---
### geeknote安装和使用

#### 写在前面

### 安装 

#### 注意要安装中国版的印象笔记的geeknote

[geeknote 中国版印象笔记support版本](https://github.com/gmajian/geeknote)

官方安装步骤如下
```shell
# Download the repository.
$ git clone git://github.com/gmajian/geeknote.git

$ cd geeknote

# Installation
$ [sudo] python setup.py install

# Launch Geeknote and go through login procedure.
$ geeknote login
```

### 问题1: 在运行geeknote login时，并没有进入登陆程序。

#### 调试

打开geeknote.pk,跟踪login代码：

<!--more-->
```python
   @GeekNoneDBConnectOnly
    def login(self):
        # track from this
        if self.getEvernote().checkAuth():
            print("You have already logged in")
            out.successMessage("You have already logged in.")
            return tools.exit()

        if self.getEvernote().auth():
            out.successMessage("You have successfully logged in.")
        else:
            out.failureMessage("Login error.")
            return tools.exit()
```

因为checkAuth返回401失败，而自己已经申请过dev token了，所以我在需要auth的地方都设置了dev token

代码修改1
```python
def auth(self):
        print "auth..."
        GNA = GeekNoteAuth()
        self.authToken = "my token"
        print self.authToken
        userInfo = self.getUserInfo()
        if not isinstance(userInfo, object):
            logging.error("User info not get")
            return False

        self.getStorage().createUser(self.authToken, userInfo)
        return True
```

```python
 def checkAuth(self):
        print "checkAuth"
        self.authToken = "my token xxxx"
        logging.debug("oAuth token : %s", self.authToken)
        if self.authToken:
            return True
        return False
```

#### 再次运行login

```shell
> python geeknote.py login
```
成功

#### 后面就可以正常操作evernote

```shell
> python geeknote.py find -s "java"

Search request: intitle:java
Total found: 17
  1 : 17.09.2018  java NIO - Rollen Holt - 博客园
  2 : 10.09.2018  深入分析Java的序列化与反序列化-HollisChuang's Blog
  3 : 10.09.2018  Java学习路径2018版
  4 : 09.09.2018  TreeMap - Java 提高篇 - 极客学院Wiki
  5 : 10.11.2017  22的JAVA学习计划
  6 : 25.11.2017  深入理解Java枚举类型(enum) - zejian的博客 - CSDN博客
  7 : 09.11.2017  Java核心技术——基础卷
  8 : 10.11.2017  Java学习——参考
  9 : 08.11.2017  Effective Java——item15:使可变性最小化
 10 : 24.10.2017  package com._4paradigm.predictor.component.antifraud;  import com._4paradigm.predictor.component.antifraud.ColumnsExpander; import org.slf4j.Logger; import org.slf4j.LoggerFactory; import com._4paradigm.antifraud.fe.schema.*; import java.text.SimpleDateFo
 11 : 10.10.2016  JAVA学习路径
 12 : 22.03.2017  Java面试
 13 : 09.10.2016  JAVA基础
 14 : 12.10.2016  JAVA高级篇
 15 : 05.12.2014  Java自定义URL协议
 16 : 03.12.2014  JAVA SOCKET 编程
 17 : 28.08.2014  JAVA/android调试打印栈信息的窍门
```
