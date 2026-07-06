# Sell, Share, And Policy Matrix

This document tracks what the involved companies say about collection, sale,
sharing, retention, and third-party services, then compares those claims with
the app-code evidence.

It is not a legal conclusion. It is an evidence matrix.

## High-Level Finding

No reviewed source proves that Bytech, BWell, or Daxin sells BWell scale
measurement records.

The stronger, proven issue is different: the Android apps are cloud-backed and
send account-linked health measurements to Daxin domains, while some app-store
labels say no data is collected. The decrypted iOS app now shows the same
class of Daxin account/profile/history/upload code paths, including
`composition/upload`, while the Apple App Store label and iOS privacy manifest
say no data is collected.

Defensible wording:

> Current evidence proves cloud collection and account-linked health data
> upload. It does not prove sale of health records.

Avoid saying:

> They sell the data.

That may be a reasonable concern to investigate, but it is not proven by the
sources reviewed so far.

## BWell / Bytech Policy Evidence

Source:

`https://bwellmonitors.com/privacy-policy`

This page is a JavaScript-rendered Builderall page. Static capture initially
shows only a JavaScript warning, but the policy text is embedded in the page
source.

Important policy language extracted from the page source:

| Topic | Policy text / meaning | Evidence boundary |
|---|---|---|
| Scope | Says the policy covers Bwell or Bwell affiliates and applies when users interact through the website, Bwell Health apps, or directly. | The text oddly says `collectively, "Apple"` when referring to Bwell/Bwell affiliates. Treat this as a drafting/copy issue unless clarified. |
| Collection | Says Bwell may collect account information, nicknames, email addresses, registered devices, account status, device serial number, height, age, gender, athlete status, and measurement result data. | This is policy evidence that BWell acknowledges collection in app use. |
| Measurement storage | Says measurement data from using the product will be stored on the company server for use when querying history. | This aligns with Android code showing cloud history/upload behavior. |
| Sharing | Says Bwell will not share personal data with a third party without consent, except when the user proactively uses share functions in the app. | This does not explain the Daxin backend relationship seen in code. |
| Sale | Says Bwell does not sell data, including as `sale` is defined in New York, Nevada, and California. | This is a policy claim, not independently verified operational proof. |
| Retention | Says Bwell retains personal data for the period necessary for the described purposes and tries to keep it for the shortest time allowed by law. | No concrete retention period for scale records is given in the extracted text. |
| Security | Says Bwell uses managerial, technical, and physical protection measures. | Generic security statement. |
| Third parties | Says Bwell may link to third-party services and tells users to review those privacy policies. | Does not specifically name Daxin or Tencent Bugly in the extracted text. |

Code comparison:

| Policy topic | Android code evidence |
|---|---|
| Measurement storage/history | BWell Android uploads final scale records to `tj.daxinhealth.com` and has cloud history/aggregate/delete endpoints. |
| Account-linked data | Upload uses `userid`; account/profile endpoints use email/phone/nickname/profile facts. |
| Third-party telemetry | Android app initializes Tencent Bugly and sets user id/email/mobile/nickname. |
| Backend vendor clarity | The BWell policy text reviewed does not clearly identify Daxin as backend operator or processor. |
| iOS backend clarity | Decrypted iOS code uses Daxin backend routes, but the Apple label and iOS privacy manifest declare no collected data types. |

## Bytech Smart Home Policy Evidence

Source:

`https://www.bytechintl.com/bytech-smart-home`

This is the policy linked by Google Play for Bwell Health and by related Bytech
apps.

| Topic | Policy text / meaning | Evidence boundary |
|---|---|---|
| Effective date | Last updated July 09, 2024. | General Bytech Smart Home policy, not BWell-specific by title. |
| Collection | Lists names, email addresses, phone numbers, passwords, geolocation, Bluetooth/mobile-device access, mobile device data, log/usage data, crash dumps, IP address, and app-feature activity. | Broad collection language fits an app ecosystem better than the Google Play `No data collected` label. |
| Sensitive data | Says it does not process sensitive personal information. | This is hard to reconcile with health/body-composition apps unless the policy is not intended for BWell Health or uses a narrow legal definition. |
| Sharing | Says information may be shared in specific situations and with third parties, including business transfers and certain Google Maps Platform API uses. | Does not specifically describe Daxin backend processing for BWell/Sealy/Equate health records. |
| Sale/share | Says it has not disclosed, sold, or shared personal information for a business or commercial purpose in the prior 12 months and will not sell/share personal information in the future. It separately discusses targeted advertising/tracking that may be deemed sale/sharing under some US state laws. | Policy claim only; no proof of data sale found. |
| Retention | Says data is kept as long as necessary for policy purposes unless law requires otherwise. | No app-specific retention period for health records. |

