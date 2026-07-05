# Related Bytech Health And Device Apps

This document tracks related Bytech-published health/device apps that may share
vendors, backend patterns, policy language, telemetry SDKs, or app-store
declaration practices with Bwell Health.

The goal is not to claim that every app shares the same backend or database.
The goal is to map what is proven by code review, what is visible in public
store/policy pages, and what still needs runtime testing.

## Why This Matters

Bwell Health is not an isolated app listing. Bytech / BYTECH NY, INC. publishes
multiple health, scale, sleep, and device-control apps. Some listings point back
to the same Bytech policy site. Two reviewed Android apps also contain
hardcoded Daxin backend domains.

That makes Daxin and Bytech a broader health-device app ecosystem question, not
only a single BWell scale question.

## Confirmed Related Listings

| App | Platform | Publisher | Package / App ID | Health/device role | Current label notes |
|---|---|---|---|---|---|
| Bwell health | Google Play | Bytech Intl | `com.ebelter.bwell` | B.Well scale, blood pressure, thermometer health app | Data Safety says `No data collected` and `No data shared with third parties`. Android code uploads scale/body records to Daxin. |
| Bwell Health | Apple App Store | BYTECH NY, INC. | `id1566290744` / `com.bytechny.B-WELL` | B.Well scale, blood pressure, thermometer, finger oximeter | App Privacy says `Data Not Collected`; iOS runtime behavior still needs testing. |
| Sealy Smart Scale | Google Play | Bytech Intl | `com.daxin.sealyscale` | BLE body-fat scale app | Data Safety says no third-party sharing; may collect `Location` and `Personal info`. Android code uploads scale composition records to Daxin. |
| Sealy Smart Scale | Apple App Store | BYTECH NY, INC. | `id6670584440` | BLE body-fat scale app | App Privacy says `Identifiers / User ID` may be collected, not linked to identity. |
| Equate Monitors | Google Play | Bytech Intl | `com.bloodoxygen.bytechintl` | Bluetooth oximeter and thermometer | Data Safety says may share `Location`, but says `No data collected`. Android code uploads oxygen and temperature records to Daxin. |
| Equate Monitors | Apple App Store | BYTECH NY, INC. | Listed under Bytech developer page | Health/fitness monitor app | Needs app-specific privacy capture. |

## Sealy Smart Scale Android APK Findings

Reviewed artifact:

| Item | Value |
|---|---|
| Source | APKPure XAPK mirror, hash matched APKPure page values at download time. |
| Package | `com.daxin.sealyscale` |
| Version | `1.1.1` |
| SHA-256 | `1d89f8f97e11068d98a71d2e713799e5077cdb92c96b30de3e72b4d00ae5a23b` |
| App name | `Sealy Smart Scale` |
| Publisher on Google Play | `Bytech Intl` |
| Backend base URL in code | `https://sealy.daxinhealth.com/api` |
| Bugly app id | `3db031a570` |

Hardcoded backend endpoints include:

| Endpoint | Purpose inferred from code name/use |
|---|---|
| `/composition/upload` | Upload a live/final body-composition scale record. |
| `/composition/uploadOffLine` | Batch-upload offline scale records. |
| `/composition/get/page/offLinedata` | Get pageable offline scale records. |
| `/composition/claimOffLineDataById` | Claim selected offline scale records. |
| `/composition/deleteOffLineById` | Delete selected offline scale records. |
| `/composition/get/page/data` | Get cloud scale history page. |
| `/composition/deleteById` | Delete cloud scale history record. |
| `/composition/get/data/day2` | Get daily scale trend/aggregate data. |
| `/composition/get/data/week2` | Get weekly scale trend/aggregate data. |
| `/composition/get/data/month2` | Get monthly scale trend/aggregate data. |
| `/composition/get/data/last2` | Get latest scale composition record. |
| `/user/loginV2` | Login. |
| `/user/register` | Registration. |
| `/user/addChild` | Add sub-user/profile. |
| `/user/deleteChildById` | Delete sub-user/profile. |
| `/user/update/info` | Update user/profile information. |
| `/user/bind/mac` | Bind scale MAC/address to account. |
| `/user/unbind/mac` | Unbind scale. |
| `/common/initialize` | Initialization/configuration. |

The scale upload request class includes:

| Field | Meaning |
|---|---|
| `userId` | Cloud/app user id. |
| `impedance` | Scale impedance/resistance-derived value. |
| `physicalAge` | Body/physical age. |
| `bodySocre` | Body score, misspelled in app/server model. |
| `weight` | Weight in kg. |
| `weightLB` | Weight in pounds. |
| `adiposeRate` | Body fat percentage/rate. |
| `bmi` | Body mass index. |
| `bmr` | Basal metabolic rate. |
| `visceralFat` | Visceral fat value. |
| `muscleQuantity` | Muscle quantity/mass value. |
| `boneMass` | Bone mass value. |
| `protein` | Protein value. |
| `moisture` | Water/moisture value. |
| `boneMineralContent` | Bone mineral content / skeletal-related value. |
| `testDate` | Measurement timestamp. |

The code serializes that request to JSON and posts it to
`/composition/upload`. It also serializes a list of the same request type and
posts it as a JSON array to `/composition/uploadOffLine`.

Crash/telemetry behavior:

- The app initializes Tencent Bugly with app id `3db031a570`.
- After user context is available, code sets the Bugly user id and adds the
  user's email as Bugly user data.

Health-platform behavior:

- The app requests Android Health Connect write permissions for weight, body
  fat, and basal metabolic rate.
- The app has an in-app Health Connect permissions screen whose URL points to
  `https://www.bytechintl.com/bytech-smart-home`.

## Sealy Smart Scale Store And Policy Signals

Google Play listing:

`https://play.google.com/store/apps/details?id=com.daxin.sealyscale`

Important observations from the public listing:

- Publisher is `Bytech Intl`.
- Package name is `com.daxin.sealyscale`.
- Description says it is a BLE-based smart body-fat scale app that calculates
  BMI, body fat, water content, and other body-composition health data from
  user age, height, gender, and other information.
- Data Safety says no third-party sharing.
- Data Safety says the app may collect `Location` and `Personal info`.
- Privacy policy link points to Bytech.

Apple App Store listing:

`https://apps.apple.com/us/app/sealy-smart-scale/id6670584440`

Important observations:

- Developer is `BYTECH NY, INC.`
- Description is also a BLE smart body-fat scale description.
- App Privacy says `Data Not Linked to You`.
- The listed collected data category is `Identifiers`, specifically `User ID`,
  for app functionality.
- Privacy policy link points to `www.bytechintl.com`.

Interpretation:

Sealy Smart Scale is no longer just a Daxin naming lead. The reviewed Android
APK proves a Daxin backend for that app and proves upload endpoints for live and
offline body-composition records. It still does not prove that Sealy Smart
Scale and Bwell Health share one database or that Sealy corporate receives app
records.

## Equate Monitors Android APK Findings

Reviewed artifact:

| Item | Value |
|---|---|
| Source | APKPure XAPK mirror, hash matched APKPure page values at download time. |
| Package | `com.bloodoxygen.bytechintl` |
| Version | `v1.1.3` |
| SHA-256 | `23d8e38d8ea7d623cefad8f6e15aad8eba17b2acf00ec03aa791da9cbbaf435a` |
| App name | `Equate Monitors` |
| Publisher on Google Play | `Bytech Intl` |
| Backend base URL in code | `https://test.daxinhealth.com` |
| Bugly app id | `d3d4281529` |

Hardcoded backend endpoints include:

| Endpoint | Purpose inferred from code name/use |
|---|---|
| `user/login22` | Login. |
| `user/register` | Registration. |
| `user/retrieve/password` | Password reset. |
| `user/send/validate/code` | Email/code validation. |
| `user/update/info` | Update user/profile info. |
| `user/addChild` | Add sub-user/profile. |
| `user/deleteChildById` | Delete sub-user/profile. |
| `bloodOxygen/upload` | Upload blood oxygen record. |
| `bloodOxygen/get/page/data` | Get oxygen history page. |
| `bloodOxygen/deleteById` | Delete oxygen history record. |
| `temperature/upload` | Upload temperature record. |
| `temperature/get/page/data` | Get temperature history page. |
| `temperature/deleteById` | Delete temperature history record. |

The blood oxygen upload sends:

| Field | Meaning |
|---|---|
| `userid` | Cloud/app user id. |
| `saO2` | Blood oxygen saturation value. |
| `pulseRate` | Pulse rate / BPM. |
| `perfusionIndex` | Perfusion index. |
| `testDate` | Measurement timestamp. |

The temperature upload sends:

