# Android-Part2

========
Android
========
Android apps can be written using Kotlin, Java, and C++ languages.

======
An activity is the entry point for interacting with the user. It represents a single screen with a user interface. For example, an email app might have one activity that shows a list of new emails, another activity to compose an email, and another activity for reading emails. a different app can start any one of these activities if the email app allows it. For example, a camera app can start the activity in the email app that composes new mail to allow the user to share a picture.

======
A service is a general-purpose entry point for keeping an app running in the background for all kinds of reasons. It is a component that runs in the background to perform long-running operations or to perform work for remote processes. A service does not provide a user interface. For example, a service might play music in the background while the user is in a different app, or it might fetch data over the network without blocking user interaction with an activity. Another component, such as an activity, can start the service and let it run or bind to it in order to interact with it. 

======
Music playback is something the user is directly aware of, so the app tells the system this by saying it wants to be foreground with a notification to tell the user about it; in this case the system knows that it should try really hard to keep that service's process running, because the user will be unhappy if it goes away.

A regular background service is not something the user is directly aware as running, so the system has more freedom in managing its process. It may allow it to be killed (and then restarting the service sometime later) if it needs RAM for things that are of more immediate concern to the user.

======
Bound services run because some other app (or the system) has said that it wants to make use of the service. This is basically the service providing an API to another process. The system thus knows there is a dependency between these processes, so if process A is bound to a service in process B, it knows that it needs to keep process B (and its service) running for A. Further, if process A is something the user cares about, then it also knows to treat process B as something the user also cares about. Because of their flexibility (for better or worse), services have turned out to be a really useful building block for all kinds of higher-level system concepts. Live wallpapers, notification listeners, screen savers, input methods, accessibility services, and many other core system features are all built as services that applications implement and the system binds to when they should be running.

======
A broadcast receiver is a component that enables the system to deliver events to the app outside of a regular user flow, allowing the app to respond to system-wide broadcast announcements. for example, an app can schedule an alarm to post a notification to tell the user about an upcoming event... and by delivering that alarm to a BroadcastReceiver of the app, there is no need for the app to remain running until the alarm goes off. Many broadcasts originate from the system—for example, a broadcast announcing that the screen has turned off, the battery is low, or a picture was captured.	It is intended to do a very minimal amount of work. For instance, it might schedule a JobService to perform some work based on the event with JobScheduler

======
A content provider manages a shared set of app data that you can store in the file system, in a SQLite database, on the web, or on any other persistent storage location that your app can access. Through the content provider, other apps can query or modify the data if the content provider allows it. For example, the Android system provides a content provider that manages the user's contact information. As such, any app with the proper permissions can query the content provider, such as ContactsContract.Data, to read and write information about a particular person. To the system, a content provider is an entry point into an app for publishing named data items, identified by a URI scheme.

======
Assigning a URI doesn't require that the app remain running, so URIs can persist after their owning apps have exited. The system only needs to make sure that an owning app is still running when it has to retrieve the app's data from the corresponding URI.

These URIs also provide an important fine-grained security model. For example, an app can place the URI for an image it has on the clipboard, but leave its content provider locked up so that other apps cannot freely access it. When a second app attempts to access that URI on the clipboard, the system can allow that app to access the data via a temporary URI permission grant so that it is allowed to access the data only behind that URI, but nothing else in the second app.

======
When the system starts a component, it starts the process for that app if it's not already running and instantiates the classes needed for the component. For example, if your app starts the activity in the camera app that captures a photo, that activity runs in the process that belongs to the camera app, not in your app's process. Therefore, unlike apps on most other systems, Android apps don't have a single entry point (there's no main() function).

======
Because the system runs each app in a separate process with file permissions that restrict access to other apps, your app cannot directly activate a component from another app. However, the Android system can. To activate a component in another app, deliver a message to the system that specifies your intent to start a particular component. The system then activates the component for you.

=========
ANDROIDX
=========
Artifacts within the androidx namespace comprise the Android Jetpack libraries. Like the Support Library, libraries in theandroidx namespace ship separately from the Android platform and provide backward compatibility across Android releases. AndroidX is a major improvement to the original Android Support Library, which is no longer maintained. androidx packages fully replace the Support Library by providing feature parity and new libraries.

======
- All packages in AndroidX live in a consistent namespace starting with the string androidx. The Support Library packages have been mapped into corresponding androidx.* packages
- Unlike the Support Library, androidx packages are separately maintained and updated. The androidx packages use strict Semantic Versioning, starting with version 1.0.0. You can update AndroidX libraries in your project independently.
- Version 28.0.0 is the last release of the Support Library. There will be no more android.support library releases. All new feature development will be in the androidx namespace

==============================
AsyncTask, Processes, Threads
==============================
AsyncTask is a wrapper for running code on a separate thread. It is similar to plain Java's Runnable submitted to ExecutorService, with additional features of "pre" and "post" hooks to be run on main thread. So, its basically an enhanced version of Thread, Runnable and Handler setup.

======
By default AsycTask's share a single thread and hence not advised for long running tasks. Because when a single background thread is shared by many tasks, a long running task may block others. However, AsycTask can also run on a custom Executor, removing such restrictions of a shared worker thread.

======
How to handle Async task efficiently
- AsyncTasks should ideally be used for short operations (a few seconds at the most.) If you need to keep threads running for long periods of time, it is highly recommended you use the various APIs provided by the java.util.concurrent package such as Executor, ThreadPoolExecutor and FutureTask.
- Create the fragment and put the async task in it, do setRetainInstance(true). This will not destroy the fragment and async task. This is always recommended.
-  AsyncTask is limited to 128 concurrent tasks, with an additional queue of 10 tasks. That means that if you queue up more than 138 tasks before they can complete, your app will crash. 

======
Service or Thread or AsyncTask
- If you need to perform work outside your main thread, but only while the user is interacting with your application, then you should probably instead create a new thread and not a service. A Thread should be destroyed when the Thread that started it is destroyed.
- AsyncTasks actually use a ThreadPool behind the scenes as creating a thread is an expensive process. If you have many short lived tasks, using AsyncTask is just a quick, easy, but efficient way to execute them without blocking your application's GUI.

======
AsyncTask and Thread: AsyncTask enables proper and easy use of the UI thread. This class allows to perform background operations and publish results on the UI thread without having to manipulate threads and/or handlers. You can control the progress using doInBackground, onPostExecute, onCancelled, onPreExecute, onProgressUpdate. Async task should only be used for small tasks. For long tasks async task is not preferred. Thread can be triggered from any thread, main or background but async task must be triggered from main thread. For multiple tasks multiple instances to be created for async task. Multiple threads has to be created for multiple tasks. Async task runs on worker thread but thread run on its thread only.

======
Running sevice in separate thread: the process attribute for service in AndroidManifest.xml. If the name assigned to this attribute begins with a colon (':'), a new process, private to the application, is created when it's needed and the service runs in that process. If the process name begins with a lowercase character, the service will run in a global process of that name, provided that it has permission to do so. This allows components in different applications to share a process, reducing resource usage.

======
A common misunderstanding about Android multitasking is the difference between a process and an application. In Android these are not tightly coupled entities: applications may seem present to the user without an actual process currently running the app; multiple applications may share processes, or one application may make use of multiple processes depending on its needs; the process(es) of an application may be kept around by Android even when that application is not actively doing something.

======
An example of when multiple processes may be appropriate is when building a music player that plays music from a service for long period of time. If the entire app runs in one process, then many of the allocations performed for its activity UI must be kept around as long as it is playing music, even if the user is currently in another app and the service is controlling the playback. An app like this may be split into two process: one for its UI, and the other for the work that continues running in the background service.

========
Palette
========
The palette library is a support library that extracts prominent colors from images to help you create visually engaging apps. A Palette object gives you access to the primary colors in an image, as well as the corresponding colors for overlaid text. Dynamically change apps color scheme based on a given source image. The palette library attempts to extract the following six color profiles:
1) Light Vibrant
2) Vibrant
3) Dark Vibrant
4) Light Muted
5) Muted
6) Dark Muted

============
Permissions
============
A central design point of the Android security architecture is that no app, by default, has permission to perform any operations that would adversely impact other apps, the operating system, or the user. This includes reading or writing the user's private data (such as contacts or emails), reading or writing another app's files, performing network access, keeping the device awake, and so on. You should always check for and request permissions at runtime to guard against runtime errors (SecurityException).

======
Normal permissions cover areas where your app needs to access data or resources outside the app's sandbox, but where there's very little risk to the user's privacy or the operation of other apps. Bluetooth, Internet

======
Signature permissions: the system grants these app permissions at install time, but only when the app that attempts to use a permission is signed by the same certificate as the app that defines the permission. BIND_DEVICE_ADMIN, BIND_INPUT_METHOD

======
Dangerous permissions cover areas where the app wants data or resources that involve the user's private information, or could potentially affect the user's stored data or the operation of other apps. The user has to explicitly grant the permission to the app. Read contact. 

======
Special permissions: There are a couple of permissions that don't behave like normal and dangerous permissions. SYSTEM_ALERT_WINDOW and WRITE_SETTINGS are particularly sensitive, so most apps should not use them. If an app needs one of these permissions, it must declare the permission in the manifest, and send an intent requesting the user's authorization. The system responds to the intent by showing a detailed management screen to the user.

