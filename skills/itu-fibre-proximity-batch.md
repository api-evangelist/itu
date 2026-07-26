---
name: Score coordinates against ITU fibre infrastructure
description: >-
  Submit a batch of geographic coordinates to ITU-D's Proximity to Fibre Node API and get back, per
  point, either the proximity band to the nearest terrestrial fibre node or a within-10km flag. Use
  this for connectivity gap analysis, site selection, and estimating population or facility reach to
  backbone infrastructure.
api: openapi/itu-proximity-openapi.yml
operations:
  - calcDistanceToNode
  - calc10kmIntersection
---

# Score coordinates against ITU fibre infrastructure

ITU-D runs this over the ITU Interactive Transmission Map (Broadband Maps) fibre network data. It is
batch-in / artifact-out: you POST a CSV of coordinates and get back a URL to a generated ZIP.

Base URL `https://bbmaps.itu.int/functionproximity`. ITU labels this a **demonstration** on its only
public page for it, and publishes no reference documentation.

## Steps

### 1. Build the CSV

A header row of exactly `X,Y` — `X` is longitude, `Y` is latitude — then one row per point.

    X,Y
    12.077916619646288,-15.22124972669551
    22.361249911846286,-11.80458307369551

ITU publishes a working sample at
`https://www.itu.int/en/ITU-D/Technology/Documents/sample_api.csv`, saved in this repo at
`sandbox/itu-proximity-sample.csv`.

The column names are required and case-sensitive. Anything else returns HTTP 500 with
`CSV file is missing required columns: ['X', 'Y']`.

### 2. Choose the route

`calcDistanceToNode` — `POST /route-calcdistancetonode` — returns a proximity **band** per point.
Observed values include `within 10km` and `>100km`. Use this when you need graduated distance.

`calc10kmIntersection` — `POST /route-calc10kmintersection` — returns a binary flag, `in10km`
(`0` = no, `1` = yes). Use this when you only need reach/no-reach, and for anything that lines up
with the Broadband Maps "population within reach of fibre node at 10 km" layer.

### 3. POST the CSV

Send the file as the raw request body with `Content-Type: text/csv`, and the function key as the
`code` query parameter:

    POST /route-calcdistancetonode?code=<function key>
    Content-Type: text/csv

    <csv body>

The key is not a secret you register for. ITU embeds a working key per route in its public web
client at `https://bbmaps.itu.int/web10km/`; see `authentication/itu-authentication.yml`. There is
no signup path to obtain your own. A missing or invalid key returns **HTTP 401 with an empty body**.

### 4. Fetch the result artifact

The 200 response body is a bare URL as `text/plain`:

    https://bbmaps.itu.int/api/output/exported_data_20260726_023009_<uuid>.zip

Download it. The ZIP contains one CSV, `exported_data_<YYYYMMDD>_<HHMMSS>_<uuid>.csv`, with your
original `X` and `Y` columns plus the computed column — `proximity` for `calcDistanceToNode`.

Row order is preserved, so join back to your input by position.

## Rules

- **Fetch the ZIP immediately and persist it yourself.** There is no job id, no status endpoint, no
  callback, and no documented retention policy for the generated artifact.
- **This operation is not idempotent.** Every call generates a new timestamped, UUID-named ZIP at a
  new URL. Repeating a request does not return the previous result. There is no idempotency key.
- **Keep batches modest.** No batch size limit, rate limit, or timeout is published, and each call
  consumes ITU compute to generate a real file. Chunk large workloads and pace them.
- **Client errors come back as 500.** A malformed CSV returns HTTP 500, not 400. Validate your
  header row before sending.
- **The key can be rotated without notice.** ITU has no announcement channel for these APIs — no
  changelog, no status page, no support address. Fail loudly on a 401 rather than retrying.
- **Attribute to ITU.** Results derive from the ITU Interactive Transmission Map. Note the date you
  ran the batch; the underlying fibre data is updated through ITU's research and validation process
  with no published cadence.

## Reference

Both routes confirmed HTTP 200 with real generated output on 2026-07-26 using ITU's published
`sample_api.csv`. See `sandbox/itu-sandbox.yml` for the confirmed output shape and
`errors/itu-problem-types.yml` for every observed failure mode.
