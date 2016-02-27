##BuildConfig.DEBUG的正确用法
在Android开发中，我们使用android.util.Log来打印日志，方便我们的开发调试。但是这些代码不想在发布后执行，一般的做法我们会自定义一个全局的标记，在打印日志时，根据该标识来决定是否打印。
但是每次在发布之前都要手动去改这个变量，不是很方便，而且不排除开发者忘记改的情况。

我们可以使用`BuildConfig.DEBUG`来代替。但要注意发布版本时的正确流程。

- 1.Project -> Build Automatically，即取消Build Automatically
- 2.Project -> Clean
- 3.Project -> Build
- 4.Android Tools -> Export Android application

否则，BuildConfig.DEBUG一直未true。