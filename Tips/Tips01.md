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
	
##android:process
在需要使用到新进程时，可以使用 android:process 属性，

如果被设置的进程名是以一个冒号开头的，则这个新的进程对于这个应用来说是私有的，当它被需要或者这个服务需要在新进程中运行的时候，这个新进程将会被创建。

如果这个进程的名字是以字符开头，并且符合 android 包名规范(如 com.roger 等)，则这个服务将运行在一个以这个名字命名的全局的进程中，当然前提是它有相应的权限。

若以数字开头(如 1Remote.com )，或不符合 android 包名规范（如 Remote），则在编译时将会报错 （ INSTALL_PARSE_FAILED_MANIFEST_MALFORMED ）。新建进程将允许在不同应用中的各种组件可以共享一个进程，从而减少资源的占用。

重点来了，因为设置了 android:process 属性将组件运行到另一个进程，相当于另一个应用程序，所以在另一个线程中也将新建一个 Application 的实例。因此，每新建一个进程 Application 的 onCreate 都将被调用一次。 如果在 Application 的 onCreate 中有许多初始化工作并且需要根据进程来区分的，那就需要特别注意了。

获取当前运行进程的名称：

```
public static String getProcessName(Context cxt, int pid) {  
    ActivityManager am = (ActivityManager) cxt.getSystemService(Context.ACTIVITY_SERVICE);  
    List<RunningAppProcessInfo> runningApps = am.getRunningAppProcesses();  
    if (runningApps == null) {  
        return null;  
    }  
    for (RunningAppProcessInfo procInfo : runningApps) {  
        if (procInfo.pid == pid) {  
            return procInfo.processName;  
        }  
    }  
    return null;  
}  
```

在 Application 的 onCreate 中获取进程名称并进行相应的判断，例如：

```
String processName = getProcessName(this, android.os.Process.myPid());

if (!TextUtils.isEmpty(processName) && processName.equals(this.getPackageName())) {//判断进程名，保证只有主进程运行
	//主进程初始化逻辑
	....
}
```