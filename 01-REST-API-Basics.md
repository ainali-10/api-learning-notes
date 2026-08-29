# REST API Basics

## What is REST?

REST stands for **Representational State Transfer**. It's an architectural style for designing networked applications using HTTP requests to perform CRUD (Create, Read, Update, Delete) operations on resources.

---

## HTTP Methods (Verbs)

REST APIs use standard HTTP methods to indicate what action you want to perform:

### 1. **GET** - Retrieve Data
- Used to fetch resources from the server
- **Safe** - doesn't modify data
- **Idempotent** - same request always returns same result
- **Example:** `GET /api/users/1` → Get user with ID 1

```
GET /api/users HTTP/1.1
Host: api.example.com
```

### 2. **POST** - Create New Data
- Used to create a new resource
- **Not idempotent** - multiple requests create multiple resources
- Sends data in the request body
- **Example:** `POST /api/users` → Create a new user

```
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

### 3. **PUT** - Update Entire Resource
- Replaces the entire resource
- **Idempotent** - multiple requests have the same effect as one
- Requires full resource data
- **Example:** `PUT /api/users/1` → Update entire user 1

```
PUT /api/users/1 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "age": 30
}
```

### 4. **PATCH** - Partial Update
- Updates only specific fields
- **Idempotent** - multiple requests have the same effect
- More efficient than PUT for partial updates
- **Example:** `PATCH /api/users/1` → Update only email

```
PATCH /api/users/1 HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "email": "newemail@example.com"
}
```

### 5. **DELETE** - Remove Resource
- Deletes the specified resource
- **Idempotent** - multiple requests have the same effect
- **Example:** `DELETE /api/users/1` → Delete user 1

```
DELETE /api/users/1 HTTP/1.1
Host: api.example.com
```

---

## HTTP Status Codes

Responses include status codes that indicate the result of the request:

### 2xx - Success
- **200 OK** - Request succeeded, data returned
- **201 Created** - Resource successfully created
- **202 Accepted** - Request accepted but processing not complete
- **204 No Content** - Request succeeded, no data to return (common with DELETE)

### 3xx - Redirection
- **301 Moved Permanently** - Resource moved to new URL
- **304 Not Modified** - Resource hasn't changed since last request

### 4xx - Client Error
- **400 Bad Request** - Invalid request format
- **401 Unauthorized** - Authentication required
- **403 Forbidden** - Authenticated but not allowed to access
- **404 Not Found** - Resource doesn't exist
- **409 Conflict** - Request conflicts with current state (e.g., duplicate)
- **422 Unprocessable Entity** - Validation failed

### 5xx - Server Error
- **500 Internal Server Error** - Server error
- **502 Bad Gateway** - Invalid response from upstream server
- **503 Service Unavailable** - Server temporarily unavailable

---

## HTTP Headers

Headers provide metadata about the request or response:

### Common Request Headers
```
Content-Type: application/json          # Format of request body
Authorization: Bearer <token>           # Authentication token
Accept: application/json                # Expected response format
User-Agent: MyApp/1.0                   # Client identification
```

### Common Response Headers
```
Content-Type: application/json          # Format of response body
Content-Length: 1234                    # Size of response body
Cache-Control: max-age=3600             # How long to cache
Set-Cookie: sessionid=abc123            # Set cookie on client
```

---

## Request/Response Structure

### Typical Request Structure
```
METHOD /endpoint HTTP/1.1
Host: api.example.com
Headers: value
Content-Type: application/json

{
  "request": "body",
  "data": "here"
}
```

### Typical Response Structure
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 150

{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2026-08-29T10:30:00Z"
}
```

---

## REST Principles

1. **Client-Server** - Separation between client and server
2. **Stateless** - Each request contains all needed info, server doesn't store context
3. **Cacheable** - Responses can be cached to improve performance
4. **Uniform Interface** - Consistent way of communicating
5. **Layered System** - Client doesn't know if connected directly to end server
6. **Code on Demand** - Server can extend client functionality (optional)

---

## URL Structure (Endpoints)

REST APIs use URLs (endpoints) to identify resources:

```
https://api.example.com/v1/users/123

├─ Scheme: https://
├─ Domain: api.example.com
├─ API Version: /v1
├─ Resource: /users
└─ Resource ID: /123
```

### Good URL Practices
- ✅ Use nouns for resources: `/users`, `/posts`, `/comments`
- ✅ Use IDs for specific resources: `/users/123`
- ✅ Use sub-resources logically: `/users/123/posts`
- ❌ Don't use verbs in URLs: `/getUsers` (use GET method instead)
- ❌ Don't mix HTTP methods: Use GET for retrieval, POST for creation

---

## Quick Reference Table

| Method | Purpose | Safe | Idempotent | Status Code |
|--------|---------|------|------------|-------------|
| GET    | Retrieve | Yes  | Yes        | 200         |
| POST   | Create   | No   | No         | 201         |
| PUT    | Replace  | No   | Yes        | 200         |
| PATCH  | Update   | No   | Yes        | 200         |
| DELETE | Remove   | No   | Yes        | 204         |

---

## Example API Flow

```
1. GET /api/users/1
   ↓ (Status 200)
   {
     "id": 1,
     "name": "John Doe",
     "email": "john@example.com"
   }

2. PATCH /api/users/1
   {
     "email": "newemail@example.com"
   }
   ↓ (Status 200)
   {
     "id": 1,
     "name": "John Doe",
     "email": "newemail@example.com"
   }

3. DELETE /api/users/1
   ↓ (Status 204 - No Content)
```

---

## Key Takeaways

- **GET** = Read, **POST** = Create, **PUT** = Replace, **PATCH** = Update, **DELETE** = Remove
- Status codes tell you if request succeeded (2xx), redirected (3xx), had client error (4xx), or server error (5xx)
- REST is **stateless** - each request stands alone
- Use **proper HTTP methods** and status codes for clear, predictable APIs.
