# Passio PassioNutritionAISDK 

## Version  2.2.23
```Swift
import AVFoundation
import Accelerate
import CommonCrypto
import CoreML
import CoreMedia
import CoreMotion
import Foundation
import Metal
import MetalPerformanceShaders
import SQLite3
import UIKit
import VideoToolbox
import Vision
import _Concurrency
import _StringProcessing
import simd

/// Returning all information of Amount estimation and directions how to move the device for better estimation
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

public struct ArchitectureStructure : Codable {

    public let modelName: String?

    public let modelId: String?

    public let datasetId: String?

    public let trainingRunId: String?

    public let filename: PassioNutritionAISDK.FileName?

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// Barcode (typealias String) is the string representation of the barcode id
public typealias Barcode = String

/// The BarcodeCandidate protocol returns the barcode candidate.
public protocol BarcodeCandidate {

    /// Passio ID recognized by the model
    var value: String { get }

    /// boundingBox CGRect representing the predicted bounding box in normalized coordinates.
    var boundingBox: CGRect { get }
}

/// Implement the BarcodeDetectionDelegate protocol to receive delegate methods from the object detection. Barcode detection is optional and initiated when starting Object Detection or Classification.
public protocol BarcodeDetectionDelegate : AnyObject {

    /// Called when a barcode is detected.
    /// - Parameter barcodes: Array of BarcodeCandidate
    func barcodeResult(barcodes: [PassioNutritionAISDK.BarcodeCandidate])
}

/// The ClassificationCandidate protocol returns the classification candidate result delegate.
public protocol ClassificationCandidate {

    /// PassioID recognized by the MLModel
    var passioID: PassioNutritionAISDK.PassioID { get }

    /// Confidence (0.0 to 1.0) of the associated PassioID recognized by the MLModel
    var confidence: Double { get }
}

/// The visual food candidates
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

public struct EnsembleArchitecture : Codable {

    public let name: String

    public let structure: [PassioNutritionAISDK.ArchitectureStructure]

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

public enum EstimationQuality : String {

    case good

    case fair

    case poor

    case noEstimation

    /// Creates a new instance with the specified raw value.
    ///
    /// If there is no value of the type that corresponds with the specified raw
    /// value, this initializer returns `nil`. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     print(PaperSize(rawValue: "Legal"))
    ///     // Prints "Optional("PaperSize.Legal")"
    ///
    ///     print(PaperSize(rawValue: "Tabloid"))
    ///     // Prints "nil"
    ///
    /// - Parameter rawValue: The raw value to use for the new instance.
    public init?(rawValue: String)

    /// The raw type that can be used to represent all values of the conforming
    /// type.
    ///
    /// Every distinct value of the conforming type has a corresponding unique
    /// value of the `RawValue` type, but there may be values of the `RawValue`
    /// type that don't have a corresponding value of the conforming type.
    public typealias RawValue = String

    /// The corresponding value of the raw type.
    ///
    /// A new instance initialized with `rawValue` will be equivalent to this
    /// instance. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     let selectedSize = PaperSize.Letter
    ///     print(selectedSize.rawValue)
    ///     // Prints "Letter"
    ///
    ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
    ///     // Prints "true"
    public var rawValue: String { get }
}

extension EstimationQuality : Equatable {
}

extension EstimationQuality : Hashable {
}

extension EstimationQuality : RawRepresentable {
}

public typealias FileLocalURL = URL

public typealias FileName = String

/// The FoodCandidates protocol returns all four potential candidates. If FoodDetectionConfiguration is not set only visual candidates will be returned.
public protocol FoodCandidates {

    /// The visual candidates returned from the recognition
    var detectedCandidates: [PassioNutritionAISDK.DetectedCandidate] { get }

    /// The Barcode candidates if available
    var barcodeCandidates: [PassioNutritionAISDK.BarcodeCandidate]? { get }

    /// The packaged food candidates if available
    var packagedFoodCandidates: [PassioNutritionAISDK.PackagedFoodCandidate]? { get }

    var deviceStability: Double? { get }
}

/// FoodDetectionConfiguration is need to configure the food detection
public struct FoodDetectionConfiguration {

    /// Only set to false if you don't want to use the ML Models to detect food.
    public var detectVisual: Bool

    /// Select the right Volume Detection Mode
    public var volumeDetectionMode: PassioNutritionAISDK.VolumeDetectionMode

    /// Set to true for detecting barcodes
    public var detectBarcodes: Bool

    ///
    public var detectPackagedFood: Bool

    /// Detect and decipher the Nutrition Facts label
    public var detectNutritionFacts: Bool

    /// Change this if you would like to control the resolution of the image you get back in the delegate. Changing this value will not change the visula recognition results.
    public var sessionPreset: AVCaptureSession.Preset

    /// The frequency of sending images for the recognitions models. The default is set to two pre seconds. Increasing this value will require more resources from the device.
    public var framesPerSecond: PassioNutritionAISDK.PassioNutritionAI.FramesPerSecond

    public init(detectVisual: Bool = true, volumeDetectionMode: PassioNutritionAISDK.VolumeDetectionMode = .none, detectBarcodes: Bool = false, detectPackagedFood: Bool = false, nutritionFacts: Bool = false)
}

/// Implement the FoodRecognitionDelegate protocol to receive delegate methods from the FoodRecognition
public protocol FoodRecognitionDelegate : AnyObject {

    /// Delegate function for food recognition
    /// - Parameters:
    ///   - candidates: Food candidates
    ///   - image: Image used for detection
    func recognitionResults(candidates: PassioNutritionAISDK.FoodCandidates?, image: UIImage?, nutritionFacts: PassioNutritionAISDK.PassioNutritionFacts?)
}

public enum IconSize : String {

    case px90

    case px180

    case px360

    /// Creates a new instance with the specified raw value.
    ///
    /// If there is no value of the type that corresponds with the specified raw
    /// value, this initializer returns `nil`. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     print(PaperSize(rawValue: "Legal"))
    ///     // Prints "Optional("PaperSize.Legal")"
    ///
    ///     print(PaperSize(rawValue: "Tabloid"))
    ///     // Prints "nil"
    ///
    /// - Parameter rawValue: The raw value to use for the new instance.
    public init?(rawValue: String)

    /// The raw type that can be used to represent all values of the conforming
    /// type.
    ///
    /// Every distinct value of the conforming type has a corresponding unique
    /// value of the `RawValue` type, but there may be values of the `RawValue`
    /// type that don't have a corresponding value of the conforming type.
    public typealias RawValue = String

    /// The corresponding value of the raw type.
    ///
    /// A new instance initialized with `rawValue` will be equivalent to this
    /// instance. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     let selectedSize = PaperSize.Letter
    ///     print(selectedSize.rawValue)
    ///     // Prints "Letter"
    ///
    ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
    ///     // Prints "true"
    public var rawValue: String { get }
}

extension IconSize : Equatable {
}

extension IconSize : Hashable {
}

extension IconSize : RawRepresentable {
}

public struct LabelMetaData : Codable {

    public let displayName: String?

    public let synonyms: [String : [PassioNutritionAISDK.SynonymLang]]?

    public let models: [String]?

    public let labelId: String

    public let description: String?

    public var modelName: String? { get }

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

public enum MealTime {

    case breakfast

    case lunch

    case dinner

    case snack

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.MealTime, b: PassioNutritionAISDK.MealTime) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }
}

extension MealTime : Equatable {
}

extension MealTime : Hashable {
}

public struct MeasurementIU {

    public var value: Double

    public let unit: String
}

public enum MoveDirection : String {

    case away

    case ok

    case up

    case down

    case around

    /// Creates a new instance with the specified raw value.
    ///
    /// If there is no value of the type that corresponds with the specified raw
    /// value, this initializer returns `nil`. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     print(PaperSize(rawValue: "Legal"))
    ///     // Prints "Optional("PaperSize.Legal")"
    ///
    ///     print(PaperSize(rawValue: "Tabloid"))
    ///     // Prints "nil"
    ///
    /// - Parameter rawValue: The raw value to use for the new instance.
    public init?(rawValue: String)

    /// The raw type that can be used to represent all values of the conforming
    /// type.
    ///
    /// Every distinct value of the conforming type has a corresponding unique
    /// value of the `RawValue` type, but there may be values of the `RawValue`
    /// type that don't have a corresponding value of the conforming type.
    public typealias RawValue = String

    /// The corresponding value of the raw type.
    ///
    /// A new instance initialized with `rawValue` will be equivalent to this
    /// instance. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     let selectedSize = PaperSize.Letter
    ///     print(selectedSize.rawValue)
    ///     // Prints "Letter"
    ///
    ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
    ///     // Prints "true"
    public var rawValue: String { get }
}

extension MoveDirection : Equatable {
}

extension MoveDirection : Hashable {
}

extension MoveDirection : RawRepresentable {
}

/// The ObjectDetectionCandidate protocol returns the object detection result
public protocol ObjectDetectionCandidate : PassioNutritionAISDK.ClassificationCandidate {

    /// boundingBox CGRect representing the predicted bounding box in normalized coordinates.
    var boundingBox: CGRect { get }
}

public protocol PackagedFoodCandidate {

    var packagedFoodCode: PassioNutritionAISDK.PackagedFoodCode { get }

    var confidence: Double { get }
}

/// packagedFoodCode (typealias String) is the string representation of the PackagedFoodCode id
public typealias PackagedFoodCode = String

/// PassioAlternative is an alternative to a food from the Database
public struct PassioAlternative : Codable, Equatable, Hashable {

    public let passioID: PassioNutritionAISDK.PassioID

    public var name: String { get }

    public let quantity: Double?

    public let unitName: String?

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioAlternative, b: PassioNutritionAISDK.PassioAlternative) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioConfiguration is needed configure the SDK with the following options:
public struct PassioConfiguration : Equatable {

    /// This is the key you have received from Passio. A valid key must be used.
    public var key: String

    /// If you have chosen to remove the files from the SDK and provide the SDK different URLs for this files please use this variable.
    public var filesLocalURLs: [PassioNutritionAISDK.FileLocalURL]?

    /// If you set this option to true, the SDK will download the models relevant for this version from Passio's bucket.
    public var sdkDownloadsModels: Bool

    /// If you have problems configuring the SDK, set debugMode = 1 to get more debugging information.
    public var debugMode: Int

    /// If you set allowInternetConnection = false without working with Passio the SDK will not work. The SDK will not connect to the internet for key validations, barcode data and packaged food data.
    public var allowInternetConnection: Bool

    public init(key: String)

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioConfiguration, b: PassioNutritionAISDK.PassioConfiguration) -> Bool
}

/// PassioFoodItemData contains all the information for a Food Item Data.
public struct PassioFoodItemData : Equatable, Codable {

    public var passioID: PassioNutritionAISDK.PassioID { get }

    public var name: String { get }

    public var selectedQuantity: Double { get }

    public var selectedUnit: String { get }

    public var entityType: PassioNutritionAISDK.PassioIDEntityType { get }

    public var servingUnits: [PassioNutritionAISDK.PassioServingUnit] { get }

    public var servingSizes: [PassioNutritionAISDK.PassioServingSize] { get }

    public var ingredientsDescription: String? { get }

    public var barcode: PassioNutritionAISDK.Barcode? { get }

    public var foodOrigins: [PassioNutritionAISDK.PassioFoodOrigin]? { get }

    public var isOpenFood: Bool { get }

    public var computedWeight: Measurement<UnitMass> { get }

    public var parents: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var parentsPassioID: [PassioNutritionAISDK.PassioID]? { get }

    public var children: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var childrenPassioID: [PassioNutritionAISDK.PassioID]? { get }

    public var siblings: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var siblingsPassioID: [PassioNutritionAISDK.PassioID]? { get }

    public var totalCalories: Measurement<UnitEnergy>? { get }

    public var totalCarbs: Measurement<UnitMass>? { get }

    public var totalFat: Measurement<UnitMass>? { get }

    public var totalProteins: Measurement<UnitMass>? { get }

    public var totalSaturatedFat: Measurement<UnitMass>? { get }

    public var totalTransFat: Measurement<UnitMass>? { get }

    public var totalMonounsaturatedFat: Measurement<UnitMass>? { get }

    public var totalPolyunsaturatedFat: Measurement<UnitMass>? { get }

    public var totalCholesterol: Measurement<UnitMass>? { get }

    public var totalSodium: Measurement<UnitMass>? { get }

    public var totalFibers: Measurement<UnitMass>? { get }

    public var totalSugars: Measurement<UnitMass>? { get }

    public var totalSugarsAdded: Measurement<UnitMass>? { get }

    public var totalVitaminD: Measurement<UnitMass>? { get }

    public var totalCalcium: Measurement<UnitMass>? { get }

    public var totalIron: Measurement<UnitMass>? { get }

    public var totalPotassium: Measurement<UnitMass>? { get }

    public var totalVitaminA: PassioNutritionAISDK.MeasurementIU? { get }

    public var totalVitaminC: Measurement<UnitMass>? { get }

    public var totalAlcohol: Measurement<UnitMass>? { get }

    public var totalSugarAlcohol: Measurement<UnitMass>? { get }

    public var totalVitaminB12Added: Measurement<UnitMass>? { get }

    public var totalVitaminB12: Measurement<UnitMass>? { get }

    public var totalVitaminB6: Measurement<UnitMass>? { get }

    public var totalVitaminE: Measurement<UnitMass>? { get }

    public var totalVitaminEAdded: Measurement<UnitMass>? { get }

    public var totalMagnesium: Measurement<UnitMass>? { get }

    public var totalPhosphorus: Measurement<UnitMass>? { get }

    public var totalIodine: Measurement<UnitMass>? { get }

    public var summary: String { get }

    public mutating func setFoodItemDataServingSize(unit: String, quantity: Double) -> Bool

    public mutating func setServingUnitKeepWeight(unitName: String) -> Bool

    public init(upcProduct: PassioNutritionAISDK.UPCProduct) throws

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioFoodItemData, b: PassioNutritionAISDK.PassioFoodItemData) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

public enum PassioFoodItemDataError : LocalizedError {

    case noUnitMassInServingSizes

    /// A localized message describing what error occurred.
    public var errorDescription: String? { get }

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioFoodItemDataError, b: PassioNutritionAISDK.PassioFoodItemDataError) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }
}

extension PassioFoodItemDataError : Equatable {
}

extension PassioFoodItemDataError : Hashable {
}

public struct PassioFoodOrigin : Codable, Equatable {

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioFoodOrigin, b: PassioNutritionAISDK.PassioFoodOrigin) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioFoodRecipe contains the list of ingredient and their amounts 
public struct PassioFoodRecipe : Equatable, Codable {

    public var passioID: PassioNutritionAISDK.PassioID { get }

    public var name: String { get }

    public var servingSizes: [PassioNutritionAISDK.PassioServingSize] { get }

    public var servingUnits: [PassioNutritionAISDK.PassioServingUnit] { get }

    public var selectedUnit: String { get }

    public var selectedQuantity: Double { get }

    public var isOpenFood: Bool { get }

    public var foodItems: [PassioNutritionAISDK.PassioFoodItemData] { get }

    public var computedWeight: Measurement<UnitMass> { get }

    public init(passioID: PassioNutritionAISDK.PassioID, name: String, foodItems: [PassioNutritionAISDK.PassioFoodItemData], selectedUnit: String, selectedQuantity: Double, servingSizes: [PassioNutritionAISDK.PassioServingSize], servingUnits: [PassioNutritionAISDK.PassioServingUnit])

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioFoodRecipe, b: PassioNutritionAISDK.PassioFoodRecipe) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioID (typealias String) is used throughout the SDK, food and other objects are identified by PassioID. All attributes (names, nutrition etc..) are referred by PassioID.
public typealias PassioID = String

public struct PassioIDAndName {

    public let passioID: PassioNutritionAISDK.PassioID

    public let name: String

    public init(passioID: PassioNutritionAISDK.PassioID, name: String)
}

/// PassioIDAttributes contains all the attributes for a PassioID.
public struct PassioIDAttributes : Equatable, Codable {

    public var passioID: PassioNutritionAISDK.PassioID { get }

    public var name: String { get }

    public var entityType: PassioNutritionAISDK.PassioIDEntityType { get }

    public var parents: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var children: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var siblings: [PassioNutritionAISDK.PassioAlternative]? { get }

    public var passioFoodItemData: PassioNutritionAISDK.PassioFoodItemData? { get }

    public var recipe: PassioNutritionAISDK.PassioFoodRecipe? { get }

    public var isOpenFood: Bool { get }

    public init(passioID: PassioNutritionAISDK.PassioID, name: String, foodItemDataForDefault: PassioNutritionAISDK.PassioFoodItemData?, entityType: PassioNutritionAISDK.PassioIDEntityType = .barcode)

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioIDAttributes, b: PassioNutritionAISDK.PassioIDAttributes) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioIDEntityType is The entity Type of the PassioID Attributes.
public enum PassioIDEntityType : String, CaseIterable, Codable {

    case group

    case item

    case recipe

    case barcode

    case packagedFoodCode

    case favorite

    case nutritionFacts

    /// Creates a new instance with the specified raw value.
    ///
    /// If there is no value of the type that corresponds with the specified raw
    /// value, this initializer returns `nil`. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     print(PaperSize(rawValue: "Legal"))
    ///     // Prints "Optional("PaperSize.Legal")"
    ///
    ///     print(PaperSize(rawValue: "Tabloid"))
    ///     // Prints "nil"
    ///
    /// - Parameter rawValue: The raw value to use for the new instance.
    public init?(rawValue: String)

    /// A type that can represent a collection of all values of this type.
    public typealias AllCases = [PassioNutritionAISDK.PassioIDEntityType]

    /// The raw type that can be used to represent all values of the conforming
    /// type.
    ///
    /// Every distinct value of the conforming type has a corresponding unique
    /// value of the `RawValue` type, but there may be values of the `RawValue`
    /// type that don't have a corresponding value of the conforming type.
    public typealias RawValue = String

    /// A collection of all values of this type.
    public static var allCases: [PassioNutritionAISDK.PassioIDEntityType] { get }

    /// The corresponding value of the raw type.
    ///
    /// A new instance initialized with `rawValue` will be equivalent to this
    /// instance. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     let selectedSize = PaperSize.Letter
    ///     print(selectedSize.rawValue)
    ///     // Prints "Letter"
    ///
    ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
    ///     // Prints "true"
    public var rawValue: String { get }
}

extension PassioIDEntityType : Equatable {
}

extension PassioIDEntityType : Hashable {
}

extension PassioIDEntityType : RawRepresentable {
}

public struct PassioMetadata : Codable {

    public let projectId: String

    public let ensembleId: String?

    public let ensembleVersion: Int?

    public let architecture: PassioNutritionAISDK.EnsembleArchitecture?

    public var labelMetadata: [PassioNutritionAISDK.PassioID : PassioNutritionAISDK.LabelMetaData]? { get }

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

public struct PassioMetadataService {

    public var passioMetadata: PassioNutritionAISDK.PassioMetadata? { get }

    public var getModelNames: [String]? { get }

    public var getlabelIcons: [PassioNutritionAISDK.PassioID : PassioNutritionAISDK.PassioID]? { get }

    public func getPassioIDs(byModelName: String) -> [PassioNutritionAISDK.PassioID]?

    public func getLabel(passioID: PassioNutritionAISDK.PassioID, languageCode: String = "en") -> String?

    public init(metatadataURL: URL? = nil)
}

/// PassioMode will report the mode the SDK is currently in.
public enum PassioMode {

    case notReady

    case isBeingConfigured

    case isDownloadingModels

    case isReadyForDetection

    case failedToConfigure

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioMode, b: PassioNutritionAISDK.PassioMode) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }
}

extension PassioMode : Equatable {
}

extension PassioMode : Hashable {
}

/// Passio SDK - Copyright © 2022 Passio Inc. All rights reserved.
public class PassioNutritionAI {

    final public let filesVersion: Int

    public var version: String { get }

    /// Shared Instance
    public class var shared: PassioNutritionAISDK.PassioNutritionAI { get }

    public var requestCompressedFiles: Bool

    /// Get the PassioStatus directly or implement the PassioStatusDelegate for updates.
    public var status: PassioNutritionAISDK.PassioStatus { get }

    /// Delegate to track PassioStatus changes. You will get the same status via the configure function.
    weak public var statusDelegate: PassioNutritionAISDK.PassioStatusDelegate?

    /// Available frames per seconds. The default set for two (2) fps.
    public enum FramesPerSecond : Int32 {

        case one

        case two

        case three

        case four

        /// Creates a new instance with the specified raw value.
        ///
        /// If there is no value of the type that corresponds with the specified raw
        /// value, this initializer returns `nil`. For example:
        ///
        ///     enum PaperSize: String {
        ///         case A4, A5, Letter, Legal
        ///     }
        ///
        ///     print(PaperSize(rawValue: "Legal"))
        ///     // Prints "Optional("PaperSize.Legal")"
        ///
        ///     print(PaperSize(rawValue: "Tabloid"))
        ///     // Prints "nil"
        ///
        /// - Parameter rawValue: The raw value to use for the new instance.
        public init?(rawValue: Int32)

        /// The raw type that can be used to represent all values of the conforming
        /// type.
        ///
        /// Every distinct value of the conforming type has a corresponding unique
        /// value of the `RawValue` type, but there may be values of the `RawValue`
        /// type that don't have a corresponding value of the conforming type.
        public typealias RawValue = Int32

        /// The corresponding value of the raw type.
        ///
        /// A new instance initialized with `rawValue` will be equivalent to this
        /// instance. For example:
        ///
        ///     enum PaperSize: String {
        ///         case A4, A5, Letter, Legal
        ///     }
        ///
        ///     let selectedSize = PaperSize.Letter
        ///     print(selectedSize.rawValue)
        ///     // Prints "Letter"
        ///
        ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
        ///     // Prints "true"
        public var rawValue: Int32 { get }
    }

    @available(iOS 13.0, *)
    public func configure(passioConfiguration: PassioNutritionAISDK.PassioConfiguration, completion: @escaping (PassioNutritionAISDK.PassioStatus) -> Void)

    /// Shut down the Passio SDK and release all resources
    public func shutDownPassioSDK()

    @available(iOS 13.0, *)
    public func startFoodDetection(detectionConfig: PassioNutritionAISDK.FoodDetectionConfiguration = FoodDetectionConfiguration(), foodRecognitionDelegate: PassioNutritionAISDK.FoodRecognitionDelegate, completion: @escaping (Bool) -> Void)

    /// Stop Food Detection to remove camera completely use public func removeVideoLayer()
    public func stopFoodDetection()

    ///
    /// - Parameters:
    ///   - image:
    ///   - detectionConfig: Detection configuration
    ///   - completion: Array of detection [FoodCandidates]
    @available(iOS 13.0, *)
    public func detectFoodIn(image: UIImage, detectionConfig: PassioNutritionAISDK.FoodDetectionConfiguration = FoodDetectionConfiguration(), slicingRects: [CGRect]? = nil, completion: @escaping (PassioNutritionAISDK.FoodCandidates?) -> Void)

    @available(iOS 13.0, *)
    public func detectBarcodesIn(image: UIImage, completion: @escaping ([PassioNutritionAISDK.BarcodeCandidate]) -> Void)

    @available(iOS 13.0, *)
    public func listFoodEnabledForAmountEstimation() -> [PassioNutritionAISDK.PassioID]

    @available(iOS 13.0, *)
    public func getPreviewLayer() -> AVCaptureVideoPreviewLayer?

    @available(iOS 13.0, *)
    public func getPreviewLayerWithGravity(sessionPreset: AVCaptureSession.Preset = .hd1920x1080, volumeDetectionMode: PassioNutritionAISDK.VolumeDetectionMode = .none, videoGravity: AVLayerVideoGravity = .resizeAspectFill) -> AVCaptureVideoPreviewLayer?

    @available(iOS 13.0, *)
    public func getPreviewLayerForFrontCamera() -> AVCaptureVideoPreviewLayer?

    /// Don't call this function if you need to use the Passio layer again. Only call this function to set the PassioSDK Preview layer to nil
    public func removeVideoLayer()

    /// Use this function to get the bounding box relative to the previewLayerBonds
    /// - Parameter boundingBox: The bounding box from the delegate
    /// - Parameter preview: The preview layer bounding box
    public func transformCGRectForm(boundingBox: CGRect, toRect: CGRect) -> CGRect

    /// Use this call to add personalizedAlternative to a Passio ID
    /// - Parameter personalizedAlternative:
    public func addToPersonalization(personalizedAlternative: PassioNutritionAISDK.PersonalizedAlternative)

    /// Lookup Personalized Alternative For PassioID
    /// - Parameter passioID: PassioID
    /// - Returns: PersonalizedAlternative
    public func lookupPersonalizedAlternativeFor(passioID: PassioNutritionAISDK.PassioID) -> PassioNutritionAISDK.PersonalizedAlternative?

    /// Clean records for one PassioID
    /// - Parameter passioID: PassioID
    public func cleanPersonalizationForVisual(passioID: PassioNutritionAISDK.PassioID)

    /// Clean all records
    public func cleanAllPersonalization()

    @available(iOS 13.0, *)
    public func lookupPassioIDAttributesFor(passioID: PassioNutritionAISDK.PassioID) -> PassioNutritionAISDK.PassioIDAttributes?

    @available(iOS 13.0, *)
    public func lookupNameFor(passioID: PassioNutritionAISDK.PassioID) -> String?

    @available(iOS 13.0, *)
    public func searchForFood(byText: String, completion: @escaping ([PassioNutritionAISDK.PassioIDAndName]) -> Void)

    @available(iOS 13.0, *)
    public func fetchPassioIDAttributesFor(barcode: PassioNutritionAISDK.Barcode, completion: @escaping ((PassioNutritionAISDK.PassioIDAttributes?) -> Void))

    @available(iOS 13.0, *)
    public func lookupIconFor(passioID: PassioNutritionAISDK.PassioID, size: PassioNutritionAISDK.IconSize = IconSize.px90, entityType: PassioNutritionAISDK.PassioIDEntityType = .item) -> (UIImage, Bool)

    @available(iOS 13.0, *)
    public func fetchIconFor(passioID: PassioNutritionAISDK.PassioID, size: PassioNutritionAISDK.IconSize = IconSize.px90, completion: @escaping (UIImage?) -> Void)

    @available(iOS 13.0, *)
    public func fetchPassioIDAttributesFor(packagedFoodCode: PassioNutritionAISDK.PackagedFoodCode, completion: @escaping ((PassioNutritionAISDK.PassioIDAttributes?) -> Void))

    @available(iOS 13.0, *)
    public func lookupAllDescendantsFor(passioID: PassioNutritionAISDK.PassioID) -> [PassioNutritionAISDK.PassioID]

    @available(iOS 13.0, *)
    public var availableVolumeDetectionModes: [PassioNutritionAISDK.VolumeDetectionMode] { get }

    @available(iOS 13.0, *)
    public var bestVolumeDetectionMode: PassioNutritionAISDK.VolumeDetectionMode { get }
}

extension PassioNutritionAI : PassioNutritionAISDK.PassioStatusDelegate {

    public func completedDownloadingAllFiles(filesLocalURLs: [PassioNutritionAISDK.FileLocalURL])

    public func completedDownloadingFile(fileLocalURL: PassioNutritionAISDK.FileLocalURL, filesLeft: Int)

    public func downloadingError(message: String)

    public func passioStatusChanged(status: PassioNutritionAISDK.PassioStatus)

    public func passioProcessing(filesLeft: Int)
}

extension PassioNutritionAI.FramesPerSecond : Equatable {
}

extension PassioNutritionAI.FramesPerSecond : Hashable {
}

extension PassioNutritionAI.FramesPerSecond : RawRepresentable {
}

/// This object will decipher the Nutrition Facts table on packaged food
public class PassioNutritionFacts {

    public init()

    public enum ServingSizeUnit : String {

        case g

        case ml

        /// Creates a new instance with the specified raw value.
        ///
        /// If there is no value of the type that corresponds with the specified raw
        /// value, this initializer returns `nil`. For example:
        ///
        ///     enum PaperSize: String {
        ///         case A4, A5, Letter, Legal
        ///     }
        ///
        ///     print(PaperSize(rawValue: "Legal"))
        ///     // Prints "Optional("PaperSize.Legal")"
        ///
        ///     print(PaperSize(rawValue: "Tabloid"))
        ///     // Prints "nil"
        ///
        /// - Parameter rawValue: The raw value to use for the new instance.
        public init?(rawValue: String)

        /// The raw type that can be used to represent all values of the conforming
        /// type.
        ///
        /// Every distinct value of the conforming type has a corresponding unique
        /// value of the `RawValue` type, but there may be values of the `RawValue`
        /// type that don't have a corresponding value of the conforming type.
        public typealias RawValue = String

        /// The corresponding value of the raw type.
        ///
        /// A new instance initialized with `rawValue` will be equivalent to this
        /// instance. For example:
        ///
        ///     enum PaperSize: String {
        ///         case A4, A5, Letter, Legal
        ///     }
        ///
        ///     let selectedSize = PaperSize.Letter
        ///     print(selectedSize.rawValue)
        ///     // Prints "Letter"
        ///
        ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
        ///     // Prints "true"
        public var rawValue: String { get }
    }

    public var foundNutritionFactsLabel: Bool { get }

    final public let titleNutritionFacts: String

    final public let titleServingSize: String

    final public let titleCalories: String

    final public let titleTotalFat: String

    final public let titleTotalCarbs: String

    final public let titleProtein: String

    public var servingSizeQuantity: Double

    public var servingSizeUnitName: String?

    public var servingSizeGram: Double?

    public var servingSizeUnit: PassioNutritionAISDK.PassioNutritionFacts.ServingSizeUnit

    public var calories: Double?

    public var fat: Double?

    public var carbs: Double?

    public var protein: Double?

    public var isManuallyEdited: Bool

    public var servingSizeText: String { get }

    public var caloriesText: String { get }

    public var fatText: String { get }

    public var carbsText: String { get }

    public var proteinText: String { get }

    public var isCompleted: Bool { get }

    public var description: String { get }

    public func clearAll()
}

extension PassioNutritionFacts {

    public func createPassioIDAttributes(foodName: String) -> PassioNutritionAISDK.PassioIDAttributes
}

extension PassioNutritionFacts.ServingSizeUnit : Equatable {
}

extension PassioNutritionFacts.ServingSizeUnit : Hashable {
}

extension PassioNutritionFacts.ServingSizeUnit : RawRepresentable {
}

/// PassioSDKError will return the error with errorDescription if the configuration has failed. 
public enum PassioSDKError : LocalizedError {

    case canNotRunOnSimulator

    case keyNotValid

    case licensedKeyHasExpired

    case modelsNotValid

    case modelsDownloadFailed

    case noModelsFilesFound

    case noInternetConnection

    case notLicensedForThisProject

    /// A localized message describing what error occurred.
    public var errorDescription: String? { get }

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioSDKError, b: PassioNutritionAISDK.PassioSDKError) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }
}

extension PassioSDKError : Equatable {
}

extension PassioSDKError : Hashable {
}

/// PassioServingSize for food Item Data
public struct PassioServingSize : Codable, Equatable, Hashable {

    public let quantity: Double

    public let unitName: String

    public init(quantity: Double, unitName: String)

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioServingSize, b: PassioNutritionAISDK.PassioServingSize) -> Bool

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    public func hash(into hasher: inout Hasher)

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    public var hashValue: Int { get }

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioServingUnit for food Item Data
public struct PassioServingUnit : Equatable, Codable {

    public let unitName: String

    public let weight: Measurement<UnitMass>

    public init(unitName: String, weight: Measurement<UnitMass>)

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PassioServingUnit, b: PassioNutritionAISDK.PassioServingUnit) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// PassioStatus is returned at the end of the configuration of the SDK.
public struct PassioStatus {

    /// The mode had several values . isReadyForDetection is full success
    public var mode: PassioNutritionAISDK.PassioMode { get }

    /// If the SDK is missing files or new files could be used. It will send the list of files needed for the update.
    public var missingFiles: [PassioNutritionAISDK.FileName]? { get }

    /// A string with more verbose information related to the configuration of the SDK
    public var debugMessage: String? { get }

    /// The error in case the SDK failed to configure
    public var error: PassioNutritionAISDK.PassioSDKError? { get }

    /// The version of the latest models that are now used by the SDK.
    public var activeModels: Int? { get }
}

/// Implement the protocol to receive status updates
public protocol PassioStatusDelegate : AnyObject {

    func passioStatusChanged(status: PassioNutritionAISDK.PassioStatus)

    func passioProcessing(filesLeft: Int)

    func completedDownloadingAllFiles(filesLocalURLs: [PassioNutritionAISDK.FileLocalURL])

    func completedDownloadingFile(fileLocalURL: PassioNutritionAISDK.FileLocalURL, filesLeft: Int)

    func downloadingError(message: String)
}

public struct PersonalizedAlternative : Codable, Equatable {

    public let visualPassioID: PassioNutritionAISDK.PassioID

    public let nutritionalPassioID: PassioNutritionAISDK.PassioID

    public var servingUnit: String?

    public var servingSize: Double?

    public init(visualPassioID: PassioNutritionAISDK.PassioID, nutritionalPassioID: PassioNutritionAISDK.PassioID, servingUnit: String?, servingSize: Double?)

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    public static func == (a: PassioNutritionAISDK.PersonalizedAlternative, b: PassioNutritionAISDK.PersonalizedAlternative) -> Bool

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

public struct SynonymLang : Codable {

    public let synonym: String?

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// UPC Product decoding struct
public struct UPCProduct : Codable {

    public let id: String

    public let name: String

    public let nutrients: [PassioNutritionAISDK.UPCProduct.NutrientUPC]?

    public let branded: PassioNutritionAISDK.UPCProduct.Branded?

    public let origin: [PassioNutritionAISDK.UPCProduct.Origin]?

    public let portions: [PassioNutritionAISDK.UPCProduct.Portion]?

    public let qualityScore: String?

    public let licenseCopy: String?

    /// Component of UPC Product decoding struct
    public struct NutrientUPC : Codable {

        public let id: Double?

        public let nutrient: PassioNutritionAISDK.UPCProduct.InternalNutrient?

        public let amount: Double?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Component of UPC Product decoding struct
    public struct InternalNutrient : Codable {

        public let name: String?

        public let unit: String?

        public let shortName: String?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Component of UPC Product decoding struct
    public struct Branded : Codable {

        public let owner: String?

        public let upc: String?

        public let productCode: String?

        public let ingredients: String?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Component of UPC Product decoding struct
    public struct Origin : Codable {

        public let source: String?

        public let id: String?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Component of UPC Product decoding struct
    public struct Portion : Codable {

        public let weight: PassioNutritionAISDK.UPCProduct.Weight?

        public let name: String?

        public let quantity: Double?

        public let suggestedQuantity: [Double]?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Component of UPC Product decoding struct
    public struct Weight : Codable {

        public let unit: String?

        public let value: Double?

        /// Encodes this value into the given encoder.
        ///
        /// If the value fails to encode anything, `encoder` will encode an empty
        /// keyed container in its place.
        ///
        /// This function throws an error if any values are invalid for the given
        /// encoder's format.
        ///
        /// - Parameter encoder: The encoder to write data to.
        public func encode(to encoder: Encoder) throws

        /// Creates a new instance by decoding from the given decoder.
        ///
        /// This initializer throws an error if reading from the decoder fails, or
        /// if the data read is corrupted or otherwise invalid.
        ///
        /// - Parameter decoder: The decoder to read data from.
        public init(from decoder: Decoder) throws
    }

    /// Encodes this value into the given encoder.
    ///
    /// If the value fails to encode anything, `encoder` will encode an empty
    /// keyed container in its place.
    ///
    /// This function throws an error if any values are invalid for the given
    /// encoder's format.
    ///
    /// - Parameter encoder: The encoder to write data to.
    public func encode(to encoder: Encoder) throws

    /// Creates a new instance by decoding from the given decoder.
    ///
    /// This initializer throws an error if reading from the decoder fails, or
    /// if the data read is corrupted or otherwise invalid.
    ///
    /// - Parameter decoder: The decoder to read data from.
    public init(from decoder: Decoder) throws
}

/// VolumeDetectionMode for detection volume.
public enum VolumeDetectionMode : String {

    /// Using best technology available in this order dualWideCamera, dualCamera or none if the device is not capable of detection volume.
    case auto

    /// If dualWideCamera is not available the mode will not fall back to dualCamera
    case dualWideCamera

    case none

    /// Creates a new instance with the specified raw value.
    ///
    /// If there is no value of the type that corresponds with the specified raw
    /// value, this initializer returns `nil`. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     print(PaperSize(rawValue: "Legal"))
    ///     // Prints "Optional("PaperSize.Legal")"
    ///
    ///     print(PaperSize(rawValue: "Tabloid"))
    ///     // Prints "nil"
    ///
    /// - Parameter rawValue: The raw value to use for the new instance.
    public init?(rawValue: String)

    /// The raw type that can be used to represent all values of the conforming
    /// type.
    ///
    /// Every distinct value of the conforming type has a corresponding unique
    /// value of the `RawValue` type, but there may be values of the `RawValue`
    /// type that don't have a corresponding value of the conforming type.
    public typealias RawValue = String

    /// The corresponding value of the raw type.
    ///
    /// A new instance initialized with `rawValue` will be equivalent to this
    /// instance. For example:
    ///
    ///     enum PaperSize: String {
    ///         case A4, A5, Letter, Legal
    ///     }
    ///
    ///     let selectedSize = PaperSize.Letter
    ///     print(selectedSize.rawValue)
    ///     // Prints "Letter"
    ///
    ///     print(selectedSize == PaperSize(rawValue: selectedSize.rawValue)!)
    ///     // Prints "true"
    public var rawValue: String { get }
}

extension VolumeDetectionMode : Equatable {
}

extension VolumeDetectionMode : Hashable {
}

extension VolumeDetectionMode : RawRepresentable {
}

extension UIImageView {

    @available(iOS 13.0, *)
    @MainActor public func loadPassioIconBy(passioID: PassioNutritionAISDK.PassioID, entityType: PassioNutritionAISDK.PassioIDEntityType, size: PassioNutritionAISDK.IconSize = .px90, completion: @escaping (PassioNutritionAISDK.PassioID, UIImage) -> Void)
}

extension simd_float4x4 : ContiguousBytes {

    /// Calls the given closure with the contents of underlying storage.
    ///
    /// - note: Calling `withUnsafeBytes` multiple times does not guarantee that
    ///         the same buffer pointer will be passed in every time.
    /// - warning: The buffer argument to the body should not be stored or used
    ///            outside of the lifetime of the call to the closure.
    public func withUnsafeBytes<R>(_ body: (UnsafeRawBufferPointer) throws -> R) rethrows -> R
}

infix operator .+ : DefaultPrecedence

infix operator ./ : DefaultPrecedence


```
<sup>Copyright 2023 Passio Inc</sup>
