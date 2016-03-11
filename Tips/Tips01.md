##启动线程时需注意
启动子线程时，要设置线程的优先级，否则也有可能造成UI卡顿，因为子线程默认与UI线程的优先级相同。

If you do, you should set the thread priority to "background" priority by calling Process.setThreadPriority() and passing THREAD_PRIORITY_BACKGROUND. If you don't set the thread to a lower priority this way, then the thread could still slow down your app because it operates at the same priority as the UI thread by default.



##回到程序主界面方法
情形：a>b>c，从c回到a


	Intent intent = new Intent();   

	intent.setClass(c.this, A.class);  

	intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);  //注意本行的FLAG设置  

	startActivity(intent);  

	finish();//关掉自己 
	
##Android Studio查看调试程序的SH1与MD5
Android Studio没有像Eclipse一样查看调试程序SH1与MD5的可视界面，需要在Android Studio中使用命令。

在我们进行调试时，Android Studio使用的是其默认的签名文件，在Mac上保存到如下路径：

	 你的当前用户\.android\debug.keystore

首先，进入该路径，然后运行如下命令


	keytool -v -list -keystore debug.keystore
	
