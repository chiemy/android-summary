##文章

###Android-屏幕适配全攻略
视频教程[http://www.imooc.com/learn/484](http://www.imooc.com/learn/484)

###Android 开发中，有哪些坑需要注意？
链接：[http://www.zhihu.com/question/27818921/answer/70279930](http://www.zhihu.com/question/27818921/answer/70279930)

排名第一回答摘要：

- 谨慎对待数据库升级（比如需要在原数据库中增加字段），避免数据丢失或者操作数据库异常的情况，数据库升级方法可以查阅《第一行代码》P263；
- 在多进程之间不要用SharedPreferences共享数据，虽然可以（MODE_MULTI_PROCESS），但极不稳定：[android - MODE_MULTI_PROCESS for SharedPreferences isn't working](http://stackoverflow.com/questions/22129717/mode-multi-process-for-sharedpreferences-isnt-working)
- 尽量不要通过Application缓存数据，这不稳定：不要在Android的Application对象中缓存数据!
- 尽量不要使用AnimationDrawable，它在初始化的时候就将所有图片加载到内存中，特别占内存，并且还不能释放，释放之后下次进入再次加载时会报错；
- Eclipse中的Lint太不靠谱，特别是主工程中依赖library的时候，很多提示都是有问题的，建议使用Android Studio的工程清理工具，特别推荐；

###App瘦身
链接：[http://zhuanlan.zhihu.com/zmywly8866/20006066](http://zhuanlan.zhihu.com/zmywly8866/20006066)

来源：知乎

相关文章

- [Putting Your APKs on Diet](http://cyrilmottier.com/2014/08/26/putting-your-apks-on-diet/)
- [Android APK安装包瘦身](http://hukai.me/android-tips-for-reduce-apk-size/)
- [你有哪些经过验证的APK瘦身方法？](https://github.com/android-cn/android-discuss/issues/119)
- [减小App大小：图片篇](http://blog.csdn.net/scai_suryani/article/details/44040917)
- [如何缩减APK包大小？](https://github.com/android-cn/android-discuss/issues/51)
- [如何给你的Android 安装文件（APK）瘦身](http://getpocket.com/a/read/766907121)
- [APK文件结构和安装过程](http://blog.csdn.net/bupt073114/article/details/42298337)
- [Android:apk文件结构](http://www.cnblogs.com/ITlearning/p/3143498.html)
- [WebP 探寻之路](http://isux.tencent.com/introduction-of-webp.html)
- [Facebook工程师是如何改进他们Android客户端的](http://greenrobot.me/devnews/facebook-engineer-improve-android-app/)
- [Opus Codec](http://www.opus-codec.org/)

###各大热补丁方案分析和比较
http://blog.zhaiyifan.cn/2015/11/20/HotPatchCompare/
主要包括Dexposed、AndFix、ClassLoader

###用MVP架构开发Android应用
http://www.kymjs.com/code/2015/11/09/01/
《MVC, MVP, MVVM比较以及区别》作者Justrun 
《android实现MVP的新思路》译者FTExplore