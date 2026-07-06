# Bwell Health iOS Decrypted App Teardown

Last updated: 2026-07-05

## Summary

The decrypted iOS app materially changes the earlier iOS evidence boundary.
Before decryption, the App Store package showed Bluetooth, HealthKit, account UI,
upload UI, and a privacy manifest declaring no collected data types. After
decryption and Ghidra review, the iOS executable shows the same Daxin backend
family already observed in Android:

```text
https://tj.daxinhealth.com/
```

The iOS app contains first-party code paths and route strings for user account
management, body-composition upload/history, blood-pressure upload/history,
temperature upload/history, blood-oxygen upload/history, Apple Health writing,
and optional Fitbit upload.

Most importantly, Ghidra recovered a measurement upload path:

```text
BLEHandler::uploadBodyfat:isOfflineData:
HTTPRequestManager::requestUploadBodyFat:success:failure:
```

The recovered upload method builds a request for:

```text
composition/upload
```

This is direct static evidence that the iOS app is not merely a local Bluetooth
reader. It has code to send account-linked body-composition records to the
Daxin backend.

## Artifact Identity

The decrypted app was obtained from an already-installed App Store copy on a
test iPad and decrypted for local research. The decrypted IPA is not published
in this repository.

| Item | Value |
|---|---|
| App name | `Bwell Health` |
| Bundle ID | `com.bytechny.B-WELL` |
| Version | `1.0.19` |
| Build | `2` |
| Decrypted IPA SHA-256 | `E50A24B8C32375DC2CE6AF33110419737992F618E2F3118AEC67903B49EEEABC` |
| Minimum iOS version | `15.6` |
| SDK shown in app metadata | `iphoneos18.4` |

The original Apple-sourced encrypted package previously reviewed had:

| Item | Value |
|---|---|
| App Store ID | `1566290744` |
| Bundle ID | `com.bytechny.B-WELL` |
| Version | `1.0.19` |
| Encrypted IPA SHA-256 | `BF78E25D74AB14DE0B12B4F998A9A3BEEF2DC2714D79A962D6AECE606E2D56D2` |

## Apple Privacy Manifest

The app's `PrivacyInfo.xcprivacy` declares:

```json
{
  "NSPrivacyTracking": false,
  "NSPrivacyCollectedDataTypes": [],
  "NSPrivacyTrackingDomains": [],
  "NSPrivacyAccessedAPITypes": []
}
```

This is a developer-supplied declaration. It is not runtime proof. In this case
it is in tension with the decrypted app, because the executable contains account,
profile, measurement upload, history retrieval, Apple Health, and Fitbit code
paths.

## App Permissions and Capabilities

Bluetooth permission text:

```text
NSBluetoothAlwaysUsageDescription:
App requires your consent to access Bluetooth and Bluetooth device pairing to measure human body data

NSBluetoothPeripheralUsageDescription:
App requires your consent to access Bluetooth and Bluetooth device pairing to measure human body data
```

Apple Health permission text:

```text
NSHealthShareUsageDescription:
This app requires your consent to access Apple Health to sync your measured body data to Apple Health, and turning off this permission will not sync data to Apple Health.

NSHealthUpdateUsageDescription:
This app requires your consent to access Apple Health to sync your measured body data to Apple Health, and turning off this permission will not sync data to Apple Health.
```

Camera/photo permission text:

```text
NSCameraUsageDescription: Use the camera to modify the avatar
NSPhotoLibraryUsageDescription: Use the photo album to modify avatar
```

The app also sets:

```text
NSAppTransportSecurity:
  NSAllowsArbitraryLoads: true
```

That setting permits network loads that normal iOS App Transport Security would
block. It does not prove plain HTTP use by itself, but it weakens the app's
transport-hardening posture.

## Linked Frameworks and SDK Evidence

Notable linked system frameworks:

```text
CoreBluetooth
HealthKit
AVFoundation
Photos
Security
SystemConfiguration
WebKit
UIKit
Foundation
libsqlite3.dylib
```

Notable embedded/static library strings:

```text
AFNetworking
FMDB
IQKeyboardManager
MBProgressHUD
Masonry
MJRefresh
SVProgressHUD
AKPickerView
CSBISV23x242
BisData_V23x242
```

