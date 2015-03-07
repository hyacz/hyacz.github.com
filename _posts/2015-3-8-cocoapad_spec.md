---
title: cocoapods specs镜像的问题
layout: post
category : Project
tags : [github, specs,cocopads]
---
cocoapod的update 和install的速度都非常的慢，再加上有时候会莫名其妙的连不上github，就去网上搜索了一下。

	pod repo remove master
	pod repo add master https://gitcafe.com/akuandev/Specs.git
	pod repo update

其他还有可用的源比如：http://git.oschina.net/akuandev/Specs.git  
如果不想在pod install pod update的时候不想升级specs库 可以使用参数忽略掉

	pod install --verbose --no-repo-update
	pod update --verbose --no-repo-update

[*来源*](url=http://akinliu.github.io/2014/05/03/cocoapods-specs-/)
