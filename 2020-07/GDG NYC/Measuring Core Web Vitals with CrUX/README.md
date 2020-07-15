Venue | Date and time | Duration
-- | -- | --
[GDG NYC (online)](https://www.meetup.com/gdgnyc/events/271424273/) | Wednesday, July 15, 2020 5:00 PM (EDT) | 45 minutes

# Measuring Core Web Vitals with CrUX

**Abstract**: Without data, there's no way to know if we're delivering great experiences to our users. In this talk we'll look at using the Chrome UX Report (CrUX) to measure and monitor how real users experience Core Web Vitals, which are the loading, interactivity, and layout stability metrics that affect a site's performance.

## Intro to Core Web Vitals

> _"What are we trying to measure?"_

https://web.dev/vitals/

### Largest Contentful Paint

![LCP](https://web.dev/vitals/lcp_8x2.svg)

**Simplified definition**: The time at which the largest image/text appeared.

- perceived loading speed
  - evolution from onload, first paint/render, first contentful paint
- changes as the page loads
- tips for [optimizing LCP](https://web.dev/optimize-lcp/)

### First Input Delay

![FID](https://web.dev/vitals/fid_8x2.svg)

**Simplified definition**: The delay from the first user interation until the browser can start handling the event.

- perceived responsiveness
  - null if user never interacts
- excludes event handler processing time
- tips for [optimizing FID](https://web.dev/optimize-fid/)

### Cumulative Layout Shift

![CLS](https://web.dev/vitals/cls_8x2.svg)

**Simplified definition**: The proportion of the viewport that has been visually unstable.

- perceived stability
- sum of [layout shift scores](https://web.dev/cls/#layout-shift-score)
- tips for [optimizing CLS](https://web.dev/optimize-cls/)

----

**What makes these Web Vitals "core"?**

- applicable to all web pages
- measured using standardized web APIs
- collectively represent the UX
- surfaced in all Google web dev tools
  - factored into Google Search's upcoming [page experience](https://webmasters.googleblog.com/2020/05/evaluating-page-experience.html) signal
- refreshed annually

**How are the threhsolds determined?**

https://web.dev/defining-core-web-vitals-thresholds/

- high quality of experience
- realistic achievability

**What makes the distribution of experiences "good"?**

- metric quality is determined using 75th percentile
- assess a page or origin's real-user performance

## Using CrUX

> _"How do we measure it?"_

https://web.dev/chrome-ux-report/

"CrUX" is a public dataset of real-user experiences from millions of public and popular websites.

### PageSpeed Insights

![PSI](https://user-images.githubusercontent.com/1120896/87355992-50ec0000-c52f-11ea-80ac-ac4a21001a82.png)

https://developers.google.com/speed/pagespeed/insights/

PSI shows page and origin-level field data (where available) and prescribes how to fix common performance issues.

- updated daily, aggregate of previous 28 days of data
- drill down into desktop/mobile
- assesses Core Web Vitals performance pass/fail
  - using p75 and "good" thresholds for LCP, FID, and CLS
  
Example:
- [web.dev](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fweb.dev)
  
### CrUX Dashboard

![CrUX Dash](https://user-images.githubusercontent.com/1120896/87355732-dcb15c80-c52e-11ea-8618-71760f759e77.png)

https://web.dev/chrome-ux-report-data-studio-dashboard/

The CrUX Dashboard visualizes UX trends for a given origin.

- track metric performance over time
- drill down into desktop/phone/tablet
- see distributions of device/connection types

Examples:
- [Creating a dashboard](g.co/chromeuxdash)
- [Augmenting it with competitor analysis](https://datastudio.google.com/s/hV_DMqY-lMI)

### CrUX BigQuery

```sql
SELECT
  `chrome-ux-report`.experimental.GET_COUNTRY(country_code) AS country,
  fast_lcp / (fast_lcp + avg_lcp + slow_lcp) AS good_lcp,
  fast_fid / (fast_fid + avg_fid + slow_fid) AS good_fid,
  small_cls / (small_cls + medium_cls + large_cls) AS good_cls
FROM
  `chrome-ux-report.materialized.country_summary`
WHERE
  yyyymm = 202005 AND
  country_code IN ('kr', 'ng') AND
  origin = 'https://web.dev' AND
  device = 'desktop'
```

https://web.dev/chrome-ux-report-bigquery/

The CrUX dataset on [BigQuery](https://console.cloud.google.com/bigquery?p=chrome-ux-report) provides fine-grained origin-level insights about origins on a monthly basis.

- segment data by origin, month, connection type, device type, and country
- full histograms for all Core Web Vitals plus several other UX metrics
- joinable with the HTTP Archive dataset for deeper insights

Examples:
- ["Mastering CrUX" cookbook recipes](https://github.com/GoogleChrome/CrUX/tree/master/sql/mastering-crux)

### CrUX API

```sh
curl "https://chromeuxreport.googleapis.com/v1/records:queryRecord?key=$API_KEY" \
  --header 'Content-Type: application/json' \
  --data '{"url": "https://web.dev/fast/"}'
```

https://web.dev/chrome-ux-report-api/

The [CrUX API](https://developers.google.com/web/tools/chrome-user-experience-report/api/reference) is a RESTful API for URL and origin-level CrUX data.

- updated daily, aggregate of previous 28 days of data
- good/poor distributions and p75 for LCP, FID, and CLS
- drill down into desktop/phone/tablet
- free quota

Examples:
- [goo.gle/crux-api-key to get your API key](https://goo.gle/crux-api-key)
- [API Explorer](https://developers.google.com/web/tools/chrome-user-experience-report/api/reference/rest/v1/records/queryRecord?apix=true&apix_params=%7B%22resource%22%3A%7B%22origin%22%3A%22https%3A%2F%2Fweb.dev%22%7D%7D)
- [crux-api-util.js](https://github.com/GoogleChrome/CrUX/blob/master/js/crux-api-util.js)
- [Monitoring using Sheets](https://docs.google.com/spreadsheets/d/1orZNj4MRVgDeBWyrMCnyKl4gBNU-HouePo2jv2VTvc4/edit?usp=sharing)
    - [crux_api.gs for Apps Script](https://github.com/GoogleChrome/CrUX/blob/master/gs/crux-api.gs)

### Search Console

![Search Console](https://user-images.githubusercontent.com/1120896/87358441-da9dcc80-c533-11ea-8334-d5d0d4057633.png)

https://support.google.com/webmasters/answer/9205520

The Core Web Vitals report on Search Console shows a site-wide view of UX performance over time.

- URLs grouped by similar UX
- monitor the number of URLs with good/poor Core Web Vitals performance

Example:
- [Web Almanac's Core Web Vitals report on Search Console](https://search.google.com/search-console/core-web-vitals?resource_id=sc-domain%3Aalmanac.httparchive.org)
