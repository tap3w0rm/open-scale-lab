# Breach And Regulatory Search Log

This is a working log for data breach, privacy litigation, regulator, SDK compliance, and reputation searches.

Important: "No credible public hit found" means only that this pass did not find a credible public record. It does not prove no incident occurred.

## Search Standards

Preferred sources:

- Official breach notices.
- State attorney-general breach portals.
- HHS OCR breach portal for health-data incidents.
- FTC, FCC, FDA, CFPB, SEC, state AG, EU/UK regulator records.
- Court filings or official settlement sites.
- Reputable security reporting: KrebsOnSecurity, BleepingComputer, The Record, TechCrunch, Wired, Reuters, Bloomberg, etc.
- Official SDK and privacy documentation.

Weak sources:

- SEO company profiles.
- Import/export databases.
- App mirror sites.
- Forum posts and social posts.
- AI-generated market reports.

Weak sources can create leads but should not be used as final proof of breaches or misconduct.

## Initial Company Search Results

| Entity | Search status | Findings so far | Next action |
|---|---|---|---|
| Bytech / BWell | Expanded web pass plus decrypted iOS review | No credible public data breach found in this pass. Google Play says Bwell health has `No data collected`; Apple listing says Bwell Health has `Data Not Collected`. Reviewed Android code and decrypted iOS code both contain Daxin health-upload paths. Search results also show California Proposition 65 notices involving Bytech products, but those are product chemical-exposure notices, not privacy incidents. BBB results show consumer-service complaints/reviews for Bytech products, useful as reputation context only, not privacy evidence. | Continue official state AG/FTC/court searches; keep BBB/app-review/product-regulatory material separate from hard privacy evidence. Runtime-test iOS upload paths. |
| Guangzhou Daxin Health Technology | Expanded web pass plus decrypted iOS review | No credible public data breach found in English and initial Chinese search passes. Daxin privacy page includes intra-company/affiliate sharing language. BWell Android and decrypted BWell iOS both contain `tj.daxinhealth.com` upload paths. Search results show FDA 510(k) device records, establishment/listing records, and Daxin app listings, which are regulatory/ecosystem context rather than privacy incidents. | Search Chinese regulator/court sources more deeply; check whether any official app-security/privacy inspection databases mention Daxin apps. Runtime-capture iOS and Android payloads where possible. |
| Shenzhen Belter | Initial web pass only | No credible public data breach found. Public FDA/FCC/product records and market/investor mentions exist. | Search regulator/court sources; avoid treating product filings as privacy issues. |
| Chipsea | Initial web pass only | No credible public data breach found in English quick pass. Public company/stock/solution information found. | Search vulnerability/security news and filings; focus on SDK/cloud/app solution privacy only if data flow appears. |
| Tencent Bugly | Initial web pass only | Proven app telemetry recipient. Official Bugly docs describe crash/operation statistics; SDK privacy review needed. Public developer complaints exist but need primary-source validation. | Review Bugly SDK privacy statement and Google Play SDK Index. Search enforcement/regulator sources. |
| Alibaba Cloud | Infrastructure observation only | BWell/Sealy/Equate-related Daxin hosts currently map to Alibaba infrastructure. No app-specific breach issue found. | Treat as infrastructure. Search only for Daxin-specific hosting disclosures or public incidents impacting this service. |
| Google Health Connect | Platform docs reviewed | Optional permission-controlled health-store path. | Compare Bwell app permissions/data safety disclosures. |
| Apple HealthKit | Platform docs reviewed | Optional iOS health-store path. Apple listing privacy declaration needs verification. | Compare iOS app declaration with iOS behavior if app is obtained. |

## Search Strings Used Or Planned

### Bytech / BWell

Used or planned:

```text
"Bytech" "data breach"
"Bytech Intl" "privacy"
"BYTECH NY, INC." "privacy policy"
"Bwell Health" "privacy policy"
"Bwell Health" "data collected"
"Bytech" site:oag.ca.gov breach
"Bytech NY Inc" site:ftc.gov privacy
"Bytech NY Inc" site:courtlistener.com privacy
"BYTECH NY" lawsuit privacy
```

Current notes:

- Google Play lists `Bwell health` under `Bytech Intl`.
- Apple lists `Bwell Health` under `BYTECH NY, INC.` and says `Data Not Collected`.
- Bytech publishes multiple related apps/brands, including BWell, Sealy, Brookstone, and Equate apps.
- Sealy Smart Scale's Google Play listing identifies Bytech NY, Inc. at `2585 W 13TH St Brooklyn, NY 11223-5812` and lists Bwell health and Equate Monitors under "More by Bytech Intl".
- FCC records for other Bytech radio products identify Bytech NY Inc. with FRN `0025388125` and the same Brooklyn address. This confirms Bytech as an active electronics/FCC ecosystem participant, but does not by itself involve the BWell scale.
- Search results found California Proposition 65 notices involving Bytech NY, Inc. products. Those are product chemical-exposure/regulatory notices, not privacy or data-breach incidents.
- BBB results show consumer complaints/reviews about Bytech product/customer-service issues. These are reputation leads only; they are not proof of privacy misconduct.
- BWell's JavaScript-rendered privacy page has extractable source text saying the BWell Health app may collect account, device, profile, and measurement-result data; it also says measurement data is stored on the company server for history queries and claims BWell does not sell data.
- Decrypted iOS version `1.0.19` build `2` contains `https://tj.daxinhealth.com/`, account/profile routes, body-composition upload/history routes, `composition/upload`, Apple Health writes, optional Fitbit routes, and local offline body-fat storage.
- The iOS App Store label still says `Data Not Collected`; the decrypted binary evidence creates a static/decompile conflict with that label. Runtime capture is still required for exact payloads and trigger conditions.
- Current web pass found Bytech/related-app privacy labels that differ across apps. A separate Bytech app discloses user content/audio and diagnostics/performance data, showing the publisher ecosystem can make non-empty App Store privacy disclosures in at least some contexts.

### Daxin

Used or planned:

```text
"Guangzhou Daxin Health Technology" "data breach"
"Daxin Health" "privacy policy"
"daxinhealth.com" "privacy"
"tj.daxinhealth.com"
"广州市大昕健康科技有限公司" "隐私"
"广州市大昕健康科技有限公司" "数据泄露"
"ehealth scale" "privacy"
"ehealth life" "privacy"
```

Current notes:

- Daxin privacy language allows use/sharing within company or affiliated enterprises.
- Daxin's app-specific `ehealth scale` policy says connected-device health data including weight, fat rate, BMI, and water rate is recorded.
- Daxin's `ehealth scale` policy names Bugly, Google Fit, Fitbit, Alibaba Cloud SMS, and Tencent enterprise email as third-party services for specific functions.
- Daxin's `ehealth scale` policy says collected information may be stored on app and/or affiliate servers and may be transferred to, accessed from, stored in, or displayed outside the user's country/region.
- Daxin pages identify product categories including body-fat scales and model `EF-919B4`.
- Daxin app listings identify Guangzhou Daxin Health Technology Co., Ltd. as developer of `ehealth scale` and `ehealth life`.
- Daxin's own app-store labels are more explicit than BWell's: `ehealth scale` Apple App Store discloses linked health/fitness data, and `ehealth life` Google Play says personal info, health and fitness, and photos/videos may be collected.
- Search results found an FDA 510(k) clearance letter for Guangzhou Daxin Health Technology Co., Ltd. This is medical-device regulatory context, not a data-practice incident.
- Initial Chinese search for Daxin plus data-leak/penalty terms found Daxin privacy/product pages and unrelated healthcare data-leak articles, but no credible Daxin-specific data breach or privacy penalty in that pass.
- Daxin's `ehealth life` app listing is a follow-up target for policy, backend, and privacy-label comparison.
- The decrypted BWell iOS app uses the same Daxin base host as the reviewed BWell Android app: `https://tj.daxinhealth.com/`.
- BWell iOS upload construction includes health/body-composition fields such as weight, impedance, BMI, fat/adipose, moisture/water, bone, BMR, muscle, visceral fat, protein, body score, physical age, heart rate, and test date.

### 2026-07-05 Expanded Web Pass

Searches run during the post-iOS-decryption update:

```text
site:bbb.org Bytech NY Inc complaints
BYTECH NY, INC. lawsuit privacy complaint
site:ftc.gov "Bytech"
site:ftc.gov "Daxin Health"
site:oag.ca.gov breach "Bytech"
site:oag.ca.gov breach "Daxin"
"Bytech NY Inc" "Proposition 65"
"Guangzhou Daxin Health Technology" "breach"
"Bwell Health" "Data Not Collected"
"Bytech" "Data Not Collected"
"Equate Monitors" "Data Not Collected" "BYTECH NY"
"Sealy Care" "Data Not Collected" "BYTECH NY"
"Sealy Sleep" "Data Not Collected" "BYTECH NY"
"Shenzhen Belter" "privacy" OR "breach" OR "lawsuit"
"Chipsea" "data breach" OR "privacy" OR "vulnerability" smart scale
"Tencent Bugly" "Google Play" "suspended" SDK privacy
"daxinhealth.com" "privacy policy" "Bugly"
"smart scale" privacy security BLE vulnerability
```

