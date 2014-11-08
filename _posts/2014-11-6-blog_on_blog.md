---
title: 在github上搭建博客
layout: post
category : Project
tags : [github, beginner, jekyll, blog]
---

## 开始使用github

github号称是程序员的Facebook，上面托管着很多开源的项目，每个项目都有一个自己的项目主页，github允许用户自定义项目首页用来替代默认的源码列表，这就是GithubPages，GithubPages可以认为是用户编写的托管在github上的静态网页，本博客就是利用GithubPages搭建的。Github允许用户上传自己的网页，但是并不是简单的上传而是经过[Jekyll](http://jekyllrb.com/)再处理。

###配置SSH

下载[github_for_windows](https://windows.github.com/)打开git shell输入
	
	$ssh-keygen -trsa
	$cd ~/.ssh
	$cat ./id_rsa.pub

访问https://github.com/settings/ssh设置页面点击`Add SSH key`，将上面命令回显的内容复制到key中去。title可以自己设置。

在git shell中输入
	
	$ssh -T git@github.com

出现选项确认输入yes，然后会提示

	Hi your_user_name! You've successfully authenticated, but GitHub does not provide shell access.

说明ssh设置成功了


##创建和配置你的blog

- 登陆[github](https://github.com)，点击右上角的`+`->`new repository`  
新建一个名为 `yourname.github.io`的repository

- 在gitshell中

		$cd (你想要放置项目的文件夹)
		$mkdir yourname.github.io
		$cd yourname.github.io
		$git init

这样就建好了一个空的本地库，你可以选择自己编码创建博客或者使用现成的模板。这里提供一些模板资源：

- [jekyllbootstrap](http://jekyllbootstrap.com/)
- [3-Jekyll](https://github.com/P233/3-Jekyll)
- [掌心的博客](http://www.zhanxin.info/jekyll/)
- [jekyllthemes](http://jekyllthemes.org/)
    
- [本博客使用的3-Jekyll的修改版](https://github.com/hyacz/3-Jekyll)

你可以选择模板进行下载，将模板下载解压到你的`yourname.github.io`文件夹，根据对应模板的说明，进行对应的配置。

##在本地运行jekyll以测试你的blog

###在Window中使用jekyll

- **安装ruby&DevKit**
访问[rubyinstaller.org](http://rubyinstaller.org/downloads/)下载并安装ruby和development kit。注意将ruby的bin目录添加到windows的path中。   
- **安装jekyll**

		gem install jekyll

- **本地启动jekyll**
在命令提示符中进入到项目根目录输入命令`jekyll serve`

- **访问你本地的博客**
现在打开浏览器输入127.0.0.1:4000就可以访问你的博客了！enjoy it

###在mac中使用jekyll
- **安装xcode command line tools**
前往[downloads for Apple Developers](https://developer.apple.com/downloads/)下载command line tools
>注意：未验证是否可以不安装xcode，如果不使用jekyll-lunr-js-search配置搜索功能可以略去安装xcode command line tools的步骤

- **配置ruby**

mac是自带ruby环境的，只需要直接安装jekyll就可以了

	sudo gem install jekyll

- **本地启动jekyll**

在`terminal`中进入到项目根目录输入命令`jekyll serve`

- **访问你本地的博客**
现在打开浏览器输入127.0.0.1:4000就可以访问你的博客了！enjoy it

##Advance

###Liquid

jekyll使用的是**Liquid**模版语言相关用法建议参考[wiki](https://github.com/Shopify/liquid/wiki)，这里我们选取的是官方wiki中Liquid for Designers中的例子来进行测试（扩展：[jekyll支持的全局变量](http://jekyllrb.com/docs/variables/)）

{% highlight liquid %}
{% raw %}
Hello {{ 'tobi' | upcase }}  
Hello tobi has {{ 'tobi' | size }} letters!  
Hello {{ '*tobi*' | textilize | upcase }}  
Hello {{ 'now' | date: "%Y %h" }}
{% endraw %}
{% endhighlight %}

复制上述内容粘贴到示例页`2011-12-29-jekyll-introduction.md`中

{% highlight liquid %}
{% raw %}
...
## test

Hello {{ 'tobi' | upcase }}  
Hello tobi has {{ 'tobi' | size }} letters!  
Hello {{ '*tobi*' | textilize | upcase }}  
Hello {{ 'now' | date: "%Y %h" }}
...
{% endraw %}
{% endhighlight %}

在本地启动jekll来看一下效果，这里提示缺少RedCloth  
按照提示安装了RedCloth之后启动jekll错误依旧，应该是RedCloth自身有问题，运行`redcloth.bat`来查看具体原因  
{% highlight bat %}
{% raw %}
C:\Ruby21-x64\bin>redcloth.bat
C:/Ruby21-x64/lib/ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot l
oad such file -- 2.1/redcloth_scan (LoadError)
Couldn't load 2.1/redcloth_scan
The $LOAD_PATH was:
C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/bin/../lib/
C:/Ruby21-x64/lib/ruby/gems/2.1.0/extensions/x64-mingw32/2.1.0/RedCloth-4.2.9
C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/lib
C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/lib/case_sensitive_require
C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/ext
C:/Ruby21-x64/lib/ruby/site_ruby/2.1.0
C:/Ruby21-x64/lib/ruby/site_ruby/2.1.0/x64-msvcrt
C:/Ruby21-x64/lib/ruby/site_ruby
C:/Ruby21-x64/lib/ruby/vendor_ruby/2.1.0
C:/Ruby21-x64/lib/ruby/vendor_ruby/2.1.0/x64-msvcrt
C:/Ruby21-x64/lib/ruby/vendor_ruby
C:/Ruby21-x64/lib/ruby/2.1.0
C:/Ruby21-x64/lib/ruby/2.1.0/x64-mingw32
		from C:/Ruby21-x64/lib/ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require'
		from C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/lib/redcloth.rb:13:in `<top (required)>'
		from C:/Ruby21-x64/lib/ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require'
		from C:/Ruby21-x64/lib/ruby/2.1.0/rubygems/core_ext/kernel_require.rb:55:in `require'
		from C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/bin/redcloth:4:in `<top(required)>'
		from C:/Ruby21-x64/bin/redcloth:23:in `load'
		from C:/Ruby21-x64/bin/redcloth:23:in `<main>'
{% endraw %}
{% endhighlight %}
提示是加载失败找不到`2.1/redcloth_scan`相关动态库，于是进入到目录`C:/Ruby21-x64/lib/ruby/gems/2.1.0/gems/RedCloth-4.2.9/ext`新建一个文件夹`2.1`并且将`redcloth_scan`里面的东西都复制过来。再次启动就成功了。

##语法高亮

>部分模板可能内置支持了pygments语法高亮的组件。可以略过此步。

jekyll原生支持Pygments语法高亮，配置过程如下：

####**1.编辑`_config.yml`**

	highlighter: pygments
	mardown: redcarpet

####**2.下载Pygments**

	pip install pygments  
		
####**3.安装Pygments.rb**

	geminstall pygments.rb  
	
####**4.进入项目目录，生成pygments样式**

	pygmentize -S default -f html > pygments.css

####**5.在网页中加入生成的样式文件**
		
	<link rel="stylesheet" href="/pygments.css">
		
####**6.使用代码高亮**
		
	{% raw %}
	{% highlight html %}
	<html>
	<head>
	<title>Hello</title>
	</head>
	<body>
	<p>Hello world!!!</p>
	</body>
	</html>
	{% endhighlight %}
	{% endraw %}

{% highlight html %}  
<html>
<head>
<title>Hello</title>
</head>
<body>
<p>Hello world!!!</p>
</body>
</html>
{% endhighlight %}
		
>注意：如果是liquid代码还应该在highlight标签中插入一对raw标签来禁用标签处理