======
An app must publicize the permissions it requires by including <uses-permission> tags in the app manifest. If your app lists normal permissions in its manifest (that is, permissions that don't pose much risk to the user's privacy or the device's operation), the system automatically grants those permissions to your app. If your app lists dangerous permissions in its manifest (that is, permissions that could potentially affect the user's privacy or the device's normal operation), such as the SEND_SMS permission above, the user must explicitly agree to grant those permissions.

======
If the device is running Android 6.0 (API level 23) or higher, and the app's targetSdkVersion is 23 or higher, the user isn't notified of any app permissions at install time. Your app must ask the user to grant the dangerous permissions at runtime. 

======
Permission for optional hardware features: Access to some hardware features (such as Bluetooth or the camera) require an app permission. However, not all Android devices actually have these hardware features. So if your app requests the CAMERA permission, it's important that you also include the <uses-feature> tag in your manifest to declare whether or not this feature is actually required. For example: <uses-feature android:name="android.hardware.camera" android:required="false" />. If you declare android:required="false" for the feature, then Google Play allows your app to be installed on devices that do not have the feature. You then must check if the current device has the feature at runtime by calling PackageManager.hasSystemFeature(), and gracefully disable that feature if it's not available. If you do not provide the <uses-feature> tag, then when Google Play sees that your app requests the corresponding permission, it assumes your app requires this feature. So it filters your app from devices without the feature, as if you declared android:required="true" in the <uses-feature> tag.

======
Access to the emails should be protected by permissions, since this is sensitive user data. However, if a URI to an image attachment is given to an image viewer, that image viewer no longer has permission to open the attachment since it has no reason to hold a permission to access all email. The solution to this problem is per-URI permissions: when starting an activity or returning a result to an activity, the caller can set Intent.FLAG_GRANT_READ_URI_PERMISSION and/or Intent.FLAG_GRANT_WRITE_URI_PERMISSION. This grants the receiving activity permission access the specific data URI in the intent, regardless of whether it has any permission to access data in the content provider corresponding to the intent.

======
Permission groups: Dangerous Android permissions belong to permission groups. If an app requests the READ_CONTACTS permission, the system dialog just says the app needs access to the device's contacts. If the user grants approval, the system gives the app just the permission it requested. If the app has already been granted another dangerous permission in the same permission group, the system immediately grants the permission without any interaction with the user. For example, if an app had previously requested and been granted the READ_CONTACTS permission, and it then requests WRITE_CONTACTS, the system immediately grants that permission without showing the permissions dialog to the user.

======
Permissions applied using the android:permission attribute to the <receiver> tag restrict who can send broadcasts to the associated BroadcastReceiver. The permission is checked after Context.sendBroadcast() returns, as the system tries to deliver the submitted broadcast to the given receiver. As a result, a permission failure doesn't result in an exception being thrown back to the caller; it just doesn't deliver the Intent.

In the same way, a permission can be supplied to Context.registerReceiver() to control who can broadcast to a programmatically registered receiver. Going the other way, a permission can be supplied when calling Context.sendBroadcast() to restrict which broadcast receivers are allowed to receive the broadcast.

======
Permissions applied using the android:permission attribute to the <provider> tag restrict who can access the data in a ContentProvider. (Content providers have an important additional security facility available to them called URI permissions which is described next.) Unlike the other components, there are two separate permission attributes you can set: android:readPermission restricts who can read from the provider, and android:writePermission restricts who can write to it.

======
URI permissions: A content provider may want to protect itself with read and write permissions, while its direct clients also need to hand specific URIs to other apps for them to operate on. A typical example is attachments in a email app. Access to the emails should be protected by permissions, since this is sensitive user data. However, if a URI to an image attachment is given to an image viewer, that image viewer no longer has permission to open the attachment since it has no reason to hold a permission to access all email. The solution to this problem is per-URI permissions: when starting an activity or returning a result to an activity, the caller can set Intent.FLAG_GRANT_READ_URI_PERMISSION and/or Intent.FLAG_GRANT_WRITE_URI_PERMISSION. This grants the receiving activity permission access the specific data URI in the intent, regardless of whether it has any permission to access data in the content provider corresponding to the intent.

======
If you distribute your app on the Google Play Store and want to access sensitive user information related to call logs and SMS messages, your app needs to be registered as the user's default handler for the core device function related to that permission, unless your app satisfies one of the exception cases that appears in the Play Console Help Center. For example, to access call-related permissions, your app needs to be registered as the user's default Phone or Assistant handler unless your app satisfies an exception case.

======
Use intents in place of permissions, as using intents the application you are calling will automatically handle the permissions for you. 

======
Because security enforcement happens at the process level, the code of any two packages cannot normally run in the same process, since they need to run as different Linux users. You can use the sharedUserId attribute in the AndroidManifest.xml's manifest tag of each package to have them assigned the same user ID. By doing this, for purposes of security the two packages are then treated as being the same app, with the same user ID and file permissions. Note that in order to retain security, only two apps signed with the same signature (and requesting the same sharedUserId) will be given the same user ID. Any data stored by an app will be assigned that app's user ID, and not normally accessible to other packages.

======
To enforce your own permissions, you must first declare them in your AndroidManifest.xml using one or more <permission> elements. 
<permission
  android:name="com.example.myapp.permission.DEADLY_ACTIVITY"
  android:label="@string/permlab_deadlyActivity"
  android:description="@string/permdesc_deadlyActivity"
  android:permissionGroup="android.permission-group.COST_MONEY"
  android:protectionLevel="dangerous" />

==============
COMPATIBILITY
==============
Device compatibility: A device is "Android compatible" only if it can correctly run apps written for the Android execution environment. As an app developer, you don't need to worry about whether a device is Android compatible, because only devices that are Android compatible include Google Play Store. So you can rest assured that users who install your app from Google Play Store are using an Android compatible device.

======
App compatibility: App is compatible with each potential device configuration. Because Android runs on a wide range of device configurations, some features are not available on all devices.
-	Device features : Google Play Store compares the features your app requires to the features available on each user's device to determine whether your app is compatible with each device. If the device does not provide all the features your app requires, the user cannot install your app.
	<manifest ... >
    	<uses-feature android:name="android.hardware.sensor.compass"
       	           android:required="true" />
    </manifest>
- Platform version: Different devices may run different versions of the Android platform, such as Android 4.0 or Android 4.4. Each successive platform version often adds new APIs not available in the previous version. Talk about targetSdkVersion and minsdkversion
- Screen sizes: Different screen sizes and of different densities. The Android system helps you achieve density independence by providing density-independent pixels (dp or dip) as a unit of measurement that you should use instead of pixels (px). 

======
The pixel density is the number of pixels within a physical area of the screen and is referred to as dpi (dots per inch). This is different from the resolution, which is the total number of pixels on a screen.

======
The Android system helps you achieve density independence by providing density-independent pixels (dp or dip) as a unit of measurement that you should use instead of pixels (px)

======
By supporting as many screens as possible, your app can be made available to the greatest number of users with different devices, using a single APK. Additionally, making your app flexible for different screen sizes ensures that your app can handle window configuration changes on the device, such as when the user enables multi-window mode.
- Use view dimensions that allow the layout to resize
- Create alternative UI layouts according to the screen configuration
- Provide bitmaps that can stretch with the views

======
If you're using a LinearLayout, you can also expand the child views with layout weight so that each view fills the remaining space proportional to their weight value. However, using weights in a nested LinearLayout requires the system to perform multiple layout passes to determine the size for each view, slowing your UI performance. Fortunately, ConstraintLayout can achieve nearly all layouts possible with LinearLayout without the performance impacts, so you should try converting your layout to ConstraintLayout.

======
You can provide screen-specific layouts by creating additional res/layout/ directories—one for each screen configuration that requires a different layout—and then append a screen configuration qualifier to the layout directory name (such as layout-w600dp for screens that have 600dp of available width). You can also use the orientation specific layouts too:
res/layout/main_activity.xml                # For handsets
res/layout-land/main_activity.xml           # For handsets in landscape
res/layout-sw600dp/main_activity.xml        # For 7” tablets
res/layout-sw600dp-land/main_activity.xml   # For 7” tablets in landscape

======
If you don't consider these variations in pixel density, the system might scale your images (resulting in blurry images) or the images might appear at the completely wrong size. This page shows you how you can design your app to support different pixel densities by using resolution-independent units of measurements and providing alternative bitmap resources for each pixel density. Defining dimensions with pixels is a problem because different screens have different pixel densities, so the same number of pixels may correspond to different physical sizes on different devices.

======
To preserve the visible size of your UI on screens with different densities, you must design your UI using density-independent pixels (dp) as your unit of measurement. One dp is a virtual pixel unit that's roughly equal to one pixel on a medium-density screen (160dpi; the "baseline" density). Android translates this value to the appropriate number of real pixels for each other density.

======
In some cases, you will need to express dimensions in dp and then convert them to pixels. The conversion of dp units to screen pixels is simple:
px = dp * (dpi / 160)
Imagine an app in which a scroll or fling gesture is recognized after the user's finger has moved by at least 16 pixels. On a baseline screen, a user's must move by 16 pixels / 160 dpi, which equals 1/10th of an inch (or 2.5 mm) before the gesture is recognized. On a device with a high-density display (240dpi), the user's must move by 16 pixels / 240 dpi, which equals 1/15th of an inch (or 1.7 mm). The distance is much shorter and the app thus appears more sensitive to the user. To fix this issue, the gesture threshold must be expressed in code in dp and then converted to actual pixels. DisplayMetrics.density equals 1.0; on a high-density screen it equals 1.5; on an extra-high-density screen, it equals 2.0; and on a low-density screen, it equals 0.75. This figure is the factor by which you should multiply the dp units in order to get the actual pixel count for the current screen.

======
MipMap: Like all other bitmap assets, you need to provide density-specific versions of you app icon. However, some app launchers display your app icon as much as 25% larger than what's called for by the device's density bucket. For example, if a device's density bucket is xxhdpi and the largest app icon you provide is in drawable-xxhdpi, the launcher app scales up this icon, and that makes it appear less crisp. So you should provide an even higher density launcher icon in the mipmap-xxxhdpi directory. Now the launcher can use the xxxhdpi asset instead.

=====
AAPT
=====
All resource IDs are defined in your project's R class, which the aapt tool (android asset packaging tool) automatically generates. When your application is compiled, aapt generates the R class, which contains resource IDs for all the resources in your res/ directory. For each type of resource, there is an R subclass (for example, R.drawable for all drawable resources), and for each resource of that type, there is a static integer (for example, R.drawable.icon). This integer is the resource ID that you can use to retrieve your resource.
A resource ID is always composed of:
- The resource type: Each resource is grouped into a "type," such as string, drawable, and layout.
- The resource name: which is either: the filename, excluding the extension; or the value in the XML android:name attribute, if the resource is a simple value (such as a string).

===============
Android Studio
===============
Apply Changes: For devices and emulators running on Android 8.0 or newer, Android Studio now features three buttons that control how much of the application is restarted:
1) Run will continue to deploy all changes and restart the application.
2) Apply Changes will attempt to apply your resource and code changes and restart only your activity without restarting your app.
3) Apply Code Changes will attempt to apply only your code changes without restarting anything.
Compatible code changes are generally limited to code that has changed within method bodies.

========
INTENTS
========
An Intent is a messaging object you can use to request an action from another app component. Although intents facilitate communication between components in several ways, there are three fundamental use cases:
- Starting an activity 
- Starting a service 
- Delivering a broadcast

======
You can set the component name with setComponent(), setClass(), setClassName(), or with the Intent constructor. This is one way of explicit intent.

======
An intent filter is an expression in an app's manifest file that specifies the type of intents that the component would like to receive. For instance, by declaring an intent filter for an activity, you make it possible for other apps to directly start your activity with a certain kind of intent. Likewise, if you do not declare any intent filters for an activity, then it can be started only with an explicit intent.

======
The primary pieces of information in an intent are:
- component: The name of the component to start.
- action: action to be performed, such as ACTION_VIEW, ACTION_EDIT, ACTION_MAIN, etc.
- data: Declares the type of data accepted, using one or more attributes that specify various aspects of the data URI (scheme, host, port, path) and MIME type
- category: A string containing additional information about the kind of component that should handle the intent.

======
Action:
ACTION_VIEW: Use this action in an intent with startActivity() when you have some information that an activity can show to the user, such as a photo to view in a gallery app, or an address to view in a map app.
ACTION_SEND: Also known as the share intent, you should use this in an intent with startActivity() when you have some data that the user can share through another app, such as an email app or social sharing app.

======
Data: The URI (a Uri object) that references the data to be acted on and/or the MIME type of that data. The type of data supplied is generally dictated by the intent's action. For example, if the action is ACTION_EDIT, the data should contain the URI of the document to edit. When creating an intent, it's often important to specify the type of data (its MIME type) in addition to its URI. For example, an activity that's able to display images probably won't be able to play an audio file, even though the URI formats could be similar. Specifying the MIME type of your data helps the Android system find the best component to receive your intent. However, the MIME type can sometimes be inferred from the URI—particularly when the data is a content: URI. A content: URI indicates the data is located on the device and controlled by a ContentProvider, which makes the data MIME type visible to the system. To set only the data URI, call setData(). To set only the MIME type, call setType(). If necessary, you can set both explicitly with setDataAndType().

Caution: If you want to set both the URI and MIME type, don't call setData() and setType() because they each nullify the value of the other. Always use setDataAndType() to set both URI and MIME type.

======
Category: A string containing additional information about the kind of component that should handle the intent. Any number of category descriptions can be placed in an intent, but most intents do not require a category. Here are some common categories:
CATEGORY_BROWSABLE: The target activity allows itself to be started by a web browser to display data referenced by a link, such as an image or an e-mail message.
CATEGORY_LAUNCHER: The activity is the initial activity of a task and is listed in the system's application launcher.
You can specify a category with addCategory().

======
EXTRA: When creating an intent to send an email with ACTION_SEND, you can specify the to recipient with the EXTRA_EMAIL key, and specify the subject with the EXTRA_SUBJECT key. The Intent class specifies many EXTRA_* constants for standardized data types. If you need to declare your own extra keys (for intents that your app receives), be sure to include your app's package name as a prefix, as shown in the following example:
const val EXTRA_GIGAWATTS = "com.example.EXTRA_GIGAWATTS"

======
Flags are defined in the Intent class that function as metadata for the intent. The flags may instruct the Android system how to launch an activity (for example, which task the activity should belong to) and how to treat it after it's launched (for example, whether it belongs in the list of recent activities). Use setFlags() method.

======
Intent Types
1) Explicit Intent: Explicit intents specify which application will satisfy the intent, by supplying either the target app's package name or a fully-qualified component class name. 
2) Implicit Intent: Implicit intents do not name a specific component, but instead declare a general action to perform, which allows a component from another app to handle it.

======
When you use an implicit intent, the Android system finds the appropriate component to start by comparing the contents of the intent to the intent filters declared in the manifest file of other apps on the device. If the intent matches an intent filter, the system starts that component and delivers it the Intent object.

======
It's possible that a user won't have any apps that handle the implicit intent you send to startActivity(). Or, an app may be inaccessible because of profile restrictions or settings put into place by an administrator. If that happens, the call fails and your app crashes. To verify that an activity will receive the intent, call resolveActivity() on your Intent object. If the result is non-null, there is at least one app that can handle the intent and it's safe to call startActivity(). If the result is null, do not use the intent and, if possible, you should disable the feature that issues the intent. 

======
If multiple apps can respond to the intent and the user might want to use a different app each time, you should explicitly show a chooser dialog. 
val sendIntent = Intent(Intent.ACTION_SEND)
// Always use string resources for UI text.
// This says something like "Share this photo with"
val title: String = resources.getString(R.string.chooser_title)
// Create intent to show the chooser dialog
val chooser: Intent = Intent.createChooser(sendIntent, title)
// Verify the original intent will resolve to at least one activity
if (sendIntent.resolveActivity(packageManager) != null) {
    startActivity(chooser)
}

======
Intent Filter very powerful feature of the Android platform. They provide the ability to launch an activity based not only on an explicit request, but also an implicit one. For example, an explicit request might tell the system to “Start the Send Email activity in the Gmail app". By contrast, an implicit request tells the system to “Start a Send Email screen in any activity that can do the job." When the system UI asks a user which app to use in performing a task, that’s an intent filter at work.
The definition of this element includes an <action> element and, optionally, a <category> element and/or a <data> element. These elements combine to specify the type of intent to which your activity can respond.
<activity android:name=".ExampleActivity" android:icon="@drawable/app_icon">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="text/plain" />
    </intent-filter>
</activity>
Declaring the <category> element as DEFAULT enables the activity to receive launch requests. The <data> element specifies the type of data that this activity can send.

======
For all activities, you must declare your intent filters in the manifest file. However, filters for broadcast receivers can be registered dynamically by calling registerReceiver(). You can then unregister the receiver with unregisterReceiver(). Doing so allows your app to listen for specific broadcasts during only a specified period of time while your app is running. 	 	 	
<!-- This activity handles "SEND" actions with text data -->
<intent-filter>
	<action android:name="android.intent.action.SEND"/>
	<category android:name="android.intent.category.DEFAULT"/>
	<data android:mimeType="text/plain"/>
