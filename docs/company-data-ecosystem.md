# Company And Data Ecosystem Map

This document maps the companies and services that appear to be involved in the BWell BW-HW-SS-100-WT / EF919B4 smart-scale ecosystem. It separates what is directly proven by the app/device evidence from what is inferred from public company records and current web research.

## Scope

The core privacy question is not only "what does the app upload?" but also "who might be involved after upload?"

The reviewed Android app proves that final scale records are posted to:

```text
https://tj.daxinhealth.com/composition/upload
```

The app also proves use of Tencent Bugly crash reporting and optional Android Health Connect sync. Public store listings identify Bytech as the app publisher/seller context. FCC filings identify Shenzhen Belter as the hardware applicant/manufacturer. Public Daxin pages and app-store listings identify Guangzhou Daxin Health Technology as a health-device/cloud/app company operating `daxinhealth.com` properties.

What remains unknown: the private contractual and data-processing relationship between Bytech, Belter, Daxin, Chipsea, Tencent, Google, Apple, retailers, and any cloud-hosting provider.

## Data Flow Summary

| Stage | Entity/service | What is known |
|---|---|---|
| Retail brand / US-facing app publisher | Bytech / BWell | Google Play lists `Bwell health` under `Bytech Intl`; Apple lists `Bwell Health` seller/developer as `BYTECH NY, INC.` |
| Hardware applicant/manufacturer | Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd. | FCC ID `2AAEEEF919B4` is filed by Shenzhen Belter for model `EF919B4`. |
| BLE/body scale hardware family | Belter / EF919B4 family | FCC and manual evidence show Bluetooth body-composition scale hardware. |
| Body-composition algorithm/chip ecosystem | Chipsea Technologies | The Android app includes a Chipsea native BIA library; Chipsea publicly markets smart scale/body-fat scale SOC, BLE, app, cloud, and BIA algorithm solutions. |
| Official Android app | Bwell Health / `com.ebelter.bwell` | App reads BLE packets, calculates body values locally, queues final records, and uploads to Daxin backend. |
| Backend API | `tj.daxinhealth.com` | Hardcoded endpoint receives scale upload JSON. Domain belongs to the Daxin Health domain family. |
| Current backend hosting observation | Alibaba (US) Technology Co., Ltd. / AS45102 | DNS currently resolves `tj.daxinhealth.com` to `47.88.15.99`; ipinfo reports that IP under Alibaba (US) Technology. This is hosting/infrastructure evidence, not proof of data use by Alibaba. |
| Crash telemetry | Tencent Bugly | App initializes Tencent Bugly with app id `e81323d3e5` and sets user identifiers after login. |
| Android optional health sharing | Google / Android Health Connect | App can write scale values to Health Connect when user enables it. Health Connect is permission controlled and separate from the vendor backend upload. |
| iOS optional health sharing | Apple Health / HealthKit | App Store description for the iOS app says it can share health data to Apple Health. This is iOS-side ecosystem evidence, not directly from the Android APK. |
| Distribution/download platforms | Google Play, Apple App Store, APK mirrors | These distribute the app or metadata. They are not proven recipients of scale readings merely by hosting the app listing. |
| Retailers | Walmart, eBay sellers, Security Depot, others | Retail/sales channel evidence only. No evidence they receive app measurement data. |

## Entity Table

