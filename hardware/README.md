# BWell / EF919B4 Hardware Evidence Bundle

This folder contains public hardware/regulatory reference material for the BWell `BW-HW-SS-100-WT` smart body-composition scale and its FCC hardware family `EF919B4` / `EF-919B4`.

## Target Product

| Item | Value |
|---|---|
| Retail model / MPN | `BW-HW-SS-100-WT` |
| FCC model / product code | `EF919B4` / `EF-919B4` |
| FCC ID | `2AAEEEF919B4` |
| UPC observed in retail listings | `805112079612` |
| Product class | Bluetooth body composition / smart scale |
| FCC frequency range | 2402-2480 MHz |
| Power from label | 3 AAA batteries |
| Labeled weight range | 2.5-150 kg |

## FCC Registration

| Item | Value |
|---|---|
| Applicant | Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd. |
| FRN | `0022714224` |
| Grantee code | `2AAEE` |
| Product code | `EF919B4` |
| Application / final action date | 2021-06-15 |
| Result | Approved |
| Test firm | EMTEK (Shenzhen) Co., Ltd. |
| Applicant address | 702, 704, Block C, Tsinghua Unis Science Park, Hi-Tech Industrial Park North, Nanshan, Shenzhen, China |

Useful public index pages:

| Page | URL |
|---|---|
| FCC report page | https://fcc.report/FCC-ID/2AAEEEF919B4 |
| Device.report mirror | https://device.report/shenzhen-belter-health-measurement-analysis-technology/ef-919b4 |
| FCC grantee page | https://fccid.io/2AAEE |

The FCC index lists block diagram, operational description, and schematic exhibit categories, but those direct files were not public in the reviewed set.

## FCC Exhibits Included

Files are in [`fcc/`](fcc/).

| File | Pages | SHA-256 | What it contributes |
|---|---:|---|---|
| [`FCC_User_Manual_5293300.pdf`](fcc/FCC_User_Manual_5293300.pdf) | 1 | `D5EF1FCDC34DA28257AA357B6B4B50055FC9633888A5F1CE400D692FCD4C9CD8` | Manual artwork, use flow, display icons, unit button, battery replacement, FCC statement. |
| [`FCC_Test_Report_5293291.pdf`](fcc/FCC_Test_Report_5293291.pdf) | 42 | `E4AEAAD3443A2A0566525CA8F22C9CCAE924BA755167F37B34EDB59C59134F18` | EMC/Bluetooth test report, radio details, lab, dates, model. |
| [`FCC_External_Photos_5293292.pdf`](fcc/FCC_External_Photos_5293292.pdf) | 3 | `391643A30AF75426FBBAE2771AEEE7CD8A5D4D2AE16533A2CBA74C86E2BEEAAA` | Exterior/industrial design reference. |
| [`FCC_Internal_Photos_5293293.pdf`](fcc/FCC_Internal_Photos_5293293.pdf) | 3 | `3D5A2B3A801B4A2C4AA90E74BB307CEB687C21607D22C1122F41E82B80A9375A` | Internal layout reference; extractable text identifies `BT ANT`. |
| [`FCC_Label_and_Location_5293294.pdf`](fcc/FCC_Label_and_Location_5293294.pdf) | 1 | `67EF5B8EDC2390C3C3D0FD309276CC0967FD39ECB70D40EA1BD4CDC51F2F0B31` | Label, FCC ID, model/product number, power, weight range. |
| [`FCC_RF_Exposure_5293297.pdf`](fcc/FCC_RF_Exposure_5293297.pdf) | 1 | `5EF14430DD5C88B1F6EDBFED04AB7CD2794C5A9FC6BAC07415CBCD6FAAA6573B` | RF exposure / SAR exclusion calculation. |
| [`FCC_Test_Setup_Photos_5293299.pdf`](fcc/FCC_Test_Setup_Photos_5293299.pdf) | 1 | `DC5A969BBE5B3309F872C0FED33600A5273C3B748836C4CB6B4C44E38EF87315` | Lab setup photos. |
| [`FCC_POA_5293296.pdf`](fcc/FCC_POA_5293296.pdf) | 1 | `B0547DF808461775CB5B441B217E1567267484B3BFEDF784B8B13F155FBE43BE` | Authorization / power of attorney filing. |
| [`FCC_Confidentiality_Letter_5293290.pdf`](fcc/FCC_Confidentiality_Letter_5293290.pdf) | 1 | `FE4CBEAAEA1126BCE1FC98EC02F6FE4626315C6E414D92AE79704B68A6B2B926` | Confidentiality request filing. |

## Radio Details From FCC Test Report

| Field | Value |
|---|---|
| Product | Body composition scale |
| Model number | `EF919B4` |
| Device type | Bluetooth V4.0 |
| Data rate | 1 Mbps for GFSK modulation |
| Modulation | GFSK |
| Operating frequency range | 2402-2480 MHz |
| Number of channels | 40 |
| Transmit power max | 3.382 dBm |
| Antenna type | PCB antenna |
| Antenna gain | -5.0 dBi |
| Power supply | AAA 1.5 V x 3 |
| Sample received date | 2021-04-13 |
| Temperature range | +5 C to +40 C |

