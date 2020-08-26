Venue | Date and time | Duration
-- | -- | --
[Google Virtual Webmaster Unconf (online)](https://events.withgoogle.com/virtual-webmaster-unconference/) | Wednesday, August 26, 2020 1:00 PM (EDT) | 30 minutes

# CrUX Show and Tell

- automated monitoring of URL performance with the CrUX API using Google Sheets and Apps Script
  - [sheet](https://docs.google.com/spreadsheets/d/1orZNj4MRVgDeBWyrMCnyKl4gBNU-HouePo2jv2VTvc4/edit?usp=sharing)
  - [script](https://github.com/GoogleChrome/CrUX/blob/master/gs/crux-api.gs)
- augmenting the CrUX Dashboard with competitive analysis
  - [side by side LCP comparison](https://datastudio.google.com/s/t-qKAY_u-cc)
  - [custom query p75 timeseries comparison](https://datastudio.google.com/s/k5gyJ2eqonY)
  
```sql
SELECT
  yyyymmdd,
  origin,
  device,
  p75_fid,
  p75_cls,
  p75_lcp
FROM
  `chrome-ux-report.materialized.device_summary`
WHERE
  origin IN ('https://web.dev', 'https://dev.to', 'https://developers.google.com')
```
