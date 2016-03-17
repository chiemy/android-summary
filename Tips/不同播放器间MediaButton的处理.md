[Allowing applications to play nice(r) with each other: Handling remote control buttons](http://android-developers.blogspot.hk/2010/06/allowing-applications-to-play-nicer.html)

Many Android devices come with the Music application used to play audio files stored on the device. Some devices ship with a wired headset that features transport control buttons, so users can for instance conveniently pause and restart music playback, directly from the headset.

But a user might use one application for music listening, and another for listening to podcasts, both of which should be controlled by the headset remote control.

If your media playback application creates a media playback service, just like Music, that responds to the media button events, how will the user know where those events are going to? Music, or your new application?

In this article, we’ll see how to handle this properly in Android 2.2. We’ll first see how to set up intents to receive “MEDIA_BUTTON” intents. We’ll then describe how your application can appropriately become the preferred media button responder in Android 2.2. Since this feature relies on a new API, we’ll revisit the use of reflection to prepare your app to take advantage of Android 2.2, without restricting it to API level 8 (Android 2.2).

An example of the handling of media button intents
In our AndroidManifest.xml for this package we declare the class RemoteControlReceiver to receive MEDIA_BUTTON intents:

```
<receiver android:name="RemoteControlReceiver">
    <intent-filter>
        <action android:name="android.intent.action.MEDIA_BUTTON" />
    </intent-filter>
</receiver>
```

Our class to handle those intents can look something like this:

```
public class RemoteControlReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (Intent.ACTION_MEDIA_BUTTON.equals(intent.getAction())) {
            /* handle media button intent here by reading contents */
            /* of EXTRA_KEY_EVENT to know which key was pressed    */
        }
    }
}
```

In a media playback application, this is used to react to headset button presses when your activity doesn’t have the focus. For when it does, we override the Activity.onKeyDown() or onKeyUp() methods for the user interface to trap the headset button-related events.

However, this is problematic in the scenario we mentioned earlier. When the user presses “play”, what application should start playing? The Music application? The user’s preferred podcast application?

Becoming the “preferred” media button responder
In Android 2.2, we are introducing two new methods in android.media.AudioManager to declare your intention to become the “preferred” component to receive media button events: registerMediaButtonEventReceiver() and its counterpart, unregisterMediaButtonEventReceiver(). Once the registration call is placed, the designated component will exclusively receive the ACTION_MEDIA_BUTTON intent just as in the example above.

In the activity below were are creating an instance of AudioManager with which we will register our component. We therefore create a ComponentName instance that references our intended media button event responder.

```
public class MyMediaPlaybackActivity extends Activity {
	private AudioManager mAudioManager;
	private ComponentName mRemoteControlResponder;
	@Override
	public void onCreate(Bundle savedInstanceState) {
   		super.onCreate(savedInstanceState);
   		mAudioManager = (AudioManager)getSystemService(Context.AUDIO_SERVICE);
   		mRemoteControlResponder = new ComponentName(getPackageName(),
                RemoteControlReceiver.class.getName());
   }
```

The system handles the media button registration requests in a “last one wins” manner. This means we need to select where it makes sense for the user to make this request. In a media playback application, proper uses of the registration are for instance:

when the UI is displayed: the user is interacting with that application, so (s)he expects it to be the one that will respond to the remote control,

when content starts playing (e.g. content finished downloading, or another application caused your service to play content)

Registering is here performed for instance when our UI comes to the foreground:

```
    @Override
    public void onResume() {
        super.onResume();
        mAudioManager.registerMediaButtonEventReceiver(
                mRemoteControlResponder);
    }
```
    
If we had previously registered our receiver, registering it again will push it up the stack, and doesn’t cause any duplicate registration.

Additionally, it may make sense for your registered component not to be called when your service or application is destroyed (as illustrated below), or under conditions that are specific to your application. For instance, in an application that reads to the user her/his appointments of the day, it could unregister when it’s done speaking the calendar entries of the day.

```
    @Override
    public void onDestroy() {
        super.onDestroy();
        mAudioManager.unregisterMediaButtonEventReceiver(
                mRemoteControlResponder);
    }
```    
    
After “unregistering”, the previous component that requested to receive the media button intents will once again receive them.

Preparing your code for Android 2.2 without restricting it to Android 2.2
While you may appreciate the benefit this new API offers to the users, you might not want to restrict your application to devices that support this feature. Andy McFadden shows us how to use reflection to take advantage of features that are not available on all devices. Let’s use what we learned then to enable your application to use the new media button mechanism when it runs on devices that support this feature.

First we declare in our Activity the two new methods we have used previously for the registration mechanism:

```
    private static Method mRegisterMediaButtonEventReceiver;
    private static Method mUnregisterMediaButtonEventReceiver;
```
    
We then add a method that will use reflection on the android.media.AudioManager class to find the two methods when the feature is supported:

```
private static void initializeRemoteControlRegistrationMethods() {
	try {
		if (mRegisterMediaButtonEventReceiver == null) {
         	mRegisterMediaButtonEventReceiver = AudioManager.class.getMethod(
               "registerMediaButtonEventReceiver",
               new Class[] { ComponentName.class } );
      	}
      	if (mUnregisterMediaButtonEventReceiver == null) {
         mUnregisterMediaButtonEventReceiver = AudioManager.class.getMethod(
               "unregisterMediaButtonEventReceiver",
               new Class[] { ComponentName.class } );
      	}
      /* success, this device will take advantage of better remote */
      /* control event handling                                    */
   } catch (NoSuchMethodException nsme) {
      /* failure, still using the legacy behavior, but this app    */
      /* is future-proof!                                          */
   }
}
```

The method fields will need to be initialized when our Activity class is loaded:

```
    static {
        initializeRemoteControlRegistrationMethods();
    }
```
    
We’re almost done. Our code will be easier to read and maintain if we wrap the use of our methods initialized through reflection by the following. Note in bold the actual method invocation on our AudioManager instance:

```
    private void registerRemoteControl() {
        try {
            if (mRegisterMediaButtonEventReceiver == null) {
                return;
            }
            mRegisterMediaButtonEventReceiver.invoke(mAudioManager,
                    mRemoteControlResponder);
        } catch (InvocationTargetException ite) {
            /* unpack original exception when possible */
            Throwable cause = ite.getCause();
            if (cause instanceof RuntimeException) {
                throw (RuntimeException) cause;
            } else if (cause instanceof Error) {
                throw (Error) cause;
            } else {
                /* unexpected checked exception; wrap and re-throw */
                throw new RuntimeException(ite);
            }
        } catch (IllegalAccessException ie) {
            Log.e(”MyApp”, "unexpected " + ie);
        }
    }
    private void unregisterRemoteControl() {
        try {
            if (mUnregisterMediaButtonEventReceiver == null) {
                return;
            }
            mUnregisterMediaButtonEventReceiver.invoke(mAudioManager,
                    mRemoteControlResponder);
        } catch (InvocationTargetException ite) {
            /* unpack original exception when possible */
            Throwable cause = ite.getCause();
            if (cause instanceof RuntimeException) {
                throw (RuntimeException) cause;
            } else if (cause instanceof Error) {
                throw (Error) cause;
            } else {
                /* unexpected checked exception; wrap and re-throw */
                throw new RuntimeException(ite);
            }
        } catch (IllegalAccessException ie) {
            System.err.println("unexpected " + ie);  
        }
    }
```    
  
We are now ready to use our two new methods, registerRemoteControl() and unregisterRemoteControl() in a project that runs on devices supporting API level 1, while still taking advantage of the features found in devices running Android 2.2.