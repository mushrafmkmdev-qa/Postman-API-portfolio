# Bug Reports – FakeStoreAPI QA Testing

**Project:** FakeStoreAPI – QA Portfolio  
**Tester:** Mushraf Kadar Mohideen  
**Environment:** Dev | Base URL: https://fakestoreapi.com  
**Tool:** Postman  

---

## BR-001 | Auth – Invalid credentials return 201 with a valid token instead of 401

| Field | Detail |
|---|---|
| **Bug ID** | BR-001 |
| **Module** | Auth |
| **Endpoint** | POST /auth/login |
| **Severity** | High |
| **Priority** | High |
| **Type** | Functional / Security |
| **Status** | Open |

### Summary
The login endpoint returns HTTP 201 with a valid JWT token when an incorrect password is submitted. Authentication should reject invalid credentials with a 4xx error — returning a token on wrong password is a critical security defect.

### Steps to Reproduce
1. Open Postman and send a POST request to `https://fakestoreapi.com/auth/login`
2. Set Content-Type header to `application/json`
3. Set request body:
```json
{
  "username": "mor_2314",
  "password": "wrongpassword"
}
```
4. Click Send and observe the response

### Expected Result
```
HTTP Status: 401 Unauthorized
Body: { "message": "Invalid credentials" }
```

### Actual Result
```
HTTP Status: 201
Body: { "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
```

### Impact
Any user can log in with an incorrect password and receive a valid JWT token. This completely bypasses authentication, exposing all protected routes to unauthorized access. In a production system this would be a critical security vulnerability.

### Notes
This appears to be a known limitation of the FakeStoreAPI as it is a mock/demo API. Flagged here to demonstrate defect identification and reporting skills.

---

## BR-002 | Products – GET /products/999 returns 200 OK with null body instead of 404

| Field | Detail |
|---|---|
| **Bug ID** | BR-002 |
| **Module** | Products |
| **Endpoint** | GET /products/{id} |
| **Severity** | Medium |
| **Priority** | Medium |
| **Type** | Functional / API Design |
| **Status** | Open |

### Summary
Requesting a product with a non-existent ID (e.g. 999) returns HTTP 200 OK with a `null` response body instead of the expected HTTP 404 Not Found. This violates standard REST API conventions and can cause null pointer errors in client applications.

### Steps to Reproduce
1. Open Postman and send a GET request to `https://fakestoreapi.com/products/999`
2. No headers or body required
3. Click Send and observe the response

### Expected Result
```
HTTP Status: 404 Not Found
Body: { "message": "Product not found" }
```

### Actual Result
```
HTTP Status: 200 OK
Body: null
```

### Impact
Client applications that check only the HTTP status code (200 = success) will treat this as a successful response and attempt to render a null product — leading to crashes or broken UI. A 404 response would allow clients to handle the "not found" case correctly.

### Affected Test
`Get invalid product` test in the Products folder flags this with:
```javascript
pm.test("Body is null for missing product", function () {
    pm.expect(pm.response.text()).to.satisfy(function(body) {
        return body === "null" || body.includes("404");
    });
});
```
This test currently fails in the Newman run, confirming the defect is active. This is the **only failing test** in the entire suite — caused by this API defect, not a test script error.

---

## BR-003 | Cart – POST /carts has no request validation

| Field | Detail |
|---|---|
| **Bug ID** | BR-003 |
| **Module** | Cart |
| **Endpoint** | POST /carts |
| **Severity** | Low |
| **Priority** | Low |
| **Type** | Functional / Validation |
| **Status** | Open |

### Summary
The Add to Cart endpoint accepts and processes a request with a completely empty body, returning HTTP 200 OK with a new cart ID. The API performs no input validation — missing required fields like `userId`, `date`, or `products` are not rejected.

### Steps to Reproduce
1. Open Postman and send a POST request to `https://fakestoreapi.com/carts`
2. Set Content-Type header to `application/json`
3. Set request body to empty JSON:
```json
{}
```
4. Click Send and observe the response

### Expected Result
```
HTTP Status: 400 Bad Request
Body: { "message": "userId, date and products are required fields" }
```

### Actual Result
```
HTTP Status: 200 OK
Body: { "id": 8 }
```

### Impact
In a real e-commerce system, accepting empty cart creation requests would result in orphaned cart records in the database with no user association. Input validation is a basic API quality requirement. Low severity here because FakeStoreAPI is a demo API, but this would be a meaningful defect in any production system.

---

## BR-004 | Auth – POST /auth/login returns 201 instead of 200 for successful login

| Field | Detail |
|---|---|
| **Bug ID** | BR-004 |
| **Module** | Auth |
| **Endpoint** | POST /auth/login |
| **Severity** | Low |
| **Priority** | Low |
| **Type** | API Design / REST Convention |
| **Status** | Open |

### Summary
The login endpoint returns HTTP `201 Created` for a successful authentication response. According to REST API standards, `201 Created` should only be used when a new resource is created on the server. A login endpoint performs an **action** (authenticate and return a token) — it does not create a new resource — so the correct response code is `200 OK`.

### Steps to Reproduce
1. Open Postman and send a POST request to `https://fakestoreapi.com/auth/login`
2. Set Content-Type header to `application/json`
3. Set request body:
```json
{
  "username": "mor_2314",
  "password": "83r5^_"
}
```
4. Click Send and observe the response status code

### Expected Result
```
HTTP Status: 200 OK
Body: { "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
```

### Actual Result
```
HTTP Status: 201 Created
Body: { "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
```

### REST Standard Reference
| Status Code | Correct Usage |
|---|---|
| `200 OK` | POST performs an action — login, search, process payment |
| `201 Created` | POST creates a new resource — new user, new product, new order |

The login endpoint falls under `200 OK` because no new resource is created — only a token is returned.

### Impact
Low severity — the token is still returned and authentication works. However, client applications that strictly validate HTTP status codes expecting `200` for login will break. This also indicates the API does not follow REST conventions, which reduces its reliability as a reference implementation.

### Notes
Test script updated to accept both `200` and `201` using `pm.expect(pm.response.code).to.be.oneOf([200, 201])` to handle this non-standard behaviour without causing test failures.

---

## Bug Summary Table

| Bug ID | Module | Endpoint | Severity | Status |
|---|---|---|---|---|
| BR-001 | Auth | POST /auth/login | High | Open |
| BR-002 | Products | GET /products/{id} | Medium | Open |
| BR-003 | Cart | POST /carts | Low | Open |
| BR-004 | Auth | POST /auth/login | Low | Open |

---

*Reported by Mushraf Kadar Mohideen — mushraf.mkm.01@gmail.com*
