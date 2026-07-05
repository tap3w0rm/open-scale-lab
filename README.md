# BWell BW-HW-SS-100-WT / EF919B4 Smart Scale Teardown

Distribution report, compiled from Android app reverse engineering, live BLE protocol testing, FCC exhibits, manual material, related Belter patent artifacts, and native body-composition analysis.

## Focused Notes

- [App-store privacy labels versus technical evidence](docs/app-store-privacy-labels.md)
- [App cloud and privacy analysis](docs/app-cloud-privacy.md)
- [Data sharing graph](docs/data-sharing-graph.md)
- [Privacy policy and reputation review](docs/privacy-policy-and-reputation-review.md)
- [Sell, share, and policy matrix](docs/sell-share-and-policy-matrix.md)
- [Related Bytech health and device apps](docs/related-bytech-health-apps.md)
- [iOS app acquisition log](docs/ios-app-acquisition-log.md)
- [iOS app initial teardown](docs/ios-bwell-app-initial-teardown.md)
- [BLE scale protocol](docs/ble-scale-protocol.md)
- [Chipsea body-composition analysis](docs/chipsea-body-composition.md)

## Executive Summary

The BWell BW-HW-SS-100-WT smart scale is the same hardware family as the FCC `EF919B4` / `EF-919B4` body composition scale, FCC ID `2AAEEEF919B4`. It is a Bluetooth Low Energy body-composition scale powered by three AAA batteries. The regulatory exhibits identify the radio as Bluetooth 4.0 GFSK over 2402-2480 MHz, with 40 channels, a PCB antenna, and low transmit power.

The Android application reviewed is `Bwell Health`, package `com.ebelter.bwell`, version `bwell.1.1.6`, versionCode `39`. The app is not local-only. It has a hardcoded backend at:

```text
https://tj.daxinhealth.com/
```

In normal logged-in use, final scale readings are queued locally and uploaded to:

```text
https://tj.daxinhealth.com/composition/upload
```

The scale itself does not appear to transmit every advertised body-composition field as separate BLE values. Instead, the scale sends compact measurement facts: weight, body-fat raw/percent value, timestamp, resistance/impedance, unit, status, and small control bytes. The Android app combines that scale data with the current user profile, then calculates the expanded body-composition record locally through a native Chipsea library named `libchipsea_bis_v23x242.so`.

The practical privacy result is simple: the company backend receives the finished health/body-composition record when the official app is used normally. The server does not need to calculate the record itself because the app calculates the values locally and uploads them.

Data-safety posture: weak-to-moderate, cloud-dependent.

This app is not a good fit for a "do not send my scale data to the company" threat model. HTTPS is used for network transport, but that only protects the data in transit from passive observers. It does not prevent the app provider or backend operator from receiving, storing, and processing the measurements.

## Highest-Confidence Findings

These are the strongest conclusions from the combined evidence.

| Finding | Confidence | Why |
|---|---:|---|
| The reviewed BWell Android app is cloud-backed. | High | Backend URLs and upload code are hardcoded in the app. |
| Final scale readings are uploaded to `tj.daxinhealth.com/composition/upload`. | High | The app builds a JSON map and posts queued scale records to that endpoint. |
| The official app calculates most body-composition values locally before upload. | High | Java calls the Chipsea native library with height, weight, impedance, sex, age, and role type. |
| The BLE protocol uses service `0000faa0` with write `0000faa1` and notify/read `0000faa2`. | High | Present in app constants and confirmed by live BLE testing. |
| App-to-scale command packets start with `AB`. Scale-to-app packets start with `8D`. | High | Present in app parser/builder behavior and confirmed by live packets. |
| App-to-scale payload bytes after offset 3 are XORed with repeating key `16 15 14 13 12 11`. | High | Recovered from app behavior and confirmed by working command reproduction. |
| The app supports offline/history records from the scale. | High | App code requests history, parses history packets, displays an offline-record prompt, and can upload accepted records. |
| Stored/offline readings could be exposed to a later connecting app profile if the scale retained them. | Medium-high | The code path exists; whether every standalone weigh-in is retained by this exact hardware requires more hardware testing. |
| A clear-history command is not fully proven. | Medium | History ACK and delete-all-user-info commands exist, but decompiled code does not prove which one clears stored measurements. |
| Local saved password protection is weak. | High | Saved password is reversible DES with hardcoded key material derived from `MD5("Ze")`. |
| Crash telemetry is tied to account identifiers. | High | Tencent Bugly is initialized and user id/email/mobile/nickname are set after login. |

