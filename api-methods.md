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

