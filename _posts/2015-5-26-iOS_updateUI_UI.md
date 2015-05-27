---
title: iOS开发中子线程刷新UI的问题
layout: post
category : Project
tags : [xcode,ios,UI]
---
这几天在折腾一个小项目，是利用`MultipeerConnectivity`和同一个局域网或者是蓝牙连接的另一个玩家下棋的小游戏。hackday一天要做完，当时写的昏天黑地，最后运行起来效果很不理想，我下一步棋对手要大概30s之后才能显示出来，这就很奇怪了，因为我刚刚下完对手就收到了我下棋的消息，但是UI却没有刷新。接收数据的方法是这样的：

	- (void)didReceiveDataWithNotification:(NSNotification *)notification{
    ...  
	//刷新数据模型
    [self.game UpdateGameStateWithString:receivedText];
	//刷新UI
    [self updateUI];
	} 

但是数据模型被刷新了，但是更新UI没有效果。后来问了一下别人以及网上百度了一下。 

+ 收到`notification`之后其实开了一个子线程去执行`selector`的方法
+ UI必须在主线程中刷新，安卓开发里面如果不在主线程中刷新UI会抛出异常  
+ 在子线程中是不能进行UI 更新的，而可以更新的结果只是一个幻像：因为子线程代码执行完毕了，又自动进入到了主线程，执行了子线程中的UI更新的函数栈，这中间的时间非常的短，就让大家误以为分线程可以更新UI。如果子线程一直在运行，则子线程中的UI更新的函数栈 主线程无法获知，即无法更新  
+ 只有极少数的UI能被更新，因为开辟线程时会获取当前环境，如点击某个按钮，这个按钮响应的方法是开辟一个子线程，在子线程中对该按钮进行UI 更新是能及时的，如换标题，换背景图，但这没有任何意义  

那么对于子线程刷新UI其实有2个办法  

	dispatch_async(dispatch_get_main_queue(), ^{
                       [self updateUI]
                       });

或者是  

	[self performSelectorOnMainThread:@selector(updateUI)
                           withObject:nil
                        waitUntilDone:YES];

关于iOS的多线程开发还有很多需要学习的地方，这里先记录一下解决方法，其中的原理等到以后在深入研究好了。    
Reference: [iOS开发- reloadData无效 (子线程更新UI错误)](http://www.2cto.com/kf/201410/342890.html)  