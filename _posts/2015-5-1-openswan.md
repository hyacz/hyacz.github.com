---
title: openswan�����Ų�
layout: post
category : Project
tags : [openswan,ipsec]
---
Ū��һ��digtailocean��vps���ã���һ��������Ǵ�һ��vpn�����pptp�����ˣ��ò��ˣ�shadowsock��iOS�ϲ�Խ��ȫ�ִ����ɹ������ǾͿ�ʼ��L2TP/IPSEC�ˣ���װopenswanʱ�ͳ������⣬DO�Դ���apt-get��Դ����û��openswan���ĺ������֮��ɹ���װopenswan��������Ľ̳��ǰ�װopenswanһ·�س�

>The openswan installation will ask some questions, this tutorial works with the default answers (just enter through it).

����ͱ������ˣ�һ·�س����ɵ�x905����Կ�޷������Լ���ȡ��������Ҳ��֪��Ϊʲô����������־`/var/log/auth.log`��������־������һ�λ�

>error loading rsa private key file ����

���ֹ�ȥgoogle����github���ҵ�һ����

	If you ever see
	
	ipsec__plutorun: 003 "/etc/ipsec.secrets" line 16: error loading RSA private key file
	
	in your syslog 
 
	root@:/etc/ipsec.d/private# openssl rsa -in vpn.example.com.key -outform pem -out vpn.example.com.key.new 
	writing RSA key
	
	may very well help.

������openswan�����⵽�������ڸ㶨�ˣ����ǻ���û�н��vpn�����⡣