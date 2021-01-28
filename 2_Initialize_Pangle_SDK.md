# Initialize Pangle SDK

You must initialize Pangle SDK before loading Pangle ads. This needs to be done only once, ideally at App launch stage. For example, in `Application #onCreate`.

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


**Warning: Ads may be preloaded by the Pangle Ads SDK or mediation partner SDKs after initial. If you need to obtain consent from users in the European Economic Area (EEA) or users under age, please ensure you do so before initializing the Pangle Ads SDK.**


If you wanna change the value of GDPR/COPPA after initializing Pangle SDK, you can call following method, as shown below:
```Java
TTAdSdk.setCoppa(int CoppaValue);

TTAdSdk.setGdpr(int GDPRValue);
```