| Entity | Website(s) | Role in this ecosystem | Parent / ownership notes | Data involvement assessment |
|---|---|---|---|---|
| Bytech Intl / BYTECH NY, INC. | https://www.bytechintl.com/ , https://bwellmonitors.com/ | BWell brand owner/app publisher context for the reviewed app; seller/developer name on app stores. | Appears to be privately held Bytech NY Inc.; no parent company confirmed from public sources reviewed. | Likely consumer-facing brand and support owner. Whether Bytech directly receives uploaded records is not proven by endpoint ownership; the app backend points to Daxin. |
| BWell brand | https://www.bytechintl.com/bwell , https://bwellmonitors.com/ | Retail health-monitor brand used for the scale/app. | Brand under Bytech. | Brand identity; not a separate proven data processor. |
| Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd. | Public pages cite `www.belter.com.cn`, `www.belterhealth.com`, and historical `www.belterscales.com` | FCC applicant/manufacturer/OEM for EF919B4; related Belter patents; prior FDA/FCC filings for health devices. | Fosun Pharma announced an RMB44.76M investment for an 11% equity stake in Shenzhen Belter in 2011. Fosun described Shenzhen Belter as an associate after 2012, not a wholly owned subsidiary. | Proven hardware applicant/manufacturer. No direct proof from the reviewed app that scale records are uploaded to Belter-controlled servers. |
| Fosun Pharma | https://www.fosunpharma.com/ | Investor/associate relationship with Shenzhen Belter, based on public 2011 investment announcement. | Public company under Fosun ecosystem; reviewed evidence says 11% stake in Shenzhen Belter. | No evidence Fosun receives app or scale data. Included only as ownership/investor context. |
| Guangzhou Daxin Health Technology Co., Ltd. | https://en.daxinhealth.com/ , https://daxinhealth.com/ | Daxin health-device/cloud/app company; `daxinhealth.com` domain family; app backend hostname is `tj.daxinhealth.com`. | Daxin website states it has registered Ehealth Technology Co., Ltd. in Hong Kong and Daxin Health Technology Co., Ltd. in Taiwan. No larger parent confirmed from reviewed public sources. | Most likely backend/data platform operator based on hardcoded app endpoint. App uploads health/body-composition records to `tj.daxinhealth.com`. |
| Ehealth Technology Co., Ltd. / Daxin Taiwan entity | Mentioned on Daxin site | Daxin international/regional expansion entities. | Daxin website says these entities were registered in Hong Kong and Taiwan. | No direct evidence they receive BWell app data. Included because Daxin lists them as part of its business structure. |
| Chipsea Technologies (Shenzhen) Corp. | https://en.chipsea.com/ | Native BIA/body-composition algorithm provider; smart scale/body-fat scale solution provider. | Public company; stock code `688595`; subsidiaries/branches include Hefei, Xi'an, Shanghai, and Chengdu Chipsea/Xinhai entities per Chipsea contact pages. | The app runs a Chipsea native library locally. No evidence the reviewed app sends records to Chipsea servers. |
| Tencent Bugly / Tencent | https://bugly.tds.tencent.com/ , https://www.tencent.com/ | Crash reporting and app telemetry SDK. | Bugly is a Tencent service. | App initializes Bugly and sets user id/email/mobile/nickname after login; HTTP/JSON exceptions can be posted to Bugly. Bugly is a proven third-party telemetry recipient. |
| Google / Android Health Connect / Google Play | https://developer.android.com/health-and-fitness/health-connect , https://play.google.com/ | App distribution; optional Health Connect integration on Android. | Google LLC / Alphabet Inc. | Google Play distributes app metadata/download. Health Connect can receive weight/body-fat/BMR/bone values only if user enables permissions. Health Connect is separate from vendor backend upload. |
| Apple / App Store / Apple HealthKit | https://developer.apple.com/documentation/healthkit , https://www.apple.com/legal/privacy/data/en/health-app/ | iOS app distribution and optional Apple Health sharing. | Apple Inc. | iOS listing says the app can share data to Apple Health. This does not prove Apple receives Android app data. Apple Health sharing is user-permission based. |
| Alibaba Cloud / Alibaba (US) Technology Co., Ltd. | https://www.alibabacloud.com/ | Current observed hosting/network for backend IP. | Alibaba Cloud is part of Alibaba Group; ipinfo reports `47.88.15.99` as `AS45102 Alibaba (US) Technology Co., Ltd.` | Infrastructure-level involvement only. Hosting provider may carry/store traffic as part of cloud service, but app evidence does not prove Alibaba uses health data for its own purposes. |
| Retailers and resellers | Walmart, eBay, Security Depot and others | Product sales channels. | Varies. | No evidence they receive app/backend measurement records. |

## Backend Operator Notes: Daxin

The app's scale backend is hardcoded to `tj.daxinhealth.com`, with scale records posted to `/composition/upload`. The endpoint domain strongly ties the backend to Daxin's domain family.

Public Daxin pages describe Guangzhou Daxin Health Technology Co., Ltd. as specializing in research, development, production, and sales of human health measurement network analysis products, home medical products, and household appliances. Daxin's own product categories include Bluetooth scales, body-fat scales, blood pressure monitors, infrared thermometers, bathroom scales, and related health-device products.

Daxin's public "about" page lists body-fat scale models including `EF-919B4`, which matches the FCC model family for the BWell scale.

Daxin's privacy page says personal information may be used and shared within the company or with affiliated enterprises to understand user needs, provide better services, perform transactions, and contact users. That is a general website privacy statement, not a specific BWell app data-retention disclosure.

Daxin also publishes its own health apps. Its `ehealth scale` policy says the
app records health data from connected devices, including weight, fat rate, BMI,
and water rate. It also names third-party services including Bugly, Google Fit,
Fitbit, Alibaba Cloud SMS, and Tencent enterprise email for specific app
functions. That policy is not a BWell policy, but it is useful because it shows
how Daxin describes similar scale-app data practices when Daxin is the named
app operator.

Current network observation:

| Hostname | Current resolved IP | Current IP owner observation |
|---|---|---|
| `tj.daxinhealth.com` | `47.88.15.99` | ipinfo reports `AS45102 Alibaba (US) Technology Co., Ltd.`, Los Angeles, California, US. |

This can change over time. DNS/IP hosting should be rechecked before making current claims.

## App Publisher / Brand Notes: Bytech And BWell

Google Play identifies the Android app publisher as `Bytech Intl`. Apple identifies the iOS app seller/developer as `BYTECH NY, INC.`. Bytech's website presents BWell as one of its brands/product areas, and the BWell contact page uses a Bytech email domain.

Public Bytech pages describe Bytech as a Brooklyn, New York company in consumer/mobile accessories and related retail product lines. Its support page lists:

```text
Bytech Intl
2585 West 13th Street
Brooklyn, NY 11223
Phone: 718.449.3700
Fax: 718.449.2488
```

Data role assessment:

- Bytech/BWell is the consumer-facing brand and app publisher.
- The reviewed Android app's health-record backend does not use a Bytech domain; it uses Daxin.
- Therefore, the evidence supports Bytech as the app/brand owner but does not prove Bytech directly operates the measurement database.

## Hardware/OEM Notes: Shenzhen Belter

The FCC filing for `2AAEEEF919B4` identifies Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd. as the applicant/manufacturer for `EF919B4`.

Public third-party/company pages describe Shenzhen Belter as focused on health measurement, body health networking/analysis products, electronic medical products, and mobile/remote medical products. Public regulatory and trade records show Belter-associated blood pressure monitors, thermometers, and scales under multiple brands.

Fosun Pharma announced in 2011 that it would invest RMB44.76 million for an 11% stake in Shenzhen Belter. Fosun later described Shenzhen Belter as an associate, meaning significant influence, not full ownership.

Data role assessment:

- Belter is proven as hardware applicant/manufacturer for the FCC model family.
- Belter likely supplies or designs related hardware and app SDK/protocol elements.
- The reviewed app does not prove direct upload of records to a Belter domain.

## Algorithm / Chip Ecosystem Notes: Chipsea

The reviewed app contains and calls `libchipsea_bis_v23x242.so`, a native Chipsea body-composition/BIA routine.

Chipsea publicly describes smart scale and smart body-fat scale solutions that include SOC chips, BLE chips, the OKOK app, cloud services, and professional BIA measurement algorithms. This aligns with what the BWell app does locally: scale sends compact measurement facts, app/native library computes the expanded body-composition values.

Data role assessment:

- Chipsea is a proven software/algorithm component provider inside the app.
- The reviewed app did not show measurement uploads to a Chipsea domain.
- Chipsea may be upstream technology provider rather than data processor for this app.

## Crash Telemetry: Tencent Bugly

The Android app initializes Tencent Bugly:

```text
Bugly App ID: e81323d3e5
```

The app sets Bugly account identifiers after login:

| Identifier | App behavior |
|---|---|
| User id | Set in Bugly user data. |
| Email | Set in Bugly user data. |
| Mobile phone | Set in Bugly user data. |
| Nickname | Set in Bugly user data. |

The app also posts caught HTTP/JSON exceptions to Bugly.

Tencent Bugly describes itself as exception reporting and operation statistics for mobile app developers. Tencent's Bugly privacy materials describe processing personal information for Bugly SDK products and services.

Data role assessment:

- Bugly is a proven third-party telemetry recipient.
- We did not observe successful scale measurement payloads intentionally sent to Bugly.
- Because the app attaches account identifiers, crash reports/errors may be linkable to a user account.

## Platform Health Stores

### Android Health Connect / Google

The app contains optional Health Connect support. If enabled and permissions are granted, the app can write scale values such as weight, body fat, BMR, bone mass/mineral value, and timestamp.

Google describes Health Connect as an Android platform/API that lets apps store and share health/fitness data with user permission and centralized controls.

Important privacy split:

- Health Connect is optional.
- Health Connect is separate from the vendor backend upload.
- Enabling Health Connect does not stop upload to `tj.daxinhealth.com`.

### Apple Health / HealthKit

The iOS App Store listing for `Bwell Health` says the app can share health data to Apple Health. Apple's HealthKit documentation describes HealthKit as a permission-controlled way for apps to access and share health/fitness data while maintaining user control.

This is relevant to the broader BWell app ecosystem, but it is not evidence from the Android APK.

## What They Do With The Data: Known vs Unknown

Known from app code:

| Entity | Data/action proven |
|---|---|
| Daxin backend | Receives final scale-upload JSON at `/composition/upload`. Provides history, averages, delete, dashboard, login, profile, and initialization endpoints. |
| Tencent Bugly | Receives crash/exception telemetry and app-set user identifiers. |
| Android Health Connect | Can receive selected health values only when enabled by the user. |
| App local storage | Stores queued upload records, local history/current records, saved credentials, and user/profile state. |

