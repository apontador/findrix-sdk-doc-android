##Prerequisites
- Android Studio 1.0 or higher
- Android 4.1 or higher (Beacon only works with Android 5.0 or higher)
- Compile Sdk Version: 21
- Min Sdk Version: 16

##Import the library
1. Add the file trls.aar to PROJECT_ROOT_FOLDER/libs (create the libs folder if it doesn’t exist)
2. Open build.gradle file and add the information below:
```gradle
repositories {
	mavenCentral()
	flatDir {
		dirs 'libs'
	} 
}

android {
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/LICENSE'
    }
}

dependencies {
	compile(name:'trls', ext:'aar')
	compile 'com.google.android.gms:play-services:6.5.87'
    	compile 'com.google.code.gson:gson:2.3.1'
    	compile('org.apache.httpcomponents:httpmime:4.3.6') {
        	exclude module: 'httpclient'
    	}
    	compile 'org.apache.httpcomponents:httpclient-android:4.3.5'
}
```

##Dependencies
Add the following dependencies to your app(right click on your module -> Open Module Settings -> Dependencies):
- com.google.android.gms:play-services:6.5.87 or higher
- com.google.code.gson:gson:2.3.1 or higher
- org.apache.httpcomponents:httpmime:4.3.6
- org.apache.httpcomponents:httpclient-android:4.3.5

##Manifest file
Add the information below before the closing </application> tag:
```xml
<receiver android:name="lbslocal.com.trls.gcm.TRLSGCMBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND" >
	<intent-filter>
		<action android:name="com.google.android.c2dm.intent.RECEIVE" /> <category android:name="lbslocal.com.trls.gcm" />
  </intent-filter>￼￼￼
</receiver>

<service android:name="lbslocal.com.trls.gcm.TRLSGCMIntentService" />
<service android:enabled="true" android:name="lbslocal.com.trls.core.TRLSService"/>

<receiver android:name="lbslocal.com.trls.core.TRLSBootReceiver">
	<intent-filter>
		<action android:name="android.intent.action.BOOT_COMPLETED"/>
	</intent-filter>
</receiver>

<activity android:name="lbslocal.com.trls.message.MessageActivity" android:label="Mensagem" android:parentActivityName="YOUR_MAIN_ACTIVITY">
	<meta-data android:name="android.support.PARENT_ACTIVITY" android:value="YOUR_MAIN_ACTIVITY"/>
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
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-permission android:name="com.google.android.gms.permission.ACTIVITY_RECOGNITION"/>
<uses-permission android:name="com.lbslocal.trls.gcm.permission.C2D_MESSAGE" />
<permission android:name="com.lbslocal.trls.gcm.permission.C2D_MESSAGE" android:protectionLevel="signature" />
```

##Initialization
```java
@Override
protected void onCreate(Bundle savedInstanceState) {

	TRLSCallback callback = new TRLSCallback() {
            @Override
            public void didCreateDevice(boolean success, String deviceId) {

                if(success) Log.d("TRLS", "Device created " + deviceId);
                else Log.d("TRLS", "Error creating device");

            }
        };

	TRLS.start(MainActivity.this, externalId, clientId, clientSecret, deviceName, devicePhoneNumber, devicePhoto, callback);
}
```

#####Parameters:
- externalId – Provide an ID for this device(can be null or empty) 
- clientId – Request a client id with TRLS dev team
- clientSecret – Request a client secret with TRLS dev team 
- deviceName – Can be null or empty
- devicePhoneNumber - Can be null or empty
- devicePhoto - Can be null or empty
- callback - Device creation callback 

##Push notifications
￼￼Ignore the instructions below if you don’t use Google Cloud Messaging to receive push notifications. TRLS library uses Google Cloud Messaging to send push notifications. As you noticed in the manifest file, TRLS library have it’s own receiver. But you can have yours, and keep your own push notification service running. If you already have a broadcast receiver to process your push notifications, you need to verify if the notification is from a TRLS server:
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
