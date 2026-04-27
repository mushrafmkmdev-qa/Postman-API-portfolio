# Bug Reports – FakeStoreAPI QA Testing

**Project:** FakeStoreAPI – QA Portfolio
**Tester:** Mushraf Kadar Mohideen
**Environment:** Dev | Base URL: https://fakestoreapi.com
**Tool:** Postman
**Total Bugs Found:** 3

---

## BR-001 | Products – GET /products/999 returns 200 OK with empty body instead of 404

| Field | Detail |
|---|---|
| **Bug ID** | BR-001 |
| **Module** | Products |
| **Endpoint** | GET /products/{id} |
| **Severity** | Medium |
| **Priority** | Medium |
| **Type** | Functional / API Design |
| **Status** | Open |

### Summary
Requesting a product with a non-existent ID (e.g. 999) returns HTTP 200 OK with an empty string `""` as the response body, instead of the expected HTTP 404 Not Found. This violates standard REST API conventions and can cause errors in client applications.

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
Body: "" (empty string)
```

### Impact
Client applications that check only the HTTP status code (200 = success) will treat this as a successful response and attempt to process an empty body — causing null pointer errors or broken UI. A proper 404 response would allow clients to handle the "not found" case correctly and show a meaningful error message to the user.

### Detected By
Newman test run — the automated assertion `Body is empty or null for missing product` caught this behaviour and confirmed the response body is an empty string instead of a structured error response.

---

## BR-002 | Cart – POST /carts accepts empty request body with no validation

| Field | Detail |
|---|---|
| **Bug ID** | BR-002 |
| **Module** | Cart |
| **Endpoint** | POST /carts |
| **Severity** | Low |
| **Priority** | Low |
| **Type** | Functional / Input Validation |
| **Status** | Open |

### Summary
The Add to Cart endpoint accepts and processes a request with a completely empty JSON body `{}`, returning HTTP 200 OK with a new cart ID. The API performs no input validation — missing required fields like `userId`, `date`, and `products` are silently ignored.

### Steps to Reproduce
1. Open Postman and send a POST request to `https://fakestoreapi.com/carts`
2. Set Content-Type header to `application/json`
3. Set request body to:
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
In a real e-commerce system, accepting empty cart creation requests would result in orphaned cart records with no user association. Input validation is a fundamental API quality requirement. Any cart created without a `userId` cannot be retrieved or managed later, creating ghost records in the database.

### Notes
Low severity here because FakeStoreAPI is a demo/mock API. However in any production system this would be a high-priority defect requiring immediate fix.

---

## BR-003 | Auth – POST /auth/login returns 201 Created instead of 200 OK

| Field | Detail |
|---|---|
| **Bug ID** | BR-003 |
| **Module** | Auth |
| **Endpoint** | POST /auth/login |
| **Severity** | Low |
| **Priority** | Low |
| **Type** | API Design / REST Convention |
| **Status** | Open |

### Summary
The login endpoint returns HTTP `201 Created` for a successful authentication response. According to REST API standards, `201 Created` is reserved for endpoints that create a new resource on the server. A login endpoint performs an action (authenticate and return a token) — it does not create a new resource — so the correct response code is `200 OK`.

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

| Status Code | When to Use | Example |
|---|---|---|
| `200 OK` | POST performs an action | Login, search, process payment |
| `201 Created` | POST creates a new resource | New user, new product, new order |

Login falls under `200 OK` — no new resource is created, only a token is returned.

### Impact
Low severity — authentication works correctly and the token is returned. However, client applications that strictly validate HTTP status codes expecting `200` for login will break. This also indicates the API does not follow REST conventions consistently, reducing its reliability as a reference implementation.

### Test Handling
Test script was written to accept both values to prevent false failures:
```javascript
pm.test("Status is 200 or 201", function () {
    pm.expect(pm.response.code).to.be.oneOf([200, 201]);
});
```

---

## Bug Summary

| Bug ID | Module | Endpoint | Severity | Status |
|---|---|---|---|---|
| BR-001 | Products | GET /products/{id} | Medium | Open |
| BR-002 | Cart | POST /carts | Low | Open |
| BR-003 | Auth | POST /auth/login | Low | Open |

---

*Reported by Mushraf Kadar Mohideen — mushraf.mkm.01@gmail.com*
