##Prerequisites
- Android Studio 1.0 or higher
- Android 4.1 or higher (Beacons only work with Android 5.0 or higher)
- Compile SDK Version: 23
- Target SDK Version: 23
- Min Sdk Version: 16

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
    compile 'com.lbslocal.findrix:library:1.0.2.6'
    
}

android {
    useLibrary 'org.apache.http.legacy'
}

```

##Manifest file
Add the information below before the closing </application> tag:
```xml

<receiver android:name="lbslocal.com.trls.receivers.TRLSLocationReceiver" />

<receiver 
	    android:name="lbslocal.com.trls.receivers.TRLSGCMBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <category android:name="lbslocal.com.trls.gcm" />
            </intent-filter>
</receiver>
		
<service android:enabled="true" android:name="lbslocal.com.trls.core.TRLSService"/>

<receiver android:name="lbslocal.com.trls.receivers.TRLSBootReceiver"
            android:enabled="true"
            android:exported="true"
            android:permission="android.permission.RECEIVE_BOOT_COMPLETED">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </receiver>

<receiver android:name="lbslocal.com.trls.receivers.TRLSWiFiReceiver" >
            <intent-filter>
                <action android:name="android.net.wifi.SCAN_RESULTS" />
                <action android:name="android.net.wifi.STATE_CHANGE" />
            </intent-filter>
        </receiver>

<activity
            android:name="lbslocal.com.trls.message.TRLSMessageActivity"
            android:label="Mensagem"
            android:parentActivityName="YOUR_MAIN_ACTIVITY">
            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value="YOUR_MAIN_ACTIVITY"/>
</activity>

<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```
Also add the permissions below:
```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.CALL_PHONE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="YOUR_PACKAGE.permission.C2D_MESSAGE" />
<permission android:name="YOUR_PACKAGE.permission.C2D_MESSAGE" android:protectionLevel="signature" />
<uses-permission android:name="android.permission.BLUETOOTH" />
```

##Initialization
Before starting the library, make sure to check for and request location permission at runtime. More info at http://developer.android.com/intl/pt-br/training/permissions/index.html
```java
@Override
protected void onCreate(Bundle savedInstanceState) {

	TRLSCallback callback = new TRLSCallback() {
            @Override
            public void didCreateDevice(String deviceId, Error error) {

                if(error == null) Log.d("TRLS", "Device created " + deviceId);
                else Log.d("TRLS", "Error creating device " + error.getMessage());

            }
        };

	try {
		TRLS.start(ctx, userId, clientId, clientSecret, name, phone, email, photo, senderId, callback);
	} catch (Exception e) {
            e.printStackTrace();
        }
}
```

#####Parameters:
- ctx - Context
- userId – Provide an ID for this device(can be null or empty) 
- clientId – Request a client id with TRLS dev team
- clientSecret – Request a client secret with TRLS dev team 
- name – Can be null or empty
- phone - Can be null or empty
- email - Can be null or empty
- photo - Can be null or empty
- senderId - You can use TRLs with other push provider(ie "SENDER_ID_1,SENDER_ID_2"). Can be null of empty
- callback - Device creation callback 

**If you want to register your app with multiple additional sender IDs, the senderId parameter should hold a comma-delimited list of sender IDs.**

##Push notifications
￼￼Ignore the instructions below if you don’t use Google Cloud Messaging to receive push notifications. TRLS library uses 

Google Cloud Messaging to send push notifications. As you noticed in the manifest file, TRLS library has it’s own receiver. But you can have yours, and keep your own push notification service running. If you already have a broadcast receiver to process your push notifications, you need to verify if the notification is from a TRLS server:

```java
@Override
public void onReceive(Context context, Intent intent){
	if(!TRLSGCMBroadcastReceiver.isTrlsNotification(intent)){
		//It's not a TRLS notification, you can process de push notification
	}else{
		//It's a TRLS notification, the library will process it automatically;
	} 
}
```

##Proguard

#####Add the following line to your proguard file:
```proguard
-keep class lbslocal.com.trls.**{*;}
```
