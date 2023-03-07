# Hopper Hotel Price Freeze SDK for iOS
NOTE: This initial version of the SDK is an implementation stub only. It provides mock functionality for the purposes of beginning to integrate with the SDK.

This repo contains the HCHotelsPriceFreeze framework which can be added to your iOS project via CocoaPods.

## Installation
1. [Install CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) on your Mac
2. Open Terminal
3. In Terminal navigate to your Xcode project root directory, where your `.xcodeproj` file lives, and type: `pod init`
4. Open the newly created Podfile
5. In the Podfile below the line `use_frameworks!` add: `pod 'HCHotels-Testing', :git => 'https://github.com/vivekmahajan/test-sdk.git'`
6. Save your Podfile
7. In Terminal in your Xcode project root directory type: `pod install` for Intel machines, or `arch -x86_64 pod install` for Apple Silicon machines
8. Close any open Xcode windows and open up the newly created `.xcworkspace` file
9. Where you'd like to use the SDK in the Xcode project type `import HCHotelsPriceFreeze` at the top of your Swift file.

If you're having issues installing CocoaPods, or for a more detailed walkthrough of installing CocoaPods, check out this [Stack Overflow post](https://stackoverflow.com/questions/20755044/how-do-i-install-cocoapods).

## Usage
NOTE: This initial version of the SDK is an implementation stub only. It provides mock functionality for the purposes of beginning to integrate with the SDK.

Below is a summary of usage, see each class and method for additional documentation.

### Setup

A single instance of HCHotelsPriceFreezeSDK should be created for your entire app and passed to your app as an @EnvironmentObject.  For example:

```swift
import HCHotelsPriceFreeze
import SwiftUI

@main
struct MyApp: App {
    @StateObject var sdk = HCHotelsPriceFreezeSDK(userId: "testUserId",
                                                  currency: "USD",
                                                  locale: "en")
                                                  
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(sdk)
        }
    }
}
```

### Displaying Price Freeze Buttons

**TODO**

### Preloading Offers

To calculate offers for rooms ahead of time, use the following method:

```swift
sdk.cacheOffers(for: rooms) // Where `rooms` is an array of rooms to preload
```

**NOTE: In this implementation stub, this function has no effect

### Purchasing Price Freezes

By wrapping your SwiftUI Buttons in an `HCPriceFreezeButtonWrapper`, clicks will automatically be detected via a ButtonStyle and launch the appropriate Hopper Price Freeze purchase screen.

**NOTE: In this implementation stub, a placeholder version of the purchase screen will be shown**

### Observing Price Freeze Purchase Results

The `HCPriceFreezeButtonWrapper` accepts a `purchaseCallback` which allows you to take action depending on the outcome of the purchase flow.

```swift
HCPriceFreezeButtonWrapper(room: room,
                           purchaseCallback: { purchaseResult in
                               switch purchaseResult {
                               case .purchased: // The user purchased the price freeze
                                   print("Price Freeze Purchased!")
                               case .cancelled: // The user exited the purchase flow without purchasing
                                   print("Price Freeze purchase flow cancelled")
                               default: // Other purchaseResult outcomes
                                   print("Error/Invalid purchaseResult: \(purchaseResult)")
                               }
                           }
)
{
    if sdk.offers[room]?.type == .signedFullOffer {
        // Your SwiftUI Button
        Button {
        } label : {
            Text("Price Freeze")
        }
    }
}
```
