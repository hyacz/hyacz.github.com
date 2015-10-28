---
title: OSX 10.11 sudo gem install 提示/usr/bin无写入权限的问题
layout: post
category : Project
tags : [OSX,ruby,gem]
---
>升级OSX 10.11之后升级cocoapods就遇到过这个问题，然后由于jekyll对markdown的处理格式实在是有些奇葩，我就打算本地装一个看看效果，又遇到了这个问题。  

###简单粗暴的解决办法

>sudo gem install -n /usr/local/bin xxx  
>sudo gem update -n /usr/local/bin xxx  
>sudo gem uninstall -n /usr/local/bin xxx  

这样就可以解决没有权限的问题了。(ps.如果在uninstall的不指定bindir的话是没法删除掉/usr/local/bin下面的可执行文件的  

###通过配置文件来解决这个问题

删除所有已经安装的gem
>gem uninstall -aIx

在用户`$HOME`下面新建一个.gemrc配置文件，写入如下内容  

	:backtrace: false
	:bulk_threshold: 1000
	:sources:
	- https://ruby.taobao.org/
	:update_sources: true
	:verbose: true
	gem: --bindir /usr/local/bin --no-ri --no-rdoc

注意看最后一行`gem: --bindir /usr/local/bin`这个就相当于上面的`-n`指定`bindir`的作用了，另外`--no-ri`和`--no-rdoc`可以提高安装gem时的速度，不安装文档之类的东西。  
如果要查看已经安装的文档的话可以在命令行输入`gem server --port 1234`(1234也可以换成一个不与其他程序冲突的端口号)然后通过浏览器访问127.0.0.1:1234就可以查看所有已经安装的文档了。  
（暂时我还没有找到删除安装的文档的办法，遂全删了然后重装了所有gem（其实也就是cocoapods和jekyll