</intent-filter>
<!-- This activity also handles "SEND" and "SEND_MULTIPLE" with media data -->
<intent-filter>
	<action android:name="android.intent.action.SEND"/>
	<action android:name="android.intent.action.SEND_MULTIPLE"/>
	<category android:name="android.intent.category.DEFAULT"/>
	<data android:mimeType="application/vnd.google.panorama360+jpg"/>
	<data android:mimeType="image/*"/>
	<data android:mimeType="video/*"/>
</intent-filter>

======
An explicit intent is always delivered to its target, regardless of any intent filters the component declares. Note: To receive implicit intents, you must include the CATEGORY_DEFAULT category in the intent filter. The methods startActivity() and startActivityForResult() treat all intents as if they declared the CATEGORY_DEFAULT category. If you do not declare this category in your intent filter, no implicit intents will resolve to your activity. An implicit intent is tested against a filter by comparing the intent to each of the three elements. To be delivered to the component, the intent must pass all three tests. If it fails to match even one of them, the Android system won't deliver the intent to the component. However, because a component may have multiple intent filters, an intent that does not pass through one of a component's filters might make it through on another filter.

======
How the application launcher is run?
The first activity, MainActivity, is the app's main entry point—the activity that opens when the user initially launches the app with the launcher icon:
1) The ACTION_MAIN action indicates this is the main entry point and does not expect any intent data.
2) The CATEGORY_LAUNCHER category indicates that this activity's icon should be placed in the system's app launcher. If the <activity> element does not specify an icon with icon, then the system uses the icon from the <application> element.

======
The MIME type, application/vnd.google.panorama360+jpg, is a special data type that specifies panoramic photos, which you can handle with the Google panorama APIs.

======
- If the filter does not list any actions, there is nothing for an intent to match, so all intents fail the test. However, if an Intent does not specify an action, it passes the test as long as the filter contains at least one action.
- An intent with no categories always passes this test, regardless of what categories are declared in the filter. Android automatically applies the CATEGORY_DEFAULT category to all implicit intents passed to startActivity() and startActivityForResult().
- The data test compares both the URI and the MIME type in the intent to a URI and MIME type specified in the filter. The data test compares both the URI and the MIME type in the intent to a URI and MIME type specified in the filter. The rules are as follows:
1) An intent that contains neither a URI nor a MIME type passes the test only if the filter does not specify any URIs or MIME types.
2) An intent that contains a URI but no MIME type (neither explicit nor inferable from the URI) passes the test only if its URI matches the filter's URI format and the filter likewise does not specify a MIME type.
3) An intent that contains a MIME type but not a URI passes the test only if the filter lists the same MIME type and does not specify a URI format.
An intent that contains both a URI and a MIME type (either explicit or inferable from the URI) passes the MIME type part of the test only if that type matches a type listed in the filter. It passes the URI part of the test either if its URI matches a URI in the filter or if it has a content: or file: URI and the filter does not specify a URI. In other words, a component is presumed to support content: and file: data if its filter lists only a MIME type.

Note: If an intent specifies a URI or MIME type, the data test will fail if there are no <data> elements in the <intent-filter>.

======
Home app populates the app launcher by finding all the activities with intent filters that specify the ACTION_MAIN action and CATEGORY_LAUNCHER category. The PackageManager has a set of query...() methods that return all components that can accept a particular intent and a similar series of resolve...() methods that determine the best component to respond to an intent. For example, queryIntentActivities() returns a list of all activities that can perform the intent passed as an argument, and queryIntentServices() returns a similar list of services. Neither method activates the components; they just list the ones that can respond. There's a similar method, queryBroadcastReceivers(), for broadcast receivers.

======
Unlike activities, services, and broadcast receivers, content providers are not activated by intents. Rather, they are activated when targeted by a request from a ContentResolver. The content resolver handles all direct transactions with the content provider so that the component that's performing transactions with the provider doesn't need to and instead calls methods on the ContentResolver object. This leaves a layer of abstraction between the content provider and the component requesting information (for security).

======
What’s the difference between setFlags() and addFlags() on an Intent object: When we’re using setFlags, we’re replacing the oldFlags with a new set of Flags. When we use addFlags, we’re appending more flags.

======
A PendingIntent object is a wrapper around an Intent object. The primary purpose of a PendingIntent is to grant permission to a foreign application to use the contained Intent as if it were executed from your app's own process. Major use cases for a pending intent include the following:
1) Declaring an intent to be executed when the user performs an action with your Notification
2) Declaring an intent to be executed when the user performs an action with your App Widget 
3) Declaring an intent to be executed at a specified future time. Alarm manager executes PendingIntent.

======
Sticky Intent is an Intent that has been sent as a sticky Broadcast, meaning the Intent stays around after the broadcast is complete. Example: The Android system uses sticky broadcasts to notify receivers that the battery level has been changed (ACTION_BATTERY_CHANGED). When you call registerReceiver() for that action you will always get the latest Intent for that action. You do not have to wait for the next broadcast! Note: Sticky broadcasts are deprecated in Android 5+.

======
The Intent object is a common mechanism for starting new activity and transferring data from one activity to another. However, you cannot start a ContentProvider using an Intent. You must instead use the ContentResolver object in your application’s Context to communicate with the provider as a client. The ContentResolver object communicates with the provider object, an instance of a class that implements ContentProvider. The provider object receives data requests from clients, performs the requested action, and returns the results.

===================
Shared Preferences
===================
If you have a relatively small collection of key-values that you'd like to save, you should use the SharedPreferences APIs. A SharedPreferences object points to a file containing key-value (HasMap) pairs and provides simple methods to read and write them. Each SharedPreferences file is managed by the framework and can be private or shared. Note: The SharedPreferences APIs are for reading and writing key-value pairs, and you should not confuse them with the Preference APIs, which help you build a user interface for your app settings.

======
To get access to the preferences, we have three APIs to choose from:
- getPreferences() : used from within your Activity, to access activity-specific preferences. Because this retrieves a default shared preference file that belongs to the activity, you don't need to supply a name.
val sharedPref = activity?.getPreferences(Context.MODE_PRIVATE) ?: return
with (sharedPref.edit()) {
    putInt(getString(R.string.saved_high_score_key), newHighScore)
    commit()
}
- getSharedPreferences() : used from within your Activity (or other application Context), to access application-level preferences. Identified by name, which you specify with the first parameter
- getDefaultSharedPreferences() : used on the PreferenceManager, to get the shared preferences that work in concert with Android’s overall preference framework.

======
apply() changes the in-memory SharedPreferences object immediately but writes the updates to disk asynchronously. Alternatively, you can use commit() to write the data to disk synchronously. But because commit() is synchronous, you should avoid calling it from your main thread because it could pause your UI rendering.

======
To retrieve values from a shared preferences file, call methods such as getInt() and getString(), providing the key for the value you want, and optionally a default value to return if the key isn't present. For example:
val sharedPref = activity?.getPreferences(Context.MODE_PRIVATE) ?: return
val defaultValue = resources.getInteger(R.integer.saved_high_score_default_key)
val highScore = sharedPref.getInt(getString(R.string.saved_high_score_key), defaultValue)

val sharedPref = activity?.getPreferences(Context.MODE_PRIVATE) ?: return
with (sharedPref.edit()) {
    putInt(getString(R.string.saved_high_score_key), newHighScore)
    commit()
}

====================
StateListDrawables
====================
Lets you assign a number of graphic images to a single Drawable and swap out the visible item by a string ID value. It can be defined in an XML file with the <selector> element. Each state Drawable is defined in a nested <item> element.
<?xml version="1.0" encoding="UTF-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
   <item android:state_enabled="false"
         android:drawable="@drawable/btn_off" />
   <item android:state_pressed="true"
         android:state_enabled="true" 
         android:drawable="@drawable/btn_off" />
   <item android:state_focused="true"
         android:state_enabled="true" 
         android:drawable="@drawable/btn_on" />
   <item android:state_enabled="true" 
         android:drawable="@drawable/btn_on" />
</selector>

=====================
Layer list drawables
=====================
Layers are drawn on top of each other in the order defined in the XML file, which means that the last Drawable in the list is drawn on top. Each layer is represented by an individual Drawable. The drawables that make up a single image are organized and managed in a <layer-list> element in XML. Within the <layer-list>, each Drawable is represented by an <item> element.
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
      <bitmap android:src="@drawable/android_red"
        android:gravity="center" />
    </item>
    <item android:top="10dp" android:left="10dp">
      <bitmap android:src="@drawable/android_green"
        android:gravity="center" />
    </item>
    <item android:top="20dp" android:left="20dp">
      <bitmap android:src="@drawable/android_blue"
        android:gravity="center" />
    </item>
</layer-list>

=====================
Level list drawables
=====================
A level list drawable defines alternate drawables, each assigned a maximum numerical value. To select which drawable to use, call the setLevel() method, passing in an integer that is matched against the maximum level integer defined in XML. The resource with the lowest maximum level greater than or equal to the integer passed into setLevel() is selected. For example, the following XML defines a level list that includes two alternate drawables, status_off and status_on:
<level-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@drawable/status_off"
        android:maxLevel="0" />
    <item
        android:drawable="@drawable/status_on"
        android:maxLevel="1" />
</level-list>
To select the status_off Drawable, call setLevel(0). To select the status_on Drawable, call setLevel(1). An example use of a LevelListDrawable is a battery level indicator icon 47f25fd92ac1fc10.png that uses different images to indicate different current battery levels. #WIDTH: 24.00[IMAGEINFO]: ic_battery_charging_icon.png, Battery icon

================
Vector Drawable
================
In Android 5.0 (API Level 21) and above, you can define vector drawables, which are images that are defined by a path. A vector Drawable scales without losing definition. Most vector drawables use SVG files, which are plain text files or compressed binary files that include two-dimensional coordinates for how the image is drawn on the screen. Because SVG files are text, they are more space efficient than most other image files. Also, you only need one file for a vector image instead of a file for each screen density, as is the case for bitmap images.

======
It is a vector graphic defined in an XML file as a set of points, lines, and curves along with its associated color information. The major advantage of using a vector drawable is image scalability. It can be scaled without loss of display quality, which means the same file is resized for different screen densities without loss of image quality. This results in smaller APK files and less developer maintenance. You can also use vector images for animation by using multiple XML files instead of multiple images for each display resolution.

======
Drawbacks of vector drawable: 
- Decoding. Your vector file has to be read and parsed into a VectorDrawable modeling the the paths, groups etc you declare. These model objects then have to be drawn by executing Canvas drawing commands. These steps are proportional to the complexity of the vector and the type of operations you perform.
- Suitability. Due to the nature of the format, vectors are great at describing some assets like simple icons etc. They’re terrible at encoding photographic type images where it’s harder to describe their contents

======
Scalable Vector Graphics (SVG) is an XML-based vector image format for two-dimensional graphics with support for interactivity and animation. 

======
app:srcCompat is the most foolproof method of integrating vector drawables into your app. Vector drawables allow you to replace multiple png assets with a single vector graphic, defined in XML. if you directly refer to vector drawables using src below lollipop, it will crash. Use srCompat.

===============
ShapeDrawables
===============
Are a series of commands that tell how to draw something on the screen. That is why they can be resized and stretched as much as we want, without losing any quality. We can recolor and manipulate them even when the app is running and use the same ShapeDrawable multiple times in our app. Since ShapeDrawables are a subclass of the Drawable abstract class, we can use them in methods where a Drawable is expected. One issue is that they took a bit longer to draw than a Bitmap since there is a lot of parsing and drawing going on behind the scenes. Complete syntax:<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    <!-- If it's a line, the stroke element is required. -->
    <corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient
        android:angle="integer"
        <!-- The angle must be 0 or a multiple of 45 -->
        android:centerX="float"
        android:centerY="float"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size
        android:width="integer"
        android:height="integer" />
    <solid
        android:color="color" />
    <stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>

============================
Serializable and Parcelable
============================
NAGARRO, EXPEDIA
Serialization of the object is usually needed when you send your object over a network or store it in files because the network infrastructure and hardware components used for storage understand bits and bytes, but not objects. In Android, when you want to send an object to another activity from any activity, you cannot send it as you do in primitive types.

======
Serializable is a Java marker interface. A class that implements this interface allows the serialization of an object from this type in certain situations. Parcelable is an Android specific interface where you have to implement the serialization by yourself. The Parcelable process is much faster than Serializable (no need for reflection). Parcelable is always the default choice in Android.

======
Serializable interface is not a part of Android SDK and it uses reflection for marshaling operations and creates lots of temp objects. In Parcelable, you are able to choose which field you want to serialize. Because of the temp object creation and garbage collection, Serialization is slower than Parcelable.

======
- In Parcelable, developers write custom code for marshalling and unmarshalling so it creates less garbage objects in comparison to Serialization. The performance of Parcelable over Serialization dramatically improves (around two times faster), because of this custom implementation.
- Serialization is a marker interface(interface with no fields or methods within it), which implies the user cannot marshal the data according to their requirements.
- In Serialization, a marshaling operation is performed on a Java Virtual Machine (JVM) using the Java reflection API. This helps identify the Java objects member and behavior, but also ends up creating a lot of garbage objects. Due to this, the Serialization process is slow in comparison to Parcelable.

======
To convert a Java object into Parcelable, then the best way to do so is by implementing the Parcelable interface and overriding the writeToParcel() methods in its own class. The first step is to override the writeToParcel() method and write all object members into parcel objects. The second is to create a static Parcelable. Creator object to de-serialize the Java object.

