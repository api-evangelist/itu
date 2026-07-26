---
name: Look up an ITU ICT indicator for a country
description: >-
  Resolve an ICT statistic by name to an ITU DataHub indicator, confirm its units and coverage, and
  fetch the time series for one country. The core ITU DataHub flow — use this whenever you need an
  authoritative figure for internet use, mobile subscriptions, broadband penetration, ICT skills or
  affordability for a specific country.
api: openapi/itu-datahub-openapi.yml
operations:
  - getIndicatorCategories
  - getIndicatorById
  - listCountries
  - getDataByCodeAndIso
---

# Look up an ITU ICT indicator for a country

ITU DataHub is the reference source for global ICT statistics. The API is anonymous — no key, no
signup, no `Authorization` header. Base URL `https://api.datahub.itu.int/v2`.

ITU publishes no documentation for this API. It is the backend of the DataHub portal and can change
without notice.

## Steps

### 1. Resolve the indicator name to a `codeID`

    GET /dictionaries/getcategories

`getIndicatorCategories` returns the whole dictionary as a category → sub-category → items tree.
Search the `items[].label` values for the statistic you want and take its `codeID`.

Do not guess a `codeID`. There is no search endpoint and no fuzzy matching — the dictionary is the
only resolver.

### 2. Confirm what the indicator actually measures

    GET /dictionaries/getbyid/{codeID}

`getIndicatorById` returns `label`, `codeDesc`, `units`, `answerType`, `startYear`, `endYear` and
`availableCountries`. Read `units` before reporting any number — ITU mixes percentages, counts and
"per 100 people" across indicators, and the value itself carries no unit.

Check `availableCountries` contains your country before step 4; that is how you distinguish "no
data" from "wrong identifier" later.

An unknown `codeID` here returns **HTTP 500 with a raw JavaScript error string**, not a 404. Treat
any 500 on this route as "no such indicator".

### 3. Get the country's ISO code

    GET /country/all

`listCountries` returns every economy with `IsoCode` (ISO 3166-1 alpha-3), `CountryID` (ITU's
numeric id) and `ShortName`. You need `IsoCode` for step 4.

This endpoint is the one place in the API that uses PascalCase field names. Everything else is
camelCase.

### 4. Fetch the time series

    GET /data/bycode/{codeID}/byiso/{iso}

`getDataByCodeAndIso` returns one record per year: `dataYear`, `answer[].value`, `dataSource` and
`dataNote`.

Use this, not `getDataByCode`. The unscoped form returns every country in one unpaginated array —
roughly 1.1 MB for a common indicator.

## Rules

- **`answer[].value` is a string here.** Parse it. On the regional aggregate endpoint the same field
  is a number. See `conventions/itu-conventions.yml`.
- **An empty array is ambiguous.** It means either "no data for this valid identifier" or "this
  identifier does not exist" — the API returns HTTP 200 for both. This is why step 2 matters.
- **Cite the source.** Every record carries `dataSource`, which names the reporting national
  regulator or marks the figure an `ITU estimate.`. Report the `dataYear` alongside the value; ITU
  series often lag two or more years for some countries.
- **No pagination, no rate limits, no headers.** There is no `limit`, `offset`, `page` or `cursor`
  parameter anywhere, and no `RateLimit-*` headers on any response. Self-throttle and cache — the
  reference data in steps 1 and 3 changes rarely.
- **No errors you can rely on.** See `errors/itu-problem-types.yml` for every observed failure shape.

## Reference values

Confirmed live 2026-07-26: `codeID=11624` is "Individuals using the Internet" (ITU code `i99H`),
`iso=USA` resolves, and `GET /data/bycode/11624/byiso/USA` returns the series from 2000 onward.