## What Data Is Sent To The Company

The official app uploads a scale record containing:

| Upload field | Meaning |
|---|---|
| `userid` | Cloud/app user id. |
| `weight` | Weight. |
| `bmi` | Body mass index. |
| `bmr` | Basal metabolic rate. |
| `adipose_rate` | Body fat percentage. |
| `visceralFat` | Visceral fat value. |
| `muscleQuantity` | Muscle mass/quantity value. |
| `boneMass` | Bone mass value. |
| `protein` | Protein percentage. |
| `moisture` | Water rate. |
| `boneMineralContent` | App maps this from its skeletal-muscle field in the upload method. |
| `bodySocre` | Body score. The app/server spelling is `bodySocre`, not `bodyScore`. |
| `physicalAge` | Body age. |
| `weightControl` | Weight-control/target-related value. |
| `size` | Optional size/body-shape-ish value when nonzero. |
| `testDate` | Measurement timestamp. |

Fields present locally but not observed in the specific `/composition/upload` JSON body:

| Local field | Upload status observed |
|---|---|
| Bluetooth device name | Not seen in scale upload body. |
| Bluetooth MAC/address | Not seen in scale upload body. |
| Raw impedance/resistance | Used locally, not seen in scale upload body. |
| `host_id` | Local database field, not seen in scale upload body. |
| `heartRate` | Local database/history field, not seen in scale upload body. |

This does not mean those values can never reach the backend by any other endpoint or telemetry path. It means they were not present in the reviewed scale-composition upload method.

## Product Identity

| Item | Value |
|---|---|
| Retail model / MPN | `BW-HW-SS-100-WT` |
| FCC model / product | `EF919B4` / `EF-919B4` |
| FCC ID | `2AAEEEF919B4` |
| Product class | Bluetooth body composition / smart scale |
| UPC observed in retail listings | `805112079612` |
| Labeled power | 3 AAA batteries |
| Labeled weight range | 2.5-150 kg |
| Advertised measurements | Weight, BMI, body fat, muscle mass, skeletal muscle, BMR, visceral fat, body age, water ratio, protein, body score, bone mass |

The FCC applicant/manufacturer is:

```text
Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd.
702, 704, Block C, Tsinghua Unis Science Park,
Hi-Tech Industrial Park North,
Nanshan, Shenzhen, China
```

FCC registration metadata:

| Item | Value |
|---|---|
| FRN | `0022714224` |
| Grantee code | `2AAEE` |
| Product code | `EF919B4` |
| Application/final action date | 2021-06-15 |
| Result | Approved |
| Test firm | EMTEK (Shenzhen) Co., Ltd. |

## FCC Hardware And Radio Findings

Public FCC exhibit set reviewed:

| Exhibit type | Pages | What it contributes |
|---|---:|---|
| User manual | 1 | Basic use, display icons, unit button, battery replacement, FCC notice. |
| Test report | 42 | Radio technical description, FCC Part 15.247 test results, applicant/lab/model data. |
| External photos | 3 | Exterior/industrial design reference. |
| Internal photos | 3 | Internal layout reference and `BT ANT` antenna marking. |
| Label and location | 1 | FCC ID, model/product number, power, weight range. |
| RF exposure | 1 | SAR test exclusion calculation and low-power RF exposure conclusion. |
| Test setup photos | 1 | Lab setup photos for radiated/spurious emission testing. |
| Power of attorney | 1 | Authorization filing artifact. |
| Confidentiality letter | 1 | Confidentiality request artifact. |

The FCC test report describes the equipment under test as:

| Field | Value |
|---|---|
| Product | Body composition scale |
| Model number | `EF919B4` |
| Device type | Bluetooth V4.0 |
| Data rate | 1 Mbps for GFSK |
| Modulation | GFSK |
| Frequency range | 2402-2480 MHz |
| Channels | 40 |
| Max transmit power in test report | 3.382 dBm |
| Antenna | PCB antenna |
| Antenna gain | -5.0 dBi |
| Power supply | AAA 1.5 V x 3 |
| Operating temperature range | +5 C to +40 C |
| Sample received date | 2021-04-13 |

The RF exposure exhibit reports:

| Field | Value |
|---|---|
| Frequency used in SAR exclusion calculation | 2.402 GHz |
| Maximum measured conducted transmitter power | 2.24 dBm |
| Tune-up maximum power | 3.5 dBm |
| 1-g SAR exclusion calculation result | 0.7 |
| Threshold used | 3.0 |
| SAR conclusion | No SAR test required under the exclusion calculation |
| RF exposure signature date | 2021-06-07 |

The internal photo exhibit has little extractable text, but it identifies the Bluetooth antenna area as `BT ANT`. The external/internal photo sets confirm that public FCC image exhibits exist, but the locally extractable text does not expose component part numbers or a full schematic.

The FCC listing indicates block diagram, operational description, and schematics exist as exhibit categories, but they are not public direct files in the reviewed set. Treat those as unavailable/confidential unless separately obtained through authorized channels.

## Retail And Advertised Features

Retail/manual listings for the BWell/BW-HW-SS-100-WT family describe:

| Feature | Observed claim |
|---|---|
| Display | Digital LED display. |
| Platform | Glass platform. |
| App support | Android and iOS app support. |
| Measurements | 12 body measurements. |
| Size | Commonly listed around 10 x 10 x 1 in. |
| Weight | Commonly listed around 2.7 lb. |

The advertised 12 body measurements are:

| # | Measurement |
|---:|---|
| 1 | Weight |
| 2 | BMI |
| 3 | Body fat percent |
| 4 | Muscle mass |
| 5 | Skeletal muscle |
| 6 | BMR |
| 7 | Visceral fat |
| 8 | Body age |
| 9 | Water ratio |
| 10 | Protein percent |
| 11 | Body score |
| 12 | Bone mass |

## User Manual Behavior

The manual identifies the product as a "Smart Body Composition Scale."

User-visible behavior from the manual:

| Behavior | Description |
|---|---|
| Unit selection | A button on the back changes units. Manual text mentions kg/lb. |
| Bluetooth icon | Shows when Bluetooth is enabled. |
| Weighing sequence | User steps on upright; scale measures weight first. |
| Final result display | Scale blinks three times before displaying percentage and heart rate. |
| Low battery | Display shows `Lo`. |
| Battery replacement | Replace with 3 new AAA alkaline batteries. |
| FCC Part 15 notice | Device must accept interference and not cause harmful interference. |

The manual text mentioning "heart rate" is notable because the app and local database contain heart-rate fields. However, heart rate was not observed in the specific scale upload body reviewed here, and the BLE packet interpretation still treats some late bytes as status/unknown/trailer unless proven otherwise by controlled measurements.

## Related Patent / Prior-Art Material

No patent artifact reviewed here uniquely names `BW-HW-SS-100-WT`, `EF919B4`, or FCC ID `2AAEEEF919B4`.

The locally reviewed patent PDFs are Belter-related or closely relevant to the design space, but they should be treated as related prior art, not proof of the exact production design for this scale.

| Patent/publication | Relevance |
|---|---|
| `CN202582695U` | Wireless human body scale architecture: sensor system, microcontroller/control unit, wireless communication module, and storage concepts. |
| `CN203447287U` | Wireless fat/body-composition scale reference. |
| `CN102645261A` | Wireless/cloud weighing meter concept. |
| `CN201697701U` | Earlier electronic scale with storage/transmission discussion. |

The patents matter because they match the broad architecture seen in the product: a weighing/body-composition device with local sensors, a controller, wireless communication, and optional stored/transmitted measurements. They do not, by themselves, prove the exact BLE command set or cloud behavior of the BWell Android app.

## Android App Artifact

| Item | Value |
|---|---|
| App name | Bwell Health |
| Package | `com.ebelter.bwell` |
| Version name | `bwell.1.1.6` |
| Version code | `39` |
| APK/XAPK SHA-256 | `629b1459424ad0f1c95ef628132ae085e7bd4b08601dcb09c2fb47a880dd5eef` |
| Application class | `com.ebelter.bwell.App` |
| Login activity | Exported |
| Health Connect permission activity/alias | Exported |
| Android backup | Enabled with `android:allowBackup="true"` |
| Legacy external storage request | Enabled with `android:requestLegacyExternalStorage="true"` |
| Native scale algorithm library | `libchipsea_bis_v23x242.so` |

The app is broader than just this scale. It contains code and endpoints for scale, blood pressure, blood oxygen, and temperature devices. For this report, the scale path is the important one.