===============
Pager Adapters
===============
FragmentPagerAdapter: This version of the pager is best for use when there are a handful of typically more static fragments to be paged through, such as a set of tabs. The fragment of each page the user visits will be kept in memory, though its view hierarchy may be destroyed when not visible. This can result in using a significant amount of memory since fragment instances can hold on to an arbitrary amount of state. For larger sets of pages, consider FragmentStatePagerAdapter. It only detaches the Views from Fragments which are currently not visible. onDestroyView() will be called on your Fragment once it’s out of reach and later onCreateView() will be called once you go back to this Fragment. This adapter is deprecated. Preferred to use ViewPager2 with FragmentStateAdapter which is in similar behaviour to FragmentStatePagerAdapter.

======
FragmentStatePagerAdapter: This version of the pager is more useful when there are a large number of pages, working more like a list view. When pages are not visible to the user, their entire fragment may be destroyed, only keeping the saved state of that fragment. This allows the pager to hold on to much less memory associated with each visited page as compared to FragmentPagerAdapter at the cost of potentially more overhead when switching between pages. It completely removes Fragment instances from the FragmentManager once they are out of reach. The state of the removed Fragments is stored inside the FragmentStatePagerAdapter. The Fragment instance is recreated once you return back to an existing item and the state is restored. 

======
FragmentPagerAdapter stores the whole fragment in memory, and could increase a memory overhead if a large amount of fragments are used in ViewPager. In contrary its sibling, FragmentStatePagerAdapter only stores the savedInstanceState of fragments, and destroys all the fragments when they lose focus. Therefore FragmentStatePagerAdapter should be used when we have to use dynamic fragments, like fragments with widgets, as their data could be stored in the savedInstanceState.Also it wont affect the performance even if there are large number of fragments. 

======
In contrary its sibling FragmentPagerAdapter should be used when we need to store the whole fragment in memory. When I say the whole fragment is kept in memory it means, its instances wont be destroyed and would create a memory overhead. Therefore it is advised to use FragmentPagerAdapter only when there are low number of fragments for ViewPager.

======
It would be even better if the fragments are static, since they would not be having large amount of objects whose instances would be stored. FragmentStatePagerAdapter destroy the fragment which is no longer needed i.e which is not visible to user. Similar to a recylerview/Listview which recyles the view memory when goes out of sight. In FragmentPagerAdapter fragment of each page which user visited is kept in memory which causes memory issues in case of heavy fragments.

======
FragmentStatePagerAdapter Saves the Fragment bundle from the SaveInstance and re-stores the fragment when user comes back to the destroyed fragment. FragmentPagerAdapter calls detach(Fragment) on the transaction instead of remove(Fragment).This only removes the fragment view but fragment Instance remains in memory.

=========================
Build variant & Flavours
=========================
Each build variant represents a different version of your app that you can build. For example, you might want to build one version of your app that's free, with a limited set of content, and another paid version that includes more. You can also build different versions of your app that target different devices, based on API level or other device variations. However, if you want to build different versions based on the device ABI or screen density, instead build multiple APKs. It is permutation and combination of buildType and flovours

======
What is buildType in Gradle file
buildTypes {
	release {
		minifyEnabled true
		proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	}

	debug {
		applicationIdSuffix ".debug"
		debuggable true
	}

	/**
	 * The `initWith` property allows you to copy configurations from other build types,
	 * then configure only the settings you want to change. This one copies the debug build
	 * type, and then changes the manifest placeholder and application ID.
	 */
	staging {
		initWith debug
		manifestPlaceholders = [hostName:"internal.example.com"]
		applicationIdSuffix ".debugStaging"
	}
}

======
Configure product flavors: Creating product flavors is similar to creating build types: add them to the productFlavors block in your build configuration and include the settings you want. The product flavors support the same properties as defaultConfig—this is because defaultConfig actually belongs to the ProductFlavor class. This means you can provide the base configuration for all flavors in the defaultConfig block, and each flavor can change any of these default values, such as the applicationId. The following code sample creates a flavor dimension named "version" and adds "demo" and "full" product flavors. These flavors provide their own applicationIdSuffix and versionNameSuffix:
android {
    ...
    defaultConfig {...}
    buildTypes {
        debug{...}
        release{...}
    }
    // Specifies one flavor dimension.
    flavorDimensions "version"
    productFlavors {
        demo {
            // Assigns this product flavor to the "version" flavor dimension.
            // This property is optional if you are using only one dimension.
            dimension "version"
            applicationIdSuffix ".demo"
            versionNameSuffix "-demo"
        }
        full {
            dimension "version"
            applicationIdSuffix ".full"
            versionNameSuffix "-full"
        }
    }
}
After the sync completes, Gradle automatically creates build variants based on your build types and product flavors, and names them according to <product-flavor><Build-Type>. For example, if you created "demo" and "full" product flavors, and kept the default "debug" and "release" build types, Gradle creates the following build variants: 1)demoDebug 2)demoRelease 3)fullDebug 4)fullRelease

========
Caching
========
LRU cache (Least recently used): A cache that holds strong references to a limited number of values. Each time a value is accessed, it is moved to the head of a queue. When a value is added to a full cache, the value at the end of that queue is evicted and may become eligible for garbage collection. There are two kinds of LruCache in Android: LruCache and DiskLruCache , the first can be used to manage an set of objects, the later can be used to manager files.
int cacheSize = 4 * 1024 * 1024; // 4MiB
LruCache<String, Bitmap> bitmapCache = new LruCache<String, Bitmap>(cacheSize) {
    protected int sizeOf(String key, Bitmap value) {
       return value.getByteCount();
    }
}
Class is thread safe. Perform multiple cache operations atomically by synchronizing on the cache. 
synchronized (cache) {
  if (cache.get(key) == null) {
    cache.put(key, value);
  }
}
This class does not allow null to be used as a key or value.
In the past, a popular memory cache implementation was a SoftReference or WeakReference bitmap cache, however this is not recommended. Starting from Android 2.3 (API Level 9) the garbage collector is more aggressive with collecting soft/weak references which makes them fairly ineffective.

======
Disk Caching: A disk cache can be used in these cases to persist processed bitmaps and help decrease loading times where images are no longer available in a memory cache. Of course, fetching images from disk is slower than loading from memory and should be done in a background thread, as disk read times can be unpredictable.


===================
Apk Split & Filter
===================
Native libraries are mainly created for performance, security and cross-platform portability reasons. Popular examples are Unity, Realm, and OnFido. As opposed to Android applications that run on Android Virtual Machine (Dalvik or Android Runtime(ART)), native libraries are compiled directly into native processor (CPU) code. This code varies depending on architecture (set of instructions) supported by the processor. The Android platform supports multiple processor architectures: mips, mips64, armeabi, armeabi-v7a, arm64-v8a, x86, x86_64. If we want to use native libraries in our application, we must provide different compilations for each of the CPU architectures we want to support. For each architecture we have one or more separate .so files containing native code. To decrease APK size we need to limit our APK to support certain architecture(s). There are two techniques that we should consider: ABI Filters, APK Split

======
ABI filters(Application Binary Interface): is an interface between two program modules; often, one of these modules is a library or operating system facility. ABI filters allow us to filter certain kind of architecture(s) that we want to include into single APK file.
release {
    ndk {
        abiFilters "arm64-v8a", "armeabi-v7a"
    }
}
By removing unnecessary architectures we can generate single APK file that has much smaller size (notice that there are not many devices that support x86 architecture for Android, but the libraries for this architecture are the biggest ones). We can apply ABI filters to default config to affect all the builds. The downside is that if we want to test our builds on emulator we usually need native libraries for x86/x86_64 architecture. That’s why it may be the better idea to configure filters only for our release build.

======
Apk split: To configure your build for multiple APKs, add a splits block to your module-level build.gradle file. Within the splits block, provide a density block that specifies how Gradle should generate per-density APKs, or an abi block that specifies how Gradle should generate per-ABI APKs. You can provide both density and ABI blocks, and the build system will create a an APK for each density and ABI combination. To create separate APKs for different screen densities, add a density block inside your splits block. 
android {
  ...
  splits {

    // Configures multiple APKs based on screen density.
    density {

      // Configures multiple APKs based on screen density.
      enable true

      // Specifies a list of screen densities Gradle should not create multiple APKs for.
      exclude "ldpi", "xxhdpi", "xxxhdpi"

      // Specifies a list of compatible screen size settings for the manifest.
      compatibleScreens 'small', 'normal', 'large', 'xlarge'
    }
  }
}
android {
  ...
  splits {

    // Configures multiple APKs based on ABI.
    abi {

      // Enables building multiple APKs per ABI.
      enable true

      // By default all ABIs are included, so use reset() and include to specify that we only
      // want APKs for x86 and x86_64.

      // Resets the list of ABIs that Gradle should create APKs for to none.
      reset()

      // Specifies a list of ABIs that Gradle should create APKs for.
      include "x86", "x86_64"

      // Specifies that we do not want to also generate a universal APK that includes all ABIs.
      universalApk false
    }
  }
}

======
By default, when Gradle generates multiple APKs, each APK will have the same version information, as specified in the module-level build.gradle file. Because the Google Play Store does not allow multiple APKs for the same app that all have the same version information, you need to ensure each APK has its own unique versionCode before you upload to the Play Store. If your build includes a universal APK, you should assign it a versionCode that's lower than that of any of your other APKs. Because Google Play Store installs the version of your app that is both compatible with the target device and has the highest versionCode, assigning a lower versionCode to the universal APK ensures that Google Play Store tries to install one of your APKs before falling back to the universal APK
android {
  ...
  defaultConfig {
    ...
    versionCode 4
  }
  splits {
    ...
  }
}
// Map for the version code that gives each ABI a value.
ext.abiCodes = ['armeabi-v7a':1, x86:2, x86_64:3]

// For per-density APKs, create a similar map like this:
// ext.densityCodes = ['mdpi': 1, 'hdpi': 2, 'xhdpi': 3]

import com.android.build.OutputFile

// For each APK output variant, override versionCode with a combination of
// ext.abiCodes * 1000 + variant.versionCode. In this example, variant.versionCode
// is equal to defaultConfig.versionCode. If you configure product flavors that
// define their own versionCode, variant.versionCode uses that value instead.
android.applicationVariants.all { variant ->

  // Assigns a different version code for each output APK
  // other than the universal APK.
  variant.outputs.each { output ->

    // Stores the value of ext.abiCodes that is associated with the ABI for this variant.
    def baseAbiVersionCode =
            // Determines the ABI for this variant and returns the mapped value.
            project.ext.abiCodes.get(output.getFilter(OutputFile.ABI))

    // Because abiCodes.get() returns null for ABIs that are not mapped by ext.abiCodes,
    // the following code does not override the version code for universal APKs.
    // However, because we want universal APKs to have the lowest version code,
    // this outcome is desirable.
    if (baseAbiVersionCode != null) {

      // Assigns the new version code to versionCodeOverride, which changes the version code
      // for only the output APK, not for the variant itself. Skipping this step simply
      // causes Gradle to use the value of variant.versionCode for the APK.
      output.versionCodeOverride =
              baseAbiVersionCode * 1000 + variant.versionCode
    }
  }
}


============
Android NDK
============
Android NDK is a set of tools that lets you implement parts of your Android app using native-code languages such as C and C++ and provides platform libraries that you can use to manage activities, and access the physical components of the device, such as the various sensors and display. Native code is embedded in the apk. As it will not go through the process of running the dex files, native code is directly run in the app. It allows developers to get the most performance out of devices, this can be beneficial for game engines like Unity or Unreal Engine. Because the source code is compiled directly into machine code for the CPU (and not into an intermediate language, as with Java) then developers are able to get the best performance. It is also possible to use other developers’ libraries or your own if there is something that you absolutely need to use.

======
Why it is helpful:
- Squeeze extra performance out of a device to achieve low latency or run computationally intensive applications, such as games or physics simulations.
- Reuse your own or other developers' C or C++ libraries.

======
How to use: Use NDK to compile C and C++ code into a native library and package it into your APK using Gradle. Your Java code can then call functions in your native library through the Java Native Interface (JNI) framework. 

======
CMake: an external build tool that works alongside Gradle to build your native library. You do not need this component if you only plan to use ndk-build.

======
You have the ability to use the “native” keyword to tell the compiler that the implementation is native. An example is public native int numbers(int x, int y);

==============
In-App Updates
==============
In-app updates works only with devices running Android 5.0 (API level 21) or higher, and requires you to use Play Core library 1.5.0 or higher. Two ways to do this:
Flexible: A user experience that provides background download and installation with graceful state monitoring.
Immediate: A full screen user experience that requires the user to update and restart the app in order to continue using the app.

===================================
Processes and Application Lifecycle
===================================
Every Android application runs in its own Linux process. This process is created for the application when some of its code needs to be run, and will remain running until it is no longer needed and the system needs to reclaim its memory for use by other applications. An unusual and fundamental feature of Android is that an application process's lifetime is not directly controlled by the application itself. Instead, it is determined by the system through a combination of the parts of the application that the system knows are running, how important these things are to the user, and how much overall memory is available in the system. It is important that application developers understand how different application components (in particular Activity, Service, and BroadcastReceiver) impact the lifetime of the application's process. Not using these components correctly can result in the system killing the application's process while it is doing important work.

