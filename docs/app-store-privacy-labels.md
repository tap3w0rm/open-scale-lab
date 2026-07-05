# App Store Privacy Labels Versus Technical Evidence

This document tracks the app-store privacy declarations for Bwell Health and
compares them against technical evidence from the reviewed app packages.

This is not a legal conclusion. It is an evidence note for a potential
privacy-label mismatch.

## Current High-Level Finding

Both major app-store listings reviewed report no collection of user data:

| Platform | App | Publisher | Current label found | Status |
|---|---|---|---|---|
| Google Play | `Bwell health` / `com.ebelter.bwell` | Bytech Intl | `No data collected`; `No data shared with third parties` | Conflicts with Android code evidence. |
| Apple App Store | `Bwell Health` / `com.bytechny.B-WELL` | BYTECH NY, INC. | `Data Not Collected` | iOS-specific network behavior still needs runtime verification. |

The Android finding is the stronger mismatch right now because the reviewed
Android app contains a hardcoded backend and an upload path for final
body-composition records.

The iOS finding is not yet a proven contradiction. The iOS app package is
official and shows account/upload/server-related UI strings plus HealthKit and
Bluetooth permissions, but the main executable is App Store encrypted. Runtime
traffic capture is still required before claiming that the iOS label is false.
See the [iOS app acquisition log](ios-app-acquisition-log.md) and
[iOS app initial teardown](ios-bwell-app-initial-teardown.md) for the current
iOS evidence boundary.

Related Bytech health apps show the same pattern is worth broader review. Sealy
Smart Scale Android discloses some collection in Google Play and its reviewed
APK does upload body-composition records to Daxin. Equate Monitors Google Play
says `No data collected`, while its reviewed APK contains Daxin upload paths
for oxygen and temperature records.

Daxin's own app-store labels provide a useful baseline: `ehealth scale` and
`ehealth life` disclose health-data collection more directly than BWell Health.

## Google Play Listing

Reviewed source:

`https://play.google.com/store/apps/details?id=com.ebelter.bwell&hl=en_US`

Current listing observations:

| Item | Value |
|---|---|
| App name | `Bwell health` |
| Publisher | `Bytech Intl` |
| Category | `Medical` |
| Downloads | `100K+` |
| Updated on | `Jun 14, 2026` |
| Data Safety sharing declaration | `No data shared with third parties` |
| Data Safety collection declaration | `No data collected` |
| Transit declaration | `Data is encrypted in transit` |
| Deletion declaration | `You can request that data be deleted` |
| Privacy policy link | `https://www.bytechintl.com/bytech-smart-home` |

The listing description says the app works with B.Well health devices over
Bluetooth to measure health data.

### Conflict With Android Technical Evidence

The reviewed Android app is not local-only. It contains a hardcoded backend:

```text
https://tj.daxinhealth.com/
```

The scale upload endpoint is:

```text
https://tj.daxinhealth.com/composition/upload
```

The upload record includes account-linked health/body-composition fields such
as:

- user id
- weight
- BMI
- BMR
- body fat percentage
- visceral fat
- muscle
- bone mass
- protein
- water/moisture
- body score
- physical/body age
- measurement timestamp

The Android app also initializes Tencent Bugly and sets Bugly user identifiers
after login:

- user id
- email
- mobile phone
- nickname

That technical evidence is difficult to reconcile with a Google Play Data
Safety declaration of `No data collected`.

## Apple App Store Listing

Reviewed source:

`https://apps.apple.com/us/app/bwell-health/id1566290744`

Current listing observations:

| Item | Value |
|---|---|
| App name | `Bwell Health` |
| Seller/developer | `BYTECH NY, INC.` |
| Version | `1.0.19` |
| Version date | `09/10/2025` |
| Size | `7.4 MB` |
| Requires | `iOS 15.6 or later` |
| App Privacy label | `Data Not Collected` |
| Privacy policy link | `https://bwellmonitors.com/privacy-policy` |

The App Store page says the app works with B.Well scale, blood pressure monitor,
thermometer, and finger oximeter devices over Bluetooth 4.0 and can share
health data to Apple Health.

The App Store page also states that app privacy information is developer
provided and has not been verified by Apple.

## Apple-Sourced iOS Package Evidence

An official Apple App Store IPA was acquired with `ipatool` for research. The
package is not redistributed.

Package identity:

| Item | Value |
|---|---|
| Bundle ID | `com.bytechny.B-WELL` |
| Version | `1.0.19` |
| Build | `2` |
| Seller metadata | `BYTECH NY, INC.` |
| Main binary encryption | `LC_ENCRYPTION_INFO_64 cryptid: 1` |

The encryption status means the package has strong Apple-source provenance but
limited static reversibility.

Visible package metadata shows:

| Evidence | Meaning |
|---|---|
| `CoreBluetooth` linked | The app can communicate with Bluetooth devices. |
| `HealthKit` linked and entitled | The app can interact with Apple Health with user permission. |
| `NSBluetoothAlwaysUsageDescription` | The app says Bluetooth is needed to measure human body data. |
| `NSHealthShareUsageDescription` / `NSHealthUpdateUsageDescription` | The app says it syncs measured body data to Apple Health. |
| `NSCameraUsageDescription` / `NSPhotoLibraryUsageDescription` | The app supports avatar/profile image flows. |
| `NSAppTransportSecurity` with `NSAllowsArbitraryLoads: true` | The app permits network loads that normal ATS would block. |
| Privacy manifest with `NSPrivacyCollectedDataTypes: []` | The iOS package declares no collected data types. |