## Account, Profile, And Initialization Data

The app has user login, registration, password reset, profile update, sub-user, target-weight, and dashboard endpoints.

Observed account/profile endpoint categories:

| Endpoint path | Purpose |
|---|---|
| `/user/login22` | Login. |
| `/user/register` | Registration. |
| `/user/register4` | Phone registration path. |
| `/user/registerDemo` | Registration without code / demo-style path. |
| `/user/send/validate/code` | Email validation code. |
| `/user/send/validate/code2` | Phone validation code. |
| `/user/retrieve/password` | Password reset by email. |
| `/user/retrieve/password2` | Password reset by phone. |
| `/user/update/info` | Profile update. |
| `/user/addChild` | Add sub-user. |
| `/user/deleteChildById` | Delete sub-user. |
| `/user/set/target/weight` | Set target weight. |
| `/common/initialize` | App/device/user initialization. |
| `/common/main` | Main dashboard data. |

Initialization sends app/device context including:

| Field class | Examples |
|---|---|
| User context | `userId` |
| App context | app version |
| Phone context | brand/provider, model, Android/system version |
| Locale context | country, language, language tag |

Registration/sub-user creation can send profile data including nickname, email or phone, verification code, password, birthday, height, weight, sex, profession, type, and photo.

The scale algorithm requires profile data. The important fields are height, sex, age, and role/athlete mode. Without those, the scale can still produce a weight, but the app-equivalent body-composition values cannot be calculated the same way.

## Local Credential Storage

The app stores saved login details in Android SharedPreferences:

| Item | Value |
|---|---|
| SharedPreferences file | `"1"` |
| Login email/name key | `"0"` / `"1"` depending use |
| Saved password key | `"2"` |
| Last-used user id key | `"3"` |

The stored password is encrypted, but the protection is weak:

| Property | Detail |
|---|---|
| Cipher family | DES |
| Key material | Derived from `MD5("Ze")` |
| Encoding | Base64 |
| Reversibility | Reversible by app code |

If an attacker or forensic process can extract the app data, this is not strong credential protection. Android backup being enabled increases the importance of that weakness because app data may participate in backup/restore flows depending on OS and device settings.

## Crash Telemetry

Tencent Bugly crash reporting is present.

| Item | Value |
|---|---|
| Bugly App ID | `e81323d3e5` |
| Bugly version observed in library code | 4.1.9.3 |

After login/user save, the app sets Bugly user identifiers:

| Identifier type | Sent to Bugly |
|---|---|
| User id | Yes |
| Email | Yes |
| Mobile phone | Yes |
| Nickname | Yes |

The app's HTTP/JSON callback can post caught exceptions to Bugly. Successful scale upload response bodies were not observed being intentionally sent to Bugly in that callback path, but tying crash reports to account identifiers is still a privacy concern.

## BLE Surface

The scale uses a custom BLE service:

| BLE item | UUID |
|---|---|
| Service | `0000faa0-0000-1000-8000-00805f9b34fb` |
| Write characteristic | `0000faa1-0000-1000-8000-00805f9b34fb` |
| Notify/read characteristic | `0000faa2-0000-1000-8000-00805f9b34fb` |

The Android app subscribes to notifications and writes commands to the write characteristic. Independent live testing reproduced the protocol using the same Windows Bluetooth stack behavior expected for a standard BLE GATT device.

## BLE Framing

App-to-scale packets:

```text
AB <len> <cmd> <payload...>
```

Scale-to-app packets:

```text
8D <len> <cmd> <payload...>
```

App-to-scale command payload protection/obfuscation:

```text
XOR key: 16 15 14 13 12 11
```

Payload bytes from offset 3 onward are XORed with that repeating key for app-to-scale packets. This is not cryptographic security. It is lightweight packet obfuscation or a checksum-like vendor convention. It does not protect the protocol from reverse engineering once the app is available.

## App-To-Scale Commands

The following commands are identified from the app and confirmed or reproduced through local BLE work.