| Field | Meaning |
|---|---|
| `userid` | Cloud/app user id. |
| `temperature` | Temperature value. |
| `tempType` | Temperature type/category. |
| `testDate` | Measurement timestamp. |

The app has a background upload thread. When network is available and a current
user exists, the thread checks local oxygen and temperature upload queues about
every two seconds. When a server upload succeeds, the local queued record is
deleted.

Crash/telemetry behavior:

- The app initializes Tencent Bugly with app id `d3d4281529`.
- The home screen sets Bugly user id to the host id.
- It also adds `SubUserId`, `Email`, and `NickName` as Bugly user data when
  those values are present.

Health-platform behavior:

- The app requests Android Health Connect write permissions for body
  temperature and oxygen saturation.
- The app contains Health Connect and Google Fit sharing code paths.

## Equate Monitors Store And Policy Signals

Google Play listing:

`https://play.google.com/store/apps/details?id=com.bloodoxygen.bytechintl`

Important observations:

- Publisher is `Bytech Intl`.
- App is for the Equate Bluetooth oximeter `EQ-HW-PO-100-BK` and thermometer
  `EQ-HW-TH-500-BK`.
- Description says it displays, records, and views SpO2, pulse rate,
  perfusion index, body temperature, and historical measurement data.
- Data Safety says the app may share `Location` with third parties.
- Data Safety says `No data collected`.
- Support email is `daxinhealth@gmail.com`.
- Privacy policy link points to Bytech.

Interpretation:

Equate Monitors is no longer only a support-email lead. The reviewed APK proves
that this Bytech-published health app posts oxygen and temperature records to a
Daxin backend. The hostname includes `test`, but it is hardcoded in the
production app artifact reviewed here. That proves Daxin backend involvement;
it does not prove the same database as Bwell or Sealy.

## App-Store Label Pattern

The related apps show inconsistent data-safety declarations:

| App | Platform | Declaration pattern |
|---|---|---|
| Bwell health | Google Play | No data collected, despite Android app upload evidence. |
| Bwell Health | Apple App Store | Data Not Collected, despite package account/upload/server UI flows. |
| Sealy Smart Scale | Google Play | May collect Location and Personal info. |
| Sealy Smart Scale | Apple App Store | May collect User ID, not linked to identity. |
| Equate Monitors | Google Play | May share Location, but says no data collected, despite APK upload paths for oxygen and temperature records. |

This pattern makes the labels themselves a research object. Some related apps
acknowledge identifiers or personal information, while Bwell Health declares no
collection.

## Updated Interpretation

The reviewed Android apps now support a stronger ecosystem-level statement:

> Multiple Bytech-published health-device Android apps reviewed here use Daxin
> backend domains for account-linked health measurement records. Bwell Health
> uploads scale/body-composition records to `tj.daxinhealth.com`; Sealy Smart
> Scale uploads scale/body-composition records to `sealy.daxinhealth.com`; and
> Equate Monitors uploads oxygen and temperature records to
> `test.daxinhealth.com`.

That is evidence of repeated Bytech/Daxin health-app backend involvement. It is
not evidence, by itself, that all apps share one user database, that any
specific retailer/brand receives all records, or that the data is sold.

## Remaining Research Leads

High-priority follow-up:

1. Runtime-test Sealy Smart Scale and Equate Monitors to confirm live network
   payloads against the static code paths.
2. Capture the full Google Play Data Safety detail pages for Sealy and Equate.
3. Capture Equate Monitors iOS App Privacy and, if possible, official iOS IPA
   metadata.
4. Capture Sealy Smart Scale iOS package metadata with `ipatool`.
5. Compare privacy manifests across Bwell Health, Sealy Smart Scale, and Equate
   Monitors.
6. Search Bytech's other health/device apps for repeated Daxin endpoints,
   policy links, and crash telemetry identifiers.

Avoid saying without code, policy, network, or business evidence:

- All Bytech health apps share one backend.
- Sealy Smart Scale and Bwell Health share one user database.
- Daxin receives all Bytech health-app data.
- All apps share one cloud database or admin console.
- Sealy, Walmart/Equate, or any other brand partner receives all health records.
- Bytech or Daxin sells health measurement records.

Defensible current wording:

> Reviewed Bytech-published health-device Android apps show repeated Daxin
> backend use for account-linked health measurements. This supports treating
> Bytech and Daxin as a recurring app/backend ecosystem, while still keeping
> database-sharing and data-sale claims in the unproven category.
