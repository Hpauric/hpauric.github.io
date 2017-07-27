---
published: true
---
I ran a couple of speed tests for my portfolio site. The results weren't pretty.

I got a 47 / 100 score (Desktop) from [Google's Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/).

[GTMetrix](https://gtmetrix.com/) gave me the following stats.

| Fully Loaded Time  | 4.9s   |
|--------------------|--------|
| Total Page Size    | 2.92MB |
| Requests           | 57     |

This is considerably below average! It looks like I have some work to do.

## Gzip compression

The biggest impact will be gzip compression. This is the best bang for your buck in terms of performance gains.

> GZip is one of those magical algorithms that make your content download much faster virtually for free. It generally only takes a flip of a switch in web server software like Node.js or nginx.

This was really easy to set up. I just had to `npm install compression` and call it in my `app.js` file.

```javascript
const express = require('express');
const app = express();
const compression = require('compression');

// compress all responses
app.use(compression());
```

This alone got me to a Google score of 87/100 for Desktop!

This one step cut about a third of the load time and page size.


| Fully Loaded Time  | 3.0s    |
|--------------------|--------|
| Total Page Size    | 1.93MB |
| Requests           | 58      |


## Eliminate render-blocking JavaScript and CSS in above-the-fold content

This is the next piece of advice for me from Google.