| Command | Meaning |
|---|---|
| `AB 01 B0` | Heartbeat / session keepalive. |
| `AB 01 9C` | Get version. |
| `AB 09 98 ...` | Sync scale clock. |
| `AB 0F 99 ...` | Send/probe user profile. |
| `AB 08 9B ...` | Request history/offline records for current user id/profile family. |
| `AB 07 9B ...` | Alternate history request shape for some models. |
| `AB 02 9B 01` | ACK history record. |
| `AB 02 A3 00` | ACK live/final measurement. |
| `AB 02 A1 00` | Delete all user info, identified in Android code. Use with caution. |
| `AB 02 A7 00` | Query scale user. |
| `AB 03 A2 FA 00` | Set sleep disconnect time. |
| `AB 01 9E` | Query system clock. |

The `delete all user info` command should not be assumed to clear measurement history until verified on hardware. The name implies profile/user-slot deletion, and the app code does not provide a cleanly named "clear all history" command.

## Scale-To-App Responses

Common response command meanings:

| Response cmd | Meaning |
|---|---|
| `0x90` | Scale wake. |
| `0x91` | Scale sleep. |
| `0x92` | Unit changed. |
| `0x98` | Scale clock response. |
| `0x9C` | Version information. |
| `0x9D` | Live measurement packet 1. |
| `0x9E` | Live/final measurement packet 2. |
| `0xA0` | History/offline record. |
| `0xA1` | Upgrade ACK. |
| `0xA2` | Upgrade result. |
| `0xA4` | Low power. |
| `0xA5` | Body-fat measurement error. |
| `0xA9` | History download done. |
| `0xB0` | User info accepted/succeeded. |
| `0xB1` | Preconnect measurement ACK response. |
| `0xB3` | Scale user info. |
| `0xB6` | History/offline record packet 2. |
| `0xB7` | Bind confirm. |

## User Profile Packet

The app sends a profile/probe packet before or during measurement sessions. This gives the scale enough user context for body-composition measurement and/or user matching.

Payload layout:

| Field | Size | Meaning |
|---|---:|---|
| BT id | 7 bytes | Derived from app/cloud user id. |
| Sex + age | 1 byte | Female sets the high bit; lower bits carry age. |
| Height | 1 byte | Height in cm. |
| BLE role type | 1 byte | Normal = 1, athlete = 2 in BLE packet. |
| Last weight | 2 bytes | Tenths of kg, little endian. |
| Last impedance | 2 bytes | Ohms, little endian, or `FF FF` if unknown/out of range. |

The user-id-derived BT id is normalized by taking or padding to 14 hex characters, then splitting into seven bytes. If invalid, the practical fallback is seven zero bytes.

The native body algorithm role input is offset from the BLE role byte:

| Meaning | BLE packet role | Native algorithm role |
|---|---:|---:|
| Normal | 1 | 0 |
| Athlete | 2 | 1 |

## Live / Final Measurement Packet

For `0x9D` and `0x9E` measurement packets, the decoded structure is:

| Byte(s) | Meaning |
|---:|---|
| 1 | Packet length. |
| 2 | Command. |
| 3 | Status. |
| 4-5 | Weight raw, little endian. kg = raw / 10. |
| 6-7 | Fat raw, little endian. percent = raw / 10. |
| 8-9 | Year, little endian. |
| 10 | Month. |
| 11 | Day. |
| 12 | Hour. |
| 13 | Minute. |
| 14 | Second. |
| 15 | Unknown/status byte. |
| 16-17 | Resistance/impedance in ohms, little endian. |
| 18 | Unit byte. |
| 19 | Optional trailer byte when present. |

Unit mapping:

| Unit byte | Unit |
|---:|---|
| 0 | kg |
| 1 | lb |
| 2 | st |
| 4 | jin |

Weight-only/object readings are identified when fat is zero and resistance is zero. Full body-composition calculation needs a meaningful impedance/resistance value and a user profile.

The scale transmits weight at tenths-of-kg resolution. Displaying more decimal places in a reader does not create additional precision if the BLE raw field is only tenths. Conversion to pounds can produce more decimal digits mathematically, but the underlying source measurement remains the raw scale resolution.

## Measurement Lifecycle

The official app's normal scale flow is:

1. App connects to the BLE scale service.
2. App subscribes to notify/read characteristic `0000faa2`.
3. App writes heartbeat/version/profile/clock commands as needed.
4. User wakes or steps on the scale.
5. Scale emits wake and live measurement packets.
6. Weight may appear first as dynamic/live data.
7. Body-composition packet arrives when impedance/body measurement completes.
8. App ACKs the measurement.
9. App calculates app-level body values through the Chipsea native library.
10. App saves the final result locally.
11. Background uploader sends queued final records to the backend when logged in and network is available.