## RF Exposure Result

| Item | Value |
|---|---|
| Frequency used | 2.402 GHz |
| Maximum measured conducted transmitter power | 2.24 dBm |
| Tune-up maximum power | 3.5 dBm |
| 1-g SAR exclusion result | 0.7 |
| 1-g SAR threshold | 3.0 |
| Conclusion | No SAR test required under the exclusion calculation. |

## Manual / User-Facing Behavior

The FCC manual identifies the product as a "Smart Body Composition Scale."

| Behavior | Detail |
|---|---|
| Unit button | Button on the back changes unit. Manual text mentions kg/lb. |
| Bluetooth icon | Displayed when Bluetooth is enabled. |
| Use posture | User should step on gently in an upright standing position. |
| Measurement sequence | Scale measures weight first, then blinks three times before showing percentage and heart rate. |
| Low battery | Display shows `Lo`. |
| Battery replacement | Replace with 3 new AAA alkaline batteries. |

## Retail / Product Reference

Retail listings observed for this model/family describe:

| Feature | Observed claim |
|---|---|
| Display | Digital LED display. |
| Platform | Glass platform. |
| App support | Android and iOS. |
| Measurements | 12 body measurements. |
| Common dimensions | Around 10 x 10 x 1 in. |
| Common listed weight | Around 2.7 lb. |

Advertised measurements:

| # | Measurement |
|---:|---|
| 1 | Weight |
| 2 | BMI |
| 3 | Body fat percentage |
| 4 | Muscle mass |
| 5 | Skeletal muscle |
| 6 | BMR |
| 7 | Visceral fat |
| 8 | Body age |
| 9 | Water ratio |
| 10 | Protein percentage |
| 11 | Body score |
| 12 | Bone mass |

Useful retail/reference pages:

| Page | URL |
|---|---|
| Walmart Business product page | https://business.walmart.com/ip/BWell-Bluetooth-Smart-Scale-with-App-Track-Weight-BMI-Body-Fat-More/892595922 |
| Walmart consumer product page | https://www.walmart.com/ip/BWell-Bluetooth-Smart-Scale-with-App-Track-Weight-BMI-Body-Fat-More/892595922 |
| Security Depot listing | https://securitydepot242.com/product/bw-hw-ss-100-wt-bt-smart-scale-w-12-different-body-measurements/ |
| Example eBay listing with FCC/model details | https://www.ebay.com/itm/317877453646 |

## Related Patent / Prior-Art Set

Files are in [`patents/`](patents/).

No included patent was found that uniquely names `BW-HW-SS-100-WT`, `EF919B4`, or FCC ID `2AAEEEF919B4`. Treat these as related Belter/body-scale prior-art references, not proof of exact coverage for this retail model.

| File | SHA-256 | Relevance |
|---|---|---|
| [`CN202582695U_Wireless_human_body_scale.pdf`](patents/CN202582695U_Wireless_human_body_scale.pdf) | `4AE87C58E716D0507757914E1838A0F94AD0E554A05EC382A6B5C1E4A3192E14` | Wireless human body scale architecture: sensors, MCU/control unit, wireless module, storage concepts. |
| [`CN203447287U_Wireless_fat_scale.pdf`](patents/CN203447287U_Wireless_fat_scale.pdf) | `CD33C873DF3E56450728F29FD0E17846FC4C22CE49FAF502A0A571B30BEADA67` | Wireless fat/body-composition scale reference. |
| [`CN102645261A_Wireless_cloud_weighing_meter.pdf`](patents/CN102645261A_Wireless_cloud_weighing_meter.pdf) | `58EBF0F2ADF931BDEF1FF128F36F37AA75B8A43C12FF3FECCA94085350F82D5E` | Cloud/wireless weighing meter concept. |
| [`CN201697701U_Electronic_balance.pdf`](patents/CN201697701U_Electronic_balance.pdf) | `FEA5B4A336003C1189E9ED4EDAE415FF9E493FF34D1776B16D4C2526991D2AF3` | Earlier electronic scale with storage/transmission discussion. |

Additional related patent page not included as a PDF due direct download restrictions:

| Patent | URL |
|---|---|
| `CN201438132U` Solar-energy electronic weigher | https://patents.google.com/patent/CN201438132U/en |

## Protocol Cross-Reference

The hardware evidence lines up with the app/protocol findings elsewhere in this repository:

| Item | Value |
|---|---|
| Android package | `com.ebelter.bwell` |
| BLE scale service | `0000faa0-0000-1000-8000-00805f9b34fb` |
| BLE write characteristic | `0000faa1-0000-1000-8000-00805f9b34fb` |
| BLE notify/read characteristic | `0000faa2-0000-1000-8000-00805f9b34fb` |
| Backend observed in app | `https://tj.daxinhealth.com/` |

