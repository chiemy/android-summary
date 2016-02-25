##MediaMetadataRetriever
顾名思义，就是用来获取媒体文件一些相关信息的类。包括一首歌的标题，作者，专辑封面和名称，时长，比特率等等。如果是视频的话，可以获取视频的长宽，预览图。

[MediaMetadataRetriever API](http://developer.android.com/intl/zh-cn/reference/android/media/MediaMetadataRetriever.html)

##TouchDelegate
用于更改View的触摸区域。场景：比如在RecyclerView的ItemView里包含了CheckBox组件, 然后想实现点击ItemView的时候，也可以触发CheckBox，就可以使用此类。

[http://developer.android.com/intl/zh-cn/training/gestures/viewgroup.html#delegate](http://developer.android.com/intl/zh-cn/training/gestures/viewgroup.html#delegate)

##ArgbEvaluator
用于计算不同颜色值之间的插值，配合ValueAnimator.ofObject或者ViewPager.PageTransformer使用，可以实现不同颜色之间的平滑过渡。

[http://developer.android.com/intl/zh-cn/reference/android/animation/ArgbEvaluator.html](http://developer.android.com/intl/zh-cn/reference/android/animation/ArgbEvaluator.html)

##Palette
用于提取一张图片的颜色。

[http://developer.android.com/intl/zh-cn/reference/android/support/v7/graphics/Palette.html](http://developer.android.com/intl/zh-cn/reference/android/support/v7/graphics/Palette.html)

##ViewDragHelper
做过自定义ViewGroup的童鞋都应该知道这个东西吧，用来处理触摸事件的神器，妈妈再也不用担心我自定义控件了。

[http://developer.android.com/intl/zh-cn/reference/android/support/v4/widget/ViewDragHelper.html](http://developer.android.com/intl/zh-cn/reference/android/support/v4/widget/ViewDragHelper.html)

[http://www.cnblogs.com/lqstayreal/p/4500219.html](http://www.cnblogs.com/lqstayreal/p/4500219.html)

##LocalBroadcastManager
用于在APP内部使用的，效率和安全性更好的广播工具类。

[http://developer.android.com/intl/zh-cn/reference/android/support/v4/content/LocalBroadcastManager.html](http://developer.android.com/intl/zh-cn/reference/android/support/v4/content/LocalBroadcastManager.html)

##Formatter.formatFileSize
根据文件大小自动转为以KB, MB, GB为单位的工具类。想想以前都是自己计算的…

[http://developer.android.com/intl/zh-cn/reference/android/text/format/Formatter.html](http://developer.android.com/intl/zh-cn/reference/android/text/format/Formatter.html)

##Activity.recreate
重新创建Activity。有什么用呢？可以在程序更换主题后，立马刷新当前Activity，而不会有明显的重启Activity的动画。

[http://developer.android.com/intl/zh-cn/reference/android/app/Activity.html#recreate%28%29](http://developer.android.com/intl/zh-cn/reference/android/app/Activity.html#recreate%28%29)

##View.setTag
用户保存一些数据。比如ListView的item保存对应位置的数据，在onItemClick时，通过getTag获取。