The English localization contains UI strings for:

- login
- registration
- password reset
- email
- mobile phone number
- verification codes
- Facebook login
- body-fat scale connection
- offline scale data sync
- upload success/failure
- server errors
- Apple Health sharing

These strings do not prove a current iOS cloud upload destination. They do prove
that the iOS app was built with account, server, upload, and offline-sync flows,
which makes runtime traffic capture a high-priority next step.

## Policy Links Point To Different Disclosures

The two app stores link to different policy locations:

| Store | Linked policy |
|---|---|
| Google Play | `https://www.bytechintl.com/bytech-smart-home` |
| Apple App Store | `https://bwellmonitors.com/privacy-policy` |

The Google Play linked Bytech policy is a broad `Bytech Smart Home Privacy
Policy`, last updated July 09, 2024. It says the company may process mobile
application information including names, email addresses, phone numbers,
passwords, Bluetooth permission/access, mobile device identifiers, device data,
log/usage data, crash dumps, IP address, geolocation, and app-feature activity.

That Bytech policy is much broader than the Google Play Data Safety summary of
`No data collected`.

The Apple-linked BWell privacy page currently renders poorly without JavaScript
in static capture and needs a browser/manual capture.

## Related App Labels

Sealy Smart Scale:

| Platform | Public label | Technical evidence |
|---|---|---|
| Google Play | No third-party sharing; may collect `Location` and `Personal info`. | Reviewed Android APK posts scale/body-composition records to `https://sealy.daxinhealth.com/api/composition/upload` and offline batches to `/composition/uploadOffLine`. |
| Apple App Store | `Data Not Linked to You`; `Identifiers / User ID` for app functionality. | iOS package not reviewed in this pass. |

Equate Monitors:

| Platform | Public label | Technical evidence |
|---|---|---|
| Google Play | May share `Location`; `No data collected`. | Reviewed Android APK posts oxygen records to `https://test.daxinhealth.com/bloodOxygen/upload` and temperature records to `https://test.daxinhealth.com/temperature/upload`. |
| Apple App Store | Needs app-specific capture. | iOS package not reviewed in this pass. |

The Equate label is especially notable because the public listing says the app
can display, record, and view historical measurement data, while the reviewed
Android code contains a background uploader for queued oxygen and temperature
records.

## Daxin App Label Baseline

Daxin publishes its own health apps. Their labels are more explicit than BWell
Health's labels:

| App | Platform | Developer | Public label |
|---|---|---|---|
| `ehealth scale` | Apple App Store | Guangzhou Daxin Health Technology Co., Ltd | `Health & Fitness` data may be collected and linked to identity. |
| `ehealth life` | Apple App Store China | Guangzhou Daxin Health Technology Co., Ltd | Identifiers may be used for tracking; health/fitness, location, contact info, user content, and identifiers may be linked to identity. |
| `ehealth life` | Google Play | Guangzhou Daxin Health Technology Co., Ltd | No third-party sharing; may collect personal info, health and fitness, and photos/videos. |

Why this matters:

The Daxin labels show that at least some Daxin-operated health apps disclose
health data collection in store privacy labels. BWell Health's `No data
collected` label is therefore not simply the only way Daxin-like scale apps are
being labeled.

## Evidence Grading

| Claim | Current confidence | Why |
|---|---:|---|
| Google Play says Android Bwell Health collects no data | High | Direct store listing capture. |
| Android app uploads account-linked body records to Daxin backend | High | App code and endpoint analysis. |
| Android app sends identifiable crash telemetry to Bugly | High | App code sets Bugly identifiers. |
| Google Play Data Safety appears inconsistent with Android technical behavior | High | Direct conflict between store declaration and app code behavior. |
| Apple App Store says iOS Bwell Health collects no data | High | Direct store listing capture. |
| iOS app package declares no collected data in privacy manifest | High | Official Apple-sourced IPA metadata. |
| iOS app has account/upload/server UI flows | Medium-high | Visible strings in official IPA resources. |
| iOS app actually uploads health records to Daxin or any backend | Unknown | Requires runtime traffic capture or decrypted app analysis. |
| Sealy Smart Scale Android uploads body-composition records to Daxin | High | Reviewed APK hardcodes Daxin backend and upload/offline-upload endpoints. |
| Equate Monitors Android uploads oxygen and temperature records to Daxin | High | Reviewed APK hardcodes Daxin backend and upload endpoints. |
| Equate Monitors Google Play label appears inconsistent with Android technical behavior | High | Public label says `No data collected`; reviewed Android code posts account-linked measurement records. |
| Daxin's own scale app labels disclose health collection | High | Apple and Google Play listings for Daxin apps disclose health/fitness collection. |

## Next Steps

1. Capture Google Play Data Safety detail view if available.
2. Capture the BWell JavaScript-rendered privacy page linked by Apple.
3. Preserve dated screenshots or page captures of both app-store privacy labels.
4. Test iOS runtime network behavior on a research iPhone.
5. If the iOS app transmits account, health, device, or telemetry data, compare
   the observed data categories directly against Apple's `Data Not Collected`
   label and the empty `NSPrivacyCollectedDataTypes` manifest.
6. Capture Google Play Data Safety detail views for Sealy Smart Scale and
   Equate Monitors.
7. Runtime-test Equate Monitors network behavior to confirm the static upload
   code paths in live use.
8. Capture Daxin app label screenshots as baseline evidence for Daxin's own
   disclosure practices.
