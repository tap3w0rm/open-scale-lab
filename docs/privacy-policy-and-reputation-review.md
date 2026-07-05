# Privacy Policy And Reputation Review

This document is the working review of privacy policies, data-practice claims, reputation signals, breach searches, and unresolved questions for companies in the BWell smart-scale ecosystem.

This is not a final legal conclusion. It is an evidence tracker.

## Executive Summary

The technical evidence proves that the Android app uploads health/body-composition records to a Daxin-hosted backend and sends identifiable crash telemetry to Tencent Bugly. The public-policy picture is less clear: the consumer-facing app publisher is Bytech/BWell, the hardware applicant is Shenzhen Belter, the backend is on Daxin's domain, and crash telemetry goes to Tencent Bugly.

The main privacy gap is responsibility clarity. A user looking at the product, app store, app package, backend hostname, and FCC filing sees different companies at different layers.

Current strongest concerns:

| Concern | Status |
|---|---|
| Health records uploaded to Daxin backend | Proven by Android code. |
| Crash telemetry is identifiable | Proven by app setting Bugly user id/email/mobile/nickname. |
| Bugly may receive health/request context on error paths | Plausible; not yet proven for routine measurement payloads. |
| Daxin policy language allows intra-company/affiliate sharing | Found in Daxin website privacy text; product-specific scope unclear. |
| Google Play says Android Bwell health has "No data collected" | Found in Google Play listing; conflicts with Android upload evidence. |
| Apple App Store says iOS Bwell Health has "Data Not Collected" | Found in Apple listing; iOS runtime verification still needed before claiming a live iOS contradiction. |
| Bytech policy linked from Google Play is broad and not BWell-specific | Found in Bytech Smart Home Privacy Policy. |
| No credible public breach found in first quick pass for Bytech/Daxin/Belter | Absence of evidence only. Needs systematic source log. |

## Company Reviews

### Bytech Intl / BYTECH NY, INC. / BWell

Role:

- App publisher/developer in Google Play and Apple App Store contexts.
- BWell retail/consumer brand.
- Product support/warranty contact for BWell devices.

Known sites:

- https://www.bytechintl.com/
- https://www.bytechintl.com/bwell
- https://bwellmonitors.com/
- https://bwellmonitors.com/contact-us
- https://www.bytechintl.com/bytech-smart-home

Current policy findings:

| Item | Finding |
|---|---|
| App publisher | Google Play lists `Bwell health` by `Bytech Intl`; Apple lists seller/developer as `BYTECH NY, INC.` |
| Google Play Data Safety | Google Play currently says `No data shared with third parties` and `No data collected` for `com.ebelter.bwell`, while also saying data is encrypted in transit and users can request deletion. |
| Google Play policy link | Google Play links to `https://www.bytechintl.com/bytech-smart-home`, a broad Bytech Smart Home Privacy Policy. |
| Bytech policy date | Bytech Smart Home Privacy Policy says last updated July 09, 2024. |
| Bytech policy collection | The policy says Bytech may collect names, email addresses, phone numbers, passwords, mobile app Bluetooth access, mobile device data, log/usage data, crash dumps, IP address, geolocation, and app-feature activity. |
| Bytech policy sensitive data claim | The policy says Bytech does not process sensitive personal information. This needs comparison against the health/body-composition nature of Bwell Health data. |
| Bytech policy sale/share claim | The policy says it has not disclosed, sold, or shared personal information to third parties for a business or commercial purpose in the preceding 12 months and will not sell/share in the future. It also separately says targeted advertising/online tracking may be deemed sale/sharing under some US state laws. |
| BWell site | Apple links to `https://bwellmonitors.com/privacy-policy`, but the static capture renders mostly as a JavaScript shell and still needs browser/screenshot capture. |
| Apple App Privacy | Apple listing says `Data Not Collected` for `Bwell Health` by `BYTECH NY, INC.` |

Potential mismatch:

The Android app uploads account-linked health records to `tj.daxinhealth.com`, but the Google Play listing currently says `No data collected` and `No data shared with third parties`. That is the clearest current app-store privacy-label conflict.

The Apple listing's `Data Not Collected` declaration applies to the iOS app declaration and is not automatically proof about Android. The official iOS package also declares no collected data in its privacy manifest, while visible resources show login, registration, upload, server, offline-sync, Bluetooth, and Apple Health flows. Runtime iOS network testing is required before saying the iOS declaration is false.

Reputation/breach status:

| Search area | Current result |
|---|---|
| Public breach reports | No credible Bytech/BWell breach found in first quick pass. |
| Regulatory/litigation | Not yet systematically checked. |
| App-store privacy concern | Potential privacy-label gap requiring verification. |

Open questions:

- Which legal entity is the data controller for Bwell Health accounts?
- Does Bytech receive or access records stored by Daxin?
- Does the broad Bytech Smart Home policy apply to Bwell Health, or is there a separate Bwell Health policy?
- Why does Google Play say no data is collected when the Android code uploads body-composition records?
- Does Bytech sell/share consumer health data under any US state privacy law definitions?
- Can users delete cloud records and accounts?

