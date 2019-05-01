# Manual Integration

## Download SDK

Extract [the Zaius SDK](https://s3.amazonaws.com/zaius-public/mobile-sdk/zaius-ios-tvos-sdk-2.1.0.tgz) and add the desired framework to your Xcode project:

* `ZaiusSDK_iOS.framework` for iOS
* `ZaiusSDK_tvOS.framework` for tvOS

In your Target's Build Phases, add the framework file you added to your project to the list of Embedded Frameworks

## Bridging Header

If integrating the Zaius iOS SDK into a Swift project \(when doing a manual integration\), you will need to add a Bridging Header. This can be accomplished in two ways:

1. The easiest way is to add a temporary Objective-C file to your Swift project and Xcode will create the Bridging Header for you. You may now delete that temporary Objective-C file.
2. Create a file called `<ProductName>-Bridging-Header.h`. If your ProductName is Example, then your Bridging Header would be named `Example-Bridging-Header.h`. Once you create this file, go into your target's Build Settings and search for `Objective-C Bridging Header`. Set this value to the path of your header, which in our case would be `Example/Example-Bridging-Header.h` since our header lives within our project's Example folder.

Lastly, open your newly created Bridging Header and enter the following code:

```objectivec
//  Use this file to import your target's public headers that you would like to expose to Swift.

#import <ZaiusSDK_iOS/ZaiusSDK_iOS.h>

// Or for tvOS

#import <ZaiusSDK_tvOS/ZaiusSDK_tvOS.h>
```

## Initialize

If you do not wish to use auto integration, you can manually integrate the Zaius SDK instead. First, you should initialize the Zaius SDK using `start` rather than `autoIntegrate`

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  
  [Zaius start:^(ZaiusBuilder *_Nonnull builder) {
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
	
  Zaius.start() { (builder) in
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
View the Auto Integration documentation for all the available builder options.
{% endhint %}

To properly handle application lifestyle changes in the non-builder integration of the Zaius SDK requires that `[Zaius suspend]` be called when the application will resign active and will be terminating. Please add the following into your `AppDelegate`.

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
- (void)applicationWillResignActive:(UIApplication *)application {
	[Zaius suspend];
}

- (void)applicationWillTerminate:(UIApplication *)application {
  [Zaius suspend];
}
```
{% endtab %}

{% tab title="Swift" %}
```swift
func applicationWillResignActive(_ application: UIApplication) {
	Zaius.suspend()
}

func applicationWillTerminate(_ application: UIApplication) {
	Zaius.suspend()
}
```
{% endtab %}
{% endtabs %}

Additionally, you need to forward notifications to the ZaiusSDK. The SDK requires the following calls to be configured in order to setup push notifications and track open events. Again in your `AppDelegate`, ensure these calls are included:

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
	[Zaius didRegisterForRemoveNotificationsWithDeviceToken:deviceToken];
}

- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error {
  [Zaius didFailToRegisterForRemoteNotificationsWithError:error];
}

// If your application receives background push notifications use this implementation:
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
    [Zaius didReceiveRemoteNotification:userInfo];

    // EXAMPLE: if we are transitioning to foreground
    if ([UIApplication sharedApplication].applicationState == UIApplicationStateInactive) {
        // perform any required processing of the notification by your application
    }
    
    // Zaius SDK does not call the completion handler for you
    completionHandler(UIBackgroundFetchResultNoData);
}

// WARNING: This method has been deprecated in iOS 10. If targeting iOS 10 or above, use application:didReveiceRemoteNotification:fetchCompletionHandler instead.
// If your application does NOT receive background push notifications use this implementation:
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
    [Zaius didReceiveRemoteNotification:userInfo];

    // EXAMPLE: if we are transitioning to foreground
    if ([UIApplication sharedApplication].applicationState == UIApplicationStateInactive) {
        // perform any required processing of the notification by your application
    }
```
{% endtab %}

{% tab title="Swift" %}
```swift
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data){
	Zaius.didRegisterForRemoteNotifications(withDeviceToken: deviceToken)
}

func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
	Zaius.didFailToRegisterForRemoteNotificationsWithError(error)
}

// If your application receives background push notifications use this implementation:
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
	Zaius.didReceiveRemoteNotification(userInfo)

	// EXAMPLE: if we are transitioning to foreground
	if application.applicationState == .inactive {
		// perform any required processing of the notification by your application
	}

	// Zaius SDK does not call the completion handler for you
	completionHandler(.noData)
}

// WARNING: This method has been deprecated in iOS 10. If targeting iOS 10 or above, use application:didReveiceRemoteNotification:fetchCompletionHandler instead.
// If your application does NOT receive background push notifications use this implementation:
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
    Zaius.didReceiveRemoteNotification(userInfo)

    // EXAMPLE: if we are transitioning to foreground
    if application.applicationState == .inactive {
		    // perform any required processing of the notification by your application
    }
}
```
{% endtab %}
{% endtabs %}