======
A common example of a process life-cycle bug is a BroadcastReceiver that starts a thread when it receives an Intent in its BroadcastReceiver.onReceive() method, and then returns from the function. Once it returns, the system considers the BroadcastReceiver to be no longer active, and thus, its hosting process no longer needed. The system may kill the process at any time to reclaim memory, and in doing so, it terminates the spawned thread running in the process. The solution to this problem is typically to schedule a JobService from the BroadcastReceiver, so the system knows that there is still active work being done in the process.

======
Process hierarchy: 
- Foreground process: running an Activity at the top of screen. BroadcastReceiver that is currently running (its BroadcastReceiver.onReceive() method is executing). Service that is currently executing code in one of its callbacks (Service.onCreate(), Service.onStart(), or Service.onDestroy()).
- Visible process: Activity with onPause, Foreground service
- Service process: holding a Service that has been started with the startService() method
- Cached process: a well running system will have multiple cached processes always available

====================
Material Components 
====================
Material Components for Android (MDC Android) unites design and engineering with a library of components for creating consistency across your app. As the Material Design system evolves, these components are updated to ensure consistent pixel-perfect implementation and adherence to Google's front-end development standards.
Gradle: com.google.android.material:material

======
https://codelabs.developers.google.com/codelabs/mdc-101-kotlin/#0

================
STYLE AND THEME
================
In Android, a style is a collection of attributes that define the look and format of a View. You can apply the same style to any number of View elements in your app. A style can specify common properties such as height, padding, font color, font size, and background color. A style should not include layout-related information. A style can be applied to a View, Activity, or the entire app. A style applied to an Activity or the entire app must be defined in the AndroidManifest.xml file. To inherit a style, a new style identifies a parent attribute in the XML. When you apply a style to a collection of View elements in an activity or in your entire app, that is known as a theme. To apply a theme, you use the android:theme attribute.

======
What's the difference between a style and a theme? A style applies to a View. In XML, you apply a style using the style attribute. A theme applies to an Activity or an entire app, rather than to an individual View. In XML, you apply a theme using the android:theme attribute.

======
The pyramid diagram shows the order in which styling methods are applied by the system, from the bottom up. For example, if you set the text size in the theme, and then set the text size differently in the view attributes, the view attributes will override the theme styling.
			VIEW
		    STYLE
		DEFAULT STYLE
			THEME
		TEXTAPPEARANCE

======
View attributes: Use view attributes to set attributes explicitly for each view. (View attributes are not reusable, as styles are.) You can use every property that can be set via styles or themes. Use for custom or one-off designs such as margins, paddings, or constraints.

Styles: Use a style to create a collection of reusable styling information, such as font size or colors.
Good for declaring small sets of common designs used throughout your app. Apply a style to several views, overriding the default style. For example, use a style to make consistently styled headers or a set of buttons.

Default style: This is the default styling provided by the Android system.
Themes Use a theme to define colors for your whole app. Use a theme to set the default font for the whole app. Apply to all views, such as text views or radio buttons. Use to configure properties that you can apply consistently for the whole app.

TextAppearance: For styling with text attributes only, such as fontFamily.

======
?attr is a way to look up a theme attribute, defined in the current theme

======
MATERIAL STYLE: style="?attr/textAppearanceHeadline5": Changes applied
TEXT APPEARANCE: android:textAppearance="?attr/textAppearanceHeadline5: Cahanges don't apply as text appearance comes lower in the heirarchy overriden by the theme of the application. TextAppearance is an attribute on any view that applies text styling. It's not the same as a style, and it only lets you define things about how to display text.

======
A Theme is used to set the global theme for the entire app. A ThemeOverlay is used to override (or "overlay") that theme for specific views, especially the toolbar. They are useful when you want to change a subsection of your app, for example, change the toolbar to be dark, but continue using a light theme for the rest of the screen. You apply a theme overlay to a view, and the overlay applies to that view and all its children. When you want a view in the hierarchy to use a particular attribute that's defined in the overlay theme, you specifically set the attribute on the view and set the value to ?attr/valuename.

======
The MaterialComponents theme does not have an option for a dark toolbar on a light screen. In this step, you change the theme for just the toolbar. You make the toolbar dark by applying the Dark theme, which is available from MaterialComponents, to the toolbar as an overlay.
<androidx.appcompat.widget.Toolbar
    android:theme="@style/ThemeOverlay.MaterialComponents.Dark.ActionBar"

The colorOnPrimary attribute is a color that meets accessibility guidelines for text or iconography when drawn on top of the primary color. In the ImageView inside the Toolbar, set the tint to colorOnPrimary. Because the drawable includes both the image and the GDG Finder text, both will be light.
android:tint="?attr/colorOnPrimary"

======
For RTL: change all the marginLeft to marginStart and marginRight to marginEnd. Also, android provides some more components for further customization:
- android:layoutDirection to set the direction of a component's layout.
- android:textDirection to set the direction of a component's text.
- android:textAlignment to set the alignment of a component's text.
- getLayoutDirectionFromLocale() is a method to programatically get the locale that specifies direction.

If there is an image that need to be mirrored due to change in direction i.e. RTL, for the imageView we need to use  android:autoMirrored="true" 

======
Difference between Material Theme and Appcompat Theme
<style name="AppTheme" parent="android:Theme.*">
This is the raw Material design theme, designed for Android 5+

<style name="AppTheme" parent="Theme.AppCompat.*">
This is a way to use material design on pre-lollipop devices(thus app compatibility). You can design for newer API's using AppCompat and as such have it work on earlier API levels than what the starting API level is.

In this case, it means you can run Material Design on platforms that doesn't come with material design(meaning pre-Android 5)

======
Only the element to which you add the style attribute receives those style attributes—any child views do not apply the styles. If you want child views to inherit styles, instead apply the style with the android:theme attribute.

======
You can also inherit styles (except those from the platform) by extending a style's name with a dot notation, instead of using the parent attribute.
<style name="GreenText.Large">
    <item name="android:textSize">22dp</item>
</style>
If you use the dot notation to extend a style, and you also include the parent attribute, then the parent styles override any styles inheritted through the dot notation.

======
Beginning with Android 5.0 (API level 21) and Android Support Library v22.1, you can also specify the android:theme attribute to a view in your layout file. This modifies the theme for that view and any child views, which is useful for altering theme color palettes in a specific portion of your interface.

======
If you’re trying to style your app and not seeing the results you expect, it's likely that other styling is overriding your changes. For example, if you apply a theme to your app, along with a style to an individual View, the style attributes would override any matching theme attributes for that View. Note, however, that any theme attributes that aren't overridden by the style are still used.

======
TextAppearance allows you to define text-specific styling while leaving a View’s style available for other uses. Note, however, that if you define any text attributes directly on the View or in a style, those values would override the TextAppearance values.

================
Day Night theme
================
There are 2 ways to support Dark Theme:
1) Make your app’s theme extend AppCompat.DayNight (or similar from MaterialComponents). It includes some predefined values suitable for the dark environment for basic things like text or background color. You are to provide an alternative version for other things yourself using -night qualifier. It’s backward compatible up to API 14.
2) Enable the new Force Dark feature by setting android:forceDarkAllowed=”true” in the app’s theme. It attempts to automatically convert your app to Dark Theme at rendering time. It’s available starting from Android Q (API 29).

======
If you’re using Material Design Components (and I recommend you to do so), then you can also use the Theme.MaterialComponents.DayNight theme. You then need to enable the feature in your app using one of the APIs provided. setDefaultNightMode: The first API we provide to do that is AppCompatDelegate.setDefaultNightMode(), which takes one of the follow values:
- MODE_NIGHT_NO. Always use the day (light) theme.
- MODE_NIGHT_YES. Always use the night (dark) theme.
- MODE_NIGHT_FOLLOW_SYSTEM (default). This setting follows the system’s setting, which on Android Q and above is a system setting (more on this below).
- MODE_NIGHT_AUTO_BATTERY. Changes to dark when the device has its ‘Battery Saver’ feature enabled, light otherwise. 

The method is static so you can call it at any time. The value you set is not persisted across process starts though, therefore you need to set it every time your app process is started. I recommend setting it in your application class
override fun onCreate() {
    super.onCreate()
    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.getDefaultNightMode())
}

======
There are 3 levels of control for Dark Theme:
- System setting;
- Application setting;
- Activity setting.
The important part to understand here is that a local setting always wins. For instance, if the system setting is set to Dark, but the application setting is Light, then the app follows Light. Once the setting is changed, all started activities get recreated (or get Activity.onConfigurationChange callback, if you opted-in in the manifest to handle the configuration change manually). 

https://developer.android.com/guide/topics/ui/look-and-feel/darktheme
https://medium.cobeisfresh.com/how-to-implement-day-night-mode-in-your-android-app-2f21907f9b0a

=======
CANVAS
=======
Canvas API in Android is a drawing framework which helps us to draw custom design like line, circle or even a rectangle. Using these we can make any shape whichever we want according to design. The drawing of canvas happens in Bitmap, where we draw the outline and then the Paint API helps to fill color and whatever style we need.

======
So, since we know Canvas helps in designing views, let's understand the way the view gets rendered. There are layout methods that get called, they are 
onMeasure() -> onLayout() -> onDraw()
Here, onMeasure() - measures the size of the view and the children present there, onLayout() - helps in setting the size and onDraw() is the one which helps in drawing on the view. So, in onDraw() the canvas is used to draw using its pre-contained methods, like drawLine, drawArc, etc.  Canvas follows the coordinate system to draw itself on the screen. It considers the phone screen as its own coordinate system. The top left corner represents the (0,0) point in the coordinate system and bottom right is then (x,y) point in the coordinate system. so, in that way, the center of the screen is represented by (x/2,y/2) point.

======
class CanvasView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {   
    override fun onDraw(canvas: Canvas?) {
        super.onDraw(canvas)
        //here we have to use the canvas API to draw
    }
}
Now, to draw on Canvas we need the Paint object in the view class.
private val myViewPaint =
    Paint().apply {
        isAntiAlias = true // isAntiAlias - is used to make the edges smooth while drawing.
        color = Color.BLACK
        style = Paint.Style.STROKE
    }
Start by drawing a line on canvas. In, onDraw() , canvas?.drawLine(0f, 5f, 0f, 10f, myViewPaint)
Here, drawLine() takes two-point co-ordinate and the paint to draw itself. In the above code, 0f and 5f are x and y coordinate for the first point and 0f and 10f are the coordinate for the second point.

======
Similarly, in place of passing point co-ordinate we can also use Path() to draw the custom shape. Path is used to draw shapes by passing them what to do from point to point. To draw a line using Path we have to use,
val path = Path().apply {
    moveTo(x1, y1)
    lineTo(x2, y2)
    close()
}
canvas?.drawPath(path, myViewPaint) 
moveTo - this is used to move the drawing tip from one point to another. So here the drawing tip has moved to (x1, y1).
lineTo - this is used to draw a line to (x2, y2) from (x1,y1). As we have moved to (x1, y1), it would be our starting point to draw the line.
PS. Using Path you can draw any type of shape you desire.
and now, just to draw the line using the path, we need to use canvas.drawPath and not canvas.drawLine as we used in earlier code. Similarly, to draw a circle, we use
canvas?.drawCircle(x,y,radius,myViewPaint)
Now, to draw this finally on screen, we call invalidate() in onDraw of the view.

======
Points to remember:
- As the drawing happens on the main UI thread, avoid re-drawing in the same section for too long.
- Don't do a lot of calculations in onDraw()
- Canvas works on pixels and not dp.

======
You can also get access to a Canvas object by programatically creating one in code, like this:
val bitmap = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888) 
val canvas = Canvas(bitmap)
It’s worth noting at this point that any Canvas created programmatically without using a View, will be software rendered and not hardware rendered. This can affect the appearance of some of the drawing commands. For instance, some commands are just not supported with hardware rendering, or only supported from a certain API level. 

======
https://medium.com/over-engineering/getting-started-with-drawing-on-the-android-canvas-621cf512f4c7

===============
DEBUG YOUR APP
===============
By putting android:debuggable="true" in your manifest file, application will go in debug mode, that's mean android will manage all logs file regarding your application. But make sure put it again false(or remove this tag) if application will going to live or for release mode. If your app depends on a library module that you also want to debug, that library must also be packaged with debuggable true so it retains its debug symbols. 

======
With the new Gradle build system, the recommended way is to assign this in the build types. In your app module's build.gradle:
android {
    buildTypes {
        debug {
            debuggable true
        }
        customDebuggableBuildType {
            debuggable true
        }
        release {
            debuggable false
        }
    }
}
For debug buildtype debuggable mode is on by default

======
The Android Studio debugger and garbage collector are loosely integrated. The Android virtual machine guarantees that any object the debugger is aware of is not garbage collected until after the debugger disconnects. This can result in a buildup of objects over time while the debugger is connected. For example, if the debugger sees a running thread, the associated Thread object is not garbage collected until the debugger disconnects, even if the thread has terminated.

======
Enable Don't keep activities in phone to increase battery life by destroying every activity as soon as the user leaves the activity's main view. You can also test different scenrios to test the values are stored or not through saveInstanceState

