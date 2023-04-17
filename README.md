# Hopper Hotel Price Freeze SDK for iOS
NOTE: This version of the SDK is under development.

This repo contains the HCHotelsPriceFreeze framework which can be added to your iOS project via CocoaPods or manually.

## CocoaPods Installation
1. [Install CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) on your Mac
2. Open Terminal
3. In Terminal navigate to your Xcode project root directory, where your `.xcodeproj` file lives, and type: `pod init`
4. Open the newly created Podfile
5. In the Podfile below the line `use_frameworks!` add: `pod 'HCHotelsPriceFreezeSDK'`
6. Save your Podfile
7. In Terminal in your Xcode project root directory type: `pod install` for Intel machines, or `arch -x86_64 pod install` for Apple Silicon machines
8. Close any open Xcode windows and open up the newly created `.xcworkspace` file
9. Where you'd like to use the SDK in the Xcode project type `import HCHotelsPriceFreeze` at the top of your Swift file.

If you're having issues installing CocoaPods, or for a more detailed walkthrough of installing CocoaPods, check out this [Stack Overflow post](https://stackoverflow.com/questions/20755044/how-do-i-install-cocoapods).

## Manual Installation
1. Download this repo 
2. Unzip the zip file
3. Drag the HCHotelsPriceFreeze.xcframework into your Xcode project

## Usage
NOTE: This version of the SDK is under development.

Below is a summary of usage, see each class and method for additional documentation.

### Setup

Access the SDK using an `ObservedObject` and call `configure()` before making requests.

```swift
import HCHotelsPriceFreeze
import SwiftUI

struct ContentView: View {
    @ObservedObject var sdk = HCHotelsPriceFreezeSDK.shared
    
    init() {
        sdk.configure(with: "your_hopper_token",
                      environmentType: .staging)
    }
    
    var body: some View {
        HCPriceFreezeButtonWrapper(roomDetails: roomDetails,
                                   purchaseCallback: { } )
        { offer, onClick in
            Button("Freeze Price") { } // This is your SwiftUI Button
        }
    }
}
``` 

### Displaying Price Freeze Buttons

Let's say you have a SwiftUI Button that represents your Price Freeze button:

```swift
// Within your SwiftUI View
Button("Price Freeze") { } // This is your SwiftUI Button
```

You can wrap your button with `HCPriceFreezeButtonWrapper`:

```swift
// Within your SwiftUI View
HCPriceFreezeButtonWrapper(roomDetails: roomDetails,
                           purchaseCallback: {})
{ offer, onClick in
    Button("Price Freeze") { } // This is your SwiftUI Button
}
```

The `PriceFreezeButtonWrapper` will handle fetching, caching, expiring, managing offer state, and will provide you with the current `HCPriceFreezeOffer` to render how you wish.

The `HCPriceFreezeOffer` will be in one of a few possible states:
- loading
- available
- unavailable
- error

You can use this status to decide how/if to render your button, for example if you only want to render it when there is an available offer:

```swift
// Within your SwiftUI View
HCPriceFreezeButtonWrapper(roomDetails: roomDetails,
                           purchaseCallback: {})
{ offer, onClick in
    if offer.state == .available {
        Button("Price Freeze") { } // This is your SwiftUI Button
    }
}
```

The wrapper will always and first provide the currently known `HCPriceFreezeOffer` state, and then again anytime the offer state changes.

If an offer for this room has not yet been calculated, the `HCPriceFreezeOffer` will have a **loading** `HCPriceFreezeOfferState`. As soon as an `HCPriceFreezeOffer` is calculated the wrapper will provide the updated `HCPriceFreezeOffer`, for example an offer in the **available** `HCPriceFreezeOfferState`.

If the offer has already been determined, you will have access to the calculated `HCPriceFreezeOffer`.

### Preloading Offers

To calculate offers for rooms ahead of time, use the following method:

```swift
sdk.cacheOffers(for: rooms) // Where `rooms` is an array of `HCRoomDetails` to preload
```

This will start calculating the offers in the background so the `PriceFreezeButtonWrapper` will have a result immediately or sooner. 

### Purchasing Price Freezes

To start the purchase flow for an offer, invoke the `onClick` function provided:

```swift
// Within your SwiftUI View
HCPriceFreezeButtonWrapper(roomDetails: roomDetails,
                           purchaseCallback: {})
{ offer, onClick in
    if offer.state == .available {
        Button("Price Freeze") { onClick() } // This is your SwiftUI Button
    }
}
```

This will trigger the purchase flow to be launched for the user.

### Observing Price Freeze Purchase Results

The `HCPriceFreezeButtonWrapper` accepts a `purchaseCallback` which allows you to take action depending on the outcome of the purchase flow.

```swift
HCPriceFreezeButtonWrapper(roomDetails: roomDetails,
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
{ offer, onClick in
    if offer.state == .available {
        Button("Price Freeze") { } // This is your SwiftUI Button
    }
}
```

## Notes
1. Xcode 14 has a known issue when running code that utilizes `WKWebView`s. You may notice an Xcode warning, displayed as a purple exclamation point, when interacting with the price freeze purchase microsite. The warning will state `Security: This method should not be called on the main thread as it may lead to UI unresponsiveness`. (Apple's Developer Technical Support addressed the issue)[https://developer.apple.com/forums/thread/714467?answerId=734799022#734799022] and outlined steps to confirm the warning can be ignored, as we can here.
