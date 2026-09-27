# Weather feature

## Which service did we choose, and what are its limitations?
We chose weatherapi.com. It has a free plan with a limited number of calls per month, and gives a forecast for several days. The API is simple, it's just one GET request with our key and a city name.

## Should the integration be in the frontend or the backend?
Backend. Our server calls weatherapi and the page gets the forecast from our own /api/weather route.

## Are any of the choices problematic in our setup?
Yes, doing it in the frontend would be. The API key would have to be in the page, so anyone could find it in DevTools and use up our calls. In the backend the key stays on the server, in a .env file locally and in GitHub secrets later.

## How would we handle a large number of users without burdening the service?
We cache the forecast on the server. It's fetched once and every user gets the saved copy, so it doesn't matter how many users visit the page.

## How do we handle the limit on API calls?
The cache is refreshed at a fixed interval of 30 minutes, so weatherapi is called at most once every 30 minutes, around 1,500 calls a month. If weatherapi is down or we run out of calls, users still get the last forecast we saved.