Interpretation:

- `AFNetworking` is the HTTP client stack.
- `FMDB` and `libsqlite3` are the local database layer.
- `CoreBluetooth` is the BLE device layer.
- `HealthKit` is Apple Health sync.
- `CSBISV23x242` / `BisData_V23x242` matches the Chipsea BIS v23x242
  body-composition algorithm lineage found in Android.
- No Tencent Bugly, Firebase Crashlytics, Sentry, or Umeng SDK strings were
  found in this iOS binary. Android does contain Tencent Bugly.

## Backend and API Routes

Base URL:

```text
https://tj.daxinhealth.com/
```

User/account routes:

```text
user/send/validate/code
user/send/validate/code2
user/validate
user/register
user/register4
user/login2
user/update/info
user/addChild
user/deleteChildById
user/retrieve/password
user/retrieve/password2
user/third/login
user/third/register
```

Body-composition routes:

```text
composition/upload
composition/get/page/data
composition/get/data/day
composition/get/data/week
composition/get/data/month
```

Target-weight routes:

```text
user/get/target/weight
user/set/target/weight
```

Other health-device routes:

```text
blood/pressure/upload
blood/pressure/get/page/data/extend
blood/pressure/get/page/data
temperature/upload
temperature/get/page/data
temperature/get/late/data/day/by/page
bloodOxygen/upload
bloodOxygen/get/page/data
common/main
%@/deleteById
```

Fitbit routes:

```text
https://api.fitbit.com/1/user/-/body/log/weight.json
https://api.fitbit.com/1/user/-/body/log/fat.json
https://api.fitbit.com/oauth2/revoke
```

Fitbit appears conditional on saved Fitbit tokens. The first-party Daxin
`composition/upload` path is separate from Fitbit.

## Recovered BLE Measurement Flow

Ghidra recovered:

```text
BT_CBManager::readBodyFatData:
```

Important behavior:

- reads weight from packet offset `+4` as a 16-bit value
- reads fat from packet offset `+6` as a 16-bit value
- reads resistance from packet offset `+0x10`
- reads unit/status marker from packet offset `+0x12`
- formats timestamp fields from packet bytes
- calls `writeToReceivedBodyFatACK`
- validates the body-fat packet
- calls delegate method:

```text
BT_CBManager:didReceivedBodyFatResult:fat:resistance:time:unit:heartRate:
```

This matches the Windows BLE reader model: the scale sends compact direct facts,
not a full expanded body-composition record.

## Recovered History / Offline Flow

Ghidra recovered:

```text
BT_CBManager::readHistoryData:
BT_CBManager::readHeartHistoryData:
BT_CBManager::readHistoryNoMore:
```

History packets decode the same core fields: weight, fat, resistance, timestamp,
unit/status, and optional heart rate. Valid records are passed to:

```text
BT_CBManager:didReceivedHistoryData:fat:resistance:time:unit:heartRate:isSuspectedData:
```

The iOS app therefore has code to read prior/offline/history records from the
scale and acknowledge each record.

## BLE Commands

Recovered command constructors include:

```text
BT_CBManager::writeToGetVersion
BT_CBManager::writeToUpdateClock
BT_CBManager::writeToRequestHistoryDataByUserID:
BT_CBManager::writeHistoryDataACK
BT_CBManager::writeToReceivedBodyFatACK
BT_CBManager::writeToClearData
```

Confirmed command bytes:

| Method | Bytes |
|---|---|
| `writeToGetVersion` | `AB 01 9C` |
| `writeToUpdateClock` | `AB 09 98 ...` |
| `writeToRequestHistoryDataByUserID` | `AB 07/08 9B <7-byte user id payload>` |
| `writeHistoryDataACK` | `AB 02 9B 01` |
| `writeToReceivedBodyFatACK` | `AB 02 A3 00` |
| `writeToClearData` | `AB 02 A1 00` |

These command bytes match the protocol work from Android and the independent
Windows BLE reader.

## User/Profile Command

Ghidra recovered profile/member command behavior in:

```text
BT_CBManager::writeMemberInfo:
ScaleBleManager::sendUserInfo:
```

