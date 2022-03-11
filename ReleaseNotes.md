# Passio SDK V2.1.3. Beta Release Notes

Version 2.1.3 which is still in Beta is not recommended for production. APIs are subject to change from version to version.

## New for V2.1.3

### Models

* Number of food items recognized via HNN: 3907
* Number of logos recognized: 300
* Nutrition database version: passio_nutrition.3907.0.300

### API Updates
The servingSizeQuantity was modified from 
```swift
public var servingSizeQuantity: Int
```
to:
```swift
public var servingSizeQuantity: Double
```

## New for V2.1.2

### API updates 

* In the  DetectedCandidate the 
```swift 
var scannedVolume: Double? { get }
var scannedWeight: Double? { get }
```
were replaced by 
```swift 
/// Scanned AmountEstimate
    var amountEstimate: PassioNutritionAISDK.AmountEstimate? { get }
```

```swift
public protocol DetectedCandidate {
    /// PassioID recognized by the MLModel
    var passioID: PassioNutritionAISDK.PassioID { get }
    /// Confidence (0.0 to 1.0) of the associated PassioID recognized by the MLModel
    var confidence: Double { get }
    /// boundingBox CGRect representing the predicted bounding box in normalized coordinates.
    var boundingBox: CGRect { get }
    /// The image that the detection was preformed upon
    var croppedImage: UIImage? { get }
    /// Scanned AmountEstimate
    var amountEstimate: PassioNutritionAISDK.AmountEstimate? { get }
}
```

```swift
public protocol AmountEstimate {
    var volumeEstimate: Double? { get }
    /// Scanned Amount in grams
    var weightEstimate: Double? { get }
    /// The quality of the estimate (eventually for feedback to the user or SDK-based app developer)
    var estimationQuality: PassioNutritionAISDK.EstimationQuality? { get }
}
```

```swift 
public enum EstimationQuality {
    case good
    case fair
    case poor
}
```

The functions of PassioDownloadDelegate were merged in to the PassioStatusDelegate
```swift
public protocol PassioStatusDelegate : AnyObject {

    func passioStatusChanged(status: PassioNutritionAISDK.PassioStatus)

    func passioProccessing(filesLeft: Int)

    func completedDownloadingAllFiles(filesLocalURLs: [PassioNutritionAISDK.FileLocalURL])

    func completedDownloadingFile(fileLocalURL: PassioNutritionAISDK.FileLocalURL, filesLeft: Int)

    func downloadingError(message: String)
}
```

* Renaming:
```swift
public func listFoodEnabledForWeightEstimation() -> public func listFoodEnabledForAmountEstimation()
```
* Renaming:
```swift
public func transformCGRectForm(boundingBox: CGRect, toPreviewLayerRect preview: CGRect) -> CGRect 
to
public func transformCGRectForm(boundingBox: CGRect, toRect: CGRect) -> CGRect
```
* Renaming:
```swift
case autoUpdateFailed to case modelsDownloadFailed
``` 


## New for V2.1.1
* The ML models are compressed. 
* iPhone with DUAL WIDE Camera (iPhone 11, 12) can perform weight estimation of several foods. iPhone 13 and more foods will be added in upcoming releases.
* Data from Open Food Facts (https://en.openfoodfacts.org) was added to the SDK. Each food that contains data from Open Food Facts will be marked by ```public var isOpenFood: Bool.```

This record contains information from Open Food Facts (https://en.openfoodfacts.org), which is made available here under the Open Database License (https://opendatacommons.org/licenses/odbl/1-0)

##  Passio Nutrition-AI Migration from SDK 1.4.x to 2.x:
* Follow the directions in [Migration from 1.4 to 2.1 ](./Migration1.4to2.1.md). 

## To install SDK 2.x - follow README file

* Download the ```PassioSDKQuickStart.zip``` or the ```PassioSDKFullDemo.zip``` and copy the ```PassioSDKiOS.xcframework``` to your project. Make sure you have followed the directions in the README files.
* The SDK will only run on iOS 13 or newer.. The XCFramework is compiled for min iOS version 12.0.

## OpenFood  license copy
Passio Nutrition-AI SDK added data from Open Food Facts (https://en.openfoodfacts.org/).

<sup>Copyright 2022 Passio Inc</sup>