Results and classification:

| Lead | Result | Classification |
|---|---|---|
| BBB Bytech Intl profile | BBB profile lists Bytech Intl as not BBB accredited with an F rating for failure to respond to two complaints. | Reputation/customer-service signal only; not privacy or breach proof. |
| California Prop 65 records | California AG records include Prop 65 settlements/notices involving Bytech products. | Product chemical-exposure/legal context only; not privacy or breach proof. |
| FTC searches for Bytech/Daxin | No FTC privacy/data-security enforcement action found in this pass. | Absence of evidence only. |
| State AG breach searches for Bytech/Daxin | No credible breach notice found in this pass. | Absence of evidence only. |
| Litigation search | A patent/product-design dispute involving Bytech appeared in results. | Unrelated to privacy/data practices. |
| Apple Bwell Health listing | Still presents `Data Not Collected` for `Bwell Health` version `1.0.19`. | Conflicts with decrypted iOS static/decompile evidence. |
| Google Play Bwell health listing | Still presents `No data collected` / `No data shared with third parties` for Android. | Conflicts with reviewed Android code evidence. |
| Daxin `ehealth scale` Apple listing | Discloses Health & Fitness data may be collected and linked to identity. | Useful comparison: Daxin-operated scale app labels can disclose health collection. |
| Bytech app-store ecosystem | Other Bytech apps have non-empty privacy labels in at least some listings. | Useful comparison; app-specific review still required. |
| Equate Monitors Apple listing | Apple listing for `Equate Monitors` under `BYTECH NY, INC.` says `Data Not Collected`. | Follow-up target because reviewed Android Equate code uploads oxygen/temperature records to Daxin. Platform/app versions may differ. |
| Sealy Care / Sealy Sleep Apple listings | Apple listings under `BYTECH NY, INC.` also show `Data Not Collected` in the checked storefronts. | Ecosystem label-pattern lead only; app-specific code review needed before alleging mismatch. |
| FTC health-app context | FTC warned that a health app may be covered by the Health Breach Notification Rule if it collects health information and can draw information through an API that syncs with a fitness tracker. | Legal/regulatory context, not a Bytech/Daxin enforcement action. |
| Bugly SDK compliance lead | A Stack Overflow report describes a Google Play suspension attributed to a non-compliant Tencent Bugly SDK with upgrade/install behavior. | Anecdotal SDK-compliance lead only; not proof about the BWell app's Bugly build. |
| Daxin ehealth scale policy | Daxin's app-specific policy names Bugly, Google Fit, Fitbit, Alibaba Cloud SMS, and Tencent enterprise email as third-party services. | Primary policy context for Daxin's own scale app, not automatically BWell policy. |
| Smart-scale security literature | Prior smart-scale security research found BLE/app privacy and security issues in other brands. | General threat context only; not evidence against BWell unless reproduced on this device/app. |
| Belter/Chipsea dirty-laundry pass | No credible Belter or Chipsea privacy breach found in this pass. Results were mostly filings, protocol/community support, or unrelated noise. | Absence of evidence only. |

### 2026-07-05 Fresh Follow-Up Pass

Additional searches:

```text
"Guangzhou Daxin Health Technology" privacy data breach penalty
"Daxin Health" "data breach" "privacy"
"BYTECH NY" "data breach" privacy
"Bwell Health" "Data Not Collected" "Daxin"
"Guangzhou Daxin Health Technology" "行政处罚"
"广州大新健康科技" "处罚" OR "泄露" OR "隐私"
"广州大新健康科技有限公司" "数据" "隐私"
"daxinhealth.com" "privacy" "ehealth scale"
```

Results and classification:

| Lead | Result | Classification |
|---|---|---|
| Bytech/Daxin breach search | No credible Bytech-, BWell-, or Guangzhou Daxin-specific breach notice found in this quick pass. | Absence of evidence only; not proof no incident exists. |
| Chinese penalty/privacy search | Results mostly returned broad regulator pages, unrelated health/advertising penalty articles, Daxin app policy pages, and unrelated privacy cases. No Daxin-specific privacy penalty was confirmed in this pass. | Keep searching official Chinese regulator portals. |
| `Daxin` ransomware search noise | Results for "Daxin Team" ransomware are a threat-group naming collision and not evidence about Guangzhou Daxin Health Technology. | Exclude unless a source ties it to the company, which this pass did not. |
| Daxin `ehealth scale` Google Play | Current listing says no third-party sharing, but may collect personal info, health and fitness, and app info/performance. | Useful baseline: Daxin's own Google Play scale-app label acknowledges health-data collection. |
| Daxin `ehealth scale` Apple App Store | Current listing says Health & Fitness data may be collected and linked to identity. | Useful baseline: Daxin's own Apple scale-app label acknowledges linked health-data collection. |
| Daxin general privacy page | Says personal information may be used/shared within the company or affiliated enterprises. | Policy context; not specific proof about BWell backend controller/processor roles. |

### Shenzhen Belter

Used or planned:

```text
"Shenzhen Belter Health Measurement" "data breach"
"Shenzhen Belter" privacy
"倍泰健康" "数据泄露"
"Shenzhen Belter" FDA warning letter
"Shenzhen Belter" lawsuit privacy
```

Current notes:

- FCC applicant for BWell/EF919B4 family.
- Fosun Pharma announced an 11% investment in 2011.
- No direct backend data flow found yet.
- Search results found market/investor documents mentioning Shenzhen Belter, but no credible privacy or breach incident in this pass.

### Chipsea

Used or planned:

```text
"Chipsea" "data breach"
"Chipsea Technologies" privacy
"Chipsea" "smart scale" cloud privacy
"OKOK App" Chipsea privacy
"libchipsea_bis_v23x242"
"cs_bis_v23x242"
```

Current notes:

- App uses Chipsea native BIA library locally.
- Chipsea public materials market smart scale/body-fat scale SOC, BLE, app, cloud, and BIA solution.
- No runtime network flow to Chipsea observed in reviewed Android path.
- First English search pass did not find a credible Chipsea data-breach hit. Results were mostly market reports, SEC/HKEX documents, USB vendor listings, and general cybersecurity-risk language.

### Tencent Bugly

Used or planned:

```text
"Tencent Bugly" privacy
"Tencent Bugly" data breach
"Bugly SDK" privacy statement
"com.tencent.bugly:crashreport" Google Play SDK Index
"Tencent Bugly SDK" compliance
"Bugly" "unknown sources" Google Play
```

Current notes:

- App initializes Bugly and attaches account identifiers.
- App can post caught exceptions to Bugly.
- Bugly's international privacy policy says app/product information collected through the service is stored for 90 days and analysis reports for 2 years.
- Bugly's policy says the app developer/company is responsible for notifying end users or seeking consent for information collected through the service.
- Bugly's Android demo README says the SDK defaults to Android ID for crash device statistics unless a custom device id is set.
- Bugly's Android demo README warns developers not to use Bugly SDKs with upgrade functionality on Google Play.
- A Stack Overflow report says an app was suspended for a non-compliant Tencent Bugly SDK with code to download/install apps from outside Google Play. Treat as anecdotal lead only, not final evidence of this app's SDK behavior.

### Alibaba Cloud / AS45102

Used or planned:

```text
"47.88.15.99" Alibaba
"tj.daxinhealth.com" Alibaba
"Alibaba Cloud" "Daxin Health"
"Alibaba Cloud" privacy subprocessors
```

Current notes:

- `tj.daxinhealth.com`, `sealy.daxinhealth.com`, `test.daxinhealth.com`, and `public.daxinhealth.com` currently resolve to `47.88.15.99`.
- ipinfo reports `47.88.15.99` as `AS45102 Alibaba (US) Technology Co., Ltd.` in Los Angeles, California, US.
- `common.daxinhealth.com` currently resolves to `8.134.36.208`.
- ipinfo reports `8.134.36.208` as `AS37963 Hangzhou Alibaba Advertising Co.,Ltd.` in Guangzhou, Guangdong, China.
- On July 5, 2026, HEAD checks returned `nginx/1.20.1` for `tj`, `sealy`, `test`, and `public` Daxin hosts, with `200` on `tj`/`test` and `404` on `sealy`/`public`. `common` timed out on that check.
- This is infrastructure evidence only.

## Initial Source Links