Dynamic/live display is not the upload trigger. The final/confirmed measurement path is what queues the upload record.

## Offline / History Behavior

The app supports scale-side history/offline measurement records.

Protocol elements:

| Direction | Packet | Meaning |
|---|---|---|
| App to scale | `AB ... 9B ...` | Request history/offline records. |
| Scale to app | `0xA0` | History/offline record. |
| Scale to app | `0xB6` | Alternate/second history record packet type. |
| App to scale | `AB 02 9B 01` | ACK a history record. |
| Scale to app | `0xA9` | History download done. |

The Android app path:

1. Requests offline/history data from the scale.
2. Parses history packets into an offline measurement object.
3. Shows an "offline measurements awaiting confirmation" style dialog.
4. If accepted, converts the offline record into a normal scale measurement result.
5. Saves and uploads that converted record through the same path as a live final reading.

Important privacy point:

The offline parser fills user id, sex, height, age, and role from the currently active app/profile context. That means a stored scale record can be interpreted under the current connecting profile rather than carrying a strong original cloud-account identity inside the packet.

Practical implication:

If a person uses the scale while no app is connected, and this hardware stores that measurement as an offline record, a later person connecting with the official app may be able to retrieve it. The app can show the stored/offline reading and, if accepted, upload it under the later/current account. The code proves the app supports this path. It does not prove that every standalone weigh-in is always stored on this exact retail unit.

Clearing history:

The app has a history ACK command and a delete-all-user-info command. The code does not conclusively prove whether history ACK consumes/deletes stored records, only advances transfer, or marks records as uploaded. It also does not prove that delete-all-user-info deletes stored measurements rather than profile slots. Clearing should be treated as hardware-test-required.

## Body Composition Algorithm

The Android app does not implement the full body-composition math in ordinary Java. It calls a native Chipsea routine:

```text
CSBiasAPI.cs_bis_v23x242(...)
```

The native library is:

```text
libchipsea_bis_v23x242.so
```

Important exported symbols:

| Symbol | Role |
|---|---|
| `cal_bia` | Core body impedance analysis math. |
| `cs_bis_v23x242` | C-style wrapper that prepares inputs and calls the core math. |
| `Java_chipsea_bis_v23x242_CSBiasAPI_cs_1bis_1v23x242` | JNI wrapper used by Android Java code. |
| `str2jstring` | JNI/helper string conversion. |

The app-equivalent call shape is:

```text
cs_bis_v23x242(
  2,
  height_cm * 10,
  weight_tenths_kg,
  resistance_ohms * 10,
  1 - sex,
  age_clamped_18_to_80,
  role_type
)
```

Native input meaning:

| Input | Meaning |
|---|---|
| `height_cm * 10` | Height represented as mm-like integer. |
| `weight_tenths_kg` | Weight in tenths of kg. |
| `resistance_ohms * 10` | Impedance/resistance scaled by ten. |
| `1 - sex` | App's sex-code transform before calling native code. |
| `age_clamped_18_to_80` | Age clamped to 18 through 80. |
| `role_type` | Normal/athlete mode, native side 0 or 1. |

Reverse-engineered native struct shape:

| Offset | Type | Meaning |
|---:|---|---|
| `0x00` | double | Height in cm. |
| `0x08` | double | Weight in kg. |
| `0x10` | double | Adjusted impedance. |
| `0x18` | int | Sex code. |
| `0x1c` | int | Age. |
| `0x20` | int | Role type. |

Native output slots:

| Offset | Native field | Meaning |
|---:|---|---|
| `0x00` | `bfm` | Body fat mass. |
| `0x08` | `bfp` | Body fat percent. |
| `0x10` | `slm` | Soft lean mass / muscle-related output. |
| `0x18` | `bmc` | Bone mineral content. |
| `0x20` | `bwm` | Body water mass. |
| `0x28` | `bwp` | Body water percent. |
| `0x30` | `bmr` | Basal metabolic rate. |
| `0x38` | `vfr` | Visceral fat rating. |
| `0x40` | `ma` | Metabolic/body age raw output. |
| `0x48` | `pm` | Protein mass. |
| `0x50` | `smm` | Skeletal muscle mass. |
| `0x58` | `bmi` | Native BMI. |
| `0x60` | `lbm` | Lean body mass. |
| `0x68` | `bw` | Ideal/body-weight related output. |
| `0x70` | `od` | Obesity degree. |
| `0x78` | `bodys` | Body-shape string pointer/buffer. |
| `0x80` | `sbc` | Body score. |

