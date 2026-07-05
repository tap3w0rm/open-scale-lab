# Privacy Research Plan And Evidence Rules

This document defines the research scope for the privacy-policy, reputation, breach, and vendor-web investigation around the BWell/BWell Health smart-scale ecosystem.

The purpose is to keep the investigation disciplined as the company graph expands.

## Core Questions

1. Which companies receive data directly from the app?
2. Which companies are involved as brand owners, hardware vendors, SDK providers, hosting providers, app stores, or retailers?
3. Which entities are likely controllers, processors, vendors, affiliates, or infrastructure providers?
4. What do the companies' privacy policies say about collection, sharing, sale, retention, deletion, cross-border transfer, and sensitive health data?
5. Do app-store privacy declarations match what the app code and network behavior show?
6. Do any involved companies have credible public records of data breaches, privacy lawsuits, regulatory action, SDK compliance problems, or security incidents?
7. Are there named vendors, affiliates, subprocessors, or technology partners that require follow-up review?
8. What remains unknown and should be asked of the companies directly?

## Working Thesis

The app creates linkable data flows. Even where full database sharing is not proven, the identifiers and telemetry are sufficient to make cross-system correlation plausible if the app operator or vendor has access to both sides.

Defensible wording:

> The official app does not keep sensitive health data in one clean privacy box. Measurements, account identity, device metadata, and crash telemetry are split across multiple systems with enough identifiers to support correlation.

Avoid unsupported wording:

> All involved companies have access to all data.

The second claim may be possible in some private business arrangement, but it is not proven by the current evidence.

## Evidence Tiers

| Tier | Source type | How to use it |
|---:|---|---|
| 1 | App code, live network behavior, official privacy policy, regulator record, court filing, official breach notice | Strong evidence. Can support direct claims. |
| 2 | App-store listing, FCC/FDA filing, official company website, investor filing | Good evidence. Use for roles, declarations, company relationships, and product claims. |
| 3 | Reputable journalism/security reporting | Useful for reputation, incidents, and context. Verify against primary documents when possible. |
| 4 | Company databases, import/export sites, app mirror pages, market reports | Use as leads or weak context only. Do not use alone for sensitive claims. |
| 5 | Forums, social media, random SEO pages | Do not use for final claims unless clearly labeled as unverified user reports. |

## Entity Types

| Type | Meaning |
|---|---|
| App publisher | Listed app developer/seller or consumer-facing brand. |
| Backend operator | Entity controlling the API/domain that receives app data. |
| Hardware/OEM | Manufacturer, FCC applicant, or hardware platform supplier. |
| Algorithm/chip provider | Provides measurement chip, BIA algorithm, or native library. |
| Telemetry SDK | Receives crash, diagnostics, analytics, or operation-statistics data. |
| Platform health store | Optional user-permission health data store such as Health Connect or Apple Health. |
| Infrastructure provider | Cloud, DNS, CDN, hosting, or network provider. |
| Retail channel | Sells product but is not proven to receive app measurement data. |
| Affiliate/investor | Related company by ownership, investment, or corporate statement. |

## Edge Types

Use precise edge labels when drawing or describing the graph.

| Edge | Meaning |
|---|---|
| `uploads_to` | App sends data to this service. |
| `publishes_app` | Entity is listed app publisher/seller/developer. |
| `brands_product` | Entity/brand markets the product. |
| `manufactures_hardware` | Entity is FCC applicant, OEM, or hardware manufacturer. |
| `provides_algorithm` | Entity provides BIA/native algorithm or scale-chip solution. |
| `crash_telemetry_to` | App sends crash/exception/diagnostic data. |
| `optional_sync_to` | Data can be sent only if user enables integration/permissions. |
| `hosted_on` | Backend currently resolves to infrastructure provider. |
| `affiliate_of` | Company claims affiliate/subsidiary/related entity relationship. |
| `investor_in` | Public investment/equity relationship. |
| `retail_seller` | Entity sells product; no app-data path proven. |
| `policy_may_share_with` | Policy language permits sharing with this category. |
| `unproven_possible_vendor` | Lead only; not a claim. |

