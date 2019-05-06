[![CocoaPods](https://img.shields.io/cocoapods/v/Iterable-iOS-SDK.svg?style=flat)](https://cocoapods.org/pods/Iterable-iOS-SDK)
[![License](https://img.shields.io/cocoapods/l/Iterable-iOS-SDK.svg?style=flat)](https://opensource.org/licenses/MIT)
[![Build Status](https://travis-ci.com/Iterable/swift-sdk.svg?branch=master)](https://travis-ci.com/Iterable/swift-sdk)

# Iterable iOS SDK

`Iterable-iOS-SDK` is a Swift implementation of an iOS client for Iterable, for iOS versions 9.0 and higher.

## Before Starting

Before you even start with the SDK, you will need to setup Iterable push
notifications for your app.

For more information, see [Getting Started Guide](https://support.iterable.com/hc/en-us/articles/115000315806-Setting-Up-iOS-Push-Notifications). 
 
## Automatic Installation (via CocoaPods)

Iterable supports [CocoaPods](https://cocoapods.org) for easy installation. If you don't have it yet, please refer to [this document](https://guides.cocoapods.org/using/getting-started.html) to install Cocoapods.

To include the Iterable SDK in your project, edit the `Podfile` and add the Iterable-iOS-SDK pod to your app target:

```
pod 'Iterable-iOS-SDK'
```

If you want to include Iterable Rich Notification Extension you will also need to include the Iterable-iOS-AppExtensions pod to your extension target:

```
pod 'Iterable-iOS-AppExtensions'
```


Please look at the included sample pod file [HERE](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/swift-sample-app/Podfile?raw=true).

Congratulations! You have now imported Iterable SDK into your project! 

## Manual Installation

Attached to the release you will find two framework bundles. 

```
IterableSDK.framework 
IterableAppExtensions.framework
```	
	
1. In XCode choose the target for your app. Now add IterableSDK.framework to the **embedded binaries** section. If you want to use Iterable Rich Notification Extension, you will have to add IterableAppExtensions.framework to the embedded binaries section as well.

	![Linking](https://github.com/Iterable/swift-sdk/blob/master/images/embedded-binaries.png?raw=true)

2. If you want to use Iterable Rich Notifiation Extension, you will need to add IterableAppExtension.framework to **Linked Frameworks and Libraries** section of your **app extension** target (not app target). Please note that you will have to add the IterableAppExtension.framework bundle to **both** the app target (step 1) and app extension target (step 2) of your project. In the app target it goes in the 'Embedded Binaries' section and in app extension target it goes in the 'Linked Frameworks and Libraries' section.

	![Linking](https://github.com/Iterable/swift-sdk/blob/master/images/app-extension-linked-framework.png?raw=true)

3. In build settings, set `Always Embed Swift Standard Libraries` setting to 'Yes'. This is required for Objective C projects.
	
	![Linking](https://github.com/Iterable/swift-sdk/blob/master/images/build-setting.png?raw=true)
 

## Initializing the SDK
**Note:** Sample projects are included in this repo.
 
- [Swift Sample Project](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/swift-sample-app?raw=true)
- [ObjC Sample Project](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/objc-sample-app?raw=true)

### 1. Import IterableSDK module in your project.

You need to import IterableSDK module in order to use it. Import in the top your Swift or Obective-C files as shown below.

*Swift*

```swift
// In AppDelegate.swift file
// and any other file where you are using IterableSDK
import IterableSDK
```

*Objective-C*

```objc
// In AppDelegate.m file
// and any other file where you are using IterableSDK
@import IterableSDK;
```


### 2. Initialize the API with API key.
	
In your app delegate, on application launch in `application:didFinishLaunchingWithOptions:` method, initialize the Iterable SDK:
	
*Swift*
	
```swift
let config = IterableConfig()
config.pushIntegrationName = "<your-iterable-push-integration-name>"
IterableAPI.initialize(apiKey: "<your-api-key>", launchOptions: launchOptions, config:config)
```
	
*Objective-C*
	
```objective-c
IterableConfig *config = [[IterableConfig alloc] init];
config.pushIntegrationName = @"<your-iterable-push-integration-name>";
[IterableAPI initializeWithApiKey:@"<your-api-key>" launchOptions:launchOptions config:config]
```
	
See the Iterable guide on how to setup your Iterable push integration and obtain push integration name [here](https://support.iterable.com/hc/en-us/articles/115000315806-Setting-Up-iOS-Push-Notifications).	
	
### 3. Set userId or email. 
Once you know the email or userId of the user, set the value.
> &#x26A0; Don't specify both email and userId in the same session, as they will be treated as different users by the SDK. Only use one type of identifier, email or userId, to identify the user.
*Swift*
	
```swift
IterableAPI.email = "user@example.com"
```

*Objective-C*

```objc
IterableAPI.email = @"user@example.com";
```
	
## Using the SDK

### Sending push notifications

See [Apple Notification Guide](https://developer.apple.com/documentation/usernotifications) regarding how to register for remote notifiations.

1. Send remote notification token to Iterable

	To send push notifications to your app, you will have to first send the application's remote notification token Iterable.

	In your `AppDelegate`’s [application:didRegisterForRemoteNotificationsWithDeviceToken:](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622958-application) method, send the token obtained to Iterable.

	*Swift*
		
	```swift
	func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
		IterableAPI.register(token: deviceToken)
	}
	```
	
	*Objective-C*
		
	```objective-c
	- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
		[IterableAPI registerToken:deviceToken];
	}
	```

2. Handle push notifications

	When the user taps on the push notification or one of the action buttons, the system calls `UNUserNotificationCenterDelegate`'s [userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:](https://developer.apple.com/documentation/usernotifications/unusernotificationcenterdelegate/1649501-usernotificationcenter?language=swift). In this method, call `IterableAppIntegration` with the same parameters to track push open event and perform the associated action (see below for custom action and URL delegates).
		
	*Swift*
		
	```swift
	public func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
		IterableAppIntegration.userNotificationCenter(center, didReceive: response, withCompletionHandler: completionHandler)
	}
	```
	
	*Objective-C*
		
	```objective-c
	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void (^)(void))completionHandler {
		[IterableAppIntegration userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}
	```

See example in sample app delegate [here](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/swift-sample-app/swift-sample-app/AppDelegate.swift).

Congratulations! You can now send remote push notifications to your device from Iterable! Please note that you can't send push notifications until you set the userId or email. Please see sample applications to see a reference implementation.

	
### Deep linking

Deep linking allows a uniform resource identifier (URI) to link to a specific location within your mobile app rather than simply launching the app.

#### Handling links from push notifications
	
Push notifications and action buttons may have `openUrl` actions attached to them. When a URL is specified, the SDK first calls the `urlDelegate` specified in your `IterableConfig` object. You can use this delegate to handle `openUrl` actions the same way as you handle normal deep links. If the delegate is not set or if it returns `false` (the default), the SDK will open Safari with that URL. If you want to navigate to a view controller on receiving a deep link, do so in the `urlDelegate`. 
	
In the code below, `DeepLinkHandler` is a custom handler which is reponsible for deep link navigation. You have to provide implementation for deep link navigation. Please see [sample application](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/swift-sample-app/swift-sample-app/DeeplinkHandler.swift) for a reference implementation of DeeplinkHandler.
	
*Swift*
	
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
	...
	// Initialize Iterable API
	let config = IterableConfig()
	...
	config.urlDelegate = self
	IterableAPI.initialize(apiKey: apiKey, launchOptions:launchOptions, config: config)
	...
}

// Iterable URL Delegate. It will be called when you receive 
// an `openUrl` event from push notification.
func handle(iterableURL url: URL, inContext context: IterableActionContext) -> Bool {
	return DeeplinkHandler.handle(url: url)
}
```
	
*Objective-C*
	
```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	...
	// Initialize Iterable SDK
	IterableConfig *config = [[IterableConfig alloc] init];
	...
	config.urlDelegate = self;
	[IterableAPI initializeWithApiKey:@"YOUR API KEY" launchOptions:launchOptions config:config];
	...
}
	
- (BOOL)handleIterableURL:(NSURL *)url context:(IterableActionContext *)context {
	// Assuming you have a DeeplinkHandler class that handles all deep link URLs and navigates to the right place in the app
	return [DeeplinkHandler handleUrl:url];
}
```
		
#### Handling email links
	
For universal links to work with link rewriting in emails, [set up an **apple-app-site-association** file](https://support.iterable.com/hc/articles/115000440206-Setting-up-iOS-Universal-Links) in the Iterable project. 

When an email link is clicked, your `UIApplicationDelegate`'s [application:continueUserActivity:restorationHandler:](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623072-application?language=swift) method is called. If you already have an Iterable `urlDelegate` defined (see *Handling Links from Push Notifications* section above), the same handler can be used for email deep links by calling `handleUniversalLink:`.

*Swift*
	
```swift
func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([Any]?) -> Void) -> Bool {
	guard let url = userActivity.webpageURL else {
		return false
	}

	// This will track the click, retrieve the original URL and call `handleIterableURL:context:` with the original URL
	return IterableAPI.handle(universalLink: url)
}
```

*Objective-C*
	
```objective-c
- (BOOL)application:(UIApplication *)application continueUserActivity(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray *restorableObjects))restorationHandler {
	// This will track the click, retrieve the original URL and call `handleIterableURL:context:` with the original URL
	return [IterableAPI handleUniversalLink:userActivity.webpageURL];
}
```

### Deferred deep linking

[Deferred deep linking](https://en.wikipedia.org/wiki/Deferred_deep_linking) allows a user who does not have a specific app installed to:

 - Click on a deep link that would normally open content in that app.
 - Install the app from the App Store.
 - Open the app and immediately see the content referenced by the link.
 
As the name implies, the deep link is _deferred_ until the app has been installed. 

After tapping a deep link in an email from an Iterable campaign, users without the associated app will be directed to the App Store to install it. If the app uses the IterableSDK and has deferred deep linking enabled, the content associated with the deep link will load on first launch.

#### Enabling deferred deep linking

Set `IterableConfig.checkForDeferredDeeplink = true` to enable deferred deep linking for IterableSDK.
	
### In-app messages

If you are already using in-app messages with IterableSDK, please check out the [migration section](#Migrating-in-app-messages-from-the-previous-version-of-the-SDK). 

In-app messages are handled via silent push messages from the server. When your application receives a silent push, you need to call IterableSDK in your AppDelegate as in the following code:

*Swift*

```swift
// In AppDelegate.swift
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    IterableAppIntegration.application(application, didReceiveRemoteNotification: userInfo, fetchCompletionHandler: completionHandler)
}
```

*Objective-C*

```objc
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
    [IterableAppIntegration application:application didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
}
```

#### Default behavior

By default, when an in-app message arrives from the server, the SDK automatically shows it if the app is in the foreground. If an in-app message is already showing when the new message arrives, the new in-app message will be shown 30 seconds after the currently displayed in-app message closes ([see how to change this default value below](#Changing-the-display-interval-between-in-app-messages)). Once an in-app message is shown, it will be "consumed" from the server queue and removed from the local queue as well. There is no need to write any code to get this default behavior. 

#### Overriding whether to show or skip a particular in-app message

An incoming in-app message triggers a call to the `onNew` method of `IterableConfig.inAppDelegate` (an object of type `IterableInAppDelegate`). To override the default behavior, set `IterableConfig.inAppDelegate` to a custom class that overrides the `onNew` method. `onNew` should return `.show` to show the incoming in-app message or `.skip` to skip showing it.

*Swift*

```swift
class YourCustomInAppDelegate : IterableInAppDelegate {
	func onNew(message: IterableInAppMessage) -> InAppShowResponse {
		// perform custom processing

		// ...
		
		return .show // or .skip
	}
}
	
// ...
	
let config = IterableConfig()
config.inAppDelegate = YourCustomInAppDelegate()
IterableAPI.initialize(apiKey: "YOUR API KEY",  launchOptions: nil, config: config)
```

*Objective-C*

```objc
// Implement this method in your custom class that implements IterableInAppDelegate
// This will most likely be the global AppDelegate class.
- (enum InAppShowResponse)onNewMessage:(IterableInAppMessage * _Nonnull)message {
	// perform custom processing
	
	// ...
	
	return InAppShowResponseShow; // or InAppShowResponseSkip
}

// ...
	
// Now set this custom class in IterableConfig
IterableConfig *config = [[IterableConfig alloc] init];
config.inAppDelegate = self; // or other class implementing the protocol
[IterableAPI initializeWithApiKey:@"YOUR API KEY" launchOptions:launchOptions config:config];
```

#### Getting the local queue of in-app messages
Until they are consumed by the app, all in-app messages that arrive from the server are stored in a local queue. To access that local queue, use the read-only `IterableAPI.inAppManager` property (which conforms to the `InAppManager` protocol). By default, all in-app messages in the local queue will be consumed and removed from this queue. To keep in-app messages around after they are shown, override the default behavior (as described above).
	
*Swift*
	
```swift
// Get the in-app messages list
let messages = IterableAPI.inAppManager.getMessages()
	
// Show an in-app message 
IterableAPI.inAppManager.show(message: message)
	
// Show an in-app message without consuming, i.e., not removing it from the queue
IterableAPI.inAppManager.show(message: message, consume: false)
	
```	
	
*Objective-C*
	
```objc
// Get the in-app messages list
NSArray *messages = [IterableAPI.inAppManager getMessages];
	
// Show an in-app message 
[IterableAPI.inAppManager showMessage:message];
	
// Show an in-app message without consuming, i.e., not removing it from the queue
[IterableAPI.inAppManager showMessage:message consume:NO callbackBlock:nil];
	
```	

#### When user clicks a button/link in the in-app message

Button/link clicks from in-app are handled similar to deep links from notifications and emails. Please see those sections above. If the in-app message's clicked button/link's `href` contains a URL (which is usually the case), tapping that button/link will call the `handle` method of `IterableConfig.urlDelegate` (an object of type `IterableURLDelegate`), if one is set. By default if this delegate is not set, tapping the button/link will open Safari with the `href` of the clicked button/link.
	
Custom action is specified by passing `itbl://customActionName` as the link URL (notice the `itbl` scheme name). If the in-app message's `href` property contains custom action name, tapping the  message will call the `handle` method of `IterableConfig.customActionDelegate` (an object that conforms to the `IterableCustomActionDelegate` protocol). If `customActionDelegate` is not set, nothing will happen by default.

Take a look at [this sample code](https://github.com/Iterable/swift-sdk/blob/master/sample-apps/swift-sample-app/swift-sample-app/AppDelegate.swift) that demonstrates how to implement and use the `IterableURLDelegate` and `IterableCustomActionDelegate` protocols.
	
```swift
let config = IterableConfig()
config.urlDelegate = YourCustomUrlDelegate()
config.customActionDelegate = YourCustomActionDelegate()
```
	
#### Changing the display interval between in-app messages

To customize the time delay between successive in-app messages (default value of 30 seconds), set `IterableConfig.inAppDisplayInterval` to an appropriate value (in seconds). 

#### Migrating in-app messages from the previous version of the SDK

If you are already using in-app messages, then you will have to make the following changes to your code:

1. `spawnInAppNotification` is no longer needed and will fail to compile. In-app messages are now displayed automatically.
2. Stop polling for in-app messages using a timer, etc. The SDK will issue a callback using `config.inAppDelegate` [as explained above](#overriding-whether-to-show-or-skip-a-particular-in-app-message).
3. If you want to handle all in-app messages manually, as before these changes, define an `inAppHandler` on `IterableConfig`. It should return `InAppShowResponse.skip` to prevent in-app messages from showing automatically. It can call `getInAppManager().getMessages()` to get the messages and `getInAppManager().showMessage(message)` to show a specific message.


### Tracking custom events

Custom events can be tracked using `IterableAPI.track(event:...)` calls.
	
### Updating user fields

User fields can be modified using `IterableAPI.updateUser` call. You also have `updateEmail` and `updateSubscriptions` methods.
	
### Disabling push notifications to a device

When a user logs out, you typically want to disable push notifications to that user/device. This can be accomplished by calling `disableDeviceForCurrentUser`. Please note that it will only attempt to disable the device if you have previously called `registerToken`.
	
In order to re-enable push notifcations to that device, simply call `registerToken` as usual when the user logs back in.
	
### Uninstall tracking

Iterable will track uninstalls with no additional work by you. 

To do this, Iterable sends a silent push notification some time (currently, twelve hours) after a campaign has been sent. Based on this silent push notification, if Iterable receives feedback that the device token is no longer valid, it assigns an uninstall to the device based on the prior campaign. Similarly, if a "real" campaign uncovers an invalid device token, it will also check for a prior (within twelve hours) campaign to mark as the cause for the uninstall. If there was no recent campaign, Iterable still tracks the uninstall, but does not attribute it to a campaign.

# Rich Push Notifications

Push notifications may contain media attachments with images, animated gifs or video, and action buttons. For this to work within your app, you need to create a Notification Service Extension. More instructions here: [Rich Push Notifications in iOS 10 and Android - Media Attachments](https://support.iterable.com/hc/en-us/articles/115003982203-Rich-Push-Notifications-in-iOS-10-and-Android-Media-Attachments).   

Iterable SDK provides an implementation that handles media attachments and action buttons. 

1. Include `Iterable-iOS-AppExtensions` in your podfile as explained above.
2. Create a new target of type notification service extension in your XCode project/solution.
3. If you are calling Iterable SDK from Swift, all you need to do is inherit the `NotificationService` class (auto generated by XCode) from `ITBNotificationServiceExtension` class. If you are using Objective-C, you will have to delegate to the implementation provided. See example below. 

*Swift*

```swift
import UserNotifications
import IterableAppExtensions

class NotificationService: ITBNotificationServiceExtension {
}
```

*Objective-C* 

You can not inherit in case of Objective C. You will have to delegate like below.

```objective-c
// File: NotificationService.m 
#import "NotificationService.h"

@import IterableAppExtensions;

@interface NotificationService ()

@property (nonatomic, strong) ITBNotificationServiceExtension *baseExtension;
@end

@implementation NotificationService
- (void)didReceiveNotificationRequest:(UNNotificationRequest *)request withContentHandler:(void (^)(UNNotificationContent * _Nonnull))contentHandler {

	self.baseExtension = [[ITBNotificationServiceExtension alloc] init];
	[self.baseExtension didReceiveNotificationRequest:request withContentHandler:contentHandler];
}

- (void)serviceExtensionTimeWillExpire {
	[self.baseExtension serviceExtensionTimeWillExpire];
}
@end

```
	
## Additional Information

For more information, take a look at:

- Iterable's [iOS SDK Release Notes](https://support.iterable.com/hc/articles/360027798391)
- Iterable's [Setting Up iOS Push Notifications](https://support.iterable.com/hc/articles/115000315806-Setting-Up-iOS-Push-Notifications) guide
- Iterable's [Push Notification Setup FAQs](http://support.iterable.com/hc/articles/206791196-Push-Notification-Setup-FAQ-s)

## License

The MIT License

See [LICENSE](https://github.com/Iterable/swift-sdk/blob/master/LICENSE?raw=true)

## Want to Contribute?

This library is open source, and we will look at pull requests!

See [CONTRIBUTING](CONTRIBUTING.md) for more information.
