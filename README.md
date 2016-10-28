##Prerequisites
- Android Studio 1.0 or higher
- Android 2.3 or higher (Beacons only work with Android 5.0 or higher)
- Compile SDK Version: 23
- Target SDK Version: 23
- Min Sdk Version: 9

##Import the library
Open build.gradle file and add the information below:
```gradle
android {
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/LICENSE'
    }
}

dependencies {
    compile 'com.android.support:appcompat-v7:23.1.1'
    compile 'com.google.android.gms:play-services-location:8.4.0'
    compile 'com.google.android.gms:play-services-gcm:8.4.0'
    compile 'com.google.code.gson:gson:2.4'
    compile 'org.apache.httpcomponents:httpmime:4.3.6'
    compile 'org.apache.httpcomponents:httpclient-android:4.3.5'
    compile 'com.lbslocal.findrix:library:2.0.9.0'
}

android {
    useLibrary 'org.apache.http.legacy'
}

```

##Manifest file
Add the information below before the closing </application> tag:
```xml

 <receiver android:name="com.lbslocal.findrix.receivers.FDXLocationReceiver" />

    <receiver
        android:name="com.lbslocal.findrix.receivers.FDXGCMBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />

            <category android:name="com.lbslocal.findrix.gcm" />
        </intent-filter>
    </receiver>

    <service
        android:name="com.lbslocal.findrix.core.FDXService"
        android:enabled="true" />

    <receiver android:name="com.lbslocal.findrix.receivers.FDXPushActionReceiver">
        <intent-filter>
            <action android:name="com.lbslocal.findrix.PUSH_ACTION" />
        </intent-filter>
    </receiver>

    <receiver
        android:name="com.lbslocal.findrix.receivers.FDXBootReceiver"
        android:enabled="true"
        android:exported="true"
        android:permission="android.permission.RECEIVE_BOOT_COMPLETED">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <category android:name="android.intent.category.DEFAULT" />
        </intent-filter>
    </receiver>

    <receiver android:name="com.lbslocal.findrix.receivers.FDXWiFiReceiver">
        <intent-filter>
            <action android:name="android.net.wifi.SCAN_RESULTS" />
            <action android:name="android.net.wifi.STATE_CHANGE" />
        </intent-filter>
    </receiver>

    <activity
        android:name="com.lbslocal.findrix.message.FDXMessageActivity"
        android:label="Mensagem"
        android:parentActivityName="YOUR_MAIN_ACTIVITY">
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="YOUR_MAIN_ACTIVITY" />
    </activity>

    <meta-data
        android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```
Also add the permissions below:
```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.CALL_PHONE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="YOUR_PACKAGE.permission.C2D_MESSAGE" />
<permission android:name="YOUR_PACKAGE.permission.C2D_MESSAGE" android:protectionLevel="signature" />
```

##Initialization
Before starting the library, make sure to check for and request location permission at runtime. More info at http://developer.android.com/intl/pt-br/training/permissions/index.html.

Call Findrix.start() on your MainActivity's or Application onCreate method:
```java
@Override
protected void onCreate(Bundle savedInstanceState) {

	FDXCallback callback = new FDXCallback() {
            @Override
            public void didCreateDevice(String deviceId, Error error) {

                if(error == null) Log.d("Findrix", "Device created " + deviceId);
                else Log.d("Findrix", "Error creating device " + error.getMessage());

            }
        };

	Findrix.start(ctx, userId, clientId, clientSecret, photo, senderId, callback);
	
}
```

#####Parameters:
- ctx - Context
- userId – Provide an ID for this device(can be null or empty) 
- clientId – Request a client id with TRLS dev team
- clientSecret – Request a client secret with TRLS dev team 
- photo - Can be null or empty
- senderId - You can use TRLs with other push provider(ie "SENDER_ID_1,SENDER_ID_2"). Can be null of empty
- callback - Device creation callback 

**If you want to register your app with multiple additional sender IDs, the senderId parameter should hold a comma-delimited list of sender IDs.**

##Push notifications

Add the following meta-data to the app's manifest.xml file:

```xml
<meta-data android:name="com.lbslocal.findrix.notification_icon" android:resource="@drawable/YOUR_NOTIFICATION_ICON"/>
```

##Proguard

#####Add the following line to your proguard file:
```proguard
-keep class com.lbslocal.findrix.**{*;}
```

##Customize the UI for Your App

Once you've set up your library integration, you can customize the UI to match the color scheme and branding of your app to ensure the best user experience for your users.  
Set the style for the message with the **android:theme** attribute for the **TRLSMessageActivity**. This attribute is read from the AndroidManifest.xml file.If no values are set, the UI will default theme.  
For example,the following style will produce the custom theme.  
```xml
 <style name="MessageStyle" parent="Theme.FindrixMessage">
 
        <item name="com_lbslocal_findrix_background_color">@android:color/white</item>
        <item name="com_lbslocal_findrix_header_background_color">#4CAF50</item>
        <item name="com_lbslocal_findrix_header_text_color">@android:color/white</item>
        <item name="com_lbslocal_findrix_status_bar_color">#388E3C</item>
        <item name="com_lbslocal_findrix_title_text_color">#727272</item>
        <item name="com_lbslocal_findrix_title_text_size">20sp</item>
        <item name="com_lbslocal_findrix_description_text_color">#212121</item>
        <item name="com_lbslocal_findrix_description_text_size">14sp</item>
        <item name="com_lbslocal_findrix_button_action_text_color">@android:color/white</item>
        <item name="com_lbslocal_findrix_button_action_text_color_pressed">@android:color/white</item>
        <item name="com_lbslocal_findrix_button_action_text_size">16sp</item>
        <item name="com_lbslocal_findrix_button_action_background_color">#448AFF</item>
        <item name="com_lbslocal_findrix_button_action_background_color_pressed">#448AFF</item>

</style>
```   
AndroidManifest.xml

```xml
<activity
            android:name="com.lbslocal.findrix.message.FDXMessageActivity"
            android:label="Mensagem"
            android:theme="@style/MessageStyle"
            android:parentActivityName="YOUR_MAIN_ACTIVITY">
            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value="YOUR_MAIN_ACTIVITY"/>
</activity>
```   


