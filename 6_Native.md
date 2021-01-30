# Native Ads

## Introducation
Native ads allow you to customize the look and feel of the ads that appear in your app. You design the ads: how they look, where they’re placed, and how they work within your existing app design. This differs from other ad formats, which don't allow you to customize the appearance of the ad.

**Note: Pangle supports 4 forms outside the chinese mainland: Large image with 1.91:1 ratio、1280*720 video、square image, square video.**


## Precondition
Create an app and native ad placement on Pangle platform
 - Create an application: [Application] -> [App] -> [Add App]
    - Reference：[How do I create a new App?](https://www.pangleglobal.com/jp/help/doc/5dd362e23d7897001168e334)

  - Create an ad placement：[Application] -> [Ad Placements] -> [Add Ad Placement] -> [Native Ads]
    - Reference：[How do I create an ad placement?](https://www.pangleglobal.com/jp/help/doc/5e62079cfe8738000fd184cf)
    
    
## Native Ads Implementation

Native ads are ad assets that are presented to users via UI components. It can be formatted to match your app's visual design. When a native ad loads, your app receives an ad object that contains its assets, and the app is then responsible for displaying them. 

Broadly speaking, there are three steps to successfully implement Native Ads: 

- Design your native ad layout.
- Loading an ad.
- Displaying the ad content in your app

### Design your native ad layout

Before loading a native ad, you should have finished the design of a native ad. Assume you have finished the design of native layout. Example as below:

```XML
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >
  
    <TextView
        android:id="@+id/ad_title"   
        ... />
  
    <TextView
        android:id="@+id/ad_desc"   
        ... />
  
    <ImageView
        android:id="@+id/ad_icon"       
        ... />
  
    <ImageView
        android:id="@+id/ad_image"       
        ... />
  
    <FrameLayout
        android:id="@+id/ad_video"

        ... />
  
    <Button
        android:id="@+id/creative_btn"       
        ... />
  
    // Other assets such as ad source, ad logo, etc follow.
        ...
   

</RelativeLayout>
```

Then inflate the layout in the app. In this example, we're inflating an XML layout that contains views for displaying a native ad.

```Java
private void findADView(Context mContext){

    View nativeAdView = LayoutInflater.from(mContext).inflate(R.layout.native_ad, null);
    
    TextView mTitle = (TextView) nativeAdView.findViewById(R.id.ad_title);
    
    TextView mSource = (TextView) nativeAdView.findViewById(R.id.ad_source);
    
    TextView mDescription = (TextView) nativeAdView.findViewById(R.id.ad_desc);
    
    ImageView mIcon = (ImageView) nativeAdView.findViewById(R.id.ad_icon);
    
    ImageView mDislike = (ImageView) nativeAdView.findViewById(R.id.ad_dislike);
    
    Button mCreativeButton = (Button) nativeAdView.findViewById(R.id.creative_btn);
    
    RelativeLayout mAdLogoView = (RelativeLayout) nativeAdView.findViewById(R.id.ad_logo);
    
    ImageView mainImage = (ImageView) nativeAdView.findViewById(R.id.ad_image);
    
    FrameLayout videoView = (FrameLayout) nativeAdView.findViewById(R.id.ad_video);
    
}
```

### Load an Ad

Native ads are loaded via the `TTAdNative` class, which has the `loadFeedAd` method used to load native ads. The `loadFeedAd` method requires `AdSlot` and `FeedAdListener` instances.


#### Create the TTAdNative Object

`TTAdNative` is a Ad Loading Manager. It is recommended to be the member variable of the Activity.

```Java
TTAdNative mTTAdNative = TTAdSdk.getAdManager().createAdNative(this);
```

#### Create the AdSlot Object

When building the `AdSlot` Object, your Ad Placement Id is required to call `setCodeId`.

```Java
AdSlot adSlot = new AdSlot.Builder()
        .setCodeId("Your_Ad_Placement_Id")
        .build();
```

Returning multi-native ads in one ad request is supported on Pangle. Publishers could set the number of ads that the SDK should attempt to load via `setAdCount()` method when building `AdSlot` Object. This number is capped at a maximum of three, and it's not guaranteed that the SDK will return the exact number of the ads.



#### Load a Native Ad and Register FeedAdListener Callback

Calling the `loadFeedAd` method on the `TTAdNative` class to load a Native Ad. The `AdSlot` and `FeedAdListener` Object are required. The Native Ad Object is returned as a parameter in the `onFeedAdLoad` callback.

```Java
mTTAdNative.loadFeedAd(adSlot, new TTAdNative.FeedAdListener() {
    @Override
    public void onError(int code, String message) {

     }

    @Override
    public void onFeedAdLoad(List<TTFeedAd> ttFeedAd) {
       
     }
});
```

| FeedAdListener callback | - |
|---------------------|------------------------------------------------------------|
| onError             | This method is invoked when an ad fails to load. It includes an error parameter of type Error that indicates what type of failure occurred. For more information, refer to the ErrorCode section.                                                 |
| onFeedAdLoad        | This method is executed when an ad material is successfully loaded.        |



### Displaying the Ad Content In the App

Once the native ad is loaded, the app is responsible for displaying ad assets. Publishers could retrieve the ad assets from returned `TTFeedAd` object and then populate the asset views.

#### Populate the Asset Views

**Note:Publishers need to inflate the native ad layout before starting this part, refer to the inflate native ad layout**

Here is an example that retrieves ad assets and populates the asset views. SDK always return image url for all image assets, publishers need to download the image from the url. In the example, it uses a third-party framework(Glide) to finish the thing.

```Java
private void PopulateNativeAdView(Context mContext, TTFeedAd ad){

    //set title
    mTitle.setText(ad.getTitle()); 
    
    //set description
    mDescription.setText(ad.getDescription()); 
    
    //set ad source
    if (ad.getSource() == null) {
        mSource.setText(ad.getSource());
    }
    
    //set ad icon
    TTImage icon = ad.getIcon();
    if (icon != null && icon.isValid()) {
        //download the image and set it to the view
        Glide.with(mContext).load(icon.getImageUrl()).into(mIcon);
    }
    
    //set button text
    if(ad.getButtonText()!= null){
        mCreativeButton.setText(ad.getButtonText());
    }else{
        mCreativeButton.setText("view");
    }
    
    //set ad logo 
    ImageView logoImageView = (ImageView) ad.getAdLogoView();
    if (mAdLogoView == null || logoImageView == null) {
        return;
    }else{
        RelativeLayout.LayoutParams lp = new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.MATCH_PARENT);
        mAdLogoView.addView(logoImageView, lp);
    }
    
    //check ad form, set video view
    if (ad.getImageMode() == TTAdConstant.IMAGE_MODE_VIDEO || ad.getImageMode() == TTAdConstant.IMAGE_MODE_VIDEO_SQUARE){
        
        if (videoView != null) {
    
            //get ad view
            View video = ad.getAdView();
        
            if (video != null) {
                if (video.getParent() == null) {
                    videoView.removeAllViews();
                    videoView.addView(video);
                }
            }     
        } 
    }
    
    //check ad form, set image view
    if (ad.getImageMode() == TTAdConstant.IMAGE_MODE_LARGE_IMG || ad.getImageMode() == TTAdConstant.IMAGE_MODE_SQUARE_IMG){
        
        if (ad.getImageList() != null && !ad.getImageList().isEmpty()) {
        
            TTImage image = ad.getImageList().get(0);
        
        if (image != null && image.isValid()) {
        
            //download the image and set it to the view
            Glide.with(mContext).load(image.getImageUrl()).into(mainImage);
            
        }
    
    }
    
}

```

**Note: You must display the ad logo to denote that the view is an advertisement.**


Publishers could check ad forms with the `getImageMode()` method. Example as below:

```Java
    if(ad.getImageMode() == TTAdConstant.IMAGE_MODE_LARGE_IMG){
        ...
    }
```

| IMAGE_MODE | Description  |
|-----------------------------|------------------------------------------------------------|
| IMAGE_MODE_VIDEO            |  1280*720 video                                 |
| IMAGE_MODE_VIDEO_SQUARE     |  1:1 square video               |
| IMAGE_MODE_LARGE_IMG        |  1200*628 image (1.91:1)                                     |
| IMAGE_MODE_SQUARE_IMG       |  1:1 square image                                |



#### Click Handling and Ad Event Handling

Clicks on the ad view assets will be handled by the SDK, calling `registerViewForInteraction` method on the ad, the method requires four instances: 
- native ad view
- image view for the main image asset or video view for the video asset
- clickable views that you want it could be clicked by the user 
- adInteractionListener

`AdInteractionListener` provides some ad event methods to get notifications of native ads.

Here is an example that uses the `registerViewForInteraction` to observe click events and `AdInteractionListener` to get notifications from the native ad.

```Java
    
    //Register clickable views that could be clicked by the user
    List<View> creativeViewList = new ArrayList<>();
    
    creativeViewList.add(mCreativeButton);
    creativeViewList.add(mIcon);
    creativeViewList.add(mTitle);
    ...
    ... 
    
    //Pass the image view for the main image asset or video view for the video asset, depends on the IMAGE_MODE
    ArrayList<View> images = new ArrayList<>();
    
    if (ad.getImageMode() == TTAdConstant.IMAGE_MODE_VIDEO || ad.getImageMode() == TTAdConstant.IMAGE_MODE_VIDEO_SQUARE){
        images.add(videoView);
    }
    
    if (ad.getImageMode() == TTAdConstant.IMAGE_MODE_LARGE_IMG || ad.getImageMode() == TTAdConstant.IMAGE_MODE_SQUARE_IMG){
        images.add(mainImage);
    }
    
    //Notice! The method involves advertising billing and must be called correctly. nativeAdView must use ViewGroup, regarding nativeAdView, refer to inflate native ad layout
    ad.registerViewForInteraction((ViewGroup) nativeAdView, images, creativeViewList, new TTNativeAd.AdInteractionListener() {
        @Override
        public void onAdCreativeClick(View view, TTNativeAd ad) {
            
        }

        @Override
        public void onAdShow(TTNativeAd ad) {
            
        }
    });


```


Here is the description of `AdInteractionListener`

| AdInteractionListener | Description |
|---------------------|------------------------------------------------------------|
| onAdCreativeClick   | This method is invoked when the ad is clicked by the user.                                  |
| onAdShow            | This method is invoked when the ad is displayed, covering the device's screen.              |



Pangle provides a specific ad event callback for video asset. Passing a `VideoAdListener` object to the setter on your ad. This step is optional, details as below:

```Java
ad.setVideoAdListener(new TTFeedAd.VideoAdListener() {
    @Override
    public void onVideoLoad(TTFeedAd ad) {
        
    }

    @Override
    public void onVideoError(int errorCode, int extraCode) {
        
    }
    
    ......
   
});
```

| VideoAdListener | Description |
|---------------------|------------------------------------------------------------|
| onVideoLoad             | This method is invoked when the video has finished loading.                                  |
| onVideoError            | This method is invoked when the video fails to play.               |
| onVideoAdStartPlay      | This method is invoked when the video start playing.                                      |
| onVideoAdPaused         | This method is invoked when the video is paused.                                  |
| onVideoAdContinuePlay   | This method is invoked when the video is continue to play.              |
| onProgressUpdate        | Reporting the progress of video playing.                                 |
| onVideoAdComplete       | This method is invoked when the the video has finished playing.              |



### Dislike Button(optional)

Pangle provides a dislike button for banner ad which could make the user have a chance to remove the banner layout and select the reason why they don't like it. This step is optional.


```Java
            
            final TTAdDislike ttAdDislike = ad.getDislikeDialog((Activity) mContext);
            
            if (ttAdDislike != null) {
                ttFeedAd.getDislikeDialog((Activity) mContext).setDislikeInteractionCallback(new TTAdDislike.DislikeInteractionCallback() {
                    @Override
                    public void onSelected(int position, String value) {
                        //You could remove ad here
                        mExampleContainer.remove(ttFeedAd);
                    }

                    @Override
                    public void onCancel() {

                    }

                    @Override
                    public void onRefuse() {

                    }
                });
            }
            dislike.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    if (ttAdDislike != null) {
                        ttAdDislike.showDislikeDialog();
                    }
                }
            });


```

## Test with test ads

Now you have finished the integration. If you wanna test your apps, make sure you use test ads rather than live, production ads. The easiest way to load test ads is to use test mode. It's been specially configured to return test ads for every request, and you're free to use it in your own apps while coding, testing, and debugging. 

Refer to the [How to add a test device?](https://www.pangleglobal.com/help/doc/5fba365f7b550100157bfc06) to add your device to the test devices on Pangle platform.




