# 1. Import Pangle SDK

## Environment requirement
- minSdkVersion 14 and higher
- targetSDKVersion 28 and higher
- compileSdkVersion 29 and higher
- gradleVersion required as below

<img src="https://github.com/JohnnyWangMiura/Pangle-Android-SDK-Integration-Guideline/blob/main/gradle.png" />

## Import Pangle SDK

Apps can import the Pangle SDK with a Gradle dependency. First, make sure that `JCenter` is referenced in the `allprojects` section of your project-level `build.gradle` file.

Example project-level build.gradle (excerpt)
```XML
allprojects {
    repositories {
        jcenter()
    }
}
```

Next, open the app-level `build.gradle` file for your app, and look for a "dependencies" section.
```XML
dependencies {
    implementation 'com.bytedance.sdk:pangle-sdk:3.4.1.1'
}
```

Add the line in bold above, which instruct Gradle to pull in the Pangle SDK and additional related dependencies. Once that's done, save the file and perform a Gradle sync.

## Update your AndroidManifestl.xml
Add the required permissions to your app's `AndroidManifest.xml` file, as shown below.
```XML
<!--Required  permissions-->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<!-- If there is a video ad and it is played with textureView, please be sure to add this, otherwise a black screen will appear -->
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

Add the TTMultiProvider to your app's `AndroidManifest.xml` file by adding a <provider> tag, as shown below:
  
```XML
<manifest>
    <application>
        <!-- add the provider between the application tag -->
        <provider    
android:name="com.bytedance.sdk.openadsdk.multipro.TTMultiProvider"   android:authorities="${applicationId}.TTMultiProvider"   android:exported="false" />
    </application>
</manifest>

  
```  

## Obfuscation
If you need to use proguard to obfuscate your code, be sure not to obfuscate the SDK code. Please add the following configuration at the end of the proguard.cfg file (or other obfuscated files):
```XML
-keep class com.bytedance.sdk.openadsdk.** { *; }
```

