# Passio SDK V2.X  Release Notes

## V2.2.23

### Recognition
There is additional improvement recognizing multiple foods in the same frame. 

### Models

* Number of food items recognized via HNN: 4117
* Nutrition database version: passio_nutrition.4118.0.301
* Number of products recognized via OCR: 21423

### New included HNN foods
```
packaged pills and supplements
white tortillas
```

### Excluded HNN foods
```
arazo nutrition omega 3
benefiber bar
benefiber probiotic
emergen-c pouch
flour tortilla
goli gummies
jakoten
metamucil package
metamucil pouch
nordic naturals bottle
solaray bottle
vitafusion multivites bottle
webber naturals bottle
```


## V2.2.21

### Recognition
There is an improvement recognizing food a bit further away than before. Below the hood the bounding box of the object detections was improved. 

### Package Recognition and Barcode

The SDK will return alternatives (parents, siblings, children ) if available in the PassioIDAttributes of Package Recognition and Barcode
### Models
* Number of food items recognized via HNN: 4128
* Nutrition database version: passio_nutrition.4129.0.301
* Number of products recognized via OCR: 18225 


## V2.2.19
### Models
* Number of food items recognized via HNN: 4136
* Nutrition database version: passio_nutrition.4137.0.301
* Number of products recognized via OCR: 14288 

## V2.2.17
### Models 
* Number of food items recognized via HNN: 4154
* Nutrition database version: passio_nutrition.4155.0.301
* Number of products recognized via OCR: 9848 

## V2.2.15
### Models 
* Number of food items recognized via HNN: 4153
* Nutrition database version: passio_nutrition.4154.0.301
* Number of products recognized via OCR: 6401 

## V2.2.13
### Models 
* Number of food items recognized via HNN: 4175
* Nutrition database version: passio_nutrition.4176.0.301

### Additional API 
```swift
public struct PassioMetadataService {

    public var passioMetadata: PassioNutritionAISDK.PassioMetadata? { get }

    public var getModelNames: [String]? { get }

    public var getlabelIcons: [PassioNutritionAISDK.PassioID : PassioNutritionAISDK.PassioID]? { get }

    public func getPassioIDs(byModelName: String) -> [PassioNutritionAISDK.PassioID]?

    public func getLabel(passioID: PassioNutritionAISDK.PassioID, languageCode: String = "en") -> String?

    public init()
}
```

## V2.2.11

### No Breaking API changes
### Models 
* Number of food items recognized via HNN: 4175
* Nutrition database version: passio_nutrition.4176.0.301

## V2.2.9

### No API changes
### Models 
* Number of food items recognized via HNN: 4164
* Nutrition database version: passio_nutrition.4165.0.301


## V2.2.7

### No API changes
### Models 
* Number of food items recognized via HNN: 4122
* Nutrition database version: passio_nutrition.4123.0.301

### Under the hood
* The SDK will recognize food only if the phone is stable. This improves overall recognition results and the weight calculations stability. 

## V2.2.5
### No API changes
### Models 
* Number of food items recognized via HNN: 4111
* Nutrition database version: passio_nutrition.4111.0.301



## V2.2.3
### API changes

Added to PassioConfiguration a new variable onlyUseLatestModels. If set to true the SDK will not use previously installed models. 
```swift
public struct PassioConfiguration : Equatable {
/// Only use latest models. Don't use models previously installed.
public var onlyUseLatestModels = false
```

### Models 
* Number of food items recognized via HNN: 4099
* Nutrition database version: passio_nutrition.4100.0.301


## V2.2.2 
### API changes

```swift
    public func removeVidoeLayer()
```
was renamed

```swift 
    public func removeVideoLayer()
```

### Models 

No updates

## V2.2.1 

### Models 

* Number of food items recognized via HNN: 4091
* Nutrition database version: passio_nutrition.4091.0.301

### API changes

The food search by text is now using fuzzy string search with returns search results even if the use is spelling mistakes or similar string. We had change the function from direct returns to completion results

```swift 
public func searchForFood(byText: String) -> [PassioNutritionAISDK.PassioIDAndName]
```
to
```swift
public func searchForFood(byText: String, completion: @escaping ([PassioNutritionAISDK.PassioIDAndName]) -> Void)
```

### API additions

Add to the  AmountEstimate protocol , moveDevice variable
```swift 
public protocol AmountEstimate {
    /// Scanned Volume estimate in ml
    var volumeEstimate: Double? { get }
    /// Scanned Amount in grams
    var weightEstimate: Double? { get }
    /// The quality of the estimate (eventually for feedback to the user or SDK-based app developer)
    var estimationQuality: PassioNutritionAISDK.EstimationQuality? { get }
    /// Hints how to move the device for better estimation.
    var moveDevice: PassioNutritionAISDK.MoveDirection? { get }
    /// The Angel in radians from the perpendicular surface.
    var viewingAngle: Double? { get }
}
```

```swift
public enum MoveDirection : String {
    case away
    case ok
    case up
    case down
    case around
}
```