How the BWell app maps native output:

| App value | Source |
|---|---|
| Water rate | `bwp` |
| BMR | `bmr` |
| Visceral fat | `vfr` |
| Muscle volume | `slm` |
| Skeletal muscle | `smm` |
| Bone / bone mineral | `bmc` |
| Protein percent | `pm / weight_kg * 100` |
| Body age | `ma`, then app clamps relative to configured age. |
| Body score | `sbc` |

Values the app directly computes:

```text
BMI = round((weight_kg / ((height_cm * height_cm) / 10000)) * 10) / 10
fat = round(fat_percent * 10) / 10
weight = round(weight_kg * 10) / 10
```

Example input/output from the reproduced native algorithm:

| Input | Value |
|---|---:|
| Height | 175 cm |
| Weight | 60.1 kg |
| Resistance | 424 ohms |
| Sex code | 1 |
| Age | 40 |
| Role type | 0 |

Native/app-style outputs:

| Output | Value |
|---|---:|
| Water rate | 59.1 |
| BMR | 1444.0 |
| Visceral fat | 4.0 |
| Muscle volume | 50.0 |
| Skeletal muscle | 28.6 |
| Bone volume | 3.2 |
| Protein | 24.1 |
| Body age | 30 |
| Body score | 80.6 |

This confirms that the expanded values can be produced locally from BLE data plus profile data. A vendor cloud calculation is not required for those values.

## Upload Pipeline

The official app's upload path is:

1. Final scale result is received by the scale activity.
2. The app normalizes/limits the result values.
3. The app copies values into a local `ScaleUploadMeasureRecord`.
4. The app saves the record into a local database-backed upload queue.
5. After login/user data is saved, a background uploader starts.
6. The uploader loops while active.
7. If network is available, it selects a queued scale record for the current user.
8. It posts the record to `/composition/upload`.
9. If the server returns success with result code `0`, the local queued record is deleted.

This is normal sync behavior, not a local-only workflow.

## Cloud History And Server-Side Processing

The app has endpoints for scale history and aggregate data:

| Endpoint path | Purpose |
|---|---|
| `/composition/get/page/data` | Scale history page. |
| `/composition/deleteById` | Delete scale record by id. |
| `/composition/get/data/day` | Daily scale aggregate/average. |
| `/composition/get/data/week` | Weekly scale aggregate/average. |
| `/composition/get/data/month` | Monthly scale aggregate/average. |

These endpoints imply backend retention and server-side organization of scale records. The app is designed around cloud history, not just a one-time upload ping.

Cloud history response models include more fields than the upload request, such as levels and `impedance` in the history data class. That means the server API schema can represent additional data. It does not prove this app upload method sends raw impedance in the reviewed path.

## Health Connect

Health Connect support is separate from the company backend.

When enabled for the host user, the app can sync:

| Health Connect value | Source |
|---|---|
| Weight | Final scale result. |
| Body fat | Final scale result. |
| BMR | Final scale result. |
| Bone mass/mineral | Final scale result. |
| Timestamp | Measurement time. |

Important: Health Connect sync does not replace the company-backend upload path. The final scale result is still queued for backend upload before/alongside Health Connect behavior.

## Security And Privacy Assessment

### Network privacy

The app uses HTTPS through common Android HTTP libraries. This protects against casual/passive local network observation.

No app-owned certificate pinning or extra app-layer encryption around the scale JSON payload was identified in the reviewed application code. Standard library code may contain generic `CertificatePinner` classes, but that is not the same as the BWell app configuring certificate pinning for this backend.

### Backend privacy

The backend receives the health/body-composition values in ordinary app-layer fields. HTTPS does not hide the data from the backend operator.

### Local phone privacy

Local risks:

| Risk | Why it matters |
|---|---|
| Reversible saved password | If app data is extracted, password recovery is practical. |
| Android backup enabled | App data can be exposed through backup paths depending device/OS settings. |
| Local measurement queues | Final records exist locally before upload and until server success. |
| Local history database | App stores downloaded/processed history records locally. |
| Crash telemetry user identifiers | Bugly reports are associated with account identity fields. |