The command begins:

```text
AB 0E/0F 99 ...
```

The command includes encoded user id bytes plus profile inputs such as age,
sex/gender, height, weight, resistance/impedance, and role type.

Diagnostic strings include:

```text
userId=%@,age=%ld,sex=%ld,height=%ld,weight=%.1f,resistance=%ld,roletype = %ld
```

This confirms that the app sends user/profile context to the scale as part of
measurement setup.

## Chipsea Body-Composition Calculation

Ghidra recovered:

```text
ScaleBleManager::bodyFat:SDKwithWeight:fat:resistance:time:userInfo:
CSBISV23x242::calc_v23x242:uHeight:uWeight:uImpendance:uSex:uAge:uMode:
```

The app combines:

- user height
- scale weight
- scale impedance/resistance
- user sex
- user age
- user role/athlete-normal mode

with the Chipsea BIS v23x242 calculator. Recovered output setters include:

```text
setBfm
setBfp
setSlm
setBmc
setBwm
setBwp
setBmr
setVfr
setMa
setPm
setSmm
setBmi
setLbm
setBw
setOd
setSbc
setBodys
```

Interpretation:

- iOS uses the same Chipsea v23x242 algorithm family as Android.
- iOS appears to compile the Chipsea logic into the main Mach-O instead of
  shipping a separate native library.
- Derived fields are calculated locally before upload.

## Body-Fat Upload Request

Ghidra recovered:

```text
HTTPRequestManager::requestUploadBodyFat:success:failure:
```

The method builds a dictionary and sends it to:

```text
composition/upload
```

A deeper Ghidra export added caller/callee evidence. The body-fat upload method
references the `composition/upload` route, and `BLEHandler::uploadBodyfat:
isOfflineData:` calls the body-fat upload selector:

```text
BLEHandler::uploadBodyfat:isOfflineData:
  -> HTTPRequestManager::requestUploadBodyFat:success:failure:

HTTPRequestManager::requestUploadBodyFat:success:failure:
  -> composition/upload
```

The shared POST helper is:

```text
HTTPRequestManager::sendPOSTParameters:appendUrl:success:failure:
```

That helper builds the final URL from:

```text
https://tj.daxinhealth.com/
```

plus the append URL, logs the URL and parameters, obtains the app HTTP session,
and sends the request through the AFNetworking POST path. This is stronger
than a bare route-string finding: the decompile shows request construction,
Daxin URL construction, and handoff to POST machinery.

Confirmed request keys:

```text
userId
moisture
waterLevel
boneMass
boneLevel
adiposeRate
fatLevel
bmr
bmrLevel
impedance
bmi
bmiLevel
muscleQuantity
muscleLevel
visceralFat
visfatLevel
bodySocre
physicalAge
weight
weightLevel
protein
proteinLevel
boneMineralContent
bmcLevel
heartRate
testDate
```

The iOS upload is not only raw weight. It includes direct scale facts, derived
body-composition values, level/category fields, account/user id, and timestamp.

## Upload Trigger After BLE Measurement

Ghidra recovered:

```text
BLEHandler::uploadBodyfat:isOfflineData:
```

Important behavior:

1. Updates local user weight.
2. If fat/resistance exists, updates local user resistance.
3. Writes body data to Apple Health through `SSHealthKitManager`.
4. Checks for Fitbit tokens in `NSUserDefaults`.
5. If Fitbit tokens exist, uploads weight and fat to Fitbit.
6. Calls `requestUploadBodyFat:success:failure:`.

This is the strongest iOS privacy finding:

> After body-fat processing reaches `uploadBodyfat:isOfflineData:`, the recovered
> code calls the first-party Daxin body-fat upload method. Fitbit is optional;
> Daxin upload is the app's own backend path.

The recovered pseudocode shows this order:

1. Update local user weight.
2. If resistance is available, update local user resistance.
3. Convert the test time into an `NSDate`.
4. Write body data to Apple Health through `SSHealthKitManager`.
5. Check `NSUserDefaults` for `FitbitAccessToken` and `FitbitTokenType`.
6. If both Fitbit values exist, upload Fitbit weight/fat.
7. Call `requestUploadBodyFat:success:failure:` for Daxin upload.

