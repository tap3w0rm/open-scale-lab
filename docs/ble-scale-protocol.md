# BWell / Belter BLE Scale Protocol Notes

This document summarizes the BLE protocol behavior observed in the BWell BW-HW-SS-100-WT / EF919B4 scale family and the matching Android app protocol implementation. It is standalone and does not require local research files.

## BLE GATT Surface

| Item | UUID |
|---|---|
| Service | `0000faa0-0000-1000-8000-00805f9b34fb` |
| Write characteristic | `0000faa1-0000-1000-8000-00805f9b34fb` |
| Notify/read characteristic | `0000faa2-0000-1000-8000-00805f9b34fb` |

The app subscribes to notifications from `faa2` and writes commands to `faa1`.

## Packet Framing

App-to-scale packets:

```text
AB <len> <cmd> <payload...>
```

Scale-to-app packets:

```text
8D <len> <cmd> <payload...>
```

App-to-scale payload bytes from offset 3 onward are XORed with a repeating six-byte key:

```text
16 15 14 13 12 11
```

This XOR layer is not strong encryption. It is a lightweight vendor packet transform.

## App-To-Scale Commands

| Command | Meaning |
|---|---|
| `AB 01 B0` | Heartbeat / session keepalive. |
| `AB 01 9C` | Get version. |
| `AB 09 98 ...` | Sync scale clock. |
| `AB 0F 99 ...` | Send/probe user profile. |
| `AB 08 9B ...` | Request history/offline records for one model path. |
| `AB 07 9B ...` | Alternate history request shape for some variants. |
| `AB 02 9B 01` | ACK history record. |
| `AB 02 A3 00` | ACK live/final measurement. |
| `AB 02 A1 00` | Delete all user info, based on app naming. Use with caution. |
| `AB 02 A7 00` | Query scale user. |
| `AB 03 A2 FA 00` | Set sleep disconnect time. |
| `AB 01 9E` | Query system clock. |

## Scale-To-App Responses

| Response cmd | Meaning |
|---|---|
| `0x90` | Wake. |
| `0x91` | Sleep. |
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
| `0xB0` | User info accepted. |
| `0xB1` | Preconnect measurement ACK response. |
| `0xB3` | Scale user info. |
| `0xB6` | History/offline record packet 2. |
| `0xB7` | Bind confirm. |

## User Profile Packet

The app sends profile data to the scale. This packet gives the scale user context and appears to support user matching, body-composition measurement, and offline/history behavior.

| Field | Size | Meaning |
|---|---:|---|
| User-derived BT id | 7 bytes | Derived from app/cloud user id. |
| Sex + age | 1 byte | Female sets the high bit; lower bits carry age. |
| Height | 1 byte | Height in centimeters. |
| BLE role type | 1 byte | Normal = 1, athlete = 2. |
| Last weight | 2 bytes | Tenths kg, little endian. |
| Last impedance | 2 bytes | Ohms, little endian, or `FF FF` if unknown/out of range. |

The body algorithm uses role type differently from the BLE packet:

| Meaning | BLE role byte | Native algorithm role |
|---|---:|---:|
| Normal | 1 | 0 |
| Athlete | 2 | 1 |

## Measurement Packet Decode

For `0x9D` and `0x9E` packets:

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

Unit byte mapping:

| Byte | Unit |
|---:|---|
| 0 | kg |
| 1 | lb |
| 2 | st |
| 4 | jin |

Weight-only/object measurements are identified when fat and resistance are both zero. Full body-composition calculation needs a nonzero impedance/resistance value plus user profile fields.

## History / Offline Records

The app and protocol support offline/history records:

| Packet | Meaning |
|---|---|
| `AB ... 9B ...` | Request history/offline data. |
| `0xA0` | History/offline record. |
| `0xB6` | Alternate/second history record type. |
| `AB 02 9B 01` | ACK history record. |
| `0xA9` | History download done. |

Privacy implication:

If the scale stores a measurement while no app is connected, a later app connection can request offline/history records. The official app can show those records for confirmation and, if accepted, convert them into normal scale results for upload.

The reviewed code does not conclusively prove whether `AB 02 9B 01` deletes a record, marks it consumed, or only acknowledges transfer progress. It also does not prove that `delete all user info` clears measurement history rather than user/profile slots.

## Model-Family Caution

The `faa0/faa1/faa2` service, `AB`/`8D` framing, XOR transform, and Chipsea-style body-composition flow appear likely to apply beyond one retail BWell SKU. However, individual variants may differ in:

| Area | Possible variation |
|---|---|
| History request length | `AB 08 9B` vs `AB 07 9B` style. |
| Weight scaling | Some variants may apply special scaling. |
| Response packet details | Status/trailer byte meanings may differ. |
| Offline storage behavior | Not all models necessarily retain no-app weigh-ins the same way. |
| Heart-rate support | Manual/app fields suggest support, but packet mapping needs controlled confirmation. |

## Conclusion

The protocol is compact and reproducible: subscribe to `faa2`, write transformed `AB` commands to `faa1`, send profile/clock/heartbeat/history commands as needed, decode `8D` measurement packets, and ACK final/history records. The scale sends raw measurement facts; the app expands those facts into body-composition metrics locally.