======
You should never compile verbose logs into your app, except during development. Debug logs are compiled in but stripped at runtime, while error, warning, and info logs are always kept.

======
In the Log level menu, select one of the following values:
- Verbose: Show all log messages (the default).
- Debug: Show debug log messages that are useful during development only, as well as the message levels lower in this list.
- Info: Show expected log messages for regular usage, as well as the message levels lower in this list.
- Warn: Show possible issues that are not yet errors, as well as the message levels lower in this list.
- Error: Show issues that have caused errors, as well as the message level lower in this list.
- Assert: Show issues that the developer expects should never happen.

======
A stack trace is generated whenever your app crashes because of an error or an exception. You can also print a stack trace at any point in your app code using methods such as Thread.dumpStack(). A stack trace shows a list of method calls that lead to the exception being thrown, together with the filenames and line numbers where the calls happened. You can click on the highlighted filenames to open the files and examine the source of the method invocation. 

======
dumpsys is a tool that runs on Android devices and provides information about system services. You can call dumpsys from the command line using the Android Debug Bridge (ADB) to get diagnostic output for all system services running on a connected device. This output is typically more verbose than you may want, so use the command line options to get output for only the system services you're interested in.

======
CPU Profiler: You can use this to inspect your app’s CPU usage and thread activity in real time while interacting with your app, or you can inspect the details in recorded method traces, function traces, and system traces. The specific kinds of information that the CPU Profiler records and shows are determined by which recording configuration you choose:
- System Trace: Captures fine-grained details that allow you to inspect how your app interacts with system resources.
- Method and function traces: For each thread in your app process, you can find out which methods (Java) or functions (C/C++) are executed over a period of time and the CPU resources each method or function consumes during its execution. You can also use method and function traces to identify callers and callees. A caller is a method or function that invokes another method or function, and a callee is one that is invoked by another method or function. You can use this information to determine which methods or functions are responsible for invoking particular resource-heavy tasks too often and optimize your app’s code to avoid unnecessary work.

======
The systrace command invokes the Systrace tool, which allows you to collect and inspect timing information across all processes running on your device at the system level. On devices running Android 9 (API level 28) or higher, you can also generate Systrace reports using the System Tracing system app. In order to run systrace, complete the following steps:
- From Android Studio, download and install the latest Android SDK Tools.
- Install Python and include it in your workstation's PATH environment variable.
- Add android-sdk/platform-tools/ to your PATH environment variable. This directory contains the Android Debug Bridge binary (adb), which is called by the systrace program.
- Connect a device running Android 4.3 (API level 18) or higher to your development system using a USB debugging connection.
- The systrace command is provided in the Android SDK Tools package and is located in android-sdk/platform-tools/systrace/.

What is the proper way of setting up an Android-powered device for app development: Declaring your application as "debuggable" in your Android Manifest enables you to debug your Android applications on an Android-powered device , just as you would on the emulator When using Studio, running your app directly from the Studio IDE automatically enables debugging. If you manually enable debugging in the manifest file, be sure to disable it before you build for release (your published application should usually not be debuggable).

======
Don't keep activity: It is one of the option provided in the Developer's settings. If selected, your activities will start destroying whenever the onStop method is called. This is for the testing purpose, in the cases where the Android system kills the activity due to memory constraint. The data can be get back using the onSaveInstance() function. If you think of keeping the activity instance or data in the application file then it's a bad idea. If you leave the App much longer Application Level object will be killed too. So it is very unsafe to store in the Application level.

======
StrictMode is a developer tool which detects things you might be doing by accident and brings them to your attention so you can fix them. StrictMode is most commonly used to catch accidental disk or network access on the application's main thread, where UI operations are received and animations take place. Keeping disk and network operations off the main thread makes for much smoother, more responsive applications. By keeping your application's main thread responsive, you also prevent ANR dialogs from being shown to users. In activity or application file
Example code to enable from early in your Application, Activity, or other application component's Application.onCreate() method:
public void onCreate() {
	 if (DEVELOPER_MODE) {
	     StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder()
	             .detectDiskReads()
	             .detectDiskWrites()
	             .detectNetwork()   // or .detectAll() for all detectable problems
	             .penaltyLog()
	             .build());
	     StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder()
	             .detectLeakedSqlLiteObjects()
	             .detectLeakedClosableObjects()
	             .penaltyLog()
	             .penaltyDeath()
	             .build());
	 super.onCreate();
	 }
}
You can decide what should happen when a violation is detected. For example, using StrictMode.ThreadPolicy.Builder.penaltyLog() you can watch the output of adb logcat while you use your application to see the violations as they happen. If you find violations that you feel are problematic, there are a variety of tools to help solve them: threads, Handler, AsyncTask, IntentService, etc. But don't feel compelled to fix everything that StrictMode finds. In particular, many cases of disk access are often necessary during the normal activity lifecycle. Use StrictMode to find things you did by accident. Network requests on the UI thread are almost always a problem, though.

====================
App Standby Buckets
====================
Android 9 (API level 28) introduces a new battery management feature, App Standby Buckets. App Standby Buckets help the system prioritize apps' requests for resources based on how recently and how frequently the apps are used. Based on app usage patterns, each app is placed in one of five priority buckets. The system limits the device resources available to each app based on which bucket the app is in.

======
Priority buckets: The system dynamically assigns each app to a priority bucket, reassigning the apps as needed. The system may rely on a preloaded app that uses machine learning to determine how likely each app is to be used, and assigns apps to the appropriate buckets. If the system app is not present on a device, the system defaults to sorting apps based on how recently they were used. More active apps are assigned to buckets that give the apps higher priority, making more system resources available to the app. 

======
The buckets are:
- Active: App is currently being used or was very recently used
- Working set: App is in regular use
- Frequent: App is often used, but not every day
- Rare: App is not frequently used

========
SECURITY
========
NAGARRO, HOME CREDIT
Always use a SSL connection between the client and the server.

======
If you thing using progaurd helps in encryption of the string API key. Not really. Proguard states in its FAQ that String encryption is not totally possible.

======
Secret Keys should not be stored in shared preferences without encrypting this data first because they can be extracted when performing a backup of your data. SharedPreferences can be easily accessed from the Emulator or any rooted device.
<application ...
    android:allowBackup="true">
</application>

======
HardCoded strings: An API key is much much more likely to be found in the assets or resources folder than in the decompiled code because it’s much easier to update xml than compiled code.

======
The Android Native Development Kit (NDK) allows you to write code in C/C++, and can be very useful when you’re trying to hide things like API keys. The good news is that NDK libraries can’t be decompiled making the information harder to find. The NDK compiled code can still be opened with a hexidecimal editor but by the nature of the API key they are harder to pick out in a hexidecimal editor.
https://medium.com/@abhi007tyagi/storing-api-keys-using-android-ndk-6abb0adcadad

======
Public/private API key exchange: Public key uses asymmetric algorithms that convert messages into an unreadable format. A person who has a public key can encrypt the message intended for a specific receiver. The receiver with the private key can only decode the message, which is encrypted by the public key. The key is available via the public accessible directory.

======
Disallow access to your app's content providers. Use tag exported to false.

======
Ask for credentials before showing sensitive information. The Biometric library displays a system prompt, asking the user to log in using a biometric credential. The resulting consistency in dialog appearance creates a more trustworthy user experience.

======
Use WebView objects carefully: Whenever possible, load only white-listed content in WebView objects. In other words, the WebView objects in your app shouldn't allow users to navigate to sites that are outside of your control. In addition, you should never enable JavaScript interface support unless you completely control and trust the content in your app's WebView objects.

======
Store all private user data within the device's internal storage, which is sandboxed per app. Your app doesn't need to request permission to view these files, and other apps cannot access the files. As an added security measure, when the user un-installs an app, the device deletes all files that the app saved within internal storage.

======
Keep all the dependencies up to date. As if there is any issue, that would be fixed in the latest release. 
Never release the production apk without progaurd or R8. We can use Firebase database to access the API keys if user is getting logged in using Firebase login method.

======
For Google API keys which are to mentioned in the xml, for all those keys the keys has to be restricted for only our app by mentioning in console.google.com.

======
Always check what are you printing in the logcat. You can use internal storage for sensitive information. Don’t process any payments on a rooted device. Because the rooted device can change your code at runtime and alter the behavior of it.

======
Always protect your services and content provider using service. To prevent leaking your data make sure to also set the exported flag to false like this.
<provider
    android:name="ContentProvider"
    android:authorities="com.ahmedabdelmeged.contentprovider"
    android:exported="false" />

======
Encrypt data on External Storage and Check the validity of that data. One of the most popular encryption algorithms is AES, short for Advanced Encryption Standard, with a key size of 256 bits. Writing code to encrypt and decrypt your app’s data using the javax.cryptopackage, which is included in the Android SDK, can be confusing. Therefore, You can use third-party libraries, such as Facebook's Conceal library, which are usually much easier to work with.

======
Android keystore system
https://medium.com/@ericfu/securely-storing-secrets-in-an-android-application-501f030ae5a3

=================
Observer pattern
=================
UrbanClap
When implementing the Observer pattern, there are two main approaches to consider: the 'push' model and the 'pull' model. In the 'push' model, the subject (i.e. the Observable) sends the observer on notification all the data it will need. The observer doesn't need to query the subject for information. In the 'pull' model, the subject merely notifies the observer that something happened, and the observer queries the subject based to get the information it needs.

======
Push: The main advantage of the 'push' model is lower coupling between the observer and the subject. The observer doesn't need to know anything about the subject in order to query it. If it needed to, we'd need to do one of the following: 
- do down-casting on the side of the observer in order to invoke class-specific get methods on the subject. This is bad. 
- make the Observable interface more class-specific, offering specific get methods, thus making the relationship between the observer and subject less general and making things more coupled. 
By implementing the 'push' model, we avoid all of this. 
However the disadvantage is less flexibility: the subject may not always know what exact information the observers need in order to send it to them. This will often mean more specific Observer interfaces, such as AgeObserver that are notified when the 'age' of the subject is changed, and HeightObserver which are sent the current height of the subject on notification. This is one option. The other is the subject sending a whole lot of information encapsulated in an Info object of some sort and have the observers query it from there. But again, we can't be sure we're sending the correct info. So it's either this, or forcing the observers to implement more specific Observer interfaces, which tightens the coupling on the observer's side.

======
Pull: The observers would have to know things about the subject in order to query the right information, which leads A- to down-casting (ugly), or B- favorably to more specific Observable interfaces, that offer more specific access methods. For example AgeObservable offers a getAge() method. The advantage of this is more flexibility. Each observer can decide for itself what to query, without relying on the subject to send the correct information.

==================
Lifecycle of view
==================
UrbanClap
View: This class represents the basic building block for user interface components. A View occupies a rectangular area on the screen and is responsible for drawing and event handling. View is the base class for widgets, which are used to create interactive UI components (buttons, text fields, etc.). The ViewGroup subclass is the base class for layouts, which are invisible containers that hold other Views (or other ViewGroups) and define their layout properties.

======
When a view is created, this is the order in which the following methods are called:
1. Constructor
	- CustomView(Context context) (if created pro-grammatically)
	- CustomView(Context context, AttributeSet attrs) (if created from xml)
2. onFinishInflate (assuming you used the xml constructor)
3. onAttachedToWindow
4. onMeasure
5. onSizeChanged
6. onLayout
7. onDraw

======
The earliest you can get the view’s measurements is in onMeasure. Before that the width and height are 0. However, the only thing you should be doing in onMeasure is determining the size of the view. This method gets called several times while the view is telling the parent how big it wants to be, but the parent is determining the actual final size.If you want to actually use the measured size for anything, the earliest place to do that is in onSizeChanged. It gets called whenever the view is created because the size is changing from 0 to whatever the size will be. Unlike onMeasure, onLayout is called only once during the traversal. So it is recommended to perform any complex calculations in this method. You can also access the size in onDraw with getMeasuredWidth() and getMeasuredHeight(). However, if you are using them to do any heavy calculations, it is better to do that beforehand. Generally speaking, try to keep as much out of onDraw as possible since it may be called multiple times. (It gets called whenever invalidate() gets called.)

======
invalidate() method used to simple redrawing view. While our view, for example, updates its text, color or touch interactivity. It means that view will only call onDraw() method once more to update its state. Tips: when dealing with animation, we need to call invalidate() to draw it. We need to call invalidate() every time new animated values comes out.

requestLayout() method, as you can see it will produce view update through its lifecycle just from onMeasure() method. This means that you will need it after your view updates. Since it changed its size, you need to measure it once again to draw it depending on the new size.

======
Difference between getHeight() and getMeasuredHeight: The size of a view is expressed with a width and a height. A view actually possess two pairs of width and height values. The first pair is known as measured width and measured height. These dimensions define how big a view wants to be within its parent (see Layout for more details.) The measured dimensions can be obtained by calling getMeasuredWidth() and getMeasuredHeight().
The second pair is simply known as width and height, or sometimes drawing width and drawing height. These dimensions define the actual size of the view on screen, at drawing time and after layout. These values may, but do not have to, be different from the measured width and height. The width and height can be obtained by calling getWidth() and getHeight().

