# API Methods

## getSearch
**HTTP Method:** GET /api/search

**What it does:** Searches the pages in the database for the word the user typed in.

**Input:** Query parameters in the URL

  q: what the user is searching for
  
  language: which language to search in. If it's left out, we use en

**How it works:**
1. If q is empty we just return an empty list and don't query the database at all.
2. If not, we look for pages where the content contains q and the language matches. We use ? placeholders in the query so the input can't be used for SQL injection.
3. We loop through the rows and add the title, url and content of each page to the result.

**Success response:** 200 OK with JSON like this:
{ "data": [ { "title": "...", "url": "...", "content": "..." } ] }

**Error response:** 500 Internal Server Error if something goes wrong with the database. This one is sent as plain text and not JSON.

## postRegister
**HTTP Method:** POST /api/register

**What it does:** Creates a new user.

**Input:** Form data (not JSON)

username: required, and can't already be in use

email: required, and has to have an @ in it

password: required

password2: optional, but if it's filled in it has to be the same as password. The old Python version checked this, so we kept it

**How it works:**
1. We check the fields one at a time and stop at the first one that's wrong.
2. If they're all fine, we check if the username is already in the database.
3. If it isn't, we hash the password and save the user in the users table.

**Success response:** 200 OK with JSON:
{ "statusCode": 200, "message": "Registered successfully" }

**Error responses:**
400 Bad Request if something in the form is wrong. The message tells you what:

"You have to enter a username"

"You have to enter a valid email address"

"You have to enter a password"

"The two passwords do not match"

"Username already taken"

500 Internal Server Error if saving the user to the database fails. Plain text again.

All of these errors are tested and works successfully.

**Notes:** Right now the password is hashed with MD5, which isn't safe for passwords. But we're sticking to the legacy code for now, but plans on changing it to possibly BCrypt.

## apiWeather
**HTTP Method:** GET /api/weather

**What it does:** Returns the weather forecast for Copenhagen. It uses a saved copy if we fetched it less than 30 minutes ago, so we don't run out of calls on weatherapi.

**Input:** None

**How it works:**
1. We lock weatherCacheMutex so only one request can use the cache at a time. This also means if a lot of requests come in when the cache is old, only the first one calls weatherapi.
2. If we have no saved forecast, or it's older than 30 minutes, we call fetchWeather to get a new one and save it in weatherCache.
3. If fetchWeather fails but we have an old forecast, we just keep using the old one. weatherCachedAt isn't updated so the next request tries again.
4. We send the forecast wrapped in data.

**Success response:** 200 OK with JSON like this:
{ "data": { "location": { ... }, "current": { ... }, "forecast": { "forecastday": [ ... ] } } }

**Error response:** 502 Bad Gateway if weatherapi fails and we have no old forecast to fall back on. The real reason is only written to the server log.
{ "data": { "error": "Could not fetch the weather forecast right now" } }

## fetchWeather
**HTTP Method:** None, it's a helper used by apiWeather. It calls GET https://api.weatherapi.com/v1/forecast.json

**What it does:** Gets the forecast from weatherapi and returns it as a Go map.

**Input:** None. The API key is read from the WEATHER_API_KEY env variable, and the city and number of days are in the URL.

**How it works:**
1. We read the API key with os.Getenv and build the URL.
2. We make the request with our own http.Client that has a 10 second timeout, since the normal http.Get could hang forever.
3. If weatherapi answers with anything other than 200, like a bad key or out of calls, we return an error with the status code.
4. We decode the JSON into a map and return it.

**Success response:** Returns the forecast map and nil.

**Error response:** Returns nil and an error if the request fails, times out, weatherapi returns a non 200 status, or the JSON can't be decoded.