### Scale-side privacy

The scale has an offline/history protocol. If it stores measurements when no app is connected, later retrieval by another profile is plausible. The app code supports receiving, confirming, and uploading those records.

The scale protocol itself uses simple custom BLE framing and XOR-obfuscated app-to-scale payloads. It should not be treated as a strong privacy boundary against someone who can connect and speak the protocol.

## What The Scale Sends Versus What The App Sends

Scale-originated BLE data:

| Field | From scale? | Notes |
|---|---:|---|
| Weight | Yes | Raw tenths kg. |
| Fat raw/percent | Yes | Present in measurement packet. |
| Timestamp | Yes | Present in measurement packet. |
| Resistance/impedance | Yes | Present in measurement packet. |
| Unit | Yes | kg/lb/st/jin byte. |
| Status/control | Yes | Command/status/trailer bytes. |
| Full water/BMR/visceral/muscle/bone/protein/body-age/body-score set | Not as separate observed BLE fields | App derives these locally through Chipsea native code. |

App-uploaded cloud data:

| Field class | Sent by official app? | Notes |
|---|---:|---|
| User id | Yes | Upload uses `userid`. |
| Final weight | Yes | Uploaded. |
| BMI | Yes | Uploaded. |
| Fat | Yes | Uploaded as `adipose_rate`. |
| BMR | Yes | Uploaded. |
| Visceral fat | Yes | Uploaded. |
| Muscle quantity | Yes | Uploaded. |
| Bone mass | Yes | Uploaded. |
| Protein | Yes | Uploaded. |
| Moisture/water | Yes | Uploaded. |
| Bone mineral / skeletal-muscle mapped value | Yes | Uploaded as `boneMineralContent`. |
| Body score | Yes | Uploaded as `bodySocre`. |
| Physical/body age | Yes | Uploaded. |
| Test date | Yes | Uploaded. |
| Raw Bluetooth address/name | Not in reviewed scale upload | May exist locally. |
| Raw resistance/impedance | Not in reviewed scale upload | History schemas can represent impedance, but upload method did not include it. |

## Practical Threat Model Conclusions

If the goal is "use the official BWell app normally":

- Expect final scale measurements and derived body-composition values to go to `tj.daxinhealth.com`.
- Expect cloud history/averages/dashboard behavior.
- Expect account/profile data to be associated with those measurements.
- Expect Tencent Bugly to receive account identifiers for crash reporting.

If the goal is "use the physical scale without sending data to the company":

- Do not use the official app while logged in with network access.
- A local BLE reader can read live scale data and calculate app-equivalent values without contacting the vendor backend.
- Offline/history records on the scale remain a concern. A later official-app connection may retrieve stored readings if the scale retained them.
- Hardware testing is needed to prove exactly when this retail unit stores offline readings and which command clears them.

If the goal is "make sure another person cannot later retrieve my no-app weigh-in":

- The code alone cannot guarantee that.
- The app clearly supports retrieving offline records.
- Treat no-app weigh-ins as potentially recoverable until the scale-side retention/clear behavior is tested and proven.

## Known Unknowns

These are unresolved or only partially proven.

| Unknown | Current state |
|---|---|
| Does this exact retail unit store every no-app weigh-in? | Plausible, app-supported, but not proven for every scenario. |
| Does history ACK delete, consume, or only acknowledge records? | Not proven. Needs controlled hardware test. |
| Does `deleteAllUserInfo` clear measurements, profiles, or both? | Not proven. Use caution. |
| Exact meaning of every status/trailer byte in measurement packets | Partially decoded, not fully mapped. |
| Full PCB component list | FCC public text does not expose it. |
| Exact production firmware behavior across all variants | Not proven; app supports multiple model paths. |
| Whether the backend stores raw impedance by another path | Not seen in reviewed upload method; history schema includes impedance-capable fields. |

## Bottom Line

The BWell BW-HW-SS-100-WT / EF919B4 scale is a BLE smart body-composition scale whose official Android app is designed for cloud sync. The scale sends compact BLE measurement data; the official app calculates the advertised body metrics locally with a Chipsea native library; then the app uploads the finished health record to `tj.daxinhealth.com` under the user's account.

For normal official-app use, the data is not local-only. The company backend receives the body-composition record. The strongest local-only path is to avoid the official cloud app and use a local BLE reader that implements the protocol and body-composition math without network upload.
