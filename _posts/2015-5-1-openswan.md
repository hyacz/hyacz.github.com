---
title: openswan错误排查
layout: post
category : Project
tags : [openswan,ipsec]
---
弄了一个digtailocean的vps来用，第一件事情就是搭一个vpn，结果pptp被封了，用不了，shadowsock在iOS上不越狱全局代理不成功，于是就开始配L2TP/IPSEC了，安装openswan时就出了问题，DO自带的apt-get的源里面没有openswan，改好了这个之后成功安装openswan，老外给的[教程](https://raymii.org/s/tutorials/IPSEC_L2TP_vpn_with_Ubuntu_12.04.html)是安装openswan一路回车

>The openswan installation will ask some questions, this tutorial works with the default answers (just enter through it).

结果就被坑死了，一路回车生成的x905的密钥无法被它自己读取。。。我也不知道为什么，最后查了日志`/var/log/auth.log`，发现日志里面有一段话

>error loading rsa private key file ……

遂又滚去google，在github上找到一个[答案](https://gist.github.com/stengaard/2955132)

	If you ever see
	
	ipsec__plutorun: 003 "/etc/ipsec.secrets" line 16: error loading RSA private key file
	
	in your syslog 
 
	root@:/etc/ipsec.d/private# openssl rsa -in vpn.example.com.key -outform pem -out vpn.example.com.key.new 
	writing RSA key
	
	may very well help.

。。。openswan的问题到这里终于搞定了，但是还是没有解决vpn的问题