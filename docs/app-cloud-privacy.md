# BWell Health App Cloud And Privacy Analysis

This document summarizes what the reviewed BWell Health Android app sends to the vendor backend, what it stores locally, and what that means for privacy. It is written as a standalone public note.

## App Identity

| Item | Value |
|---|---|
| App name | Bwell Health |
| Android package | `com.ebelter.bwell` |
| Version reviewed | `bwell.1.1.6` |
| Version code | `39` |
| Backend base URL | `https://tj.daxinhealth.com/` |
| Scale upload endpoint | `https://tj.daxinhealth.com/composition/upload` |

The app is not local-only. It is designed around account login, cloud upload, cloud history, dashboard data, and server-side record deletion.

## Summary

Normal logged-in use sends final body-composition records to the company backend. The scale sends compact BLE data to the phone; the phone calculates the expanded body metrics locally; then the app uploads the finished record.

Data-safety posture: weak-to-moderate, cloud-dependent.

If the privacy goal is "do not send my scale data to the company," the official app does not satisfy that goal when used normally with an account and network access.

## Scale Upload Fields

The scale-composition upload includes:

| Field | Meaning |
|---|---|
| `userid` | App/cloud user id. |
| `weight` | Weight. |
| `bmi` | Body mass index. |
| `bmr` | Basal metabolic rate. |
| `adipose_rate` | Body fat percentage. |
| `visceralFat` | Visceral fat value. |
| `muscleQuantity` | Muscle quantity/muscle mass field. |
| `boneMass` | Bone mass field. |
| `protein` | Protein percentage. |
| `moisture` | Water/moisture percentage. |
| `boneMineralContent` | Uploaded bone-mineral field; the app maps this from a skeletal-muscle field in code. |
| `bodySocre` | Body score, misspelled this way by the app/server API. |
| `physicalAge` | Body age. |
| `weightControl` | Weight-control related value. |
| `size` | Optional field when nonzero. |
| `testDate` | Measurement timestamp. |

Fields not observed in the reviewed scale upload body:

| Field | Status |
|---|---|
| Bluetooth device name | Not seen in `/composition/upload`. |
| Bluetooth MAC/address | Not seen in `/composition/upload`. |
| Raw impedance/resistance | Used locally, not seen in `/composition/upload`. |
| `host_id` | Local database field, not seen in `/composition/upload`. |
| `heartRate` | Local/history field, not seen in `/composition/upload`. |

The absence of those fields from this specific upload method does not prove they can never appear in any other endpoint or telemetry path.

## Upload Flow

The app's scale upload behavior is:

1. A final scale measurement is received.
2. The app applies its result normalization/limit logic.
3. The app copies the result into a local scale-upload record.
4. The record is saved into a local upload queue.
5. After login, a background uploader starts.
6. The uploader checks for network availability.
7. A queued record for the current user is posted to `/composition/upload`.
8. On server success, the local queued record is deleted.

This is a cloud sync pipeline, not a local-only display path.

## Cloud History Endpoints

The app also uses backend endpoints for history, dashboard, averages, and deletion:

| Endpoint path | Purpose |
|---|---|
| `/common/main` | Main dashboard data. |
| `/composition/get/page/data` | Scale history list. |
| `/composition/deleteById` | Delete a scale record by id. |
| `/composition/get/data/day` | Daily scale aggregate/average data. |
| `/composition/get/data/week` | Weekly scale aggregate/average data. |
| `/composition/get/data/month` | Monthly scale aggregate/average data. |

These endpoints indicate backend retention and processing of scale records.

## Account And Profile Data

Observed account and profile behavior includes:

| Endpoint path | Purpose |
|---|---|
| `/user/login22` | Login. |
| `/user/register` | Registration. |
| `/user/register4` | Phone registration. |
| `/user/registerDemo` | Registration/demo path. |
| `/user/update/info` | Profile update. |
| `/user/addChild` | Add sub-user. |
| `/user/deleteChildById` | Delete sub-user. |
| `/user/set/target/weight` | Set target weight. |
| `/common/initialize` | App/device/user initialization. |

Registration and sub-user creation can send nickname, email or phone, verification code, password, birthday, height, weight, sex, profession, type, and photo.

The initialize call sends user/app/device/locale context such as user id, app version, phone brand, phone model, Android version, country, language, and language tag.

## Local Credential Storage

The app stores saved login details in Android SharedPreferences.

| Item | Value |
|---|---|
| SharedPreferences file | `"1"` |
| Login name key | `"1"` |
| Saved password key | `"2"` |
| Login email key | `"0"` |
| Last-used user id key | `"3"` |

The stored password is protected with reversible DES using hardcoded key material derived from `MD5("Ze")`, then Base64 encoded. This is weak protection if app data can be extracted.

The app manifest enables Android backup with `android:allowBackup="true"`, which increases concern around local stored account data depending on device and OS backup behavior.

## Crash Telemetry

Tencent Bugly crash reporting is present.

| Item | Value |
|---|---|
| Bugly app id | `e81323d3e5` |

After login/user save, the app sets Bugly user identifiers:

| Identifier | Sent to Bugly |
|---|---:|
| User id | Yes |
| Email | Yes |
| Mobile phone | Yes |
| Nickname | Yes |

Caught HTTP/JSON errors can be posted to Bugly as exceptions. Successful scale upload response bodies were not observed being intentionally sent through that callback path, but the crash-reporting SDK is tied to account identifiers.

## Health Connect

Health Connect support is separate from the vendor backend. When enabled for the host user, the app can sync:

| Health Connect value | Source |
|---|---|
| Weight | Final scale result. |
| Body fat | Final scale result. |
| BMR | Final scale result. |
| Bone mineral/bone mass value | Final scale result. |
| Timestamp | Measurement time. |

Health Connect sync does not replace the vendor upload path. The official app can still queue and upload the same final result to the company backend.

## Conclusion

The official BWell Health app uploads sensitive body-composition records to a hardcoded backend. HTTPS protects transit against casual network observation, but it does not prevent the backend operator from receiving the data. Local saved password handling is weak, Android backup is enabled, and crash telemetry receives account identifiers.

For a local-only privacy model, the official app should be avoided or isolated from network/account use.

