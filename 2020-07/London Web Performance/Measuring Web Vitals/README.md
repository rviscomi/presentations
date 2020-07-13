Venue | Date and time | Duration
-- | -- | --
[London Web Performance (online)](https://ldnwebperf.org/events/lwp-july-measurement-and-observability/) | 14th July 2020, 18:30 (GMT) | 20 minutes

# Measuring Web Vitals

**Abstract**: Without data, there's no way to know if we're delivering great experiences to our users. In this talk we'll look at tools for measuring and monitoring how real users experience Core Web Vitals, which are the loading, interactivity, and layout stability metrics that affect a site's performance.

## Intro to Core Web Vitals

https://web.dev/vitals/

![LCP](https://web.dev/vitals/lcp_8x2.svg)

**Simplified definition**: The time at which the largest image/text appeared.

- perceived loading speed
  - evolution from onload, first paint/render, first contentful paint
- changes as the page loads
- tips for [optimizing LCP](https://web.dev/optimize-lcp/)

![FID](https://web.dev/vitals/fid_8x2.svg)

**Simplified definition**: The delay from the first user interation until the browser can start handling the event.

- perceived responsiveness
  - null if user never interacts
- excludes event handler processing time
- tips for [optimizing FID](https://web.dev/optimize-fid/)

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
