---
title: 在github上搭建博客
layout: post
category : Project
tags : [github, beginner, jekyll, blog]
---


## 在github新建一个repository
登陆[github](https://github.com)，点击右上角的`+`->`new repository`  
新建一个名为 `**yourname**.github.io`的repository

##在本地运行jekyll以测试你的blog

###在Window中使用jekyll
- 安装ruby&DevKit


###在mac中使用jekyll
- 安装xcode command line tools
前往[downloads for Apple Developers](https://developer.apple.com/downloads/)下载command line tools
>注意：未验证是否可以不安装xcode，如果不使用jekyll-lunr-js-search配置搜索功能可以略去安装xcode command line tools的步骤
- 配置ruby
mac是自带ruby环境的，只需要安装一些包就可以了

	sudo gem install jekyll
	sudo gem install jekyll-lunr-js-search

- 本地启动jekyll
在`terminal`中进入到项目根目录`jekyll serve`

##Advance

###<a name="Liquid">Liquid

jekyll使用的是**Liquid**模版语言相关用法建议参考[wiki](https://github.com/Shopify/liquid/wiki)，这里我们选取的是官方wiki中Liquid for Designers中的例子来进行测试  

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
{% highlight bat %}
{% raw %}
C:\Users\浩浩\Documents\GitHub\hyacz.github.com>jekyll serve
Configuration file: C:/Users/浩浩/Documents/GitHub/hyacz.github.com/_config.yml
			Source: C:/Users/浩浩/Documents/GitHub/hyacz.github.com
	   Destination: C:/Users/浩浩/Documents/GitHub/hyacz.github.com/_site
	  Generating...
You are missing a library required for Textile. Please run:
  $ [sudo] gem install RedCloth
  Liquid Exception: Missing dependency: RedCloth in _posts/core-samples/2011-12-29-jekyll-
introduction.md
			 ERROR: YOUR SITE COULD NOT BE BUILT:
					------------------------------------
					Missing dependency: RedCloth
{% endraw %}
{% endhighlight %}
按照提示安装了RedCloth之后启动jekll错误依旧，应该是RedCloth自身有问题，运行redcloth.bat来查看具体原因  
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

jekyll原生支持Pygments语法高亮，配置过程如下：

#####**1.编辑`_config.yml`**

	highlighter: pygments
	mardown: redcarpet
	
#####**2.下载Pygments**

	pip install pygments  
		
#####**3.安装Pygments.rb**

	geminstall pygments.rb  
	
#####**4.进入项目目录，生成pygments样式**

	pygmentize -S default -f html > pygments.css

#####**5.在网页中加入生成的样式文件**
		
	<link rel="stylesheet" href="/pygments.css">
		
#####**6.使用代码高亮**
		
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
