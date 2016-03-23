##开发工具
###1、内存泄露问题检测工具 - [LeakCanary](https://github.com/square/leakcanary)
良心企业Square最近刚开源的一个非常有用的工具，强烈推荐，帮助你在开发阶段方便的检测出内存泄露的问题，使用起来更简单方便。

###2、序列化代码生成插件 - [ParcelableGenerator](https://github.com/mcharmas/android-parcelable-intellij-plugin)

Android中的序列化有两种方式，分别是实现Serializable接口和Parcelable接口，但在Android中是推荐使用Parcelable，只不过我们这种方式要比Serializable方式要繁琐，那么有了这个插件一切就ok了。

###3、[Gson实体类生成插件](https://github.com/chiemy/GsonFormat)

###4、[日志输出库](https://github.com/orhanobut/logger)

一款简洁、好用、方便的Android Log库，大家不妨尝试下。

###5、[图片处理库Fresco](http://fresco-cn.org/)

Facebook开源的图片处理库

###6、接口数据调试插件 - [JsonOnlineViewer](https://plugins.jetbrains.com/plugin/7838?pr=)
可实现直接在android studio中调试接口数据，可以选择请求类型，自定义请求头及请求体，json数据格式化后展示

###7、单元测试--Roboletric

[Roboletric探索之路，从抗拒到依赖](http://iceanson.github.io/Robolectric-%E6%8E%A2%E7%B4%A2%E4%B9%8B%E8%B7%AF)

现在是个讲究效率的时代，我们希望能够快速高效的验证我们的代码逻辑是否有问题，我们不希望验证一个简单的逻辑或者一个方法是否有效，是通过run一次模拟器或者整个工程实现的，这样花费的时间太长了，降低了开发效率。

Roboletric不需要Run你的模拟器，直接在jvm上运行你的测试代码，能在几秒钟之内快速验证，通过体验之后，它确实非常高效，编写测试代码反而加速了开发效率。 

