# Rewarded Video

## Introducation
Rewarded video is a full-screen video ad. Users can get rewards after watching the video. At present, the forms of Pangle rewarded video ads can:

- Display a Endcard after the video.
- Display a playable ad after the video.
- Display a playable ad directly.

## Precondition
 Create an app and reward video ad placement on Pangle platform

  - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Rewarded Video Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)
    
### Parameter Setting：
- `Orienation`: Select the orientation of the video.
- `Reward amount`: Enter the number of reward items the user will receive. Must be a whole number.
- `Reward item`: Enter the name of reward item the user will receive. Examples: Coins, extra lives
- `Reward deliver setting`：Validate each completed rewarded video ad view by the third-party server and ensure you're only rewarding users who have actually finished watching the video in your app.
    
    
## Rewarded Video Implementation
The main steps to integrate rewarded video ads are:

- Load an Rewarded ad.
- Register ad event callbacks.
- Display the ad.
- Preload a Rewarded Ad.

### Load an Rewarded Ad
Loading an ad is accomplished using the `loadRewardVideoAd()` method on the `TTAdNative` class. The `loadRewardVideoAd()` method requires a `AdSlot` Object and a `RewardVideoAdListener` to be notified when ad loading succeeds or fails. The loaded `TTRewardVideoAd` object is provided as a parameter in the `onRewardVideoAdLoad(TTRewardVideoAd ad)` callback.

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

#### Load a Rewarded Video and Register RewardVideoAdListener Callback
Calling the `loadRewardVideoAd()` method on the `TTAdNative` class to load a Rewarded Video Ad.
```Java
mTTAdNative.loadRewardVideoAd(adSlot, new TTAdNative.RewardVideoAdListener() {
    @Override
    public void onError(int code, String message) {
      
    }
    
    @Override
    public void onRewardVideoCached() {
       
    }

    @Override
    public void onRewardVideoAdLoad(TTRewardVideoAd ttRewardVideoAd) {
       
    }
});
```

| RewardVideoAdListener callback | - |
|---------------------|------------------------------------------------------------|
| onError             | This method is invoked when an ad fails to load. It includes an error parameter of type Error that indicates what type of failure occurred. For more information, refer to the ErrorCode section                                                 |
| onRewardVideoCached | This method is executed when the video file has finished loading               |
| onRewardVideoAdLoad | This method is executed when an ad material is loaded successfully.             |


### Register Ad Event Callbacks

Before the ad to be shown, you need to register ad event callbacks. Passing a `RewardAdInteractionListener` object to the setter on your ad. The `RewardAdInteractionListener` provides some Ad Event methods to get notifications of rewarded ad events. Each of the methods in `RewardAdInteractionListener` corresponds to an event in the lifecycle of a rewarded video ad.

```Java
ttRewardVideoAd.setRewardAdInteractionListener(new TTRewardVideoAd.RewardAdInteractionListener() {
    @Override
    public void onAdVideoBarClick() {
       
    }

    @Override
    public void onAdClose() {
       
    }
   
    @Override
    public void onVideoComplete() {
       
    }

    @Override
    public void onVideoError() {

    }

    @Override
    public void onRewardVerify(boolean rewardVerify, int rewardAmount, String rewardName) {

    }

    @Override
    public void onSkippedVideo() {

    }

    @Override
    public void onAdShow() {

    }
});
```

#### RewardAdInteractionListener Callback Instruction
| RewardAdInteractionListener callback | - |
|---------------------|------------------------------------------------------------|
| onAdVideoBarClick   | This method is invoked when the rewarded ad is clicked by the user.                                      |
| onAdClose           | This method is invoked when the rewarded ad is closed due to the user tapping on the close icon on the endcard.               |
| onVideoComplete     | This method is invoked when the video has finished playing.                                    |
| onVideoError        | This method is invoked when an ad fails to display.                                            |
| onRewardVerify      | The method is invoked when the user should be rewarded.  Parameter: `rewardVerify`: sdk will return true if the user meet the our requirements, for example, the video has finished playing. `rewardName`: The name of reward item you entered on Pangle platform. `rewardAmout`: The number of reward items you entered on Pangle platform.                         |
| onSkippedVideo      | The method is invoked when the user skipped the video.                                    |
| onAdShow            | This method is invoked when the ad is displayed, covering the device's screen.                                      |



### Display the Ad
Before displaying a rewarded video ad to users, you must present the user with an choice to view rewarded ad in exchange for a reward. Rewarded ads must always be an opt-in experience.

To show a rewarded video ad, we recommend to use the `onRewardVideoCached()` method to verify if it's finished loading, then call `showRewardVideoAd()` on `TTRewardVideoAd`. The `showRewardVideoAd()` method requires `Activity` instance. The Activity instance should be the activity from which the rewarded ad is presented.

```Java
if (ttRewardVideoAd != null) {

   ttRewardVideoAd.showRewardVideoAd(RewardVideoActivity.this);
       
 }
```

### Preload a Rewarded Ad
`TTRewardVideoAd` is a one-time-use object. This means that once a rewarded ad is shown, the object can't be used to load another ad. To request another rewarded ad, you'll need to create a new `TTRewardVideoAd` object.

A best practice is to load another rewarded video ad in the `onAdClose()` method on `RewardAdInteractionListener` so that the next rewarded video ad starts loading as soon as the previous one is closed:
```Java
@Override
public void onAdClose() {
    //Start loading the next rewarded video ad here.
}
```



## Server-Side Reward Verification Callback
**Note：**
The Server-side verification is not necessary. Server-side verification acts as an additional layer of validation for rewarded ad views in your app. It’s performed in addition to the standard client-side callback. You can use server-side verification to validate each completed rewarded video ad view and ensure you're only rewarding users who have actually finished watching the video in your app.


The server-side verification callback will append query parameters to your postback url describing the rewarded video interaction:
`user_id=%s&trans_id=%s&reward_name=%s&reward_amount=%d&extra=%s&sign=%s`



| Field Name    | Field Definition               | Field Type | Remarks                                                                 |
|---------------|--------------------------------|------------|-------------------------------------------------------------------------|
| sign          | signSignature of   the request | string     | Signature of the   request guarantees security                          |
| user_id       | User id                        | string     | App's unique   user identifier, it depends on what you entered via SDK. |
| trans_id      | Transaction id                 | string     | Unique   transaction ID for completing viewing the ad                   |
| reward_amount | number of   rewards            | int        | Pangle platform   configuration                                         |
| reward_name   | Name of rewards                | string     | Pangle platform   configuration                                         |
| extra         | Extra                          | string     | Call SDK input   and pass-through，leave it   empty if not needed.      |

### Signature Generation：
- `appSecurityKey`: The key you get for adding rewarded video ad code bit on the Pangle Network
- `transId`: transaction id
- `sign`: sha256(appSecurityKey:transId)

Python sample:
```python
import hashlib
if __name__ == "__main__":
    trans_id = "6FEB23ACB0374985A2A52D282EDD5361u6643"
    app_security_key = "7ca31ab0a59d69a42dd8abc7cf2d8fbd"
    check_sign_raw = "%s:%s" % (app_security_key, trans_id)
    sign = hashlib.sha256(check_sign_raw).hexdigest()

```

### Return：
Returns json data with the following fields:

| Field Definition | Field Name        | Field Type | Remarks                                        |
|------------------|-------------------|------------|------------------------------------------------|
| isValid          | Validation result | BOOL       | determines the result, whether to award or not |

Instance:
```json
{
   "isValid": true
}
```






