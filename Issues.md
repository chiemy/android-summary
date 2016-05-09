##BuildConfig.DEBUG的正确用法
在Android开发中，我们使用android.util.Log来打印日志，方便我们的开发调试。但是这些代码不想在发布后执行，一般的做法我们会自定义一个全局的标记，在打印日志时，根据该标识来决定是否打印。
但是每次在发布之前都要手动去改这个变量，不是很方便，而且不排除开发者忘记改的情况。

我们可以使用`BuildConfig.DEBUG`来代替。但要注意发布版本时的正确流程。

- 1.Project -> Build Automatically，即取消Build Automatically
- 2.Project -> Clean
- 3.Project -> Build
- 4.Android Tools -> Export Android application

否则，BuildConfig.DEBUG一直未true。

##动态更新ViewPager
[StackOverflow](http://stackoverflow.com/questions/10849552/update-viewpager-dynamically/10852046#10852046)


When using FragmentPagerAdapter or FragmentStatePagerAdapter, it is best to deal solely with getItem() and not touch instantiateItem() at all. The instantiateItem()-destroyItem()-isViewFromObject() interface on PagerAdapter is a lower-level interface that FragmentPagerAdapter uses to implement the much simpler getItem() interface.
Before getting into this, I should clarify that

if you want to switch out the actual fragments that are being displayed, you need to avoid FragmentPagerAdapter and use FragmentStatePagerAdapter.

An earlier version of this answer made the mistake of using FragmentPagerAdapter for its example - that won't work because FragmentPagerAdapter never destroys a fragment after it's been displayed the first time.

I don't recommend the setTag() and findViewWithTag() workaround provided in the post you linked. As you've discovered, using setTag() and findViewWithTag() doesn't work with fragments, so it's not a good match.

The right solution is to override getItemPosition(). When notifyDataSetChanged() is called, ViewPager calls getItemPosition() on all the items in its adapter to see whether they need to be moved to a different position or removed.

By default, getItemPosition() returns POSITION_UNCHANGED, which means, "This object is fine where it is, don't destroy or remove it." Returning POSITION_NONE fixes the problem by instead saying, "This object is no longer an item I'm displaying, remove it." So it has the effect of removing and recreating every single item in your adapter.

This is a completely legitimate fix! This fix makes notifyDataSetChanged behave like a regular Adapter without view recycling. If you implement this fix and performance is satisfactory, you're off to the races. Job done.

If you need better performance, you can use a fancier getItemPosition() implementation. Here's an example for a pager creating fragments off of a list of strings:

ViewPager pager = /* get my ViewPager */;
// assume this actually has stuff in it
final ArrayList<String> titles = new ArrayList<String>();

FragmentManager fm = getSupportFragmentManager();
pager.setAdapter(new FragmentStatePagerAdapter(fm) {
    public int getCount() {
        return titles.size();
    }

    public Fragment getItem(int position) {
        MyFragment fragment = new MyFragment();
        fragment.setTitle(titles.get(position));
        return fragment;
    }

    public int getItemPosition(Object item) {
        MyFragment fragment = (MyFragment)item;
        String title = fragment.getTitle();
        int position = titles.indexOf(title);

        if (position >= 0) {
            return position;
        } else {
            return POSITION_NONE;
        }
    }
});
With this implementation, only fragments displaying new titles will get displayed. Any fragments displaying titles that are still in the list will instead be moved around to their new position in the list, and fragments with titles that are no longer in the list at all will be destroyed.

What if the fragment has not been recreated, but needs to be updated anyway? Updates to a living fragment are best handled by the fragment itself. That's the advantage of having a fragment, after all - it is its own controller. A fragment can add a listener or an observer to another object in onCreate(), and then remove it in onDestroy(), thus managing the updates itself. You don't have to put all the update code inside getItem() like you do in an adapter for a ListView or other AdapterView types.


One last thing - just because FragmentPagerAdapter doesn't destroy a fragment doesn't mean that getItemPosition is completely useless in a FragmentPagerAdapter. You can still use this callback to reorder your fragments in the ViewPager. It will never remove them completely from the FragmentManager, though.

##ViewPager不设置id报错
动态创建ViewPager时，如果不设置id时，会报错，原因不明
	
	android.content.res.Resources$NotFoundException: Unable to find resource ID #0xffffffff
                                                       at android.content.res.Resources.getResourceName(Resources.java:2235)
                                                       at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1059)
                                                       at android.support.v4.app.FragmentManagerImpl.moveToState(FragmentManager.java:1248)
                                                       at android.support.v4.app.BackStackRecord.run(BackStackRecord.java:738)
                                                       at android.support.v4.app.FragmentManagerImpl.execPendingActions(FragmentManager.java:1613)
                                                       at android.support.v4.app.FragmentManagerImpl.executePendingTransactions(FragmentManager.java:570)
                                                       at android.support.v4.app.FragmentPagerAdapter.finishUpdate(FragmentPagerAdapter.java:141)
                                                       at android.support.v4.view.ViewPager.populate(ViewPager.java:1106)
                                                       at android.support.v4.view.ViewPager.populate(ViewPager.java:952)
                                                       at android.support.v4.view.ViewPager.setAdapter(ViewPager.java:447)
                                                       
                                                      
##ListView调用removeFooterView(footerView)方法报错
在Android 4.4之前，会出现如下错误：

	ExampleAdapter cannot be cast to android.widget.HeaderViewListAdapter
	
原因是`addFooterView()`方法需要在`setAdapter`方法之前调用

`addFooterView()` API level 15:

	/*
    * NOTE: Call this before calling setAdapter. This is so ListView can wrap
    * the supplied cursor with one that will also account for header and footer
    * views.
    
所以在4.4之前调用的顺序应该如下：

- 1.addFooterView(footer);
- setAdapter(adapter);
- removeFooterView(footer);

##ScrollView嵌套GridView自动滑动到底部
ScrollView嵌套GridView，在GridView加载数据时，ScrollView会自动滚动到GridView的底部，解决办法：在设置数据时

	gridView.setFocusable(false);
	
##WebView引起的内存泄露

[StackOverFlow](http://stackoverflow.com/questions/3130654/memory-leak-in-webview)

**解决方式一：**

当在XML文件中定义`WebView`时，Activity会作为context传递给WebView，当Activity结束时，WebView还会保持对activity的引用，造成内存泄露，我们可以选择动态创建WebView:

	webView = new WebView(getApplicationContext());
	
但要注意，如果只是简单的展示一个网页，这种方式没有问题，但如果网页能够跳转超链接，或弹出对话框，这种方式是有问题的。

**解决方式二：**

在xml中给WebView再包裹一层，如`FrameLayout`或`RelativeLayout`，在`onDestroy()`中：

	@Override
    protected void onDestroy() {
        super.onDestroy();
        mWebContainer.removeAllViews();
        mWebView.destroy();
    }
    
> 注：以上方法并未实际测试过

##VideoView设置为不可见是，onPrepared方法不回调