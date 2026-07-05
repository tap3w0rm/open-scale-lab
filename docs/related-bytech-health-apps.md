# Related Bytech Health And Device Apps

This document tracks related Bytech-published health/device apps that may share
vendors, backend patterns, policy language, or app-store declaration practices
with Bwell Health.

The goal is not to claim that every app shares the same backend. The goal is to
map leads that deserve code review, policy review, and network testing.

## Why This Matters

Bwell Health is not an isolated app listing. Bytech / BYTECH NY, INC. publishes
multiple health, scale, sleep, and device-control apps. Some listings point back
to the same Bytech policy site, and at least one related smart-scale app uses a
package name under `com.daxin`.

That makes Daxin and Bytech a broader app ecosystem question, not only a single
BWell scale question.

## Confirmed Related Listings

| App | Platform | Publisher | Package / App ID | Health/device role | Current label notes |
|---|---|---|---|---|---|
| Bwell health | Google Play | Bytech Intl | `com.ebelter.bwell` | B.Well scale, blood pressure, thermometer health app | Data Safety says `No data collected` and `No data shared with third parties`. |
| Bwell Health | Apple App Store | BYTECH NY, INC. | `id1566290744` / `com.bytechny.B-WELL` | B.Well scale, blood pressure, thermometer, finger oximeter | App Privacy says `Data Not Collected`. |
| Sealy Smart Scale | Google Play | Bytech Intl | `com.daxin.sealyscale` | BLE body-fat scale app | Data Safety says no third-party sharing; may collect `Location` and `Personal info`. |
| Sealy Smart Scale | Apple App Store | BYTECH NY, INC. | `id6670584440` | BLE body-fat scale app | App Privacy says `Identifiers / User ID` may be collected, not linked to identity. |
| Equate Monitors | Google Play | Bytech Intl | `com.bloodoxygen.bytechintl` | Bluetooth oximeter and thermometer | Data Safety says may share `Location`, but says `No data collected`. Support email is `daxinhealth@gmail.com`. |
| Equate Monitors | Apple App Store | BYTECH NY, INC. | Listed under Bytech developer page | Health/fitness monitor app | Needs app-specific privacy capture. |

## Sealy Smart Scale Signals

Google Play listing:

`https://play.google.com/store/apps/details?id=com.daxin.sealyscale`

Important observations:

- Publisher is `Bytech Intl`.
- Package name is `com.daxin.sealyscale`.
- Description says it is a BLE-based smart body-fat scale app that calculates
  BMI, body fat, water content, and other body-composition health data from
  user age, height, gender, and other information.
- Data Safety says no third-party sharing.
- Data Safety says the app may collect `Location` and `Personal info`.
- Support email is `support@bytechintl.com`.
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

Sealy Smart Scale looks like a close cousin to the Bwell Health scale stack.
The Android package name strongly suggests Daxin involvement, and the App Store
privacy label is more candid than Bwell Health's iOS `Data Not Collected`
label. The app should be reviewed separately before making shared-backend
claims.

## Equate Monitors Signals

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

The `daxinhealth@gmail.com` support email is a strong lead tying Daxin support
operations to another Bytech-published health-device app. It does not prove the
same backend or database, but it supports treating Daxin as a recurring vendor
or operational partner across Bytech health apps.

## App-Store Label Pattern

The related apps show inconsistent data-safety declarations:

| App | Platform | Declaration pattern |
|---|---|---|
| Bwell health | Google Play | No data collected, despite Android app upload evidence. |
| Bwell Health | Apple App Store | Data Not Collected, despite package account/upload/server UI flows. |
| Sealy Smart Scale | Google Play | May collect Location and Personal info. |
| Sealy Smart Scale | Apple App Store | May collect User ID, not linked to identity. |
| Equate Monitors | Google Play | May share Location, but says no data collected. |

This pattern makes the labels themselves a research object. Some related apps
acknowledge identifiers or personal information, while Bwell Health declares no
collection.

## Research Leads

High-priority follow-up:

1. Download and inspect the Android APK for `com.daxin.sealyscale`.
2. Compare its backend domains, upload endpoints, local database schema, and
   SDKs against Bwell Health.
3. Download and inspect the Android APK for `com.bloodoxygen.bytechintl`.
4. Check whether Equate Monitors uses Daxin endpoints or support/config
   endpoints.
5. Capture Sealy Smart Scale iOS package metadata with `ipatool`.
6. Compare privacy manifests across Bwell Health and Sealy Smart Scale.
7. Search Bytech's other health/device apps for repeated policy links and
   Daxin contact information.

Avoid saying without code or network evidence:

- All Bytech health apps share one backend.
- Sealy Smart Scale and Bwell Health share one user database.
- Daxin receives all Bytech health-app data.
- Equate Monitors sends measurement records to Daxin.

Defensible current wording:

> Bytech's related health-device apps show recurring Daxin signals, including a
> Daxin package name in Sealy Smart Scale and a Daxin support email in Equate
> Monitors. These are leads for shared-vendor or shared-backend research, not
> proof of a single shared database.
