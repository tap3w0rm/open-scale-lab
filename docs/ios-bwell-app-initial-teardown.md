# Bwell Health iOS App Initial Teardown

Last updated: 2026-07-05

## Acquisition Summary

The iOS app package reviewed here was acquired from the Apple App Store.

App Store metadata target:

| Item | Value |
|---|---|
| App name | `Bwell Health` |
| Apple App ID | `1566290744` |
| Bundle ID | `com.bytechny.B-WELL` |
| Seller | `BYTECH NY, INC.` |
| Apple-reported version | `1.0.19` |
| Apple-reported `fileSizeBytes` | `7,434,240` |
| Apple privacy label | `Data Not Collected` |

Downloaded package metadata:

| Item | Value |
|---|---|
| Actual IPA size | `5,831,398` bytes |
| SHA256 | `BF78E25D74AB14DE0B12B4F998A9A3BEEF2DC2714D79A962D6AECE606E2D56D2` |
| Source class | Apple App Store acquisition |

The IPA extracted successfully and contained the expected App Store package
layout:

```text
Payload/Bwell Health.app/
META-INF/
iTunesMetadata.plist
SC_Info/
```

## Version And Identity

`Payload/Bwell Health.app/Info.plist` reported:

| Field | Value |
|---|---|
| `CFBundleIdentifier` | `com.bytechny.B-WELL` |
| `CFBundleDisplayName` | `Bwell Health` |
| `CFBundleShortVersionString` | `1.0.19` |
| `CFBundleVersion` | `2` |
| `MinimumOSVersion` | `15.6` |
| `DTSDKName` | `iphoneos18.4` |
| `DTPlatformVersion` | `18.4` |

`iTunesMetadata.plist` reported:

| Field | Value |
|---|---|
| `artistName` | `BYTECH NY, INC.` |
| `bundleDisplayName` | `Bwell Health` |
| `bundleShortVersionString` | `1.0.19` |
| `bundleVersion` | `2` |
| `genre` | `Health & Fitness` |
| `itemName` | `Bwell Health` |
| `softwareVersionBundleId` | `com.bytechny.B-WELL` |
| `softwareVersionExternalIdentifier` | `877963809` |
| `vendorId` | `122979355` |

## Encryption Status

The main executable is a thin 64-bit Mach-O binary.

Observed Mach-O details:

| Item | Value |
|---|---|
| Executable | `Payload/Bwell Health.app/Bwell Health` |
| Mach-O magic | `0xfeedfacf` |
| UUID | `ef0c83cb04923e7ba308657bf10040db` |
| Build target | iOS min `15.6.0`, SDK `18.4.0` |
| Encryption load command | `LC_ENCRYPTION_INFO_64` |
| `cryptoff` | `16384` |
| `cryptsize` | `1474560` |
| `cryptid` | `1` |

`cryptid: 1` means the main executable is still App Store encrypted. This
limits static analysis. Metadata, resources, entitlements, framework links, and
strings can still be reviewed, but the app's executable logic needs a decrypted
binary or runtime testing on a device.

## Entitlements And Capabilities

The extracted package contains capability evidence consistent with a Bluetooth
health-device app:

| Capability area | Evidence meaning |
|---|---|
| Bluetooth | The app is expected to communicate with nearby BLE devices. |
| HealthKit / Apple Health | The app can request access to write/read health-related data through Apple Health, depending on user permission. |

This does not by itself prove cloud upload behavior. It proves that the iOS app
has the platform permission surface needed for Bluetooth scale interaction and
Apple Health integration.

## Resource And String Findings

The resource layer contains strings and artifacts consistent with:

- login
- registration
- password reset
- user profile handling
- Bluetooth device interaction
- measurement history
- Apple Health / HealthKit-related flows
- local data storage
- user/profile deletion

The presence of account, profile, and measurement strings means the app is not
obviously a minimal local Bluetooth reader. It appears to implement the same
general app class as the Android package: an account/profile-driven health app
that talks to B.Well devices.

Important limitation: strings are leads. They are not proof that a specific
network endpoint is contacted during a successful measurement flow.

## Relationship To Android Findings

The Android app has confirmed backend and telemetry evidence:

- hardcoded backend at `https://tj.daxinhealth.com/`
- final body-composition upload to `/composition/upload`
- local Chipsea body-composition calculation before upload
- Tencent Bugly crash telemetry with account identifiers set after login

The iOS app should not be assumed to behave identically just because it is the
same brand/app family. The correct evidence chain is:

1. confirm iOS package identity and version
2. inspect resources, entitlements, and linked frameworks
3. obtain decrypted executable or perform runtime traffic capture
4. compare observed runtime behavior to the Apple privacy label

## Later Decrypted-App Update

This document reflects the initial encrypted App Store package review. A later
decrypted-app review superseded the original evidence boundary.

The decrypted iOS app confirmed:

- base backend `https://tj.daxinhealth.com/`
- body-composition upload route `composition/upload`
- method-level upload path through
  `BLEHandler::uploadBodyfat:isOfflineData:` and
  `HTTPRequestManager::requestUploadBodyFat:success:failure:`
- local offline body-fat storage and history/offline read behavior
- Apple Health write behavior and optional Fitbit upload behavior

See [iOS decrypted app teardown](ios-decrypted-app-teardown.md).

## Current Privacy-Label Position

The Apple listing says `Data Not Collected`.

The encrypted-package-only review did not prove that label false. The later
decrypted-app review is stronger: static and Ghidra analysis now prove iOS code
paths for account/profile and health-measurement upload to the Daxin backend.
Runtime traffic capture is still needed for exact per-workflow payloads.

## Next Testing Steps

Recommended next steps:

- Install the app on a test iPhone signed into a controlled test account.
- Pair with the scale and capture network traffic through a trusted test proxy
  or controlled network monitor.
- Record whether login, profile creation, measurement capture, history sync, or
  Apple Health export causes network requests.
- Compare any observed endpoint, payload, and telemetry behavior against the
  App Store `Data Not Collected` label.
- If a decrypted binary is obtained lawfully, compare embedded endpoints and
  telemetry SDKs to the Android evidence.
