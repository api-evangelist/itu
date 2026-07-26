---
name: Build a country connectivity scorecard from ITU
description: >-
  Assemble ITU's two official composite views of a country's connectivity — the ICT Development
  Index (IDI) and the Universal and Meaningful Connectivity (UMC) targets — into one scorecard, and
  benchmark it against the country's region. Use this for country briefs, market entry assessments
  and digital-divide analysis.
api: openapi/itu-datahub-openapi.yml
operations:
  - listCountries
  - listRegions
  - getIdiStructure
  - listIdiVersions
  - getIdiByCountry
  - getIdiByRegion
  - listUmcTopics
  - listUmcMacros
  - listUmcTargets
  - getUmcByCountry
---

# Build a country connectivity scorecard from ITU

Two ITU composites, one country. IDI scores a country's ICT development on a 0–100 scale across
Universal and Meaningful connectivity pillars. UMC scores it against explicit connectivity targets
by topic. Both are anonymous reads from `https://api.datahub.itu.int/v2`.

## Steps

### 1. Resolve the country's numeric `CountryID`

    GET /country/all

`listCountries`. Take `CountryID` — **not** `IsoCode`. This is the single most common mistake with
this API: the IDI and UMC country endpoints key on ITU's numeric id, while the data endpoints filter
by ISO code. Passing the wrong one returns HTTP 200 with an empty array and no error.

While you are here, record the country's `Regions` array — you need a `regionID` for step 5.

### 2. Establish the IDI edition and structure

    GET /idi/dashboard/version
    GET /idi/dashboard/all

`listIdiVersions` returns the available edition years. `getIdiStructure` returns the composite tree:
the IDI root, its Universal connectivity and Meaningful connectivity pillars, and the indicators
beneath each, keyed by `codeID`.

Keep this tree. The score endpoints return bare `codeID` → `score` pairs with no labels; the tree is
the only way to name them.

### 3. Get the country's IDI scores

    GET /idi/data/bycountryid/{countryID}

`getIdiByCountry` returns `codeID`, `score` and `dataYear` per node. Join `codeID` against the tree
from step 2 to label each score.

An empty array means ITU has not scored this country in the IDI — a real and common outcome for
small economies. Report it as "not scored", never as zero.

### 4. Get the country's UMC scorecard

    GET /umc/topic/all
    GET /umc/macro/all
    GET /umc/target/all
    GET /umc/bycountryid/{countryID}

`listUmcTopics` gives the six topics (People, Homes, Businesses, Affordability, Schools,
Infrastructure). `listUmcMacros` gives the two macros (Universal connectivity, Meaningful
connectivity). `listUmcTargets` gives the scoring bands — No data, Limited, Satisfactory, Advanced,
Target met — with their numeric ranges.

`getUmcByCountry` returns each indicator with its observed `answer`, its `targetMinValue` and
`targetMaxValue`, and the `targetDesc` describing what "met" means. Classify each observed value
into a band from `listUmcTargets` rather than inventing your own thresholds.

### 5. Benchmark against the region

    GET /region/all
    GET /idi/region/byregionid/{regionID}

`listRegions` resolves the regional grouping. `getIdiByRegion` returns the same `codeID` → `score`
shape as step 3, so the country-vs-region comparison is a direct join on `codeID` and `dataYear`.

Pick the region deliberately. `listRegions` returns several different classification families —
ITU/BDT (Africa, Americas, Arab States, Asia & Pacific, CIS, Europe), UN M49 including LDC/LLDC/SIDS,
and World Bank income classes by year. Comparing a country to its income peers and to its
geographic region answers different questions. Say which one you used.

## Rules

- **Match the year.** IDI and UMC edition years differ from the underlying indicator `dataYear`.
  Never compare scores across editions without saying so.
- **`/idi/dashboard/version` is data versioning, not API versioning.** It returns IDI edition years.
- **No data ≠ zero.** UMC has an explicit `targetID: 0` band labelled "No data" for exactly this
  reason. Carry it through to your output.
- **Attribute to ITU.** These are UN reference statistics. Name the composite, the edition year, and
  ITU as the source.
- **No rate limits are published and no rate-limit headers are returned.** The reference calls in
  steps 1, 2 and 4 are small and stable — cache them across countries rather than refetching.

## Reference values

Confirmed live 2026-07-26: `listIdiVersions` returns `[2023, 2024, 2025, 2026]`; `regionID=3` is
Arab States (ITU/BDT Classification); `getUmcByCountry` returns scored indicators for
`countryID=840`.
