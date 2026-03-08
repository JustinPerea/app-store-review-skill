# Privacy Usage Description Keys — Complete Reference

This is the complete mapping of iOS APIs to their required Info.plist privacy keys.
If code (including third-party SDKs) uses any of these APIs, the corresponding key MUST
exist in Info.plist with a specific, descriptive string.

## API-to-Key Mapping

| Info.plist Key | Controls Access To | Common API Patterns to Search For |
|---|---|---|
| `NSCameraUsageDescription` | Camera | `AVCaptureDevice`, `AVCaptureSession`, `UIImagePickerController` (sourceType: .camera), `VNDocumentCameraViewController` |
| `NSMicrophoneUsageDescription` | Microphone | `AVAudioRecorder`, `AVAudioSession` (requestRecordPermission), `AVCaptureDevice` (audio), `SFSpeechAudioBufferRecognitionRequest` |
| `NSPhotoLibraryUsageDescription` | Photo library (read) | `PHPhotoLibrary`, `PHAsset`, `UIImagePickerController` (sourceType: .photoLibrary), `PHPickerViewController` (for write access too) |
| `NSPhotoLibraryAddUsageDescription` | Photo library (write/save only) | `UIImageWriteToSavedPhotosAlbum`, `PHPhotoLibrary.shared().performChanges`, `PHAssetCreationRequest` |
| `NSLocationWhenInUseUsageDescription` | Location (foreground) | `CLLocationManager` (requestWhenInUseAuthorization), `MKMapView` (showsUserLocation) |
| `NSLocationAlwaysAndWhenInUseUsageDescription` | Location (always) | `CLLocationManager` (requestAlwaysAuthorization), significant location changes, geofencing |
| `NSLocationAlwaysUsageDescription` | Location always (legacy, pre-iOS 11) | Same as above, needed alongside AlwaysAndWhenInUse for backward compat |
| `NSContactsUsageDescription` | Contacts | `CNContactStore`, `CNContact`, `ABAddressBook` (legacy) |
| `NSCalendarsUsageDescription` | Calendar events (read + write, pre-iOS 17) | `EKEventStore`, `EKEvent`, `EKCalendar` |
| `NSCalendarsWriteOnlyAccessUsageDescription` | Calendar (write only, iOS 17+) | `EKEventStore` (requestWriteOnlyAccessToEvents) |
| `NSCalendarsFullAccessUsageDescription` | Calendar (full access, iOS 17+) | `EKEventStore` (requestFullAccessToEvents) |
| `NSRemindersUsageDescription` | Reminders (pre-iOS 17) | `EKEventStore` (entityType: .reminder) |
| `NSRemindersFullAccessUsageDescription` | Reminders (full access, iOS 17+) | `EKEventStore` (requestFullAccessToReminders) |
| `NSBluetoothAlwaysUsageDescription` | Bluetooth (iOS 13+) | `CBCentralManager`, `CBPeripheralManager`, `CBPeripheral` |
| `NSBluetoothPeripheralUsageDescription` | Bluetooth (legacy, pre-iOS 13) | Same as above, for backward compat |
| `NSMotionUsageDescription` | Accelerometer / gyroscope / pedometer | `CMMotionManager`, `CMPedometer`, `CMMotionActivityManager`, `CMAltimeter` |
| `NSSpeechRecognitionUsageDescription` | Speech recognition | `SFSpeechRecognizer`, `SFSpeechRecognitionRequest` |
| `NSFaceIDUsageDescription` | Face ID | `LAContext` (evaluatePolicy with biometrics), note: Touch ID doesn't need this |
| `NSHealthShareUsageDescription` | HealthKit (read) | `HKHealthStore` (requestAuthorization, toShare: nil) |
| `NSHealthUpdateUsageDescription` | HealthKit (write) | `HKHealthStore` (requestAuthorization, toShare: ...) |
| `NSHomeKitUsageDescription` | HomeKit | `HMHomeManager`, `HMHome`, `HMAccessory` |
| `NSAppleMusicUsageDescription` | Media library / Apple Music | `MPMediaLibrary`, `MPMediaQuery`, `SKCloudServiceController` |
| `NSSiriUsageDescription` | Siri / SiriKit intents | `INInteraction`, `INIntent`, Siri Shortcuts donation |
| `NFCReaderUsageDescription` | NFC reader | `NFCTagReaderSession`, `NFCNDEFReaderSession` |
| `NSLocalNetworkUsageDescription` | Local network access | `NWBrowser`, `NWListener`, `NWConnection` (to local addresses), Bonjour/mDNS |
| `NSUserTrackingUsageDescription` | App Tracking Transparency (IDFA) | `ATTrackingManager`, `ASIdentifierManager.shared().advertisingIdentifier` |
| `NSNearbyInteractionUsageDescription` | Nearby Interaction (UWB) | `NISession`, `NINearbyPeerConfiguration` |
| `NSGKFriendListUsageDescription` | Game Center friend list | `GKLocalPlayer.loadFriends`, `GKFriendRequestComposeViewController` |
| `NSSensorKitUsageDescription` | SensorKit | `SRSensorReader`, `SRFetchRequest` |
| `NSWorldSensingUsageDescription` | ARKit world sensing | `ARWorldTrackingConfiguration`, `ARSession` with world tracking |
| `NSHandsTrackingUsageDescription` | ARKit hand tracking | `ARHandTrackingConfiguration`, hand tracking in visionOS |
| `NSIdentityUsageDescription` | Digital identity verification | `ASAuthorizationSecurityKeyPublicKeyCredentialProvider` |
| `NSFocusStatusUsageDescription` | Focus status | `INFocusStatusCenter` |
| `NSFileProviderDomainUsageDescription` | File provider | `NSFileProviderManager` |

## Privacy Manifest — Required Reason API Categories

These APIs require a `PrivacyInfo.xcprivacy` declaration with an approved reason code.

### UserDefaults (`NSPrivacyAccessedAPICategoryUserDefaults`)

| Reason Code | When to Use |
|---|---|
| `CA92.1` | App reads/writes data only accessible to the app itself |
| `1C8F.1` | Accessing UserDefaults shared via App Groups |
| `C56D.1` | Third-party SDK accessing UserDefaults |
| `AC6B.1` | Managed app configuration (MDM) |

### File Timestamp (`NSPrivacyAccessedAPICategoryFileTimestamp`)

| Reason Code | When to Use |
|---|---|
| `DDA9.1` | Displaying file timestamps to the user |
| `C617.1` | Accessing timestamps inside app container / app group / CloudKit |
| `3B52.1` | Files the user explicitly granted access to (document picker) |
| `0A2A.1` | Third-party SDK wrapper |

### System Boot Time (`NSPrivacyAccessedAPICategorySystemBootTime`)

| Reason Code | When to Use |
|---|---|
| `35F9.1` | Measuring elapsed time between events or enabling timers |
| `8FFB.1` | Calculating absolute timestamps for server communication |
| `3D61.1` | Third-party SDK wrapper |

### Disk Space (`NSPrivacyAccessedAPICategoryDiskSpace`)

| Reason Code | When to Use |
|---|---|
| `E174.1` | Checking sufficient disk space for writes, or cleanup when low |
| `7D9E.1` | Displaying disk space info to the user |
| `85F4.1` | Third-party SDK wrapper |

### Active Keyboards (`NSPrivacyAccessedAPICategoryActiveKeyboards`)

| Reason Code | When to Use |
|---|---|
| `3EC4.1` | Customizing UI based on active keyboards (on-device only) |
| `54BD.1` | Third-party SDK wrapper |
