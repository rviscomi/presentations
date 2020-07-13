Venue | Date and time | Duration
-- | -- | --
[London Web Performance (online)](https://ldnwebperf.org/events/lwp-july-measurement-and-observability/) | 14th July 2020, 18:30 (GMT) | 20 minutes

# Measuring Web Vitals

**Abstract**: Without data, there's no way to know if we're delivering great experiences to our users. In this talk we'll look at tools for measuring and monitoring how real users experience Core Web Vitals, which are the loading, interactivity, and layout stability metrics that affect a site's performance.

## Intro to Core Web Vitals

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

## Web Vitals in the field

https://web.dev/vitals-tools/

Field data is the source of truth of how users are experiencing a page.

### RUM tools

https://web.dev/vitals-field-measurement-best-practices/

- some vendors have built-in support for Web Vitals
- most vendors allow you to report custom metrics
  - collect client-side data using [web-vitals.js](https://github.com/GoogleChrome/web-vitals)

```js
import {getCLS, getFID, getLCP} from 'web-vitals';

function sendToAnalytics({name, value, id}) {
  const body = JSON.stringify({name, value, id});
  // Use `navigator.sendBeacon()` if available, falling back to `fetch()`.
  (navigator.sendBeacon && navigator.sendBeacon('/analytics', body)) ||
      fetch('/analytics', {body, method: 'POST', keepalive: true});
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getLCP(sendToAnalytics);
```

### Chrome UX Report tools

https://web.dev/chrome-ux-report/

"CrUX" is a public dataset of real-user experiences from millions of public and popular websites.

#### PageSpeed Insights

![PSI](https://user-images.githubusercontent.com/1120896/87355992-50ec0000-c52f-11ea-80ac-ac4a21001a82.png)

https://developers.google.com/speed/pagespeed/insights/

PSI shows page and origin-level field data (where available) and prescribes how to fix common performance issues.

- updated daily, aggregate of previous 28 days of data
- drill down into desktop/mobile
- assesses Core Web Vitals performance pass/fail
  - using p75 and "good" thresholds for LCP, FID, and CLS
  
#### CrUX Dashboard

![CrUX Dash](https://user-images.githubusercontent.com/1120896/87355732-dcb15c80-c52e-11ea-8618-71760f759e77.png)

https://web.dev/chrome-ux-report-data-studio-dashboard/

The [CrUX Dashboard](g.co/chromeuxdash) visualizes UX trends for a given origin.

- track metric performance over time
- drill down into desktop/phone/tablet
- see distributions of device/connection types

#### CrUX BigQuery

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

#### CrUX API

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

#### Search Console

![Search Console](https://user-images.githubusercontent.com/1120896/87358441-da9dcc80-c533-11ea-8334-d5d0d4057633.png)

https://support.google.com/webmasters/answer/9205520

The Core Web Vitals report on Search Console shows a site-wide view of UX performance over time.

- URLs grouped by similar UX
- monitor the number of URLs with good/poor Core Web Vitals performance

#### Web Vitals extension

![Web Vitals extension](https://user-images.githubusercontent.com/1120896/87359822-a37cea80-c536-11ea-8b73-6eb730d6d44b.png)

https://chrome.google.com/webstore/detail/web-vitals/ahfhijdlegdabablpippeagghigmibma

The [Web Vitals Chrome extension](https://github.com/GoogleChrome/web-vitals-extension) provides a heads-up display of UX performance as you browse the web.

- see how Core Web Vitals data is actually measured and correlates with your perceived experience
- (soon) see how UX aligns with the distribution of other real-user experiences via CrUX API data

## Web Vitals in the lab

Lab data helps you understand why performance may be degraded and how to improve it. For results to be relevant, the tests must be calibrated using realistic user conditions.

### PageSpeed Insights

### Lighthouse

### WebPageTest
