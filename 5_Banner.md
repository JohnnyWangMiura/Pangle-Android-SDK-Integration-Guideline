# Banner Ads

## Introducation
Banner ads occupy a spot within an app's layout, either at the top or bottom of the device screen. 

**Note: Pangle only supports 300x250 and 320x50 for the traffic outside of Chinese Mainland at this time.**

## Precondition
Create an app and banner ad placement on Pangle platform
 - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Banner Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)
    
    
## Banner Ads Implementation
The main steps to integrate banner ads are:

- Load an banner ad.
- Register ad event callbacks. 
- Handle dislike button(optional).
- Render banner ad.
- Display the ad.


### Load an Banner Ad

Banner ads are requested by `loadBannerExpressAd` method on the `TTAdNative` class. The `loadBannerExpressAd` method requires a `AdSlot` Object and a `NativeExpressAdListener`. `AdSlot` is the information requested by the user, and `NativeExpressAdListener`is the callback that indicates if the loading succeeds or fails. 

Once the loading succeeds, you need to render the banner ad by the `render()` method on the `TTNativeExpressAd` class , if `onRenderSuccess` method is executed, then could call `addView()` to add banner view to your app layout.


#### Create the TTAdNative Object
`TTAdNative` is a Ad Loading Manager. It is recommended to be the member variable of the Activity.

```Java
TTAdNative mTTAdNative = TTAdSdk.getAdManager().createAdNative(this);
```

#### Create the AdSlot Object
When building the `AdSlot` Object, the ad placement id and size of the banner are required.

```Java
AdSlot adSlot = new AdSlot.Builder()
        .setCodeId("your ad placement id") 
        .setExpressViewAcceptedSize("Width", "Height") 
        .build();
```

#### Load a Banner Ad and Register NativeExpressAdListener Callback
Calling the `loadBannerExpressAd()` method on the `TTAdNative` class to load a Banner Ad. The `AdSlot` and `NativeExpressAdListener` Object are required. The Banner Object is returned as a parameter in the `onNativeExpressAdLoad` callback.

**Note: The Banner class is named as `TTNativeExpressAd`**

```Java
mTTAdNative.loadBannerExpressAd(adSlot, new TTAdNative.NativeExpressAdListener() {
      @Override
      public void onError(int code, String message) {
               
      }

      @Override
      public void onNativeExpressAdLoad(List<TTNativeExpressAd> BannerAds) {
               
      }
});
```

| NativeExpressAdListener callback | Description |
|---------------------|------------------------------------------------------------|
| onError             | This method is invoked when an ad fails to load. It includes an error parameter of type Error that indicates what type of failure occurred. For more information, refer to the ErrorCode section                                                 |
| onNativeExpressAdLoad | This method is executed when an ad material is loaded successfully.        |


### Register Ad Event Callbacks

Once the banner ad is loaded, you need to register ad event callbacks. Passing a `ExpressAdInteractionListener` object to the setter on your ad. The `ExpressAdInteractionListener` provides some ad event methods to get notifications of banner ads.

```Java
mTTAdNative.loadBannerExpressAd(adSlot, new TTAdNative.NativeExpressAdListener() {
      @Override
      public void onError(int code, String message) {
               
      }

      @Override
      public void onNativeExpressAdLoad(List<TTNativeExpressAd> BannerAds) {
          
          BannerAds.setExpressInteractionListener(new TTNativeExpressAd.ExpressAdInteractionListener() {
            @Override
            public void onAdClicked(View BannerView, int type) {
       
            }

            @Override
            public void onAdShow(View BannerView, int type) {
       
            }

            @Override
            public void onRenderFail(View BannerView, String msg, int code) {

            }

            @Override
            public void onRenderSuccess(View BannerView, float width, float height) {
       
            }
        });
          
      }
});

```

#### ExpressAdInteractionListener Callback Instruction
| ExpressAdInteractionListener callback | Description |
|---------------------|------------------------------------------------------------|
| onAdClicked         | This method is invoked when the ad is clicked by the user.                                  |
| onAdShow            | This method is invoked when the ad is displayed, covering the device's screen.              |
| onRenderFail        | This method is invoked when the ad is rendered failed.                                      |
| onRenderSuccess     | This method is invoked when the ad is rendered successfully                                 |

**Warning: the Banner Ad could be shown after the onRenderSuccess method is invoked.**


### Handle Dislike Button(optional)
Once the `ExpressAdInteractionListener` callback is registered, the next step is to handle dislike button.
Pangle provides a dislike button for banner ad which could make the user have a chance to remove the banner layout and select the reason why they don't like it.

The `setDislikeCallback` method requires `Acitivty` and `DislikeInteractionCallback` instances. The `Activity` instance should be the activity from which the banner ad is presented.

```Java
mTTAdNative.loadBannerExpressAd(adSlot, new TTAdNative.NativeExpressAdListener() {
      @Override
      public void onError(int code, String message) {
               
      }

      @Override
      public void onNativeExpressAdLoad(List<TTNativeExpressAd> BannerAds) {
          
          BannerAds.setDislikeCallback(BannerExpressActivity.this, new TTAdDislike.DislikeInteractionCallback() {
            @Override
            public void onSelected(int position, String value) {
                //Remove Banner Ads
                mExampleContainer.removeAllViews();
            }

            @Override
            public void onCancel() {
                
            }

            @Override
            public void onRefuse() {

            }

        });
          
      }
});


```

### Render Banner Ad

Before the ad is shown, using the `render()` method to render the ad. This step is required before displaying the ad.

```Java

mTTAdNative.loadBannerExpressAd(adSlot, new TTAdNative.NativeExpressAdListener() {
      @Override
      public void onError(int code, String message) {
               
      }

      @Override
      public void onNativeExpressAdLoad(List<TTNativeExpressAd> BannerAds) {
               
          BannerAds.render();
               
      }     

```



### Display the Ad
If `onRenderSuccess` method is executed, calling `addView()` to add banner view to your app layout.

```Java
  @Override
  public void onRenderSuccess(View BannerView, float width, float height) {
       mExampleContainer.addView(BannerView);
  }

```


## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.