## Data Categories

| Category | Examples from this project |
|---|---|
| Health/body metrics | Weight, BMI, body fat, BMR, visceral fat, water, protein, muscle, bone, body age, body score. |
| Account identifiers | User id, email, phone/mobile, nickname. |
| Profile facts | Height, sex, age, birthday, target weight, photo, profession. |
| Device/app metadata | Phone brand, model, OS version, app version, language, country. |
| BLE/device data | Scale packets, unit, timestamp, resistance/impedance, optional unknown status bytes. |
| Crash/diagnostics | Bugly crash reports, caught HTTP/JSON exceptions, app-set user identifiers. |
| Optional health-store data | Health Connect or Apple Health values written with user permission. |
| Infrastructure metadata | IP addresses, TLS connection metadata, logs handled by hosting/cloud provider. |

## Policy Review Checklist

For each company, collect:

- Policy URL and effective date.
- Entity named in the policy.
- Whether policy covers the specific app/product or only a website.
- Categories of personal data collected.
- Whether health/body-composition data is specifically named.
- Whether account identifiers are named.
- Whether device diagnostics/crash data is named.
- Purposes of processing.
- "Sell", "share", targeted advertising, or marketing language.
- Affiliate sharing language.
- Vendor/subprocessor language.
- Retention period or deletion criteria.
- Account deletion/export/correction rights.
- Cross-border transfer language.
- Children's/minor data language.
- Security safeguards.
- Contact email/address.
- Whether policy matches app code and app-store privacy labels.

## Reputation And Breach Checklist

For each company, check:

- Official breach notices.
- State attorney-general breach portals.
- HHS OCR breach portal if health data is plausibly involved.
- FTC, FCC, FDA, CFPB, SEC, state AG, EU/UK regulator actions where relevant.
- Court filings and settlement sites.
- Credible security reporting.
- SDK compliance warnings or platform policy issues.
- Public app-store privacy-label conflicts.
- Public vulnerability reports.

Record search strings even when no credible hit is found. "No credible public breach found" is not the same as "no breach occurred."

## Current High-Priority Leads

| Lead | Why it matters |
|---|---|
| Daxin privacy policy | Backend domain receives the health records. Need retention/sharing/deletion language. |
| Bytech/BWell privacy policy | Consumer-facing app publisher/brand. Need controller responsibility and sell/share language. |
| Apple App Store `Data Not Collected` declaration for Bwell Health | Potential mismatch with cloud-connected app behavior; must verify iOS separately before alleging contradiction. |
| Google Play app privacy/data safety label | Must compare against Android code evidence. |
| Tencent Bugly SDK privacy | Proven telemetry recipient with user identifiers set by app. |
| Daxin affiliates | Daxin says personal info may be shared within company or affiliated enterprises. Need list and policy scope. |
| Bytech related apps/brands | Google Play shows Bytech apps for BWell, Sealy, Brookstone, Equate, etc.; investigate whether they reuse similar backend/vendor stack. |
| Alibaba hosting observation | Backend currently resolves to Alibaba infrastructure; infrastructure-only unless policy/contracts say more. |

## Claim Language Guide

| Situation | Use this wording |
|---|---|
| Direct app upload observed | "The app uploads..." |
| App sets identifiers in telemetry SDK | "The app attaches account identifiers to telemetry..." |
| Policy permits sharing | "The policy permits..." |
| Likely but not proven business relationship | "The evidence suggests..." |
| No public breach found | "No credible public breach record was found in this pass..." |
| Vendor/infrastructure only | "Infrastructure-level involvement only; no evidence of independent data use..." |
| App-store label mismatch not fully proven | "Potential mismatch requiring platform-specific verification..." |

