# Initialize Pangle SDK

You must initialize Pangle SDK before loading Pangle ads. This needs to be done only once, ideally at App launch stage. 



Initialize Pangle SDK synchronously, need to pass `Context`, `TTAdConfig` in the method `TTAdSdk.init()`. For example, in `Application #onCreate`.

**Note: SDK Initialization method should be called on the main thread**

```Java
public class DemoApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        TTAdSdk.init(context,
               new TTAdConfig.Builder()
                   .appId("Your_App_Id")
                   .supportMultiProcess(false) // Whether to support multi-process
                   .coppa(0) // Fields to indicate SDK whether the user is a child or an adult ，0:adult ，1:child
                   .setGDPR(0)//Fields to indicate SDK whether the user grants consent for personalized ads, the value of GDPR : 0 User has granted the consent for personalized ads, SDK will return personalized ads; 1: User doesn't grant consent for personalized ads, SDK will only return non-personalized ads.
                   .build());
    }
}
```

Initialize Pangle SDK asynchronously is supported since the v3.5.0.0 SDK, need to pass `Context`, `TTAdConfig` and `InitCallback` instance in the method `TTAdSdk.init()`. `InitCallback` is used to get the notification. Then publishers **must** make sure the sdk is initialized successfully before trying to load Pangle Ads.

**Note: SDK Initialization method should be called on the main thread**

```Java

    public class DemoApplication extends Application {
        @Override
        public void onCreate() {
            super.onCreate();

            TTAdSdk.init(context, buildConfig(), new TTAdSdk.InitCallback() {
                @Override
                public void success() {
                    //load pangle ads after this method is triggered.
                   
                }

                @Override
                public void fail(int code, String msg) {
                    
                }
            });
        }
        
            private TTAdConfig buildConfig() {
              return new TTAdConfig.Builder()
                    .appId("Your_App_Id")
                    .supportMultiProcess(false) // Whether to support multi-process
                    .coppa(0) // Fields to indicate SDK whether the user is a child or an adult ，0:adult ，1:child
                    .setGDPR(0)//Fields to indicate SDK whether the user grants consent for personalized ads, the value of GDPR : 0 User has granted the consent for personalized ads, SDK will return personalized ads; 1: User doesn't grant consent for personalized ads, SDK will only return non-personalized ads.
                    .build());
        }
    }

    
```

Publisher also could check the initialization status with the method `isInitSuccess()`
```Java
    TTAdSdk.isInitSuccess();
```



**Warning: Ads may be preloaded by the Pangle Ads SDK or mediation partner SDKs after initial. If you need to obtain consent from users in the European Economic Area (EEA) or users under age, please ensure you do so before initializing the Pangle Ads SDK.**


If you wanna change the value of GDPR/COPPA after initializing Pangle SDK, you can call following method, as shown below:
```Java
TTAdSdk.setCoppa(int CoppaValue);

TTAdSdk.setGdpr(int GDPRValue);
```

