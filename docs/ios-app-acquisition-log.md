# iOS App Acquisition Log

Last updated: 2026-07-04

## Target

| Item | Value |
|---|---|
| App | `Bwell Health` |
| Apple App Store ID | `1566290744` |
| Bundle ID | `com.bytechny.B-WELL` |
| Seller/developer | `BYTECH NY, INC.` |
| Apple-reported version | `1.0.19` |
| Apple-reported release timestamp | `2025-09-10T20:10:52Z` |
| Apple-reported size | `7,434,240` bytes |
| Minimum iOS version | `15.6` |
| Apple privacy label | `Data Not Collected` |

## Why The iOS App Matters

The Apple App Store listing reports `Data Not Collected`. The app description
also says the app works with B.Well health devices over Bluetooth and shares
health data to Apple Health.

Because the reviewed Android app has confirmed cloud and telemetry paths, the
iOS app needs its own evidence chain before making any public claim that the iOS
privacy label is wrong.

## Evidence Standard

Mirrored IPAs can be useful for leads, but should not be treated as definitive
evidence for the current App Store binary unless provenance can be established.

Any candidate IPA should be recorded with:

- source URL
- download timestamp
- claimed version
- extracted `CFBundleIdentifier`
- extracted `CFBundleShortVersionString`
- extracted `CFBundleVersion`
- file size
- SHA256
- signing/provisioning details, if present
- whether the binary appears encrypted, decrypted, repacked, or resigned

## Official Metadata Sources

### Apple App Store

Reviewed URL:

```text
https://apps.apple.com/us/app/bwell-health/id1566290744
```

Observed metadata:

| Item | Value |
|---|---|
| Version | `1.0.19` |
| Size | `7.4 MB` |
| Seller | `BYTECH NY, INC.` |
| Requires | `iOS 15.6 or later` |
| App Privacy label | `Data Not Collected` |
| Privacy policy link | `https://bwellmonitors.com/privacy-policy` |

### Apple Lookup API

Reviewed URL:

```text
https://itunes.apple.com/lookup?id=1566290744&country=us
```

Useful fields from the official metadata response:

| Field | Value |
|---|---|
| `bundleId` | `com.bytechny.B-WELL` |
| `version` | `1.0.19` |
| `fileSizeBytes` | `7434240` |
| `currentVersionReleaseDate` | `2025-09-10T20:10:52Z` |
| `minimumOsVersion` | `15.6` |
| `sellerName` | `BYTECH NY, INC.` |

## Mirror Search Result

Public mirror searches found many pages that looked like click-through or
metadata-only traps rather than direct, provenance-friendly IPA downloads.

No mirror source was treated as evidence for the iOS app.

## Acquisition Outcome

The app was acquired through Apple App Store tooling and matched the official
metadata target:

| Item | Value |
|---|---|
| App name | `Bwell Health` |
| App Store ID | `1566290744` |
| Bundle ID | `com.bytechny.B-WELL` |
| Version | `1.0.19` |
| IPA size | `5,831,398` bytes |
| SHA256 | `BF78E25D74AB14DE0B12B4F998A9A3BEEF2DC2714D79A962D6AECE606E2D56D2` |

The package extracted successfully and contained a normal App Store layout:

```text
Payload/Bwell Health.app/
META-INF/
iTunesMetadata.plist
SC_Info/
```

The main executable was still App Store encrypted, so static review can inspect
metadata, entitlements, resources, strings, and linked frameworks, but not the
decrypted executable logic.

## Current Evidence Boundary

Confirmed:

- Apple currently reports `Data Not Collected` for the iOS app.
- The acquired package identifies as `com.bytechny.B-WELL`.
- The acquired package version is `1.0.19`.
- The package includes Bluetooth and Apple Health-related capability evidence.
- The main executable is encrypted and requires runtime/device work for deeper
  behavior confirmation.

Not proven yet:

- That the iOS app uploads scale measurements to the Daxin backend.
- That the iOS app contradicts the Apple `Data Not Collected` label in runtime
  behavior.
- That the iOS app has the same telemetry behavior as the reviewed Android app.