======
When a user brings an Android view into focus, the Android framework directs the view to draw itself. This drawing process comprises 3 phases:
- Measure: The system completes a top-down traversal of the view tree to determine how large each ViewGroup and View element should be. When a ViewGroup is measured, it also measures its children.
- Layout: Another top-down traversal occurs, with each ViewGroup determining the positions of its children using the sizes determined in the measure phase.
- Draw: The system performs yet another top-down traversal. For each object in the view tree, a Canvas object is created to send a list of drawing commands to the GPU. These commands include the ViewGroup and View objects' sizes and positions, which the system determined during the previous 2 phases.

Each phase within the drawing process requires a top-down traversal of the view tree. Therefore, the more views you embed within each other (or nest) into the view hierarchy, the more time and computation power it takes for the device to draw the views. By keeping a flat hierarchy in your Android app layouts, you can create a fast and responsive user interface for your app. Nested hierarchies can adversely affect performance.

======
Detail lifecycle of view:
Constructor: Creating attributeSet. Create a new file and call it attrs.xml
public PageIndicatorView(Context context, AttributeSet attrs) {
    super(context, attrs);
    TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable.PageIndicatorView);
    int count = typedArray.getInt(R.styleable.PageIndicatorView_piv_count,0);
    typedArray.recycle();
}

======
onAttachedToWindow: After parent view calls addView(View) that view will be attached to a window.  At this stage our view will know the other views that it is surrounded by. If your view is working with user’s other views located in same layout.xml it is good place to find them by id (which you can set by attributes) and save as a global reference (if needed).

onMeasure: Means that our custom view is on stage to find out it’s own size. It’s very important method, as for most cases you will need your view to have specific size to fit in your layout. While overriding this method, what you need to do this is to set setMeasuredDimension(int width, int height).

onLayout: This method, incorporates assigning a size and position to each of its children. Because of that, we are looking into a flat custom view (that extends a simple View) that does not have any children so there is no reason to override this method.

onDraw: That’s where the magic happens. Having both Canvas and Paint objects will allow you draw anything you need. A Canvas instance comes as onDraw parameter, it basically respond for drawing a different shapes, while Paint object defines that color that shape will get. Simply, Canvas respond for drawing an object, while Paint for styling it. And it used mostly everywhere whether it is going to be a line, circle or rectangle. While making a custom view, always keep in mind that onDraw calls lots of time, like really a lot. While having some changes, scrolling, swiping your will be redrawn. So that’s why even Android Studio recommend to avoid object allocation during onDraw operation, instead to create it once and reuse further on.

======
invalidate() method used to simple redrawing view. While your view for example updates its text, color or touch interactivity. It means that view will only call onDraw() method once more to update its state.
requestLayout() method, as you can see will produce view update through its lifecycle just from onMeasure() method. And what it means that you will need it while after your view updates, it changed it’s size and you need to measure it once again to draw it depending on new size.

======
Animation: Animations in custom view is frame by frame process. It means that if you for example want to make a circle radius animate from smaller to bigger you will need to increase it one by one and after each step call invalidate() to draw it. Your best friend in custom view animations is ValueAnimator. This class will help you to animate any value from start to the end with even Interpolator support (if you need).
ValueAnimator animator = ValueAnimator.ofInt(0, 100);
animator.setDuration(1000);
animator.setInterpolator(new DecelerateInterpolator());
animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
  public void onAnimationUpdate(ValueAnimator animation) {
    int newRadius = (int) animation.getAnimatedValue();
  }
});
animator.start();
https://proandroiddev.com/android-draw-a-custom-view-ef79fe2ff54b

======
Zoom-in Zoom-out
Extending the onTouchListener and creating the instance of ScaleGestureDetector which helps you to detect how much scaling of image user has done. Then overriding the onMeaure method of the ImageView on which we have implemented onTouchListener. We use the scale value to get the width and height of the new zoomed image to be shown in our imageView.

======================
IllegalStateException
======================
Cannot perform this action after onSaveInstanceState().
Consider you are sending a network request from an activity and want to place a fragment using FragmentTransaction.commit() once the network request is successful. Suppose user presses home button before the request returns successfully, this will lead to onSaveInstanceState of the Activity being called and hence when you try to commit the fragment transaction after this the app will crash with the above error.
Issue: The main root cause of this exception is that fragment transactions are not allowed after the OnSaveInstanceState of the activity is called. Post honeycomb Activities are considered killable only after onStop() which means onSaveInstanceState will be called before onStop.

======
When you commit a Fragment(using fragmentTransaction.commit) the transaction wont happen immediately it is rather scheduled on the worker thread whenever the thread has time. You wont encounter this exception if you commit a FragmentTransaction in onCreate or in response to a user click but when you try to do this after lifecycle callbacks like onPause(),onSaveInstanceState() chances are high that you will encounter an exception. In simple terms this could be understood as fragment should only be committed when the activity is in foreground else you will encounter IllegalStateException.

======
Solution: 
commitAllowStateLoss: This does the same this what commit does but even if the state is lost this wont throw an exception. So basically we are not solving the issue but rather hiding it. Android developer docs describe this as dangerous since it might lead to unexpected UI behavior where the fragment commit is lost if the activity is restored again. But this will behaviore handy if the UI behavior doesn’t matter.
There is another solution by using the two booleans given in the below link
https://androidclarified.com/illegalstateexception-cannot-perform-this-action-after-onsaveinstancestate/

======
TOOLS
======
Tools attributes are special XML attributes that enable not only design-time features (such as which layout should be drawn inside the fragment), as well as compile-time behavior (such as suppressing lint warnings).

=====
These attributes are used solely by Android Studio and removed by build tools at build time of the app. So, using these attributes has no impact on your APK size or runtime behavior. To use these attributes, tools namespace should be added to the root element of XML file, as shown below:
<RootTag xmlns:android=”http://schemas.android.com/apk/res/android"
 	xmlns:tools=”http://schemas.android.com/tools" >

===================
Android App Bundle
===================
App Bundle: It is a new upload format that includes all your app’s compiled code and resources, but defers APK generation and signing to Google Play. But the contents like dex file, native libraries, resources, assets, android manifest file are present in the app bundle. Format is aab. Android app bundle. 

======
Why should we use this new format?
- Dynamic Delivery : Google Play’s new app serving model, called Dynamic Delivery, uses your app bundle to generate and serve optimized APKs for each user’s device configuration, so they download only the code and resources they need to run your app. For example, you don’t need other languages strings if you have set English as your default language.

- No need to manually manage multiple APKs : You no longer have to build, sign, and manage multiple APKs to support different devices, and users get smaller, more optimized downloads. For example, now you don’t have to create multiple APKs for devices with different screen resolutions.

- Dynamic Feature Module : These modules contain features and assets that you can decide not to include when users first download and install your app. Using the Play Core Library, your app can later request to download those modules as dynamic feature APKs. For example, video calling feature and camera filters can be downloaded later on demand.

- Reduced APK size : Using Split APK mechanism(explained later), Google Play can break up a large app into smaller, discrete packages that are installed on a user’s device as required. On average, apps published with app bundles are 20% smaller in size.

======
Splitting the APKs
- Base APK : This APK contains code and resources that all other split APKs can access and provides the basic functionality for your app. When the user downloads the app, they always get this APK.

- Configuration APKs : Each of these APKs includes only native libraries and resources for a given device configuration — screen density, language or CPU architecture. When a device downloads a base or dynamic feature APK, it downloads only the libraries and resources it needs. Add the following code in android {} block to enable language, density, and abi configuration splits
bundle {
   language {
       enableSplit = true
   }
   density {
       enableSplit = true
   }
   abi {
       enableSplit = true
   }
}

- Dynamic Feature APKs : Each of these APKs contain code and resources that are not required when your app is first installed, but may be downloaded and installed later.

========
Context
========
The context is the current state of the application/object. It lets newly-created objects understand what has been going on. Typically you call it to get information regarding another part of your program (activity and package/application). It is a handle to the system, it provides services like resolving resources, obtaining access to databases and preferences. An Android app has activities. It’s like a handle to the environment your application is currently running in. The activity object inherits the Context object. It allows access to application specific resources and class and information about the application environment. Wrong use of Context can easily lead to memory leaks in an android application.

======
Application Context: It is an instance which is the singleton and can be accessed in an activity via getApplicationContext(). This context is tied to the lifecycle of an application. The application context can be used where you need a context whose lifecycle is separate from the current context or when you are passing a context beyond the scope of an activity. Example: If you have to create a singleton object for your application and that object needs a context, always pass the application context. If you pass the activity context here, it will lead to the memory leak as it will keep the reference to the activity and activity will not be garbage collected. In case, when you have to initialize a library in an activity, always pass the application context, not the activity context. You only use getApplicationContext() when you know you need a Context for something that may live longer than any other likely Context you have at your disposal.

======
Activity Context: This context is available in an activity. This context is tied to the lifecycle of an activity. The activity context should be used when you are passing the context in the scope of an activity or you need the context whose lifecycle is attached to the current context. Example Use: If you have to create an object whose lifecycle is attached to an activity, you can use the activity context.

======
getContext() in ContentProvider: This context is the application context and can be used similar to the application context. This can be accessed via getContext() method.

======
When not to use getApplicationContext(): It’s not a complete Context, supporting everything that Activity does. Various things you will try to do with this Context will fail, mostly related to the GUI. It can create memory leaks if the Context from getApplicationContext() holds onto something created by your calls on it that you don't clean up. With an Activity, if it holds onto something, once the Activity gets garbage collected, everything else flushes out too. The Application object remains for the lifetime of your process.

======
The Rule of Thumb: In most cases, use the Context directly available to you from the enclosing component you’re working within. You can safely hold a reference to it as long as that reference does not extend beyond the lifecycle of that component. As soon as you need to save a reference to a Context from an object that lives beyond your Activity or Service, even temporarily, switch that reference you save over to the application context.

=================
Handling Bitmaps
=================
There are several libraries that follow best practices for loading images. You can use these libraries in your app to load images in the most optimized manner. We recommend the Glide library, which loads and displays images as quickly and smoothly as possible. Other popular image loading libraries include Picasso from Square and Fresco from Facebook. These libraries simplify most of the complex tasks associated with bitmaps and other types of images on Android.

======
BitmapFactory: Creates Bitmap objects from various sources, including files, streams, and byte-arrays.
The BitmapFactory class provides several decoding methods (decodeByteArray(), decodeFile(), decodeResource(), etc.) for creating a Bitmap from various sources. Choose the most appropriate decode method based on your image data source. These methods attempt to allocate memory for the constructed bitmap and therefore can easily result in an OutOfMemory exception.

======
Each type of decode method has additional signatures that let you specify decoding options via the BitmapFactory.Options class. Setting the inJustDecodeBounds property to true while decoding avoids memory allocation, returning null for the bitmap object but setting outWidth, outHeight and outMimeType. This technique allows you to read the dimensions and type of the image data prior to construction (and memory allocation) of the bitmap.

BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;

======
Load a Scaled Down Version into Memory: Now that the image dimensions are known, they can be used to decide if the full image should be loaded into memory or if a sub-sampled version should be loaded instead. Here are some factors to consider:
1) Estimated memory usage of loading the full image in memory.
2) Amount of memory you are willing to commit to loading this image given any other memory requirements of your application.
3) Dimensions of the target ImageView or UI component that the image is to be loaded into.
4) Screen size and density of the current device.

public static int calculateInSampleSize(BitmapFactory.Options options, int reqWidth, int reqHeight) {
    // Raw height and width of image
    final int height = options.outHeight;
    final int width = options.outWidth;
    int inSampleSize = 1;

    if (height > reqHeight || width > reqWidth) {

        final int halfHeight = height / 2;
        final int halfWidth = width / 2;

        // Calculate the largest inSampleSize value that is a power of 2 and keeps both
        // height and width larger than the requested height and width.
        while ((halfHeight / inSampleSize) >= reqHeight && (halfWidth / inSampleSize) >= reqWidth) {
            inSampleSize *= 2;
        }
    }
    return inSampleSize;
}

public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId, int reqWidth, int reqHeight) {

    // First decode with inJustDecodeBounds=true to check dimensions
    final BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(res, resId, options);

    // Calculate inSampleSize
    options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);

    // Decode bitmap with inSampleSize set
    options.inJustDecodeBounds = false;
    return BitmapFactory.decodeResource(res, resId, options);
}

=======
If you want to fetch the size
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    // From Android 4.4 (KitKat) onward we can re-use if the byte size of
    // the new bitmap is smaller than the reusable bitmap candidate allocation byte count.
    int width = targetOptions.outWidth / targetOptions.inSampleSize;
    int height = targetOptions.outHeight / targetOptions.inSampleSize;
    int byteCount = width * height * getBytesPerPixel(candidate.getConfig());
    return byteCount <= candidate.getAllocationByteCount();
}
	
// A helper function to return the byte usage per pixel of a bitmap based on its configuration.
static int getBytesPerPixel(Config config) {
    if (config == Config.ARGB_8888) {
        return 4;
    } else if (config == Config.RGB_565) {
        return 2;
    } else if (config == Config.ARGB_4444) {
        return 2;
    } else if (config == Config.ALPHA_8) {
        return 1;
    }
    return 1;
}

========
Layouts
========
Why Constraint layout is preferred upon other layouts:
Systrace automatically highlights the (numerous) performance problems with this layout, as well as suggestions for fixing them. Triggering that many expensive measure and layout phases is far from ideal; such a large amount of drawing activity could result in skipped frames that users notice. We can conclude that the layout has poor performance due to the nested hierarchy as well as the characteristic of RelativeLayout, which measures each of its children twice. ConstraintLayout allows you to build complex layouts without having to nest View and ViewGroup elements. It has flat hierarchy.