## Daxin Website Policy Evidence

Source:

`https://www.daxinhealth.com/html/1/yinsibaohu/index.html`

This is Daxin's general website privacy page.

| Topic | Policy text / meaning | Evidence boundary |
|---|---|---|
| Personal information | Defines personal information broadly, including name, sex, date of birth, ID number, address, phone, email, occupation, education, and work experience. | Website policy, not app-specific. |
| Affiliate use/share | Says provided personal information will be used/shared inside the company or among affiliates to understand needs, provide services, execute transactions, and contact users. | Supports an affiliate-sharing concern, but does not identify which affiliates receive BWell records. |
| Outside third parties | Says Daxin will not provide personal information to third parties outside the company and affiliates unless by the user's own choice. | General policy language; app-specific backend/vendor chain still needs clarification. |
| Legal disclosure | Says disclosure may occur as required by law. | Standard legal-disclosure language. |

## Daxin ehealth Scale App Policy Evidence

Source:

`https://img.daxinhealth.com/img/ebas/uploadPath/privacy/index_zh.html`

This is an app-specific Daxin policy for `ehealth scale`, dated January 18,
2024.

| Topic | Policy text / meaning | Evidence boundary |
|---|---|---|
| App operator | Says `ehealth scale` is provided by Guangzhou Daxin Health Technology Co., Ltd. | Daxin app, not the BWell app. Useful comparison for Daxin's own scale-app practices. |
| Account/profile collection | Names account login/profile data including name, sex, account, nickname, phone/email. | Daxin acknowledges account/profile data collection in its own health app. |
| Body/profile collection | Names age, height, weight, sex, and birth date for health analysis. | Similar profile data is needed by BWell body-composition calculations. |
| Device health records | Says the app records health data from connected devices, including weight, fat rate, BMI, and water rate. | Directly comparable to BWell/Sealy scale data categories. |
| Device/app data | Names device model, OS version, software package name, software version, network type/signal, and log information. | Similar to initialization/device metadata seen in BWell code. |
| Bugly | Says Bugly is used for error-log collection and analysis; the third-party SDK list says Bugly collects phone model, brand, Android version/API level, vendor OS version, CPU architecture, root status, network type, process name, and PID. | BWell/Sealy/Equate code also initializes Bugly, and BWell/Equate attach account identifiers beyond this generic SDK list. |
| Google Fit / Fitbit | Names Google Fit and Fitbit for non-mainland-China health data sync if authorized. | Related to optional health-platform sharing. |
| AliCloud SMS | Names Alibaba Cloud SMS service and phone number collection for SMS codes. | Relevant to account verification vendor chain, not scale measurement upload by itself. |
| Tencent enterprise email | Names Tencent enterprise email service and email collection for email verification codes. | Relevant to account verification vendor chain. |
| Sharing | Says the app will not provide, sell, rent, share, or trade personal information to unrelated third parties unless permitted by the policy; it allows sharing where a third party and the app or affiliates provide services to the user. | This is broader than "no sharing" because service-provider sharing is allowed. |
| Storage / cross-border | Says collected information may be stored on the app and/or affiliate servers and may be transferred to, accessed from, stored in, or displayed outside the user's country/region. | Important because BWell users are in the US and backend/infrastructure locations are not fully disclosed. |
| Account deletion | Provides `https://public.daxinhealth.com/api/cancel?lang=zh` and says deletion is irreversible and personal information will be deleted except as otherwise required by law. | Useful deletion mechanism lead. |

## Daxin App-Store Label Baseline

Daxin's own app-store labels are more explicit than BWell's labels.