### Guangzhou Daxin Health Technology Co., Ltd.

Role:

- Backend/domain family for `tj.daxinhealth.com`.
- Public health-device/app/cloud company.
- Likely operator or provider of the Bwell Health backend, based on hardcoded app endpoint.

Known sites:

- https://en.daxinhealth.com/
- https://daxinhealth.com/
- https://en.daxinhealth.com/html/2/guanyuwomen/index.html
- https://en.daxinhealth.com/html/2/yinsibaohu/index.html

Policy findings:

| Item | Finding |
|---|---|
| Backend endpoint | Android app posts scale records to `https://tj.daxinhealth.com/composition/upload`. |
| Privacy page | Daxin privacy page says personal information may be used and shared within the company or affiliated enterprises for services, transactions, understanding needs, and contact. |
| Product scope | Public Daxin pages list Bluetooth scales and body-fat scales, including `EF-919B4`. |
| Affiliates/entities | Daxin about page says Daxin registered Ehealth Technology Co., Ltd. in Hong Kong and Daxin Health Technology Co., Ltd. in Taiwan. |

Sell/share status:

- The reviewed English privacy language supports "use/share within company or affiliated enterprises."
- No specific evidence yet of sale of BWell user health data.
- Need Chinese policy review and app-specific privacy policy review.

Reputation/breach status:

| Search area | Current result |
|---|---|
| Public breach reports | No credible Daxin health-data breach found in first quick pass. |
| Regulatory/litigation | FDA/device records exist for Daxin devices; no data breach/regulatory privacy hit found yet. |
| Policy concern | Affiliate-sharing language appears broad relative to sensitive health data. |

Open questions:

- Is Daxin the backend data controller, a processor for Bytech, or both?
- Which Daxin affiliate receives or can access BWell records?
- What retention period applies to uploaded scale records?
- Does Daxin allow deletion/export/correction of BWell records?
- Are records stored in China, the US, or both?

### Shenzhen Belter Health Measurement and Analysis Technology Co., Ltd.

Role:

- FCC applicant/manufacturer/OEM for EF919B4.
- Related health-device and body-scale patent owner/assignee in the same product space.

Known/public sites cited by public sources:

- `www.belter.com.cn`
- `www.belterhealth.com`
- historical `www.belterscales.com`

Policy findings:

| Item | Finding |
|---|---|
| Hardware role | FCC filing proves Belter role for EF919B4 hardware. |
| Data role | No direct app upload to a Belter domain has been observed. |
| Investor relationship | Fosun Pharma announced an 11% equity stake in Shenzhen Belter in 2011. |

Reputation/breach status:

| Search area | Current result |
|---|---|
| Public breach reports | No credible Belter breach found in first quick pass. |
| Regulatory/product records | Multiple FCC/FDA-style health-device records exist. These are product/regulatory context, not privacy incidents. |

Open questions:

- Does Belter provide the app SDK/backend, or only hardware/protocol?
- Does Belter receive data from Daxin or Bytech?
- Is there a Belter privacy policy covering connected health products?

### Chipsea Technologies (Shenzhen) Corp.

Role:

- Native BIA/body-composition library provider in the Android app.
- Public smart scale/body-fat scale solution provider.

Known site:

- https://en.chipsea.com/

Policy/reputation findings:

| Item | Finding |
|---|---|
| Local algorithm | App runs `libchipsea_bis_v23x242.so` locally. |
| Public product position | Chipsea markets smart scale/body-fat scale SOC, BLE, app, cloud, and BIA algorithm solutions. |
| Runtime data flow | No network endpoint to Chipsea observed in the reviewed app path. |

Reputation/breach status:

| Search area | Current result |
|---|---|
| Public breach reports | Not yet systematically checked. |
| Data processor risk | Low based on current app evidence, unless further network or policy evidence appears. |

Open questions:

- Is the native library licensed only as local code, or part of a broader Chipsea cloud/app solution?
- Does any related app variant connect to Chipsea/OKOK cloud?

### Tencent Bugly / Tencent

Role:

- Crash reporting / exception telemetry SDK.

Known sites:

- https://bugly.tds.tencent.com/
- https://docs.msdk.qq.com/v5/en/Channel/Bugly/
- https://bugly.tds.tencent.com/docs/other/Bugly_Privacy_Policy

Policy/code findings:

| Item | Finding |
|---|---|
| SDK presence | Android app initializes Tencent Bugly. |
| App ID | `e81323d3e5`. |
| User identifiers | App sets user id, email, mobile phone, and nickname after login. |
| Error posting | App can post caught HTTP/JSON exceptions to Bugly. |
| Bugly purpose | Tencent describes Bugly as exception reporting and operation statistics for mobile developers. |

PII assessment:

Bugly telemetry is identifiable in this app because the app explicitly attaches account identifiers. Health/scale values are not proven to be routinely sent to Bugly, but crash/error content may include request, response, endpoint, or parsing context if failures occur in health-data paths.

Reputation/breach status:

| Search area | Current result |
|---|---|
| SDK compliance concerns | Public developer discussions mention Bugly SDK compliance/policy issues in some contexts; needs primary-source verification before final claim. |
| Public breach reports | Not yet systematically checked. |
| Policy concern | Third-party telemetry gets account identifiers and potentially contextual error data. |

