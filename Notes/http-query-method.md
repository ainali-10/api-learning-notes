# HTTP QUERY Method: My Notes

> The new HTTP method that's basically **GET with a body**. Made official in **RFC 10008 (June 2026)**, and it's the first new HTTP method since 2010. Kinda a big deal fr.

---

## Table of Contents

- [What even is it?](#what-even-is-it)
- [GET vs POST vs QUERY](#get-vs-post-vs-query)
- [Why did they make it?](#why-did-they-make-it)
- [Two words you gotta know](#two-words-you-gotta-know)
- [Example](#example)
- [Where it's actually useful](#where-its-actually-useful)
- [Current status](#current-status)
- [Pentesting angle](#pentesting-angle)
- [Quick recap](#quick-recap)

---

## What even is it?

QUERY is a new HTTP method, same family as `GET`, `POST`, `PUT`, `DELETE`.

Simple version: **you send a detailed question in the request body, the server sends back the answer, and nothing gets changed on the server.**

---

## GET vs POST vs QUERY

| Method  | What it means                        | Has a body? | Safe? | Idempotent? |
| ------- | ------------------------------------ | :---------: | :---: | :---------: |
| `GET`   | "Give me that data"                  |     No      |  Yes  |     Yes     |
| `POST`  | "Here's data, do something with it"  |     Yes     |  No   |     No      |
| `QUERY` | "Here's my question, just answer it" |     Yes     |  Yes  |     Yes     |

So basically:

- **GET** = fetch this thing
- **POST** = create or change stuff
- **QUERY** = ask a detailed question, change nothing

---

## Why did they make it?

There was a gap in HTTP and devs were kinda stuck choosing between two not-so-great options.

**Problem with GET:**
- Everything goes in the URL, so complex filters turn into an unreadable mess
- URLs have length limits, and you don't always know them ahead of time (the RFC recommends supporting at least ~8000 octets)
- URLs get saved in **logs, browser history, bookmarks**, which is not great for sensitive stuff

**Problem with POST:**
- It can carry a big body, but POST means *"this might change something"*
- Caches can't treat it as a safe read
- Auto-retry after a network drop is risky (what if it creates the same thing twice?)

**QUERY fixes both**: big body like POST + safe-read guarantees like GET. Best of both worlds.

---

## Two words you gotta know

### Safe
The request doesn't ask the server to change anything. It only **reads**.
(The server can still do stuff behind the scenes like logging or hitting a database, but the resource itself stays the same.)

### Idempotent
Send it **1 time or 10 times** and you get the same result.
That's why it's chill to auto-retry when the network drops. You won't accidentally create 10 orders instead of 1.

---

## Example

The old way with GET (gets ugly fast):

```http
GET /users?role=admin&country=PK&sort=name&limit=10 HTTP/1.1
Host: example.com
```

The old workaround with POST (works, but semantically kinda wrong):

```http
POST /users/search HTTP/1.1
Host: example.com
Content-Type: application/json

{"role": "admin", "country": "PK", "sort": "name", "limit": 10}
```

The new way with QUERY:

```http
QUERY /users HTTP/1.1
Host: example.com
Content-Type: application/json

{"role": "admin", "country": "PK", "sort": "name", "limit": 10}
```

The body can be any content type as long as it makes sense as a query (JSON, form data, etc.).

---

## Where it's actually useful

- Big search / filter endpoints
- Complex queries that are a pain to squeeze into a URL
- Anywhere you don't want sensitive query details sitting in URLs and logs

---

## Current status

- Official standard now (RFC 10008)
- But support is still **new**, so a lot of frameworks, proxies, CDNs and WAFs might not handle it properly yet
- Always **test first** before relying on it in a real project

---

## Pentesting angle

> **Note:** these are **ideas to test**, not confirmed facts.

Since QUERY is new, stuff might not be ready for it:

- [ ] WAF rules that only cover `GET` / `POST` might let `QUERY` slip through
- [ ] Auth middleware or method-based access control might not cover `QUERY`
- [ ] CORS config might handle it differently (check preflight behavior)
- [ ] Logging might skip it, or log the body differently
- [ ] Try it on endpoints and see the response: `405`? `501`? Or does it just work?

---

## Quick recap

- QUERY = **GET with a body**
- Safe + idempotent = reads only, retry-friendly
- Fixes the ugly-URL problem of GET and the "maybe changes stuff" problem of POST
- Official since June 2026 (RFC 10008), but support is still catching up
- Worth testing for gaps in WAFs, auth, and method filtering

---

## Reference

- [RFC 10008: The HTTP QUERY Method](https://rfc-editor.org/rfc/rfc10008)
