# Auto Integration

## Install CocoaPods

If you don't already have CocoaPods installed you can do so by running the following command in your terminal:

```bash
gem install cocoapods
```

## Create Project Podfile

If you don't already have a `Podfile` associate with your project, create one. To do this execute the following in the top level of your project’s directory:

```text
pod init
```

{% hint style="info" %}
This will result in a `Podfile` being added to the top level of your project’s directory.
{% endhint %}

## Modify the Podfile

The newly created `Podfile` will be pre-populated with content.

1. Start by making sure that at the top of your file you have the line `platform :ios, '9.0'`.
2. Next, add the line `use_frameworks!` which will instruct CocoaPods to use the Zaius dynamic framework.
3. Finally, you'll notice that the `Podfile` contains an entry for each target of your project. Add the line `pod 'Zaius'` which will indicate to CocoaPods that the target is dependent on the ZaiusSDK and will install it into that target.

## Install Zaius CocoaPod

```bash
pod install
```

Once this command completes execution a new Xcode project workspace will be created \(`YOUR-PROJECT-NAME.xcworkspace`\).

You should now open the project workspace instead of the project file \(`YOUR-PROJECT-NAME.xcodeproj`\)

Then you may simply import the SDK into your `AppDelegate` like so:

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
#import <ZaiusSDK_iOS/ZaiusSDK_iOS.h> //iOS

#import <ZaiusSDK_tvOS/ZaiusSDK_tvOS.h> //tvOS
```
{% endtab %}

{% tab title="Swift" %}
```swift
import ZaiusSDK_iOS //iOS

import ZaiusSDK_tvOS //tvOS
```
{% endtab %}
{% endtabs %}

## Initialize the SDK

Add this code into your `application:didFinishLaunchingWithOptions:` call within your `AppDelegate`:

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  
    [Zaius autoIntegrate:^(ZaiusBuilder *_Nonnull builder) {

      builder.trackerId = @"ZAIUS_TRACKER_ID";
      builder.appId = @"APP_ID";
      builder.launchOptions = launchOptions;
      builder.collectTokenWhenAnonymous = YES;

    }];
  
  // Initialize your application

}
```
{% endtab %}

{% tab title="Swift" %}
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
	
  Zaius.autoIntegrate() { (builder) in
  	builder.trackerId = "ZAIUS_TRACKER_ID"
    builder.appId = "APP_ID"
    builder.launchOptions = launchOptions
    builder.collectTokenWhenAnonymous = true
    }
	// Initialize your application
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Zaius registers for all notification types `UIUserNotificationTypeAlert`, `UIUserNotificationTypeBadge`, `UIUserNotificationTypeSound`
{% endhint %}

### Additional Options

```swift
builder.sandbox // if this app is setup as a Sandbox app within Zaius
builder.collectTokenWhenAnonymous // collect push tokens when customer ID is not set (not recommended to disable)
builder.enablePushNotification // request push permissions on app initialization
```

{% hint style="danger" %}
An incorrect sandbox setting may result in a certificate mismatch \(e.g. sending with a sandbox certificate to an app in "production" mode\). Ensure this is set to `false` / `NO` before releasing to production
{% endhint %}

### Delay Push Notification Prompt

If you would like to delay prompting the user to accept push notifications, set the builder property as follows:

```objectivec
builder.enablePushNotification = NO
```

When you are ready to prompt the user for push permissions call the following:

```objectivec
[Zaius enablePushNotifications:YES]
```

## 



