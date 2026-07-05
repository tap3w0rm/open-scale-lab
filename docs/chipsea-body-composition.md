# Chipsea Body-Composition Algorithm Notes

This document describes how the reviewed BWell Health Android app calculates the expanded body-composition values shown to the user and uploaded to the backend.

## Summary

The scale does not appear to transmit every advertised body-composition value as separate BLE fields. It sends compact measurement facts such as weight, fat raw/percent, timestamp, impedance/resistance, unit, and status bytes.

The Android app calculates the larger body-composition set locally by calling a native Chipsea library:

```text
libchipsea_bis_v23x242.so
```

The relevant native API is:

```text
CSBiasAPI.cs_bis_v23x242(...)
```

This means the vendor cloud is not needed to calculate the body-composition record. The phone app can calculate the values locally, then upload the finished record.

## Native Call Shape

The app-equivalent call is:

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

Input meanings:

| Input | Meaning |
|---|---|
| `height_cm * 10` | Height scaled by ten. |
| `weight_tenths_kg` | Weight in tenths of kg. |
| `resistance_ohms * 10` | Impedance/resistance scaled by ten. |
| `1 - sex` | App sex-code transform before native call. |
| `age_clamped_18_to_80` | Age clamped to 18 through 80. |
| `role_type` | Normal/athlete mode; native side uses 0 or 1. |

The BLE profile packet uses a different role encoding:

| Meaning | BLE role byte | Native role input |
|---|---:|---:|
| Normal | 1 | 0 |
| Athlete | 2 | 1 |

## Native Input Structure

Reverse-engineered native structure:

| Offset | Type | Meaning |
|---:|---|---|
| `0x00` | double | Height in cm. |
| `0x08` | double | Weight in kg. |
| `0x10` | double | Adjusted impedance. |
| `0x18` | int | Sex code. |
| `0x1c` | int | Age. |
| `0x20` | int | Role type. |

The wrapper validates that sex code and role type are in the expected 0/1 range.

## Native Output Structure

The native function writes a set of 64-bit output slots:

| Offset | Field | Meaning |
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

## App Field Mapping

The BWell app maps native output into app values:

| App/display value | Source |
|---|---|
| Water rate | `bwp` |
| BMR | `bmr` |
| Visceral fat | `vfr` |
| Muscle volume | `slm` |
| Skeletal muscle | `smm` |
| Bone / bone mineral | `bmc` |
| Protein percent | `pm / weight_kg * 100` |
| Body age | `ma`, then clamped relative to configured age. |
| Body score | `sbc` |

The app directly computes:

```text
BMI = round((weight_kg / ((height_cm * height_cm) / 10000)) * 10) / 10
fat = round(fat_percent * 10) / 10
weight = round(weight_kg * 10) / 10
```

## Example

Input:

| Input | Value |
|---|---:|
| Height | 175 cm |
| Weight | 60.1 kg |
| Resistance | 424 ohms |
| Sex code | 1 |
| Age | 40 |
| Role type | 0 |

Native raw outputs:

| Output | Value |
|---|---:|
| `bfm` | 6.9 |
| `bfp` | 11.4 |
| `slm` | 50.0 |
| `bmc` | 3.2410099999999957 |
| `bwm` | 35.53777 |
| `bwp` | 59.13106489184692 |
| `bmr` | 1444.0 |
| `vfr` | 4.0 |
| `ma` | 21.12264000000001 |
| `pm` | 14.462229999999998 |
| `smm` | 28.58305 |
| `bmi` | 19.624489795918368 |
| `lbm` | 53.2 |
| `bw` | 66.5 |
| `od` | -9.624060150375938 |
| `sbc` | 80.60447482719101 |

App-style displayed values:

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

## Privacy Meaning

The body-composition calculation is local. The scale sends raw facts; the app expands them into the advertised health metrics; then the official app can upload the finished values to the vendor backend.

Blocking backend access or replacing the official app with a local-only BLE reader can prevent the vendor upload while still allowing the same class of local calculation.

## Caution

The native algorithm is proprietary Chipsea code. The behavior described here comes from reverse engineering and local equivalence testing. Any clean-room reimplementation should be validated against a broad set of input/output vectors before claiming app-equivalent results.