======
- LinearLayout means you can align views one by one (vertically/ horizontally).
- RelativeLayout means based on relation of views from its parents and other views.
- ConstraintLayout is similar to a RelativeLayout in that it uses relations to position and size widgets, but has additional flexibility and is easier to use in the Layout Editor.
- FrameLayout to load child one above another, like cards inside a frame, we can place one above another or anywhere inside the frame.

============
APP STARTUP
============
General:
- Android starts a new Linux process for an app when an app component (Activity, Service, Content Provider, Broadcast Receiver) needs to be started and the app does not have a running process yet
- All app components of the same app run in the same process and thread (main thread) → default behavior
- If an app component starts and there already exists a process for that app, then the new component is started within that already existing process

App startup sequence:
- Start Linux process
- Create runtime: Dalvik or ART (Android Runtime)
- Create Application instance (android.app.Application.onCreate() is called)
- Create the component for the application (e.g. Activity, Service, …) - It is the component that needs to be executed and triggered the startup

======
App launch can take place in one of three states, each affecting how long it takes for your app to become visible to the user: cold start, warm start, or hot start. In a cold start, your app starts from scratch. In the other states, the system needs to bring the running app from the background to the foreground. 

======
Cold start: refers to an app’s starting from scratch: the system’s process has not, until this start, created the app’s process. Cold starts happen in cases such as your app’s being launched for the first time since the device booted, or since the system killed the app.
At the beginning of a cold start, the system has three tasks. These tasks are:
- Loading and launching the app.
- Displaying a blank starting window for the app immediately after launch.
- Creating the app process.

As soon as the system creates the app process, the app process is responsible for the next stages:
- Creating the app object.
- Launching the main thread.
- Creating the main activity.
- Inflating views.
- Laying out the screen.
- Performing the initial draw.
Once the app process has completed the first draw, the system process swaps out the currently displayed background window, replacing it with the main activity. At this point, the user can start using the app.

======
Hot start: of your application is much simpler and lower-overhead than a cold start. In a hot start, all the system does is bring your activity to the foreground. If all of your application’s activities are still resident in memory, then the app can avoid having to repeat object initialization, layout inflation, and rendering. However, if some memory has been purged in response to memory trimming events, such as onTrimMemory(), then those objects will need to be recreated in response to the hot start event. A hot start displays the same on-screen behavior as a cold start scenario: The system process displays a blank screen until the app has finished rendering the activity.

======
Warm start: encompasses some subset of the operations that take place during a cold start; at the same time, it represents less overhead than a hot start. There are many potential states that could be considered warm starts. For instance: 
- The user backs out of your app, but then re-launches it. The process may have continued to run, but the app must recreate the activity from scratch via a call to onCreate(). 
- The system evicts your app from memory, and then the user re-launches it. The process and the activity need to be restarted, but the task can benefit somewhat from the saved instance state bundle passed into onCreate().

=============
Localization
=============
The user sees an error message and a Force Close button when this app is launched on a device set to a language other than English(device having only english resources). The app doesn't load. That's wy default resource is important

======
The only qualifiers that take precedence over locale in the selection process are MCC and MNC (mobile country code and mobile network code).

Ex: Assume that you have the following situation:
- The app code calls for R.string.text_a
Two relevant resource files are available:
- res/values-mcc404/strings.xml, which includes text_a in the app's default language, in this case English.
- res/values-hi/strings.xml, which includes text_a in Hindi.
The app is running on a device that has the following configuration:
- The SIM card is connected to a mobile network in India (MCC 404).
- The language is set to Hindi (hi).
Android loads text_a from res/values-mcc404/strings.xml (in English), even if the device is configured for Hindi. That is because in the resource-selection process, Android prefers an MCC match over a language match.

======
To mark text that should not be translated, use an <xliff:g> placeholder tag.
Here is an example tag that ensures the text "%1$s" isn't changed during translation (otherwise it could break the message):
<string name="countdown">
  <xliff:g id="time" example="5 days">%1$s</xliff:g> until holiday
</string>

=========
SNACKBAR
=========
Snackbars work best if they are displayed inside of a CoordinatorLayout. CoordinatorLayout allows the snackbar to enable behavior like swipe-to-dismiss, as well as automatically moving widgets like FloatingActionButton.

======
The view used to make the snackbar. This should be contained within the view hierarchy you want to display the snackbar. Generally it can be the view that was interacted with to trigger the snackbar, such as a button that was clicked, or a card that was swiped.
View contextView = findViewById(R.id.context_view);
Snackbar.make(contextView, R.string.item_removed_message, Snackbar.LENGTH_SHORT).show();

======
To add an action, use the setAction method on the object returned from make. Actions require both text to display, and an OnClickListener to handle clicks on the action. Snackbars are automatically dismissed when the action is clicked.
Snackbar.make(contextView, R.string.item_removed_message, Snackbar.LENGTH_LONG)
    .setAction(R.string.undo, new OnClickListener() {
      @Override
      public void onClick(View v) {
        // Respond to the click, such as by undoing the modification that caused
        // this message to be displayed
      })
    });
	
======
Android Snackbar does not allow set custom layout. But it follows builder pattern which allows for easy styling. 

Style Action Text color: snackbar.setActionTextColor(colorId)

Style text color, font, and text size and number of lines
View snackbarView = snackbar.getView();
Now get TextView from View and apply all TextView properties as below
TextView textView = snackbarView.findViewById(R.id.snackbar_text);
// set no of text line
textView.setMaxLines(2);
//set text color
textView.setTextColor(colorId);
//set text size
textView.setTextSize(TypedValue.COMPLEX_UNIT_SP, 22);

Background color
snackbarView.setBackgroundColor(colorId);

=================
ConstraintLayout
=================
A ConstraintLayout is a ViewGroup that allows you to position and size the layout's child views in a flexible way. In a constraint layout, each view's position is defined using at least one horizontal constraint, and at least one vertical constraint. A constraint connects or aligns a view to another UI element, to the parent layout, or to an invisible guideline. It flatens the heirarcy allowing to save the time and complexity

======
Advantages of using ConstraintLayout:
- You can make a constraint layout responsive to devices that have different screen sizes and resolutions.
- ConstraintLayout usually results in a flatter view hierarchy than LinearLayout.
- The design editor and the view inspector in Android Studio help you add and configure constraints.

======
A chain is a group of views that are linked to each other with bidirectional constraints. The views within a chain can be distributed either vertically or horizontally.

======
Design-time attributes are used and applied only during the layout design, not at runtime. When you run the app, design-time attributes are ignored. Design-time attributes are prefixed with the tools namespace. For example, the tools:layout_editor_absoluteY and tools:text attributes are design-time attributes.

======
A baseline constraint aligns a view's text baseline to the text baseline of another view that has text.
Baseline constraints are helpful when views have different font sizes.

======
When a user brings an Android view into focus, the Android framework directs the view to draw itself. This drawing process comprises 3 phases:

Measure: The system completes a top-down traversal of the view tree to determine how large each ViewGroup and View element should be. When a ViewGroup is measured, it also measures its children.

Layout:Another top-down traversal occurs, with each ViewGroup determining the positions of its children using the sizes determined in the measure phase.

Draw: The system performs yet another top-down traversal. For each object in the view tree, a Canvas object is created to send a list of drawing commands to the GPU. These commands include the ViewGroup and View objects' sizes and positions, which the system determined during the previous 2 phases.

Each phase within the drawing process requires a top-down traversal of the view tree. Therefore, the more views you embed within each other (or nest) into the view hierarchy, the more time and computation power it takes for the device to draw the views. By keeping a flat hierarchy in your Android app layouts, you can create a fast and responsive user interface for your app.

==============
ACCESSIBILITY
==============
Add a live region to the button by setting the android:accessibilityLiveRegion attribute. As you type, you have several options for its value. Depending on the importance of the change, you can choose whether to interrupt the user. With the value "assertive", the accessibility services interrupt ongoing speech to immediately announce changes to this view. If you set the value to "none", no changes are announced. Set to "polite", the accessibility services announce changes, but wait their turn. Set the value to "polite".
android:accessibilityLiveRegion="polite"

======
Accessibility scanner app helps you to find issues related to your screen. They provide the suggestion related to accessibility in your app.

=====
CHIP
=====
The Chip widget is a thin view wrapper around the ChipDrawable, which contains all of the layout and draw logic. The extra logic exists to support touch, mouse, keyboard, and accessibility navigation. The main chip and close icon are considered to be separate logical sub-views, and contain their own navigation behavior and state.

======
Chips use drawables. Android drawables let you draw images, shapes, and animations on the screen, and they can have a fixed size or be dynamically sized. 

======
You can create a Horizontal scrollview having the Chip Group. Set its singleLine property to true, so that all chips are lined up on one horizontally scrollable line. Set the singleSelection property to true so that only one chip in the group can be selected at a time.
<com.google.android.material.chip.ChipGroup
    android:id="@+id/region_list"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:singleSelection="true"
    android:padding="@dimen/spacing_normal"/>

<com.google.android.material.chip.Chip
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	style="@style/Widget.MaterialComponents.Chip.Choice"
	app:chipBackgroundColor="@color/selected_highlight"
	app:checkedIconVisible="true"
	tools:checked="true"/>
	
===========
PREFERENCE
===========
Settings allow users to change the functionality and behavior of an application. Settings can affect background behavior, such as how often the application synchronizes data with the cloud, or they can be more wide-reaching, such as changing the contents and presentation of the user interface. This library manages the user interface and interacts with storage so that you define only the individual settings that the user can configure. 

======
A Preference is the basic building block of the Preference Library. A settings screen contains a Preference hierarchy. 
<PreferenceScreen
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <SwitchPreferenceCompat
        app:key="notifications"
        app:title="Enable message notifications"/>
    <Preference
        app:key="feedback"
        app:title="Send feedback"
        app:summary="Report technical issues or suggest new features"/>
</PreferenceScreen>

This has to be placed under res/xml

======
Inflate heirarchy: Create a PreferenceFragmentCompat, override onCreatePreferences(), and provide the XML resource to inflate
class MySettingsFragment : PreferenceFragmentCompat() {
    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        setPreferencesFromResource(R.xml.preferences, rootKey)
    }
}

// display the fragment
etSupportFragmentManager().beginTransaction()
	.replace(android.R.id.content, new SettingsFragment())
    .commit();

======
Preference Category: A PreferenceCategory displays a category title and visually separates the category.
<PreferenceScreen
    <PreferenceCategory
        app:key="notifications_category"
        app:title="Notifications">
        <SwitchPreferenceCompat
            app:key="notifications"
            app:title="Enable message notifications"/>
    </PreferenceCategory>
    <PreferenceCategory
        app:key="help_category"
        app:title="Help">

        <Preference
            app:key="feedback"
            app:summary="Report technical issues or suggest new features"
            app:title="Send feedback"/>
    </PreferenceCategory>
</PreferenceScreen>

======
To link screens with a Preference, you can declare an app:fragment in XML, or you can use Preference.setFragment(). Set the full package name of the PreferenceFragmentCompat that should be launched when the Preference is tapped, as shown below:

<Preference
	app:fragment="com.example.SyncFragment"
	.../>
When a user taps a Preference with an associated Fragment, the interface method PreferenceFragmentCompat.OnPreferenceStartFragmentCallback.onPreferenceStartFragment() is called. This method is where you should handle displaying the new screen and should be implemented in the surrounding Activity.

Note: if you do not implement onPreferenceStartFragment(), a fallback implementation is used instead. While this works in most cases, we strongly recommend implementing this method so you can fully configure transitions between Fragment objects and update the title displayed in your Activity toolbar, if applicable.

class MyActivity : AppCompatActivity(),
    PreferenceFragmentCompat.OnPreferenceStartFragmentCallback {
    ...
    override fun onPreferenceStartFragment(caller: PreferenceFragmentCompat, pref: Preference): Boolean {
        // Instantiate the new Fragment
        val args = pref.extras
        val fragment = supportFragmentManager.fragmentFactory.instantiate(classLoader, pref.fragment)
        fragment.arguments = args
        fragment.setTargetFragment(caller, 0)
        // Replace the existing Fragment with the new Fragment
        supportFragmentManager.beginTransaction()
                .replace(R.id.settings_container, fragment)
                .addToBackStack(null)
                .commit()
        return true
    }
}

======
<EditTextPreference
	app:key="signature"
	app:useSimpleSummaryProvider  = true // the value set by user is shown automatically
	app:title="Your signature"
	fragment="com.example.preference.MyFragment" // to open the next screen when this preference selected 
	app:icon="@drawable/messages"
	app:dependency = "enable_sync" // this will map your preference with other preference. 
	app:isPreferenceVisible="false"/>
	
override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
    setPreferencesFromResource(R.xml.preferences, rootKey)
    if(/*some feature*/) {
        val signaturePreference: EditTextPreference? = findPreference("signature")
        signaturePreference?.isVisible = true
    }
}

======
Reading preference value
val sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this /* Activity context */)
val name = sharedPreferences.getString("signature", "")

======
Save the default values in shared preferences
android.support.v7.preference.PreferenceManager.setDefaultValues(this, R.xml.preferences, false);