What still needs dynamic testing:

- Whether this handler is reached for every final measurement.
- Whether login is required before a server upload can complete.
- Whether upstream flow suppresses upload under no-account/offline conditions.
- Exact runtime request bodies under each user path.

## User Context Before Upload

The deeper Ghidra pass clarifies an important upstream condition. The iOS app
does not appear to create body-fat result objects without a user context.

For live readings, the recovered method:

```text
ScaleBleManager::BT_CBManager:didReceivedBodyFatResult:fat:resistance:time:unit:heartRate:
```

checks for a current user before constructing a `BodyFat` result. If no current
user object is available, the recovered pseudocode exits before building the
result object. If a current user exists, the app builds the result from scale
facts such as weight, fat, resistance, time, unit, and heart-rate value plus
profile context, then dispatches the measurement delegate.

For history/offline readings, the recovered method:

```text
ScaleBleManager::BT_CBManager:didReceivedHistoryData:fat:resistance:time:unit:heartRate:isSuspectedData:
```

also requires an offline/current user object before constructing
`OfflineBodyFat`. If that user context exists, the app builds the offline
measurement from scale packet values and profile context, sets unit, heart-rate,
and suspected/offline state, and dispatches the offline-measurement delegate.

This narrows the claim:

- The iOS app contains code paths that build account/profile-linked body
  composition upload payloads.
- Static evidence indicates live and offline measurement-object construction is
  user-context gated.
- A no-login/no-profile iOS workflow still needs runtime testing.
- Once a body-fat object reaches `BLEHandler::uploadBodyfat:isOfflineData:`, the
  Daxin upload method is called in the recovered pseudocode.

## Local Offline Storage

Static SQL and first-launch database inspection confirmed an `OfflineBodyFat`
table with body-composition fields including weight, fat, moisture, BMR,
visceral fat, muscle, bone, BMI, protein, bone muscle, resistance, body age,
score, user id, time, unit, suspected/offline marker, and heart rate.

Related selectors:

```text
insertOfflineBodyFat:
deleteOfflineBodyFatWithDbID:
selectAllSuspectedOfflineDataWithUserID:
selectAllUnsuspectedOfflineDataWithUserID:
selectLastSuspectedOfflineDataWithUserID:
checkOfflineDataWithUserId:
startToRequestOfflineData
uploadBodyfat:isOfflineData:
```

This supports the model that offline/history readings can be stored locally and
later uploaded.

A deeper Ghidra pass also recovered offline queue helpers:

```text
DBUtil::insertOfflineBodyFat:
DBUtil::deleteOfflineBodyFatWithDbID:
DBUtil::selectAllSuspectedOfflineDataWithUserID:
DBUtil::selectAllUnsuspectedOfflineDataWithUserID:
DBUtil::selectLastSuspectedOfflineDataWithUserID:
OfflineDataUtility::checkOfflineDataWithUserId:
```

`OfflineDataUtility::checkOfflineDataWithUserId:` obtains queued offline
records from `DBUtil`, obtains `HTTPRequestManager::shareManager`, and calls
the body-fat upload method on queued records. Other recovered code deletes
offline rows by database id after success paths. This supports an offline queue
model: the app is not limited to only sending a live measurement at the moment
it is taken.

Additional callback-reference evidence strengthens the retry model:

```text
BLEHandler::uploadBodyfat:isOfflineData:
  failure block -> insertOfflineBodyFat:

OfflineDataUtility::checkOfflineDataWithUserId:
  queued OfflineBodyFat row -> requestUploadBodyFat:success:failure:
  success block -> deleteOfflineBodyFatWithDbID:
```

In plain terms: if a body-fat upload fails, the app has a local insert path for
the offline body-fat queue. Later, the offline-data checker can replay queued
rows through the same Daxin body-fat upload method and delete the queued row
after a success path.

## Login, Profile, and Credential Storage

The app exposes account methods:

```text
requestLoginWithAccount:password:success:failure:
requestRegisterUser:emailVeriCode:success:failure:
requestRegisterUser:mobileVeriCode:success:failure:
requestResetPasswordWithEmail:newPassword:veriCode:success:failure:
requestResetPasswordWithMobile:newPassword:veriCode:success:failure:
requestModifyUser:success:failure:
requestValidateNickName:success:failure:
requestSNSLoginWithSNSId:snsType:success:failure:
requestSNSRegisterUser:SNSId:snsType:success:failure:
```

Static SQL shows a local `User` table with:

```text
userID
nickName
gender
birthday
height
weight
profession
resistance
goalWeight
email
password
```

The decompile recovered an `encryptByMD5:` helper and `_CC_MD5` import, but did
not prove the exact login password transform for storage or transit. Android's
saved password handling was proven weak; iOS should be treated as needing more
method-level testing before making an equivalent claim.

## Apple Health and Fitbit

HealthKit identifiers include:

```text
HKQuantityTypeIdentifierBodyMass
HKQuantityTypeIdentifierBodyFatPercentage
HKQuantityTypeIdentifierBodyMassIndex
HKQuantityTypeIdentifierLeanBodyMass
HKQuantityTypeIdentifierBloodPressureSystolic
HKQuantityTypeIdentifierBloodPressureDiastolic
HKQuantityTypeIdentifierBodyTemperature
HKQuantityTypeIdentifierOxygenSaturation
```

Recovered behavior writes body data to Apple Health. This is not the only data
path: the Daxin upload route exists separately.

Fitbit upload routes are present for weight/fat and token revocation. Fitbit
upload is conditional on stored Fitbit credentials/tokens.

## Relationship to Android Findings

Confirmed overlap with Android:

- Same Daxin base backend: `https://tj.daxinhealth.com/`
- Same body-composition upload concept: `composition/upload`
- Same supported health verticals: composition, blood pressure, oxygen,
  temperature
- Same BLE body-fat service family: `FAA0/FAA1/FAA2`
- Same Chipsea BIS v23x242 algorithm lineage
- Same account model concepts: login, register, password reset, profile/member
  management

Differences observed so far:

- Android contains Tencent Bugly crash reporting and sets Bugly user
  identifiers after login. This iOS binary did not show Bugly/Tencent/Firebase/
  Sentry crash SDK strings.
- Android ships `libchipsea_bis_v23x242.so`; iOS appears to compile the Chipsea
  logic into the main app binary.
- Android saved-password storage was proven reversible DES/Base64. iOS has a
  local password column and MD5 helper, but exact password storage/transit
  handling is not proven yet.

## Privacy Interpretation

The Apple App Store page says `Data Not Collected`, and the app privacy manifest
declares:

```text
NSPrivacyCollectedDataTypes: []
```

The decrypted executable contains:

```text
https://tj.daxinhealth.com/
composition/upload
requestUploadBodyFat:success:failure:
requestUploadBloodPressure:success:failure:
requestUploadOxygen:success:failure:
requestUploadTemperature:success:failure:
requestLoginWithAccount:password:success:failure:
requestRegisterUser:emailVeriCode:success:failure:
requestRegisterUser:mobileVeriCode:success:failure:
```

Accurate conclusion:

> Static analysis of the decrypted iOS app proves that it contains code paths
> and route strings for transmitting user/account/profile and health measurement
> data to `https://tj.daxinhealth.com/`, despite declaring no collected data
> types in its Apple privacy manifest and App Store privacy label.

Avoid overclaiming:

- This does not prove developer intent.
- This does not prove every app launch uploads data.
- This does not prove every measurement uploads when no account or no network is
  available.
- Runtime traffic capture is still needed to document exact request bodies for
  each user workflow.

## Recommended Next Tests

1. Runtime network capture for first launch, login, profile edit, measurement,
   history/offline sync, Apple Health enable/disable, and Fitbit enable/disable.
2. Hook or log calls to:
   - `requestUploadBodyFat:success:failure:`
   - `uploadBodyfat:isOfflineData:`
   - `bodyFat:SDKwithWeight:fat:resistance:time:userInfo:`
   - `requestLoginWithAccount:password:success:failure:`
   - `encryptByMD5:`
3. Compare iOS request bodies against Android request bodies.
4. Inspect the app sandbox database after login and after a real measurement.
