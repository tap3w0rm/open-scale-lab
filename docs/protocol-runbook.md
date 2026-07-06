# BWell Scale BLE Protocol Runbook

This is a step-by-step communication manual for talking to the BWell
BW-HW-SS-100-WT / EF919B4 smart scale as if manually replacing the official
Android app.

It is based on Android app reverse engineering and live BLE testing with a local
Windows reader. It documents what is known, what packet bytes were seen, and
where behavior still needs hardware confirmation.

## BLE Surface

Connect to the device, enumerate GATT, and use:

```text
Service: 0000faa0-0000-1000-8000-00805f9b34fb
Write:   0000faa1-0000-1000-8000-00805f9b34fb
Notify:  0000faa2-0000-1000-8000-00805f9b34fb
```

Required setup:

1. Connect to the scale.
2. Find service `0000faa0`.
3. Find write characteristic `0000faa1`.
4. Find notify characteristic `0000faa2`.
5. Enable notifications on `0000faa2`.
6. Write commands to `0000faa1`.

Do not start sending the profile/session sequence until notifications are
enabled, or wake/status responses can be missed.

## Packet Framing

App to scale:

```text
AB <len> <cmd> <payload...>
```

Scale to app:

```text
8D <len> <cmd> <payload...>
```

The second byte is a vendor length field. For observed packets, total byte count
is usually `len + 3`: frame byte, length byte, command byte, then `len` bytes of
command payload/status/trailer.

## App-To-Scale XOR Rule

For app-to-scale packets, bytes starting at offset 3 are XORed before writing to
BLE.

```text
XOR key: 16 15 14 13 12 11
```

Offsets:

```text
offset 0  frame byte AB, not XORed
offset 1  length, not XORed
offset 2  command, not XORed
offset 3  payload byte 0, XOR with 16
offset 4  payload byte 1, XOR with 15
offset 5  payload byte 2, XOR with 14
offset 6  payload byte 3, XOR with 13
offset 7  payload byte 4, XOR with 12
offset 8  payload byte 5, XOR with 11
offset 9  payload byte 6, XOR with 16 again
```

Example:

```text
raw:       AB 02 A3 00
encrypted: AB 02 A3 16
```

The command has one payload byte, `00`.

```text
00 XOR 16 = 16
```

When this document says "raw", it means the logical packet before XOR. When it
says "write", it means the encrypted bytes that are actually written to the BLE
write characteristic.

Scale-to-app packets are not XORed.

## High-Level Session State Machine

The practical communication order is:

1. Scan.
2. Find likely scale advertisement.
3. Connect.
4. Discover service and characteristics.
5. Enable notify.
6. Send heartbeat.
7. Optionally request version.
8. Optionally query current scale user.
9. Sync clock.
10. Send user profile/probe packet.
11. Optionally query current scale user again.
12. Optionally request history/offline records.
13. Tell user to step on the scale.
14. Receive wake packet.
15. Receive live/final measurement packet.
16. Decode measurement.
17. ACK final measurement.
18. Wait for sleep or re-arm on next wake.

The scale can wake after the app is already running. The reader should be ready
before telling the user to wake or step on the scale.

## Phase 1: Scan And Connect

Scan for either:

```text
advertised service: 0000faa0-0000-1000-8000-00805f9b34fb
advertised name: Body Fat-B2
```

Observed advertisement example:

```text
name="Body Fat-B2"
services=[0000faa0-0000-1000-8000-00805f9b34fb]
```

After connecting, confirm:

```text
found scale write characteristic 0000faa1-0000-1000-8000-00805f9b34fb
found scale notify characteristic 0000faa2-0000-1000-8000-00805f9b34fb
CCCD notify write status=success
```

## Phase 2: Heartbeat

Purpose:

- Start or keep alive the vendor session.
- Provoke a wake/status response when the scale is awake.

Packet:

```text
raw:   AB 01 B0
write: AB 01 B0
```

There is no payload, so XOR does not change the packet.

Expected response seen in live testing:

```text
8D 01 90 90
```

Breakdown:

| Offset | Byte | Meaning |
|---:|---|---|
| 0 | `8D` | Scale-to-app frame. |
| 1 | `01` | Length. |
| 2 | `90` | Wake command/status. |
| 3 | `90` | Payload/trailer echo/status byte. |

## Phase 3: Version Request

Purpose:

- Ask the scale for firmware/version information.
- Useful for logging, but not required for weight capture in tested sessions.

Packet:

```text
raw:   AB 01 9C
write: AB 01 9C
```

There is no payload, so XOR does not change the packet.

Expected response:

```text
8D <len> 9C ...
```

The exact version response payload was not consistently needed by the local
reader and should be logged raw when seen.

## Phase 4: Query Scale User

Purpose:

- Ask the scale for current scale-side user/profile state.
- Useful during setup; not required to decode a final measurement.

Packet:

```text
raw:   AB 02 A7 00
write: AB 02 A7 16
```

Breakdown:

| Offset | Raw byte | Write byte | Meaning |
|---:|---|---|---|
| 0 | `AB` | `AB` | App-to-scale frame. |
| 1 | `02` | `02` | Length. |
| 2 | `A7` | `A7` | Query scale user command. |
| 3 | `00` | `16` | Payload byte XORed with `16`. |

Expected response if implemented by scale:

```text
8D <len> B3 ...
```

`B3` is understood as scale user info. Some runs only produced wake/status
responses around this command.

## Phase 5: Sync Clock

Purpose:

- Set or confirm the scale clock so measurement packets carry sane timestamps.

Raw packet layout:

```text
AB 09 98 <year_le_2> <month> <day> <hour> <minute> <second> <weekday_or_seq>
```

Observed example:

```text
raw:   AB 09 98 EA 07 07 05 17 19 29 07
write: AB 09 98 FC 12 13 16 05 08 3F 12
```

Raw breakdown:

| Offset | Byte(s) | Meaning |
|---:|---|---|
| 0 | `AB` | App-to-scale frame. |
| 1 | `09` | Length. |
| 2 | `98` | Clock sync command. |
| 3-4 | `EA 07` | Year little endian: `0x07EA` = 2026. |
| 5 | `07` | Month: July. |
| 6 | `05` | Day: 5. |
| 7 | `17` | Hour: 23. |
| 8 | `19` | Minute: 25. |
| 9 | `29` | Second: 41. |
| 10 | `07` | Weekday or vendor sequence byte, not fully proven. |

XOR check:

```text
EA XOR 16 = FC
07 XOR 15 = 12
07 XOR 14 = 13
05 XOR 13 = 16
17 XOR 12 = 05
19 XOR 11 = 08
29 XOR 16 = 3F
07 XOR 15 = 12
```

Expected response seen in live testing:

```text
8D 09 98 EA 07 07 05 17 19 29 7B 2B
```

Response breakdown:

| Offset | Byte(s) | Meaning |
|---:|---|---|
| 0 | `8D` | Scale-to-app frame. |
| 1 | `09` | Length. |
| 2 | `98` | Clock response. |
| 3-4 | `EA 07` | Year 2026. |
| 5 | `07` | Month. |
| 6 | `05` | Day. |
| 7 | `17` | Hour. |
| 8 | `19` | Minute. |
| 9 | `29` | Second. |
| 10-11 | `7B 2B` | Vendor status/trailer bytes, not fully mapped. |

## Phase 6: Send User Profile / Probe

Purpose:

- Provide profile context needed for body-composition measurement and user
  matching.
- The profile includes sex, age, height, role, previous weight, and previous
  impedance.

Raw packet layout:

```text
AB 0F 99 <bt_id_7> <sex_age> <height_cm> <role> <last_weight_le_2> <last_impedance_le_2>
```

Observed example:

```text
raw:   AB 0F 99 00 00 00 00 00 00 01 9C B1 01 59 02 A8 01
write: AB 0F 99 16 15 14 13 12 11 17 89 A5 12 4B 13 BE 14
```

Raw breakdown:

| Offset | Byte(s) | Meaning |
|---:|---|---|
| 0 | `AB` | App-to-scale frame. |
| 1 | `0F` | Length. |
| 2 | `99` | Send/probe user profile command. |
| 3-9 | `00 00 00 00 00 00 01` | 7-byte user/profile id. |
| 10 | `9C` | Sex + age. Female high bit set; `0x1C` = age 28. |
| 11 | `B1` | Height: 177 cm. |
| 12 | `01` | BLE role: normal. |
| 13-14 | `59 02` | Last weight: `0x0259` = 601 tenths kg = 60.1 kg. |
| 15-16 | `A8 01` | Last impedance: `0x01A8` = 424 ohms. |

Sex/age byte:

```text
male age 28:   1C
female age 28: 9C
```

The lower bits carry age. The high bit indicates female in the observed app
logic. The app clamps algorithm age to 18-80 for body composition.

Role mapping:

| Meaning | BLE role byte | Native algorithm role |
|---|---:|---:|
| Normal | `01` | `0` |
| Athlete | `02` | `1` |

Expected response seen in live testing:

```text
8D 01 B0 B0
```

Breakdown:

| Offset | Byte | Meaning |
|---:|---|---|
| 0 | `8D` | Scale-to-app frame. |
| 1 | `01` | Length. |
| 2 | `B0` | User info accepted/succeeded. |
| 3 | `B0` | Echo/status byte. |

## Phase 7: Optional History Request

Purpose:

- Ask the scale for stored/offline records.
- This is privacy-relevant because a later app/profile may retrieve stored
  records if the scale retained them.

Observed request:

```text
raw:   AB 08 9B 00 00 00 00 00 00 01 00
write: AB 08 9B 16 15 14 13 12 11 17 15
```

Raw breakdown:

| Offset | Byte(s) | Meaning |
|---:|---|---|
| 0 | `AB` | App-to-scale frame. |
| 1 | `08` | Length. |
| 2 | `9B` | History/offline request command. |
| 3-9 | `00 00 00 00 00 00 01` | 7-byte user/profile id. |
| 10 | `00` | Request/status byte. |

Expected response sequence:

```text
8D <len> A0 ...   history/offline record
8D <len> B6 ...   alternate or second history/offline record packet
8D <len> A9 ...   no more history / history download done
```

ACK a history record:

```text
raw:   AB 02 9B 01
write: AB 02 9B 17
```

The `01` payload is XORed with `16`, producing `17`.

Important unknown:

The code proves the ACK exists. It does not prove whether ACK deletes the stored
record, marks it consumed, or only advances the transfer session. That must be
verified with controlled hardware tests.

## Phase 8: Measurement Capture

At this point the reader should be connected, notifications enabled, clock
synced, and profile sent. The user can step on the scale.

Common scale packets:

```text
8D 01 90 90   wake
8D 11 9D ...  live/intermediate measurement packet
8D 11 9E ...  live/final measurement packet
8D 01 91 91   sleep
```

The scale may send only `9E` in simple object/weight testing, or may send live
and final packets depending on the measurement session.

## Final Measurement Packet Example

Observed final packet:

```text
8D 11 9E 00 59 02 D7 00 EA 07 07 05 04 2F 07 00 A2 01 01 73
```

Full-frame breakdown:

| Offset | Byte(s) | Meaning |
|---:|---|---|
| 0 | `8D` | Scale-to-app frame. |
| 1 | `11` | Length. |
| 2 | `9E` | Final/live measurement command. |
| 3 | `00` | Status. |
| 4-5 | `59 02` | Weight raw little endian: `0x0259` = 601. |
| 6-7 | `D7 00` | Fat raw little endian: `0x00D7` = 215. |
| 8-9 | `EA 07` | Year: `0x07EA` = 2026. |
| 10 | `07` | Month: July. |
| 11 | `05` | Day: 5. |
| 12 | `04` | Hour: 04. |
| 13 | `2F` | Minute: 47. |
| 14 | `07` | Second: 07. |
| 15 | `00` | Unknown/status byte. |
| 16-17 | `A2 01` | Resistance: `0x01A2` = 418 ohms. |
| 18 | `01` | Unit byte: pounds selected/displayed. |
| 19 | `73` | Trailer/check/status byte, not fully mapped. |

Decoded values:

```text
weight_raw = 601
weight_kg = 601 / 10 = 60.1 kg
weight_lb = 60.1 * 2.2046226218 = 132.50 lb

fat_raw = 215
fat_percent = 215 / 10 = 21.5 %

resistance = 418 ohms
timestamp = 2026-07-05 04:47:07
unit = 1 = lb
```

Second observed final packet:

```text
8D 11 9E 00 59 02 D8 00 EA 07 07 05 04 24 05 00 A8 01 01 7F
```

Decoded values:

```text
weight_raw = 0x0259 = 601 = 60.1 kg = 132.50 lb
fat_raw = 0x00D8 = 216 = 21.6 %
timestamp = 2026-07-05 04:36:05
resistance = 0x01A8 = 424 ohms
unit = 1 = lb
```

## Weight-Only / Object Packet Example

Observed object/weight-only style packet:

```text
8D 11 9E 00 41 00 00 00 EA 07 07 05 17 1A 12 00 8C 01 01 A3
```

Decoded values:

```text
weight_raw = 0x0041 = 65 = 6.5 kg = 14.33 lb
fat_raw = 0
timestamp = 2026-07-05 23:26:18
resistance = 0x018C = 396 ohms
unit = 1 = lb
```

This was not a full person/body-composition read in local testing. The app
should not run full body composition unless the measurement has meaningful body
inputs. Full body metrics require nonzero fat/body data and a usable impedance
value.

## ACK Final Measurement

After receiving and decoding the final `0x9E` measurement, send the measurement
ACK.

Packet:

```text
raw:   AB 02 A3 00
write: AB 02 A3 16
```

Expected next response:

```text
8D 01 91 91
```

This is a sleep packet in observed runs.

## Re-Arm After Sleep / Next Wake

After a completed measurement and sleep packet:

```text
8D 01 91 91
```

The app should not spam heartbeat while the scale is asleep. It can remain idle
and wait for a future wake:

```text
8D 01 90 90
```

When a wake packet is received after a completed measurement, re-prime:

1. Sync clock again.
2. Send profile/probe again.
3. Wait for measurement packets.

Observed re-prime sequence:

```text
scale wake:
8D 01 90 90

raw clock:
AB 09 98 EA 07 07 05 17 1A 12 07

write clock:
AB 09 98 FC 12 13 16 05 0B 04 12

clock response:
8D 09 98 EA 07 07 05 17 1A 12 7B 13

raw profile:
AB 0F 99 00 00 00 00 00 00 01 9C B1 01 59 02 A8 01

write profile:
AB 0F 99 16 15 14 13 12 11 17 89 A5 12 4B 13 BE 14

profile accepted:
8D 01 B0 B0
```

## Known App-To-Scale Commands

| Raw command | Write example | Meaning |
|---|---|---|
| `AB 01 B0` | `AB 01 B0` | Heartbeat / keepalive. |
| `AB 01 9C` | `AB 01 9C` | Get version. |
| `AB 02 A7 00` | `AB 02 A7 16` | Query scale user. |
| `AB 09 98 ...` | Payload XORed | Sync clock. |
| `AB 0F 99 ...` | Payload XORed | Send/probe user profile. |
| `AB 08 9B ...` | Payload XORed | Request history/offline records. |
| `AB 02 9B 01` | `AB 02 9B 17` | ACK history record. |
| `AB 02 A3 00` | `AB 02 A3 16` | ACK live/final measurement. |
| `AB 02 A1 00` | `AB 02 A1 16` | Delete all user info; do not assume this clears measurements. |
| `AB 03 A2 FA 00` | `AB 03 A2 EC 15` | Set sleep disconnect time, identified from app code. |
| `AB 01 9E` | `AB 01 9E` | Query system clock, identified from app code. |

## Known Scale-To-App Commands

| Command | Meaning |
|---:|---|
| `0x90` | Wake. |
| `0x91` | Sleep. |
| `0x92` | Unit changed. |
| `0x98` | Clock response. |
| `0x9C` | Version information. |
| `0x9D` | Live/intermediate measurement. |
| `0x9E` | Live/final measurement. |
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

## Measurement Decode Formulae

Given a scale-to-app measurement packet:

```text
8D 11 9E STATUS WL WH FL FH YL YH MO DD HH MM SS U0 RL RH UNIT T
```

Decode:

```text
weight_raw = WL + (WH << 8)
weight_kg = weight_raw / 10
weight_lb = weight_kg * 2.2046226218

fat_raw = FL + (FH << 8)
fat_percent = fat_raw / 10

year = YL + (YH << 8)
month = MO
day = DD
hour = HH
minute = MM
second = SS

resistance_ohms = RL + (RH << 8)
unit = UNIT
```

Unit values:

```text
00 kg
01 lb
02 st
04 jin
```

Full body composition requires:

```text
weight_tenths_kg = weight_raw
fat_tenths_percent = fat_raw
impedance_ohms = resistance_ohms
age
sex
height_cm
role_type
```

The scale does not appear to send every advertised body-composition field as
separate BLE values. The app calculates expanded body values locally.

## Manual Communication Checklist

Use this checklist when driving the scale by hand.

1. Start BLE scan.
2. Wake scale.
3. Find `Body Fat-B2` or service `0000faa0`.
4. Connect.
5. Discover service `0000faa0`.
6. Enable notifications on `0000faa2`.
7. Write heartbeat:

```text
AB 01 B0
```

8. Confirm wake:

```text
8D 01 90 90
```

9. Write get version:

```text
AB 01 9C
```

10. Write query user:

```text
AB 02 A7 16
```

11. Write encrypted clock sync for current time.
12. Confirm clock response `8D 09 98 ...`.
13. Write encrypted profile/probe packet.
14. Confirm profile accepted:

```text
8D 01 B0 B0
```

15. Optionally write encrypted history request.
16. Tell user to step on scale.
17. Capture `8D 11 9D ...` and/or `8D 11 9E ...`.
18. Decode the final packet.
19. Write measurement ACK:

```text
AB 02 A3 16
```

20. Wait for sleep:

```text
8D 01 91 91
```

21. On future wake, re-sync clock and resend profile before accepting another
    measurement.

## Safety Notes

- Do not assume `AB 02 A1 00` clears stored measurements. It is identified as
  delete-all-user-info in app code, but its effect on history/offline records
  is not proven.
- Do not assume history ACK deletes scale-side records. It may only acknowledge
  or advance transfer.
- Log every BLE write and notification during testing.
- Treat the trailer/check/status bytes as unknown until proven by controlled
  packet variation.
- Do not run body-composition calculations for object/weight-only reads unless
  the packet has valid body inputs.