Open questions:

- What exact fields does Bugly collect in version `4.1.9.3`?
- Are exception strings/request bodies included in the app's posted caught exceptions?
- Where is Bugly data stored for this app and who can access it?
- Does the app disclose Bugly clearly to users?

### Google / Android Health Connect

Role:

- Optional user-permission health data store on Android.
- Google Play app distribution.

Known sites:

- https://developer.android.com/health-and-fitness/health-connect
- https://developer.android.com/health-and-fitness/health-connect/data-types

Findings:

| Item | Finding |
|---|---|
| App code | App can sync weight, body fat, BMR, bone mass/mineral, and timestamp to Health Connect. |
| Optionality | Controlled by app setting and Health Connect permissions. |
| Backend independence | Health Connect does not stop vendor backend upload. |

Open questions:

- Does the Google Play Data Safety label disclose the vendor backend and Bugly telemetry accurately?

### Apple / HealthKit

Role:

- Optional iOS health data store.
- App Store distribution.

Known sites:

- https://developer.apple.com/documentation/healthkit
- https://www.apple.com/legal/privacy/data/en/health-app/

Findings:

| Item | Finding |
|---|---|
| App Store description | iOS app says it can share health data to Apple Health. |
| App privacy label | Apple listing says `Data Not Collected` for Bwell Health. |
| Official IPA | Apple-sourced IPA identifies bundle `com.bytechny.B-WELL`, version `1.0.19`, build `2`. |
| iOS privacy manifest | The official package declares `NSPrivacyCollectedDataTypes: []`, no tracking, and no tracking domains. |
| iOS app capabilities | The package declares Bluetooth, HealthKit, camera, photo library, and `NSAllowsArbitraryLoads: true`. |
| iOS evidence boundary | The main executable is App Store encrypted, so runtime traffic capture is needed for backend claims. |

Open questions:

- Does the iOS app use the same Daxin backend?
- Is the `Data Not Collected` declaration accurate for the current iOS app?

### Alibaba Cloud / Alibaba (US) Technology Co., Ltd.

Role:

- Current observed infrastructure provider for the backend IP.

Finding:

| Item | Value |
|---|---|
| Hostname | `tj.daxinhealth.com` |
| Resolved IP observed | `47.88.15.99` |
| IP owner observation | ipinfo reports `AS45102 Alibaba (US) Technology Co., Ltd.`, Los Angeles, California. |

Evidence boundary:

This proves an infrastructure observation at the time checked. It does not prove Alibaba independently uses or sells health data.

Open questions:

- Does Daxin use Alibaba Cloud logs, databases, CDN, storage, or only compute/network hosting?
- Are there Daxin/Alibaba data processing terms or subprocessors?

## Code-vs-Policy Review Targets

| Claim/source | Technical fact to compare |
|---|---|
| Apple `Data Not Collected` for Bwell Health | Does the iOS app actually avoid collection, or does it use the Daxin backend like Android? |
| Google Play `No data collected` for Bwell health | Android code uploads health/body-composition records to Daxin and sends identifiable crash telemetry to Bugly. |
| Bytech/BWell privacy policy | Does it disclose Daxin backend, Tencent Bugly, Health Connect/Apple Health, retention, deletion, affiliates? |
| Daxin privacy policy | Does it cover the BWell app specifically and sensitive health/body data? |
| Tencent Bugly SDK privacy | Does it disclose identifiers and crash report contents consistent with app usage? |

## First-Pass Breach/Reputation Status

| Entity | First-pass result | Confidence |
|---|---|---:|
| Bytech/BWell | No credible public data breach found in quick pass; app privacy-label review needed. | Low-medium |
| Daxin | No credible public data breach found in quick pass; broad affiliate-sharing policy language found. | Low-medium |
| Belter | No credible public data breach found in quick pass; regulatory/product records found. | Low-medium |
| Chipsea | Not enough breach/reputation search completed. | Low |
| Tencent Bugly | Proven telemetry SDK; policy/compliance review needed; anecdotal developer complaints require primary verification. | Medium |
| Alibaba Cloud | Infrastructure provider; broader Alibaba Cloud reputation not specific to this app. | Medium |
| Google/Apple | Platform-policy review needed only for optional health-store paths and app-store declarations. | Medium |

## Next Research Actions

1. Capture dated screenshots/PDFs of Google Play Data Safety and Apple App Privacy labels.
2. Capture the JavaScript-rendered BWell privacy page linked from the Apple listing.
3. Ask Bytech whether the Bytech Smart Home policy is intended to govern Bwell Health.
4. Capture Daxin privacy/legal pages in English and Chinese.
5. Search official breach/regulator portals for Bytech, Daxin, Belter, Chipsea, Tencent Bugly, and Alibaba Cloud.
6. Review Tencent Bugly SDK privacy statement and Google Play SDK Index entry for `com.tencent.bugly:crashreport`.
7. Review Bytech's related apps for package names, backend domains, and privacy labels.
8. Start an evidence table for "sell/share" language by company.
