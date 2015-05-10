---
title: NSUnknownKeyException
layout: post
category : Project
tags : [xcode,ios,exception]
---
在调试代码的时候遇到了一个错误，我下意识的把刚刚加的代码注释掉了，但是依旧是无法载入视图，很简单的single view app。我就继续删代码，删到只剩下视图生命周期代码的时候还是无法运行，这时候我开始怀疑是storyboard的问题了，仔细看了下console，发现有一个叫做NSUnkonwnKeyException的错误，仔细检查才发现是因为原来是storyboard里面的控件设置了outlet到viewcontroler里面，但是对应的变量被删掉了，这种链接关系还在，加载视图的时候找不到这种链接导致的错误。