Known from public policies/statements:

| Entity | Public statement |
|---|---|
| Daxin | General website privacy statement says personal information may be used/shared within the company or affiliated enterprises for services, transactions, understanding needs, and contact. |
| Tencent Bugly | Bugly is a crash/exception/statistics service; privacy materials describe processing personal information for SDK services. |
| Google Health Connect | Health Connect is permission controlled and designed for on-device sharing among apps. |
| Apple HealthKit | Apps must request permission to read/write Health app data; user can manage access. |

Unknown:

| Question | Status |
|---|---|
| Does Bytech receive a copy of measurements from Daxin? | Not proven. |
| Does Belter receive a copy of measurements from Daxin or Bytech? | Not proven. |
| Does Daxin share BWell app records with affiliates beyond general privacy-policy language? | Not proven. |
| Retention period for uploaded scale records | Not found in app code or public Daxin page reviewed. |
| Whether raw server databases include raw impedance even though upload body did not include it | Not proven. |
| Whether Daxin uses uploaded data for analytics/modeling beyond app features | Not proven. |
| Whether Alibaba Cloud has any role beyond hosting/network infrastructure | Not proven. |
| Whether Chipsea receives runtime app measurements | Not proven. |
| Whether Daxin's `ehealth scale` policy terms also govern BWell/Sealy/Equate records | Not proven. |

## Risk Assessment

| Risk | Level | Reason |
|---|---:|---|
| Vendor backend receives health/body-composition records | High | Directly proven by upload endpoint and fields. |
| Backend retention/history | High | History/average/delete endpoints prove cloud history features. |
| Account identifiers tied to health records | High | Upload includes user id and profile/account endpoints exist. |
| Third-party crash telemetry tied to account identifiers | Medium-high | Bugly receives account identifiers; exceptions can be posted. |
| Cross-company sharing after upload | Unknown | Daxin privacy language allows intra-company/affiliate sharing, but specific BWell sharing chain is not proven. |
| Hosting-provider exposure | Medium | Backend currently resolves to Alibaba infrastructure; typical cloud hosting implies infrastructure custody, but not independent data use. |
| Retailer access to health records | Low based on evidence | Retailers sell the product; no data path to retailers was observed. |
| Chipsea runtime data access | Low based on evidence | Native library runs locally; no Chipsea network endpoint observed. |

## Source Links

| Source | URL |
|---|---|
| Bwell health on Google Play | https://play.google.com/store/apps/details?id=com.ebelter.bwell |
| Bwell Health on Apple App Store | https://apps.apple.com/us/app/bwell-health/id1566290744 |
| Bytech main site | https://www.bytechintl.com/ |
| Bytech BWell page | https://www.bytechintl.com/bwell |
| BWell contact page | https://bwellmonitors.com/contact-us |
| Daxin English site | https://en.daxinhealth.com/ |
| Daxin about page | https://en.daxinhealth.com/html/2/guanyuwomen/index.html |
| Daxin privacy page | https://en.daxinhealth.com/html/2/yinsibaohu/index.html |
| Daxin ehealth scale app privacy policy | https://img.daxinhealth.com/img/ebas/uploadPath/privacy/index_zh.html |
| Daxin ehealth scale Google Play listing | https://play.google.com/store/apps/details?id=com.daxinhealth.bodyfatscale |
| Daxin ehealth scale Apple App Store listing | https://apps.apple.com/us/app/ehealth-scale/id6446401791 |
| Daxin ehealth life Google Play listing | https://play.google.com/store/apps/details?id=com.daxinhealth.ehealthlife |
| Chipsea main site | https://en.chipsea.com/ |
| Chipsea smart body-fat scale solution | https://en.chipsea.com/app/ydjk/743.html |
| Chipsea contact/company page | https://en.chipsea.com/about/call/ |
| Tencent Bugly overview | https://docs.msdk.qq.com/v5/en/Channel/Bugly/ |
| Tencent Bugly privacy policy | https://bugly.tds.tencent.com/docs/other/Bugly_Privacy_Policy |
| Google Health Connect docs | https://developer.android.com/health-and-fitness/health-connect |
| Google Health Connect data types | https://developer.android.com/health-and-fitness/health-connect/data-types |
| Apple HealthKit docs | https://developer.apple.com/documentation/healthkit |
| Apple Health app privacy | https://www.apple.com/legal/privacy/data/en/health-app/ |
| Fosun Pharma Belter investment announcement | https://www.fosunpharma.com/en/content/details37_11238.html |
| FCC report page for EF919B4 | https://fcc.report/FCC-ID/2AAEEEF919B4 |
