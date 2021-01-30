# Fullscreen Video

## Introducation
Fullscreen Video ads, also known as the Interstitial Video Ads, are full-screen video ads with 15-30 seconds long that cover the interface of an app until closed by the user. It can be skipped after 5 seconds. They're typically displayed at natural transition points in the flow of an app, such as between activities or during the pause between levels in a game. When an app shows an interstitial ad, the user has the choice to either tap on the ad and continue to its destination or close it and return to the app.

## Precondition
Create an app and fullscreen video ad placement on Pangle platform

   - Create an application: [Application] -> [App] -> [Add App]
    
      - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Interstitial Video Ads]
    
      - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)
    
**Note: 'Fullscreen Video Ads' mentioned here is named as 'Interstitial Video Ads' on Pangle's platform. **

### Parameter Setting：
- `Orienation`: Select the orientation of the video.
    
    
## FullscreenVideo Implementation
The main steps to integrate fullscreen video ads are:

- Load an fullscreen video ad.
- Register ad event callbacks.
- Display the ad.
- Preload a fullscreen ad.

### Load an Fullscreen Ad
Loading an ad is accomplished using the `loadFullScreenVideoAd` method on the `TTAdNative` class. The `loadFullScreenVideoAd` method requires a `AdSlot` Object and a `FullScreenVideoAdListener` to be notified when ad loading succeeds or fails. The loaded `TTFullScreenVideoAd` object is provided as a parameter in the `onFullScreenVideoAdLoad(TTFullScreenVideoAd ttFullScreenVideoAd)` callback.

#### Create the TTAdNative Object
`TTAdNative` is a Ad Loading Manager. It is recommended to be the member variable of the Activity. You always need to create a `TTAdNative` Object before loading ads.

```Java
TTAdNative mTTAdNative = TTAdSdk.getAdManager().createAdNative(this);
```

#### Create the AdSlot Object
In this section, you need to pass ad placement id in the method `setCodeId()`.

```Java
AdSlot adSlot = new AdSlot.Builder()
        .setCodeId("Your_Ad_Placement_Id")
        .setOrientation(orientation) //Expected Orientation：TTAdConstant.HORIZONTAL or TTAdConstant.VERTICAL
        .build();
```

#### Load a Fullscreen Ad and Register FullScreenVideoAdListener Callback
Calling the `loadFullScreenVideoAd()` method on the `TTAdNative` class to load a Fullscreen Video Ad.

```Java
mTTAdNative.loadFullScreenVideoAd(adSlot, new TTAdNative.FullScreenVideoAdListener() {
    @Override
    public void onError(int code, String message) {
       
    }

    @Override
    public void onFullScreenVideoAdLoad(TTFullScreenVideoAd ttFullScreenVideoAd) {
       
    }
    @Override
    public void onFullScreenVideoCached() {
   
   }
);
```

| FullScreenVideoAdListener callback | - |
|---------------------|------------------------------------------------------------|
| onError             | This method is invoked when an ad fails to load. It includes an error parameter of type Error that indicates what type of failure occurred. For more information, refer to the ErrorCode section                                                 |
| onFullScreenVideoCached | This method is executed when the video file has finished loading.               |
| onFullScreenVideoAdLoad | This method is executed when an ad material is loaded successfully.             |


### Register Ad Event Callbacks

Before the ad to be shown, you need to register ad event callbacks. Passing a `FullScreenVideoAdInteractionListener` object to the setter on your ad. The `FullScreenVideoAdInteractionListener` provides some Ad Event methods to get notifications of rewarded ad events. Each of the methods in `FullScreenVideoAdInteractionListener` corresponds to an event in the lifecycle of a fullscreen video ad.

```Java
ttFullScreenVideoAd.setFullScreenVideoAdInteractionListener(new TTFullScreenVideoAd.FullScreenVideoAdInteractionListener() {

    @Override
    public void onAdShow() {
       
    }

    @Override
    public void onAdVideoBarClick() {

   }

    @Override
    public void onAdClose() {
       
    }
    
    @Override
    public void onVideoError() {
       
    }

    @Override
    public void onVideoComplete() {
       
    }

    @Override
    public void onSkippedVideo() {

    }
});
```

#### FullScreenVideoAdInteractionListener Callback Instruction
| FullScreenVideoAdInteractionListener callback | - |
|---------------------|------------------------------------------------------------|
| onAdVideoBarClick   | This method is invoked when the ad is clicked by the user.                                      |
| onAdClose           | This method is invoked when the ad is closed due to the user tapping on the close icon on the endcard.               |
| onVideoComplete     | This method is invoked when the video has finished playing.                                    |
| onVideoError        | This method is invoked when an ad fails to display.   |
| onSkippedVideo      | The method is invoked when the user skipped the video.                                    |
| onAdShow            | This method is invoked when the ad is displayed, covering the device's screen.                                      |



### Display the Ad
Fullscreen ads should be displayed during natural pauses in the flow of an app.

To show a fullscreen video ad, we recommend to use the `onFullScreenVideoCached()` method to verify if it's finished loading, then call `showFullScreenVideoAd()` on `TTFullScreenVideoAd`. The `showFullScreenVideoAd()` method requires `Activity` instance. The Activity instance should be the activity from which the fullscreen video ad is presented.

```Java
if (ttFullScreenVideoAd != null) {

   ttFullScreenVideoAd.showFullScreenVideoAd(FullScreenVideoActivity.this);
       
 }
```

### Preload a Fullscreen Ad

A best practice is to load another fullscreen video ad in the `onAdClose()` method on `FullScreenVideoAdInteractionListener` so that the next fullscreen video ad starts loading as soon as the previous one is closed:

```Java
@Override
public void onAdClose() {
    //Start loading the next fullscreen video ad here.
}
```

## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.