| Source | URL | Use |
|---|---|---|
| Bwell health Google Play listing | https://play.google.com/store/apps/details?id=com.ebelter.bwell | App publisher, downloads, description, support contact, Data Safety label. |
| Bwell Health Apple App Store listing | https://apps.apple.com/us/app/bwell-health/id1566290744 | iOS developer/seller, version history, privacy declaration, policy link. |
| Bytech site | https://www.bytechintl.com/ | Brand/company context. |
| Bytech support page | https://www.bytechintl.com/support | Official address/phone/fax context. |
| Bytech Smart Home Privacy Policy | https://www.bytechintl.com/bytech-smart-home | Google Play-linked privacy policy and broad collection/sale/share language. |
| BWell site | https://bwellmonitors.com/ | Brand/support context. |
| BWell privacy page | https://bwellmonitors.com/privacy-policy | Apple-linked privacy policy target; static capture needs JavaScript/browser follow-up. |
| Daxin English site | https://en.daxinhealth.com/ | Backend-domain company context. |
| Daxin privacy page | https://en.daxinhealth.com/html/2/yinsibaohu/index.html | Policy language. |
| Daxin ehealth scale app privacy policy | https://img.daxinhealth.com/img/ebas/uploadPath/privacy/index_zh.html | App-specific Daxin scale-policy baseline. |
| Daxin about page | https://en.daxinhealth.com/html/2/guanyuwomen/index.html | Company/app/product/affiliate context. |
| Daxin ehealth scale Apple App Store listing | https://apps.apple.com/us/app/ehealth-scale/id6446401791 | Daxin app-store privacy-label baseline. |
| Daxin ehealth life Google Play listing | https://play.google.com/store/apps/details?id=com.daxinhealth.ehealthlife | Daxin app-store privacy-label baseline. |
| Tencent Bugly overview | https://docs.msdk.qq.com/v5/en/Channel/Bugly/ | SDK purpose. |
| Tencent Bugly privacy policy | https://bugly.tds.tencent.com/docs/other/Bugly_Privacy_Policy | SDK privacy review target. |
| Google Play SDK Index for Bugly | https://play.google.com/sdks/details/com-tencent-bugly-crashreport | SDK metadata review target. |
| Google Health Connect docs | https://developer.android.com/health-and-fitness/health-connect | Optional health-store behavior. |
| Apple HealthKit docs | https://developer.apple.com/documentation/healthkit | Optional health-store behavior. |
| Fosun Pharma Belter investment announcement | https://www.fosunpharma.com/en/content/details37_11238.html | Belter investor relationship. |
| FCC EF919B4 page | https://fcc.report/FCC-ID/2AAEEEF919B4 | Hardware applicant/manufacturer. |
| Sealy Smart Scale Google Play listing | https://play.google.com/store/apps/details?id=com.daxin.sealyscale | Related Bytech app and Bytech NY address context. |
| Bytech FCC FRN example | https://fcc.report/FCC-ID/2AHN6-PGMC200 | Bytech FRN/address context for other radio products. |
| Bytech FCC attestation example | https://fcc.report/FCC-ID/2AHN6-AUBO105/7586685.pdf | Bytech FRN/address/contact context for other radio products. |
| FDA 510(k) K243082 | https://www.accessdata.fda.gov/scripts/cdrh/cfdocs/cfpmn/pmn.cfm?ID=K243082 | Daxin device-regulatory context, not privacy incident. |
| BBB Bytech Intl profile | https://www.bbb.org/us/ny/brooklyn/profile/electronic-equipment-dealers/bytech-intl-0121-93391 | Consumer-service reputation lead only, not privacy proof. |
| ipinfo `47.88.15.99` | https://ipinfo.io/47.88.15.99 | Current backend IP ownership observation. |
| ipinfo `8.134.36.208` | https://ipinfo.io/8.134.36.208 | Current backend IP ownership observation. |

## Open Research Risks

| Risk | Mitigation |
|---|---|
| Search result noise from unrelated "breach" pages | Use exact company names, official sources, and source-type filtering. |
| Chinese-language policy/regulatory sources missed | Search Chinese company names and terms such as `隐私`, `个人信息`, `数据泄露`, `处罚`. |
| App-store privacy labels differ by country/version | Capture country, date, app version, and URL. |
| DNS/IP hosting changes | Record lookup date and avoid timeless claims. |
| Vendor graph gets speculative | Use edge labels and confidence levels. |
