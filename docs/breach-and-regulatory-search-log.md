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
| Bytech / BWell | Initial web pass only | No credible public data breach found. Apple listing says Bwell Health has `Data Not Collected`, which requires platform-specific verification. | Check app-store privacy policy URL, state AG portals, FTC, court records, BBB/consumer complaint sources only for leads. |
| Guangzhou Daxin Health Technology | Initial web pass only | No credible public data breach found. Daxin privacy page includes intra-company/affiliate sharing language. | Search Chinese-language policy/regulator sources; check app-store privacy labels for Daxin apps. |
| Shenzhen Belter | Initial web pass only | No credible public data breach found. Public FDA/FCC/product records exist. | Search regulator/court sources; avoid treating product filings as privacy issues. |
| Chipsea | Initial web pass only | No credible public data breach found yet. Public company/stock/solution information found. | Search vulnerability/security news and filings; focus on SDK/cloud/app solution privacy only if data flow appears. |
| Tencent Bugly | Initial web pass only | Proven app telemetry recipient. Official Bugly docs describe crash/operation statistics; SDK privacy review needed. Public developer complaints exist but need primary-source validation. | Review Bugly SDK privacy statement and Google Play SDK Index. Search enforcement/regulator sources. |
| Alibaba Cloud | Infrastructure observation only | Backend IP currently maps to Alibaba (US) Technology / AS45102. No app-specific breach issue found. | Treat as infrastructure. Search only for Daxin-specific hosting disclosures or public incidents impacting this IP/service. |
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
"BYTECH NY" lawsuit privacy
```

Current notes:

- Google Play lists `Bwell health` under `Bytech Intl`.
- Apple lists `Bwell Health` under `BYTECH NY, INC.` and says `Data Not Collected`.
- Bytech publishes multiple related apps/brands, including BWell, Sealy, Brookstone, and Equate apps.

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
- Daxin pages identify product categories including body-fat scales and model `EF-919B4`.
- Daxin app listings identify Guangzhou Daxin Health Technology Co., Ltd. as developer of `ehealth scale` and `ehealth life`.

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
- Need to review exact SDK privacy statement and data fields.

### Alibaba Cloud / AS45102

Used or planned:

```text
"47.88.15.99" Alibaba
"tj.daxinhealth.com" Alibaba
"Alibaba Cloud" "Daxin Health"
"Alibaba Cloud" privacy subprocessors
```

Current notes:

- `tj.daxinhealth.com` currently resolves to `47.88.15.99`.
- ipinfo reports the IP as `AS45102 Alibaba (US) Technology Co., Ltd.`
- This is infrastructure evidence only.

## Initial Source Links

| Source | URL | Use |
|---|---|---|
| Bwell health Google Play listing | https://play.google.com/store/apps/details?id=com.ebelter.bwell | App publisher, downloads, description, support contact, Data Safety label. |
| Bwell Health Apple App Store listing | https://apps.apple.com/us/app/bwell-health/id1566290744 | iOS developer/seller, version history, privacy declaration, policy link. |
| Bytech site | https://www.bytechintl.com/ | Brand/company context. |
| Bytech Smart Home Privacy Policy | https://www.bytechintl.com/bytech-smart-home | Google Play-linked privacy policy and broad collection/sale/share language. |
| BWell site | https://bwellmonitors.com/ | Brand/support context. |
| BWell privacy page | https://bwellmonitors.com/privacy-policy | Apple-linked privacy policy target; static capture needs JavaScript/browser follow-up. |
| Daxin English site | https://en.daxinhealth.com/ | Backend-domain company context. |
| Daxin privacy page | https://en.daxinhealth.com/html/2/yinsibaohu/index.html | Policy language. |
| Daxin about page | https://en.daxinhealth.com/html/2/guanyuwomen/index.html | Company/app/product/affiliate context. |
| Tencent Bugly overview | https://docs.msdk.qq.com/v5/en/Channel/Bugly/ | SDK purpose. |
| Tencent Bugly privacy policy | https://bugly.tds.tencent.com/docs/other/Bugly_Privacy_Policy | SDK privacy review target. |
| Google Play SDK Index for Bugly | https://play.google.com/sdks/details/com-tencent-bugly-crashreport | SDK metadata review target. |
| Google Health Connect docs | https://developer.android.com/health-and-fitness/health-connect | Optional health-store behavior. |
| Apple HealthKit docs | https://developer.apple.com/documentation/healthkit | Optional health-store behavior. |
| Fosun Pharma Belter investment announcement | https://www.fosunpharma.com/en/content/details37_11238.html | Belter investor relationship. |
| FCC EF919B4 page | https://fcc.report/FCC-ID/2AAEEEF919B4 | Hardware applicant/manufacturer. |

## Open Research Risks

| Risk | Mitigation |
|---|---|
| Search result noise from unrelated "breach" pages | Use exact company names, official sources, and source-type filtering. |
| Chinese-language policy/regulatory sources missed | Search Chinese company names and terms such as `隐私`, `个人信息`, `数据泄露`, `处罚`. |
| App-store privacy labels differ by country/version | Capture country, date, app version, and URL. |
| DNS/IP hosting changes | Record lookup date and avoid timeless claims. |
| Vendor graph gets speculative | Use edge labels and confidence levels. |