| App | Platform | Label summary |
|---|---|---|
| `ehealth scale` | Apple App Store | Says `Health & Fitness` data may be collected and linked to identity. |
| `ehealth life` | Apple App Store China | Says identifiers may be used for tracking and health/fitness, location, contact info, user content, and identifiers may be linked to identity. |
| `ehealth life` | Google Play | Says no third-party sharing; may collect personal info, health and fitness, and photos/videos. |

Why this matters:

These Daxin-operated apps acknowledge health-data collection in store labels and
policy language. That makes BWell's `No data collected` declarations more
notable, because the BWell Android app uses a Daxin backend for similar kinds
of scale/body-composition data, and the decrypted BWell iOS app contains Daxin
upload code paths for similar data.

## Tencent Bugly Policy Evidence

Sources:

- `https://bugly.tds.tencent.com/docs/other/Bugly_Privacy_Policy`
- `https://github.com/BuglyDevTeam/Bugly-Android-Demo`
- `https://developer.android.com/distribute/sdk-index`
- `https://support.google.com/googleplay/android-developer/answer/12034434`

Important findings:

| Topic | Finding |
|---|---|
| Service purpose | Bugly is a crash/exception/operation-statistics service. |
| App developer responsibility | Bugly's policy says the app developer/company is responsible for notifying end users and seeking consent for information collected from apps/products through the service. |
| Retention | Bugly's international policy says information collected from apps/products is stored for 90 days and analysis reports are stored for 2 years. |
| Business transfer | Bugly's policy says information may be disclosed in connection with business transfers, mergers, reorganizations, or asset sales. |
| Android ID note | Bugly's Android demo README says the SDK defaults to Android ID for crash device statistics unless the developer sets a custom device id, and warns developers not to use Bugly SDKs with upgrade functionality on Google Play. |
| SDK Index context | Google says SDK Index provides SDK safety/reliability signals and data-safety guidance, but developers remain responsible for ensuring SDKs do not cause Play policy violations. |

BWell-specific significance:

The risk is not merely that Bugly collects generic crash data. In the BWell app,
the app itself attaches user id, email, mobile phone, and nickname to Bugly.
Equate also sets host id, sub-user id, email, and nickname. That makes crash
telemetry identifiable at the app-account level.

## Current Sell/Share Assessment

| Claim | Current status | Best wording |
|---|---|---|
| BWell Android collects scale data | Proven by code and BWell policy text. | The Android app collects/uploads account-linked scale records. |
| BWell iOS contains Daxin upload paths | Proven by decrypted binary and Ghidra decompile. | The iOS app contains code paths to upload body-composition records to Daxin; exact runtime payloads still need capture. |
| Daxin receives BWell Android scale data | Proven by code endpoint. | The BWell Android app uploads scale records to a Daxin domain. |
| BWell/Bytech sells scale data | Not proven; BWell and Bytech policies claim no sale. | No evidence of sale was found in this pass; policies claim no sale. |
| Daxin sells scale data | Not proven; Daxin ehealth policy says it will not sell/rent/share/trade with unrelated third parties except as policy permits. | No evidence of sale was found in this pass. |
| Service providers can receive data | Supported by policy language and code. | Policies allow service-provider/affiliate/third-party processing in some situations; code proves Bugly and Daxin flows. |
| App-store labels match code | Mixed. | BWell Android, BWell iOS, and Equate labels appear inconsistent with technical evidence; Daxin's own ehealth labels are more explicit. |

## Remaining Questions

1. Is Daxin a processor for Bytech, an independent controller, or both?
2. Does Bytech have administrative access to Daxin-hosted BWell records?
3. Are BWell, Sealy, Equate, and Daxin ehealth records stored in separate
   tenant databases or one shared platform?
4. Which entity receives deletion requests for BWell Health cloud records?
5. Does the BWell privacy page intentionally cover BWell Health apps despite
   the `collectively, "Apple"` drafting error?
6. Why do BWell and Equate app-store labels say no data is collected when
   Android code shows account-linked uploads?
7. Why does the BWell iOS App Store label and privacy manifest say no data is
   collected when decrypted iOS code contains Daxin upload paths?
