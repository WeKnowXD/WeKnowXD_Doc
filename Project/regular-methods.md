# Regular Methods

## main

## router

## layoutHandler

## searchHandler

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

**Error response:** 500 Internal Server Error if something goes wrong with the database. This one is sent as plain text and not JSON

## registerHandler

**HTTP Method:** GET /api/search

**What it does:**

**Input:**
The user sends a request for registration that contains the floowing - username and email.

**How it works:**
When a user sends a GET request to /register, registerHandler renders register.html with an empty RegisterData{} struct, writing the resulting HTML directly to the response so the browser always shows a blank registration form with no pre-filled fields or error message.

**Success response:** 200 OK with JSON like this:
{
"statusCode": 200,
"message": "Registered successfully"
}

**Error response:**
{
"statusCode": 400,
"message": "You have to enter a username"
}

## initDB

## hashPassword

## HTTP-metoder og router

GET - SearchHandler
GET - registreHandler
