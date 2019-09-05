# Appodeal Cordova Plugin

This is an official Appodeal Cordova plugin, created to support Appodeal SDK with Apache Cordova.

# How to Update Plugin Manually

### Android Part:

+ Download new Appodeal Android SDK [here](https://www.appodeal.com/sdk/documentation?framework=1&full=1&platform=1) and unzip it somewhere.
+ Copy jars except `android-support-v7-recyclerview` (it is needed for native ads witch is not supported by Cordova plugin) into `libs/Android`.
+ aar libraries from `aar` folder should be unzipped and composed into ant libraries, you can check how it should be structured on existing libraries in [Android](/libs/Android/) folder.
+ Open `plugin.xml` file, scroll down to the end of android platform tag, edit `source-file` tags (it should be equal to the jar names, you've put into `libs/Android` folder) and `framework` tags (should be equal to the library folders, you've composed from aar libraries).
+ Copy new `AndroidManifest.xml` tags from [Appodeal Android Docs](https://www.appodeal.com/sdk/documentation?framework=1&full=1&platform=1) page and replace old with new inside `<config-file parent="/manifest/application" target="AndroidManifest.xml">`.
+ Check [AppodealPlugin.java](/src/android/AppodealPlugin.java) for any API changes.
+ Done, you can use updated plugin for Android Platform.

### iOS Part:

+ Download new Appodeal iOS SDK [here](https://www.appodeal.com/sdk/documentation?framework=20&full=1&platform=4) and unzip it somewhere.
+ Remove [Adapters](/libs/iOS/Adapters/) folder and [Appodeal.bundle](/libs/iOS/Appodeal/). Replace old `Appodeal.framework` with new one and put `Resources` folder if exists into [libs/iOS/Appodeal](/libs/iOS/Appodeal).
+ Open [plugin.xml](/plugin.xml), scroll down to `<platform name="ios">` and check system frameworks for any changes due to changes in 3 Step of 5.3 Manual Integration in [Appodeal iOS Doc page](https://www.appodeal.com/sdk/documentation?framework=20&full=1&platform=4).
+ Remove all the content after `<framework custom="true" src="libs/iOS/Appodeal/Appodeal.framework"/>` in [plugin.xml](/plugin.xml) up to `</platform>` close tag. 
+ Skip this step if unzipped iOS SDK does not contain `Resources` folder. Add all resources from `Resources` folder of unzipped iOS SDK manually after `<framework custom="true" src="libs/iOS/Appodeal/Appodeal.framework"/>`, for example: 

```xml
<resource-file src="libs/iOS/Appodeal/Resources/MPCloseBtn.png"/>
```

+ Check [AppodealPlugin.m](/scr/ios/AppodealPlugin.m) for any API changes.
+ Done, you can use updated plugin for iOS Platform.

## Install this branch

Simply go to the project folder over console/terminal and run there following command:

    cordova plugin add https://github.com/GartorwareCorp/appodeal-cordova-plugin.git#trimmed --variable ADMOB_APP_ID="<YOUR_ANDROID_ADMOB_APP_ID_AS_FOUND_IN_ADMOB>"

Then in your code you must disable these networks **BEFORE** initialization:
```
// Networks
Appodeal.disableNetwork(activity, "mailru");
Appodeal.disableNetwork(activity, "yandex");
Appodeal.disableNetwork(activity, "facebook");
Appodeal.disableNetwork(activity, "chartboost");
Appodeal.disableNetwork(activity, "inmobi");
Appodeal.disableNetwork(activity, "unity_ads");
Appodeal.disableNetwork(activity, "my_target");
```

Also, *if targeting Android >= 28*, create a `resources/android/xml/network_security_config.xml` file with this content:
```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system"/>
            <certificates src="user" />
        </trust-anchors>
    </base-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain>localhost</domain>
    </domain-config>
</network-security-config>
```
And edit your config.xml to include this file in your build:

```
<edit-config file="app/src/main/AndroidManifest.xml" mode="merge" target="/manifest/application" xmlns:android="http://schemas.android.com/apk/res/android">
     <application android:networkSecurityConfig="@xml/network_security_config" />
</edit-config>
<resource-file src="resources/android/xml/network_security_config.xml" target="app/src/main/res/xml/network_security_config.xml" />
        
```

Google Play Services (v17+) already included to plugin dependencies.

If you have issues while installing plugin, follow the Command-line Interface Guide.

Minimum OS requirements: 

+ iOS 8.1
+ Android API level 14 (Android OS 4.0)

Appodeal Cordova Plugin includes:

+ Android Appodeal SDK version 2.5.6
+ iOS Appodeal SDK version 2.4.4.3-Beta

### Multidex
 
If receiving errors while compiling about method number limites (>65K), please, enable Multidex:

**For Android X**
```
cordova plugin add https://github.com/GartorwareCorp/cordova-plugin-androidx-multidex.git
```

### Proguard

If you are using proguard (cordova-plugin-proguard) don't forget to add custom rules:

<details>
  <summary>Appodeal proguard rules</summary>
  
  ```
  # To enable ProGuard in your project, edit project.properties
# to define the proguard.config property as described in that file.
# Add project specific ProGuard rules here.
# By default, the flags in this file are appended to flags specified
# in ${sdk.dir}/tools/proguard/proguard-android.txt
# You can edit the include path and order by changing the ProGuard
# include property in project.properties.
#
# For more details, see
#   http://developer.android.com/guide/developing/tools/proguard.html

# Add any project specific keep options here:
#-dontshrink
#-dontoptimize
#-dontobfuscate

# If your project uses WebView with JS, uncomment the following
# and specify the fully qualified class name to the JavaScript interface
# class:
#-keepclassmembers class android.webkit.WebView {
#   public *;
#}

#-injars      bin/classes
#-injars      libs
#-outjars     bin/classes-processed.jar

# Using Google's License Verification Library 
-keep class com.android.vending.licensing.ILicensingService

# Specifies to write out some more information during processing. 
# If the program terminates with an exception, this option will print out the entire stack trace, instead of just the exception message.
-verbose

# Annotations are represented by attributes that have no direct effect on the execution of the code. 
-keepattributes *Annotation*

-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

-keepattributes InnerClasses
-keep class **.R
-keep class **.R$* {
    <fields>;
}

# These options let obfuscated applications or libraries produce stack traces that can still be deciphered later on 
-renamesourcefileattribute SourceFile    
-keepattributes SourceFile,LineNumberTable

##---------------Begin: proguard configuration for Cordova  ----------
-keep class org.apache.cordova.** { *; }
-keep public class * extends org.apache.cordova.CordovaPlugin

-keep class com.google.android.gms.dynamite.DynamiteModule$DynamiteLoaderClassLoader { java.lang.ClassLoader sClassLoader; }
-keep class com.google.android.gms.dynamite.descriptors.com.google.android.gms.flags.ModuleDescriptor { int MODULE_VERSION; }
-keep class com.google.android.gms.dynamite.descriptors.com.google.android.gms.flags.ModuleDescriptor { java.lang.String MODULE_ID; }

-keep class org.apache.cordova.CordovaBridge { org.apache.cordova.PluginManager pluginManager; }
-keep class org.apache.cordova.CordovaInterfaceImpl { org.apache.cordova.PluginManager pluginManager; }
-keep class org.apache.cordova.CordovaResourceApi { org.apache.cordova.PluginManager pluginManager; }
-keep class org.apache.cordova.CordovaWebViewImpl { org.apache.cordova.PluginManager pluginManager; }
-keep class org.apache.cordova.ResumeCallback { org.apache.cordova.PluginManager pluginManager; }
-keep class org.apache.cordova.engine.SystemWebViewEngine { org.apache.cordova.PluginManager pluginManager; }

-keep class com.google.gson.internal.UnsafeAllocator { ** theUnsafe; }
-keep class me.leolin.shortcutbadger.ShortcutBadger { ** extraNotification; }
-keep class me.leolin.shortcutbadger.impl.XiaomiHomeBadger { ** messageCount; }
-keep class me.leolin.shortcutbadger.impl.XiaomiHomeBadger { ** extraNotification; }

-dontnote org.apache.harmony.xnet.provider.jsse.NativeCrypto
-dontnote sun.misc.Unsafe

-keep class com.worklight.androidgap.push.** { *; }
-keep class com.worklight.wlclient.push.** { *; }

# apache.http
-optimizations !class/merging/vertical*,!class/merging/horizontal*,!code/simplification/arithmetic,!field/*,!code/allocation/variable

-keep class net.sqlcipher.** { *; }
-dontwarn net.sqlcipher.**

-keep class org.codehaus.** { *; }
-keepattributes *Annotation*,EnclosingMethod

-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

# Remove debug logs in release build
-assumenosideeffects class android.util.Log {
    public static *** d(...);
}

# These classes contain references to external jars which are not included in the default MobileFirst project.
-dontwarn com.worklight.common.internal.WLTrusteerInternal*
-dontwarn com.worklight.jsonstore.**
-dontwarn org.codehaus.jackson.map.ext.*
-dontwarn com.worklight.androidgap.push.GCMIntentService
-dontwarn com.worklight.androidgap.plugin.WLInitializationPlugin

-dontwarn android.support.v4.**
-dontwarn android.net.SSLCertificateSocketFactory
-dontwarn android.net.http.*
##---------------End: proguard configuration for Cordova  ----------

##---------------Begin: proguard configuration for Ionic  ----------
-keep class io.ionic.keyboard.IonicKeyboard.** { *; }
-keep class com.ionicframework.cordova.webview.** { *; }
##---------------End: proguard configuration for Ionic  ----------

##---------------Begin: proguard configuration for Google Libs  ----------
-keep class com.google.** { *; }
-dontwarn com.google.common.**
-dontwarn com.google.ads.**
##---------------End: proguard configuration for Google Libs  ----------

##---------------Begin: proguard configuration for Android X  ----------
-dontwarn androidx.**
-keep class androidx.** { *; }
-keep interface androidx.** { *; }
##---------------End: proguard configuration for Android X  ----------

##---------------Begin: proguard configuration for Gson  ----------
# Gson uses generic type information stored in a class file when working with fields. Proguard
# removes such information by default, so configure it to keep all of it.
-keepattributes Signature

# For using GSON @Expose annotation
-keepattributes *Annotation*

# Gson specific classes
-dontwarn sun.misc.**
#-keep class com.google.gson.stream.** { *; }

# Application classes that will be serialized/deserialized over Gson
-keep class com.google.gson.examples.android.model.** { *; }

# Prevent proguard from stripping interface information from TypeAdapterFactory,
# JsonSerializer, JsonDeserializer instances (so they can be used in @JsonAdapter)
-keep class * implements com.google.gson.TypeAdapterFactory
-keep class * implements com.google.gson.JsonSerializer
-keep class * implements com.google.gson.JsonDeserializer
##---------------End: proguard configuration for Gson  ----------

##---------------Begin: proguard configuration for Admob  ----------
## Google AdMob specific rules ##
## https://developers.google.com/admob/android/quick-start ##
 
-keep public class com.google.ads.** {
   public *;
}
##---------------End: proguard configuration for Admob  ----------

##---------------Begin: proguard configuration for Appodeal  ----------
# Appodeal
-keep class com.appodeal.** { *; }
-dontwarn com.appodeal.**
-keep class com.appodealx.** { *; }
-dontwarn com.appodealx.**
-keepattributes EnclosingMethod, InnerClasses, Signature, JavascriptInterface

# AdMediator
-keep class com.admediator.** { *; }

# Amazon
-keep class com.amazon.** { *; }
-dontwarn com.amazon.**

# Mopub
-keep public class com.mopub.**
-keepclassmembers class com.mopub.** { public *; }
-dontwarn com.mopub.**
-keep class * extends com.mopub.mobileads.CustomEventBanner {}
-keepclassmembers class com.mopub.mobileads.CustomEventBannerAdapter {!private !public !protected *;}
-keep class * extends com.mopub.mobileads.CustomEventInterstitial {}
-keep class * extends com.mopub.nativeads.CustomEventNative {}
-keep class * extends com.mopub.mobileads.CustomEventRewardedVideo {}
-keep class * extends com.mopub.nativeads.CustomEventRewardedAd {}
-keepclassmembers class ** { @com.mopub.common.util.ReflectionTarget *; }
-dontwarn com.mopub.volley.toolbox.**
-keepclassmembers,allowshrinking,allowobfuscation class com.android.volley.NetworkDispatcher {
    void processRequest();
}
-keepclassmembers,allowshrinking,allowobfuscation class com.android.volley.CacheDispatcher {
    void processRequest();
}
-keep public class android.webkit.JavascriptInterface {}
-keepnames class * implements android.os.Parcelable {
    public static final ** CREATOR;
}

# Applovin
-keepattributes Signature,InnerClasses,Exceptions,*Annotation*
-keep public class com.applovin.sdk.AppLovinSdk { *; }
-keep public class com.applovin.sdk.AppLovin* { public protected *; }
-keep public class com.applovin.nativeAds.AppLovin* { public protected *; }
-keep public class com.applovin.adview.* { public protected *; }
-keep public class com.applovin.mediation.* { public protected *; }
-keep public class com.applovin.mediation.ads.* { public protected *; }
-keep public class com.applovin.impl.**.AppLovin* { public protected *; }
-keep public class com.applovin.impl.**.*Impl { public protected *; }
-keepclassmembers class com.applovin.sdk.AppLovinSdkSettings { private java.util.Map localSettings; }
-keep class com.applovin.mediation.adapters.** { *; }
-keep class com.applovin.mediation.adapter.** { *; }
-dontwarn com.applovin.**

# Facebook
-keepnames public class com.facebook.ads.** { public protected *; }
-keeppackagenames com.facebook.*
-dontwarn com.facebook.ads.**

# Chartboost
-keep class com.chartboost.** { *; }
-dontwarn com.chartboost.**

# Unity Ads
-keepattributes SourceFile,LineNumberTable
-keepattributes JavascriptInterface
-keep class android.webkit.JavascriptInterface { *; }
-keep class com.unity3d.ads.** { *; }
-keep class com.unity3d.services.** { *; }
-dontwarn com.google.ar.core.**

# Yandex
-keep class com.yandex.metrica.impl.** { *; }
-dontwarn com.yandex.metrica.impl.**
-keep class com.yandex.metrica.* { *; }
-dontwarn com.yandex.metrica.*
-keep class com.yandex.mobile.ads.** { *; }
-dontwarn com.yandex.mobile.ads.**
-keepattributes *Annotation*
-keep class com.android.installreferrer.api.* { *; }
-dontwarn com.android.installreferrer.api.*

# StartApp
-keep class com.startapp.** { *;}
-keep class com.truenet.** { *;}
-dontwarn com.startapp.**
-dontwarn android.webkit.JavascriptInterface
-keepattributes Exceptions, InnerClasses, Signature, Deprecated, SourceFile, LineNumberTable, *Annotation*, EnclosingMethod
-dontwarn org.jetbrains.annotations.**

# Flurry
-keep class com.flurry.** { *; }
-dontwarn com.flurry.**
-keepattributes *Annotation*,EnclosingMethod,Signature
-keepclasseswithmembers class * {
  public <init>(android.content.Context, android.util.AttributeSet, int);
}
-keep class * extends java.util.ListResourceBundle { protected Object[][] getContents(); }
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable { public static final *** NULL; }
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * { @com.google.android.gms.common.annotation.KeepName *; }
-keepnames class * implements android.os.Parcelable { public static final ** CREATOR; }

# Vungle
-keep class com.vungle.warren.** { *; }
-dontwarn com.vungle.warren.error.VungleError$ErrorCode

# Vungle/Moat SDK
-keep class com.moat.** { *; }
-dontwarn com.moat.**

# Vungle/Fetch
-keepnames class com.tonyodev.fetch.Fetch

# Vungle/Okio
-keepnames class okio.Okio
-dontwarn org.codehaus.mojo.animal_sniffer.IgnoreJRERequirement

# Vungle/Retrofit
-dontwarn okio.**
-dontwarn retrofit2.Platform$Java8
-keepnames class retrofit2.converter.gson.GsonConverterFactory
-keepnames class retrofit2.Retrofit

# Vungle/Gson
-keepattributes Signature
-keepattributes *Annotation*
-dontwarn sun.misc.**
-keep class com.google.gson.** { *; }

# Vungle/Google Android Advertising ID
-keep class com.google.android.gms.internal.** { *; }
-dontwarn com.google.android.gms.ads.identifier.**

# Vungle/okhttp3
-keep class okhttp3.logging.HttpLoggingInterceptor
-keepnames class okhttp3.HttpUrl

# MyTarget
-keep class com.my.target.** { *; }
-dontwarn com.my.target.**
-dontwarn com.my.target.nativeads.mediation.**
-dontwarn com.my.target.core.net.cookie.**
-dontwarn com.my.target.ads.mediation.MyTargetAdmobCustomEventBanner**
-dontwarn com.my.target.ads.mediation.MyTargetAdmobCustomEventInterstitial**
-dontwarn com.my.target.ads.mediation.MyTargetAdmobCustomEventRewarded**
-dontwarn com.my.target.ads.mediation.MyTargetMopubCustomEventBanner**
-dontwarn com.my.target.ads.mediation.MyTargetAdmobCustomEventInterstitial**
-dontwarn com.my.target.ads.mediation.MyTargetAdmobCustomEventRewarded**
-dontwarn com.my.target.ads.mediation.MyTargetMopubCustomEventBanner**
-keep class com.mopub.MopubCustomParamsUtils**{ *; }
-keep class com.mopub.mobileads.MyTargetMopubCustomEventInterstitial**{ *; }
-keep class com.mopub.mobileads.MyTargetMopubCustomEventRewardedVideo**{ *; }
-keep class com.mopub.nativeads.MyTargetCustomEventNative**{ *; }
-keep class com.mopub.nativeads.MyTargetStaticNativeAd**{ *; }
-dontwarn com.mopub.MopubCustomParamsUtils**
-dontwarn com.mopub.mobileads.MyTargetMopubCustomEventInterstitial**
-dontwarn com.mopub.mobileads.MyTargetMopubCustomEventRewardedVideo**
-dontwarn com.mopub.nativeads.MyTargetCustomEventNative**
-dontwarn com.mopub.nativeads.MyTargetStaticNativeAd**
-keep class com.google.android.exoplayer2.** { *; }
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient {
    com.google.android.gms.ads.identifier.AdvertisingIdClient$Info getAdvertisingIdInfo(android.content.Context);
}
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient$Info {
    java.lang.String getId();
    boolean isLimitAdTrackingEnabled();
}

# Mintegral
-keepattributes Signature
-keepattributes *Annotation*
-keep class com.mintegral.** {*; }
-keep interface com.mintegral.** {*; }
-keep class android.support.v4.** { *; }
-dontwarn com.mintegral.**
-keep class **.R$* { public static final int mintegral*; }
-keep class com.alphab.** {*; }
-keep interface com.alphab.** {*; }

# Admob
-keep class com.google.android.gms.ads.** { *; }

# Tapjoy
-keep class com.tapjoy.** { *; }
-keep class com.moat.** { *; }
-keepattributes JavascriptInterface
-keepattributes *Annotation*
-keep class * extends java.util.ListResourceBundle { protected Object[][] getContents(); }
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable { public static final *** NULL; }
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * { @com.google.android.gms.common.annotation.KeepName *; }
-keepnames class * implements android.os.Parcelable { public static final ** CREATOR; }
-keep class com.google.android.gms.ads.identifier.** { *; }
-dontwarn com.tapjoy.**

# IronSource
-keepclassmembers class com.ironsource.sdk.controller.IronSourceWebView$JSInterface { public *; }
-keepclassmembers class * implements android.os.Parcelable { public static final android.os.Parcelable$Creator *; }
-keep public class com.google.android.gms.ads.** { public *; }
-keep class com.ironsource.adapters.** { *; }
-keepnames class com.ironsource.mediationsdk.IronSource
-dontwarn com.ironsource.mediationsdk.**
-dontwarn com.ironsource.adapters.**
-dontwarn com.moat.**
-keep class com.moat.** { public protected private *; }

# AdColonyV3
-keepclassmembers class * { @android.webkit.JavascriptInterface <methods>; }
-keep class com.adcolony.** { *; }
-dontwarn com.adcolony.**
-dontwarn android.app.Activity

# Inmobi
-keepattributes SourceFile,LineNumberTable
-keep class com.inmobi.** { *; }
-dontwarn com.inmobi.**
-keep public class com.google.android.gms.**
-dontwarn com.google.android.gms.**
-dontwarn com.squareup.picasso.**
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient{public *;}
-keep class com.google.android.gms.ads.identifier.AdvertisingIdClient$Info{public *;}

# Picasso
-keep class com.squareup.picasso.** {*;}
-dontwarn com.squareup.picasso.**
-dontwarn com.squareup.okhttp.**

# Moat
-keep class com.moat.** {*;}
-dontwarn com.moat.**

# AVID
-keep class com.integralads.avid.library.** {*;}

# Ogury
-dontwarn io.presage.**

# Google
-keep class com.google.android.gms.common.GooglePlayServicesUtil { *; }
-keep class com.google.android.gms.common.GoogleApiAvailability { *; }
-keep class com.google.android.gms.ads.identifier.** { *; }
-dontwarn com.google.android.gms.**

# Legacy
-keep class org.apache.http.** { *; }
-dontwarn org.apache.http.**
-dontwarn android.net.http.**

# Google Play Services library
-keep class * extends java.util.ListResourceBundle {
  protected Object[][] getContents();
}
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable {
  public static final *** NULL;
}
-keepnames class * implements android.os.Parcelable
-keepclassmembers class * implements android.os.Parcelable {
  public static final *** CREATOR;
}
-keep @interface android.support.annotation.Keep
-keep @android.support.annotation.Keep class *
-keepclasseswithmembers class * {
  @android.support.annotation.Keep <fields>;
}
-keepclasseswithmembers class * {
  @android.support.annotation.Keep <methods>;
}
-keep @interface com.google.android.gms.common.annotation.KeepName
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * {
  @com.google.android.gms.common.annotation.KeepName *;
}
-keep @interface com.google.android.gms.common.util.DynamiteApi
-keep public @com.google.android.gms.common.util.DynamiteApi class * {
  public <fields>;
  public <methods>;
}
-keep class com.google.android.gms.common.GooglePlayServicesNotAvailableException {*;}
-keep class com.google.android.gms.common.GooglePlayServicesRepairableException {*;}

# Google Play Services library 9.0.0 only
-dontwarn android.security.NetworkSecurityPolicy
-keep public @com.google.android.gms.common.util.DynamiteApi class * { *; }

# support-v4
-keep class android.support.v4.app.Fragment { *; }
-keep class android.support.v4.app.FragmentActivity { *; }
-keep class android.support.v4.app.FragmentManager { *; }
-keep class android.support.v4.app.FragmentTransaction { *; }
-keep class android.support.v4.content.ContextCompat { *; }
-keep class android.support.v4.content.LocalBroadcastManager { *; }
-keep class android.support.v4.util.LruCache { *; }
-keep class android.support.v4.view.PagerAdapter { *; }
-keep class android.support.v4.view.ViewPager { *; }
-keep class android.support.v4.content.ContextCompat { *; }

# support-v7-widget
-keep class android.support.v7.widget.** { *; }

# MultiDex
-keepnames class android.support.multidex.MultiDex

# AndroidX
-keep @interface androidx.annotation.Keep
-keep @androidx.annotation.Keep class *
-keepclasseswithmembers class * {
  @androidx.annotation.Keep <fields>;
}
-keepclasseswithmembers class * {
  @androidx.annotation.Keep <methods>;
}
-keep class androidx.fragment.app.Fragment { *; }
-keep class androidx.fragment.app.FragmentActivity{ *; }
-keep class androidx.fragment.app.FragmentManager { *; }
-keep class androidx.fragment.app.FragmentTransaction { *; }
-keep class androidx.core.content.ContextCompat { *; }
-keep class androidx.localbroadcastmanager.content.LocalBroadcastManager { *; }
-keep class androidx.collection.LruCache { *; }
-keep class androidx.viewpager.widget.PagerAdapter { *; }
-keep class androidx.viewpager.widget.ViewPager { *; }
-keep class androidx.core.content.ContextCompat { *; }
-keep class androidx.appcompat.widget.** { *; }

# AndroidX Multidex
-keepnames class androidx.multidex.MultiDex

##---------------End: proguard configuration for Appodeal  ----------

  ```
</details>


## Cordova Integration

### Ad Types

+ Appodeal.INTERSTITIAL
+ Appodeal.BANNER
+ Appodeal.REWARDED_VIDEO
+ Appodeal.NON_SKIPPABLE_VIDEO

Ad types can be combined using "|" operator. For example Appodeal.INTERSTITIAL | Appodeal.NON_SKIPPABLE_VIDEO.

### SDK Initialization

To initialize SDK for INTERSTITIAL ad type, call the following code:

```javascript
var appKey = "e7e04e54ae0a8d28cff2f7e7e7d094e78b2a09743be2cc4a";
Appodeal.disableLocationPermissionCheck();
Appodeal.initialize(appKey, Appodeal.INTERSTITIAL);
```

+ To initialize only interstitials use `Appodeal.initialize(appKey, Appodeal.INTERSTITIAL)`
+ To initialize only rewarded video use `Appodeal.initialize(appKey, Appodeal.REWARDED_VIDEO)`
+ To initialize only non-skippable video use `Appodeal.initialize(appKey, Appodeal.NON_SKIPPABLE_VIDEO)`
+ To initialize interstitials and non-skippable videos use `Appodeal.initialize(appKey, Appodeal.INTERSTITIAL | Appodeal.NON_SKIPPABLE_VIDEO)`
+ To initialize only banners use `Appodeal.initialize(appKey, Appodeal.BANNER)`

### Display Ad

To display ad you need to call the following code:

```javascript
Appodeal.show(adTypes);
```

+ To display interstitial use `Appodeal.show(Appodeal.INTERSTITIAL)`
+ To display rewarded video use `Appodeal.show(Appodeal.REWARDED_VIDEO)`
+ To display non-skippable video use `Appodeal.show(Appodeal.NON_SKIPPABLE_VIDEO)`
+ To display interstitial or non-skippable video use `Appodeal.show(Appodeal.INTERSTITIAL | Appodeal.NON_SKIPPABLE_VIDEO)`
+ To display banner at the bottom of the screen use `Appodeal.show(Appodeal.BANNER_BOTTOM)`
+ To display banner at the top of the screen use `Appodeal.show(Appodeal.BANNER_TOP)`

Also it can be used in this way, to get boolean value if ad was successfully shown:

```javascript
Appodeal.show(adTypes, function(result) {
   // result is a boolean value, that is indicates whether show call was passed to appropriate SDK 
});
```

### Checking if interstitial is loaded

To check if ad type was loaded, use following code:

```javascript
Appodeal.isLoaded(adTypes, function(result){
    // result returns bool value
});
```

### Hide Banner Ad

To hide banner you need to call the following code:

```javascript
Appodeal.hide(Appodeal.BANNER);
```

### Callbacks integration

To set Interstitial callbacks, use following code:

```javascript
Appodeal.setInterstitialCallbacks( function(container) {
       if (container.event == 'onLoaded') {
            console.log("Appodeal. Interstitial. " + container.event + ", isPrecache: " + container.isPrecache  );
            // your code
       } else if (container.event == 'onFailedToLoad') {
            // your code
       } else if (container.event == 'onShown') {
            // your code
       } else if (container.event == 'onClick') {
            // your code
       } else if (container.event == 'onClosed') {
            // your code
       }
});
```

To set Banner callbacks, use following code:

```javascript
Appodeal.setBannerCallbacks( function(container) {
       if (container.event == 'onLoaded') {
            console.log("Appodeal. Banner. " + container.event + ", height: " + container.height + ", isPrecache: " + container.isPrecache);
            // your code
       } else if (container.event == 'onFailedToLoad') {
            // your code
       } else if (container.event == 'onShown') {
            // your code
       } else if (container.event == 'onClick') {
            // your code
       }
});
```

To set Rewarded Video callbacks, use following code:

```javascript
Appodeal.setRewardedVideoCallbacks( function(container) {
       if (container.event == 'onLoaded') {
            // your code
       } else if (container.event == 'onFailedToLoad') {
            // your code
       } else if (container.event == 'onShown') {
            // your code
       } else if (container.event == 'onFinished') {
            // container also returns "name" and "amount" variables with reward amount and currency name you have set for your application
            console.log( "Appodeal. Rewarded. " + container.event + ", amount: " + container.amount + ", name: " + container.name);
            // your code
       } else if (container.event == 'onClosed') {
            // container also returns "finished" variable with boolean value for indicating if video was finished
            console.log("Appodeal. Rewarded. " + container.event + ", finished: " + container.finished);
            // your code
       }
});
```

To set Non Skippable Video callbacks, use following code:

```javascript
Appodeal.setNonSkippableVideoCallbacks( function(container) {
       if (container.event == 'onLoaded') {
            // your code
       } else if (container.event == 'onFailedToLoad') {
            // your code
       } else if (container.event == 'onShown') {
            // your code
       } else if (container.event == 'onFinished') {
            // your code
       } else if (container.event == 'onClosed') {
            // container also returns "finished" variable with boolean value for indicating if video was finished
            console.log("Appodeal. Non Skippable Video. " + container.event + ", finished: " + container.finished);
            // your code
       }
});
```

### Advanced Features

#### Getting reward data for placement

To get placement reward data before video is shown use:

```javascript
Appodeal.getRewardParameters( function(result) {
   console.log("Appodeal Reward Amount:" + result.amount);
   console.log("Appodeal Reward Currency:" + result.currency);
});
```

#### Enabling 728*90 banners

To enable 728*90 banner use the following method:

```javascript
Appodeal.set728x90Banners(true);
```

#### Disabling banner refresh animation

To disable banner refresh animation use:

```javascript
Appodeal.setBannerAnimation(false);
```

#### Disabling smart banners

```javascript
Appodeal.setSmartBanners(false);
```

#### Enabling test mode

```javascript
Appodeal.setTesting(true);
```

In test mode test ads will be shown and debug data will be written to log.

#### Enabling logging

```javascript
Appodeal.setLogLevel(Appodeal.LogLevel.debug);
```

Available parameters: Appodeal.LogLevel.none, Appodeal.LogLevel.debug, Appodeal.LogLevel.verbose.

#### Checking if loaded ad is precache

```javascript
Appodeal.isPrecache(adTypes, function(result){
  // result is a boolean value, that equals true if ad is precache
})
```

Currently supported only for interstitials and banners

To check if loaded interstitial is precache: use `Appodeal.isPrecache(Appodeal.INTERSTITIAL);`

To check if loaded banner is precache: use `Appodeal.isPrecache(Appodeal.BANNER);`

#### Manual ad caching

```javascript
Appodeal.cache(adTypes);
```

+ You should disable automatic caching before SDK initialization using `setAutoCache(adTypes, false)`.
+ To cache interstitial use `Appodeal.cache(Appodeal.INTERSTITIAL)`
+ To cache rewarded video use `Appodeal.cache(Appodeal.REWARDED_VIDEO)`
+ To cache interstitial and non-skippable video use `Appodeal.cache(Appodeal.INTERSTITIAL | Appodeal.NON_SKIPPABLE_VIDEO)`
+ To cache banner use `Appodeal.cache(Appodeal.BANNER)`

#### Enabling or disabling automatic caching

```javascript
Appodeal.setAutoCache(adTypes, false);
```

+ Should be used before SDK initialization
+ To disable automatic caching for interstitials use `Appodeal.setAutoCache(Appodeal.INTERSTITIAL, false)`
+ To disable automatic caching for rewarded videos use `Appodeal.setAutoCache(Appodeal.REWARDED_VIDEO, false)`
+ To disable automatic caching for banners use `Appodeal.setAutoCache(Appodeal.BANNER, false)`

#### Triggering onLoaded callback on precache

```javascript
Appodeal.setTriggerOnLoadedOnPrecache(adTypes, true);
```

+ Currently supported only for interstitials
+ `setOnLoadedTriggerBoth(Appodeal.INTERSTITIAL, false)` - onInterstitialLoaded will trigger only when normal ad was loaded (default)..
+ `setOnLoadedTriggerBoth(Appodeal.INTERSTITIAL, true)` - onInterstitialLoaded will trigger twice, both when precache and normal ad were loaded..
+ Should be used before SDK initialization

#### Disabling data collection for kids apps

```javascript
Appodeal.setChildDirectedTreatment(true);
```

#### Disabling networks

```javascript
Appodeal.disableNetwork(network);
```

Available parameters: "adcolony", "admob", "amazon_ads", "applovin", "appnext", "avocarrot", "chartboost", "facebook", "flurry", "inmobi", "inner-active", "ironsource", "mailru", "mmedia", "mopub", "ogury", "openx", "pubnative", "smaato", "startapp", "tapjoy", "unity_ads", "vungle", "yandex"

Should be used before SDK initialization

#### Disabling location permission check

To disable toast messages ACCESS_COARSE_LOCATION permission is missing, use the following method:

```javascript
Appodeal.disableLocationPermissionCheck();
```

Should be used before SDK initialization.

#### Disabling write external storage permission check

To disable toast messages WRITE_EXTERNAL_STORAGE permission is missing use the following method:

```javascript
Appodeal.disableWriteExternalStoragePermissionCheck();
```

Disables all ad networks that need this permission may lead to low video fillrates.

Should be used before SDK initialization.

#### Tracking in-app purchase

```javascript
Appodeal.trackInAppPurchase(this, 5, "USD");
```

#### Testing third-party networks adapters integration

To show test screen for testing adapters integration call:

```javascript
Appodeal.showTestScreen();
```

#### Muting videos if call volume is muted

```javascript
Appodeal.muteVideosIfCallsMuted(true);
```

### Setting User Data

#### Set the age of the user

```javascript
Appodeal.setAge(25);
```

#### Specify gender of the user

```javascript
Appodeal.setGender(UserSettings.Gender.FEMALE);
```

Possible values: Appodeal.Gender.FEMALE, Appodeal.Gender.MALE, Appodeal.Gender.OTHER.

## Changelog

3.0.5 (21.04.2018)

+ Appodeal iOS SDK updated to 2.1.10
+ Appodeal Android SDK updated to 2.1.11
+ Ogury for Android added as required library
